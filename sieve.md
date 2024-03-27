# Adspect Sieve&trade;

Adspect Sieve is an enterprise-grade web traffic security and quality
control system powered by in-depth device fingerprinting and VLA&trade;
machine learning technology.

Copyright &copy; 2019&ndash;2023 by [Adspect](https://www.adspect.ai/).
All rights reserved.

## adspectd

The server software that powers Adspect Sieve is called `adspectd`, which is
a Unix daemon with a simple JSON API and HTTP interface.  It answers HTTP
requests with information about website visitors and determines whether each
particular visitor is legitimate or malicious (i.e. fraudulent, suspicious,
or otherwise unwanted.)

The `adspectd` server is written in C++17.  Currently, it is built for and
supported on Debian-like systems only.  Still, release builds are statically
linked and should run on most contemporary Linux distributions, possibly with
minor tweaks to the `LD_LIBRARY_PATH` environment variable.

The external dependencies are:

| Library     | Package      | Description                  |
|:------------|:-------------|:-----------------------------|
| `libc`      | `libc6`      | GNU C standard library.      |
| `libstdc++` | `libstdc++6` | GNU C++ standard library v3. |
| `libgcc`    | `libgcc1`    | GCC support library.         |

## Usage

```bash
adspectd [options] [--statefile=path]
adspectd [options] [--account=aid] [--apikey=key]
adspectd [options] [--listen=127.0.0.1:8003]
adspectd [options] [--listen=/run/adspectd.sock]
adspectd --help
```

The daemon starts a plain HTTP 1.1 service that listens on `127.0.0.1:8003`
by default.  You may specify a different IP address and TCP port number to
listen on, or an absolute path to a Unix domain socket, using the `--listen`
command line option.

In order to use `adspectd` you will need to specify Adspect account ID and
API key either on the command line or via the [configuration API endpoint](#configuration).
The daemon will start regardless of account existence or key validity, but
a valid account with active subscription is required to enable the [scanner API endpoint](#scanner).
The daemon will perform periodic checks with Adspect servers in background.
Once acquired, active subscription status is maintained for 3 days before
expiring in order to mitigate any connectivity outages or subscription renewal
delays.  *This status is not stored on-disk* and exists purely at runtime.

Additionally, `adspectd` can store Adspect credentials in a file specified
via the `--statefile` (`-s`) command line option.  This file is read at
startup to obtain credentials to authorize with, and gets rewritten each time
credentials are changed via the [configuration API endpoint](#configuration).

Run `adspectd --help` to get information on available command line options.

## Security

The `adspectd` server is designed as a bare-bones microservice with the
[KISS principle](https://en.wikipedia.org/wiki/KISS_principle) in mind.
As such, it lacks access control features and therefore *should be deployed
behind a reverse HTTP proxy with an ACL*, a firewall, or otherwise shielded
from unauthorized access by any applicable means.

The server does not read or write files except for the PID file and the Unix
domain socket, if used.  It does not require superuser privileges to run.

## HA Clustering

The scanning service provided by `adspectd` is stateless: each scan request
is evaluated independently of other requests and does not have side effects.
This allows for building of high availability (HA) clusters with a simple
load balancer, such as HAProxy or NGINX.  The [heartbeat API endpoint](#heartbeat)
may be used for heartbeat.

## Docker

Adspect Sieve is Docker-ready and is shipped with a sample `Dockerfile`.
You may build the image from the distribution directory using `make`:

```bash
cd adspectd-2.0
make image
docker run -tip 8003:8003 adspectd:2.0
```

The image exposes the `adspectd` HTTP service on TCP port number 8003.

## JSON API

The API of `adspectd` uses JSON data encoding and is designed around HTTP
verbs: particular endpoint is determined by the HTTP method of the request.
`Content-Type: application/json` is assumed for all requests with content,
so the header is not required.

All endpoints respect the `X-Forwarded-For` request header when determining
the IP address of the client.  This is especially important with the
[scanner endpoint](#scanner) because it performs IP address checks as part
of its operation.

The path part of request URI is ignored, which allows for simple proxying of
incoming clicks to `adspectd` should the use case call for such operation,
e.g. transparent filtering in front of an ad tracker or DSP.

Available API endpoints are detailed below.  Requests made to nonexistent
endpoints will be answered with status `501 Not Implemented`.

### Scanner

```http
GET / HTTP/1.1
```
```http
POST / HTTP/1.1
```

This endpoint is used to scan click data and classify it either as valid
or invalid.  Data is divided into three parts, each part being optional:

- **Query string**: append full click query string to the API request URI;
- **Headers**: add each click header to the API request as is;
- **Request body**: put JSON fingerprint data collected by
  [one of JavaScript data collectors](#data-collectors) (omitting these data
  turns off fingerprint-based filters.)

`adspectd` inspects supplied data and builds an array of [mnemonic tags](#tags).
Each tag indicates a failed anti-fraud check.  An empty array means the click
has been determined valid.  Scanning is performed locally; no click data is
transmitted over the network.

This endpoint understands an optional `Fingerprint` header which may be used
to specify the field of the input JSON object that contains fingerprint data
(defaults to `data`.)  If it is set to an empty string, then the entire input
object is deemed to be fingerprint data.

Possible response codes:

| Status code             | Description                                  |
|:------------------------|:---------------------------------------------|
| 200 OK                  | Data processed successfully.                 |
| 503 Service Unavailable | Invalid credentials or expired subscription. |

Two modes of operation are supported: simple and handover.  Mode is selected
on per-request basis depending on whether the `Handover` request header has
been set.  The modes differ in how scan results are returned.

#### Simple Mode

Simple mode is selected for requests that lack the `Handover` header.
Tags are returned as a JSON-encoded array in response body.

Example NGINX configuration:

```nginx
location = /adspect {
	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	proxy_pass http://127.0.0.1:8003/;

	# Cross-origin resource sharing (CORS)
	add_header Access-Control-Allow-Origin *;
}
```

Example request:

```http
GET /?utm_medium=cpc&utm_source=search HTTP/1.1
X-Forwarded-For: 192.0.2.1
User-Agent: Mozilla/5.0
Referer: https://example.com/
```
```
200 OK
Content-Type: application/json
[]
```

#### Handover Mode

Handover mode is selected for requests that specify the `Handover` header.  It
is designed specifically for NGINX as a load balancer in front of `adspectd`.
This mode is useful in cases when you need to perform additional actions on
the results returned by `adspectd`, e.g. logging or saving in a database.

For each request, the daemon takes the value of the `Handover` header as a URI
template, replaces the `<tags>` macro in it with a comma-separated list of
tags, and returns the resulting URI in the
[`X-Accel-Redirect`](https://www.nginx.com/resources/wiki/start/topics/examples/x-accel/)
response header.  NGINX captures this header when doing `proxy_pass` and
performs internal redirection to "hand over" further request processing to
the location that matches the URI in the header.

Example NGINX configuration:

```nginx
location = /adspect {
	set $handover /click.php?$args;

	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	proxy_set_header Handover $handover&tags=<tags>;
	proxy_intercept_errors on;
	proxy_pass http://127.0.0.1:8003/;

	error_page 500 502 503 504 $handover;
}

location = /click.php {
	internal;
	fastcgi_pass unix:/run/php.sock;
}
```

Example request:

```http
GET /?utm_medium=cpc&utm_source=search HTTP/1.1
X-Forwarded-For: 192.0.2.1
User-Agent: Mozilla/5.0 AdsBot
Referer: https://example.com/
Handover: /click.php?tags=<tags>&utm_medium=cpc&utm_source=search
```
```
200 OK
X-Accel-Redirect: /click.php?tags=BOT&utm_medium=cpc&utm_source=search
```

### Configuration

```http
PATCH / HTTP/1.1
```

This endpoint is used to update runtime configuration of `adspectd`.
It accepts a JSON object that *must* contain the following fields:

| Field           | Description                                                                     |
|:----------------|:--------------------------------------------------------------------------------|
| `account`       | String, Adspect account ID (see [profile](https://clients.adspect.ai/profile).) |
| `api_key`       | String, Adspect API key (see [profile](https://clients.adspect.ai/profile).)    |
| `save`          | Boolean, whether to save supplied credentials internally.                       |

Possible response codes:

| Status code               | Description                                              |
|:--------------------------|:---------------------------------------------------------|
| 200 OK                    | Adspect credentials are valid; subscription is active.   |
| 204 No Content            | Adspect credentials are empty.                           |
| 400 Bad Request           | Malformed input data (e.g. missing fields.)              |
| 401 Unauthorized          | Adspect credentials are invalid.                         |
| 402 Payment Required      | Adspect credentials are valid; subscription has expired. |
| 500 Internal Server Error | Credentials validation failed due to technical error.    |

Example request:

```http
PATCH / HTTP/1.1
Content-Type: application/json

{
   "account": "1eb5363c-b763-65a8-95fe-72794ca03a45",
   "api_key": "2SKBvhadJCewXINq_QPRTIULJvBS-66z"
}
```
```
200 OK
```

### Heartbeat

```http
HEAD / HTTP/1.1
```

This endpoint may be used as a heartbeat for monitoring system availability
in HA clusters behind load balancers such as HAProxy or NGINX Plus.  It accepts
no data and always responds with status `200 OK`.

### System Status

```http
OPTIONS / HTTP/1.1
```

This endpoint is used to query status and runtime configuration of `adspectd`.
It returns a JSON object that contains the following fields:

| Field            | Description                                                                     |
|:-----------------|:--------------------------------------------------------------------------------|
| `account`        | String, Adspect account ID (see [profile](https://clients.adspect.ai/profile).) |
| `api_key`        | String, Adspect API key (see [profile](https://clients.adspect.ai/profile).)    |
| `active`         | Boolean, indicates whether subscription is active.                              |
| `requests_total` | Integer, total number of accepted HTTP requests.                                |
| `requests_scan`  | Integer, number of accepted scan requests.                                      |
| `requests_human` | Integer, number of scan requests determined legitimate.                         |
| `requests_robot` | Integer, number of scan requests determined malicious.                          |
| `requests_error` | Integer, number of erroneous HTTP requests.                                     |

This endpoint may be used for monitoring system availability and performance
with third party software such as Zabbix or Prometheus.

Example request:

```http
OPTIONS / HTTP/1.1
```
```
200 OK
Content-Type: application/json
{
   "account": "1eb5363c-b763-65a8-95fe-72794ca03a45",
   "api_key": "2SKBvhadJCewXINq_QPRTIULJvBS-66z",
   "active": true,
   "requests_total": 9,
   "requests_scan": 8,
   "requests_human": 3,
   "requests_robot": 5,
   "requests_error": 0
}
```

### Reset Counters

```http
DELETE / HTTP/1.1
```

This endpoint is used to reset request counters reported by system status
endpoint.  It accepts no data and always responds with status `200 OK`.

## Data Collectors

Adspect Sieve distribution comes with several data collectors written in
JavaScript.  They implement a single `collect()` function in the global
scope that is used to collect and optionally submit a browser fingerprint.
Function signatures differ between files and are detailed below.  Please
refer to the [Google Closure Compiler wiki](https://github.com/google/closure-compiler/wiki/Annotating-Types)
for details on type annotation syntax.

### ajax.min.js

```js
collect(String url, callback(Array))
```

Collect a fingerprint, submit it to `url` via asynchronous `XMLHttpRequest`,
decode the result (array of tags as returned by `adspectd` in simple mode),
and call the `callback` function, passing the decoded result as its argument.

This script is suitable for integration into DSP or JavaScript tag managers.
The `callback` function should decide what to do with the visitor based
on the scan results passed to it.  It might look like this:

```js
function callback(tags) {
  if (tags.length)
    location.replace("https://example.com/page-for-robots");
  else
    location.replace("https://example.com/page-for-humans");
}

collect("https://example.com/scan" + location.search, callback);
```

In this example, the visitor is redirected to one of two possible pages
depending on whether Adspect Sieve has flagged it as malicious or not.

### beacon.min.js

```js
collect(String url, Object data = {})
```

Collect a fingerprint and submit it to `url` via the `Navigator.sendBeacon()`
method, falling back to asynchronous `XMLHttpRequest` if
[Beacon API](https://developer.mozilla.org/en-US/docs/Web/API/Beacon_API)
is not available.  This script is meant for passive traffic quality analysis.

The optional second parameter may be used to supply pre-collected data that
will be submitted to `url` along with the fingerprint (in case you want to
submit your own data via `Navigator.sendBeacon()`).

### sync.min.js

Collect a fingerprint and submit it to the current URL (`location.href`)
synchronously via HTML form.  The code is not wrapped in a function and is run
immediately.  This script is meant to be used with intermediate party such as
an ad tracker that must accept the `POST` request with form data, extract
its `data` field, and relay its JSON contents to `adspectd`.

Stripped down relay script example in PHP:

```php
<?php

$url = 'http://127.0.0.1/?'.$_SERVER['REQUEST_URI'];
$headers = [];

foreach ($_SERVER as $key => $value) {
	if ($key !== 'HTTP_CONTENT_LENGTH' && substr_compare('HTTP_', $key, 0, 5) === 0) {
		$header = strtr(strtolower(substr($key, 5)), '_', '-');
		$headers[] = $header.': '.$value;
	}
}

/* heed HTTP_X_FORWARDED_FOR and HTTP_CF_CONNECTING_IP */
$headers[] = 'x-forwarded-for: '.$_SERVER['REMOTE_ADDR'];

$curl = curl_init();

curl_setopt($curl, CURLOPT_URL, $url);
curl_setopt($curl, CURLOPT_PORT, 8003);
curl_setopt($curl, CURLOPT_HTTPHEADER, $headers);
curl_setopt($curl, CURLOPT_POST, true);
curl_setopt($curl, CURLOPT_POSTFIELDS, $_POST['data']);
curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);

$tags = curl_exec($curl);
$code = curl_getinfo($curl, CURLINFO_HTTP_CODE);

curl_close($curl);

if ($code === 200) {
    $tags = json_decode($tags);
    if (empty($tags)) {
        /* handle click as valid */
    } else {
        /* handle click as invalid */
    }
} else {
    /* subscription has expired */
}
```

## Tags

Each tag represents a particular fact about a visitor as discovered by Sieve.
The exact nature of checks that are used to tag visitors is a trade secret.
**Not all tags indicate invalid or malicious visitors!**  Some tags (e.g.
`EMBED`) may be perfectly normal for some environments or advertising formats.
It is up to customer's code to determine whether to heed or ignore tags.

The table below documents all meaningful tags.  The meaning of its columns is
as follows:

* **Tag** – tag as reported by `adspectd` [scanner](#scanner);
* **Fraud** – average chance of tagged clicks being fraudulent (non-human);
* **Cloak** – whether clicks with the tag should be cloaked when `adspectd` is
  used for cloaking; *failing to do so may result in rejected ads or bans!*
* **False+** – average chance of false positive (wrongful) tagging;
* **Description** – functional meaning of the tag, *which must be taken into
  consideration when deciding whether to allow or block the visitor.*

| Tag     | Fraud  | Cloak | False+ | Description                                                                                         |
|:--------|:-------|:------|:-------|:----------------------------------------------------------------------------------------------------|
| `CLOAK` | Medium | Yes   | Low    | Cloaking-specific Adspect filters triggered.                                                        |
| `EXT`   | Medium | Yes   | Medium | Cloaking-specific Adspect [extended ("paranoid")](#paranoid_mode) filters triggered.                |
| `BOT`   | Yes    | Yes   | Low    | Openly declared bot.                                                                                |
| `APP`   | Medium | No    | No     | Click coming from or triggered by an application.                                                   |
| `DCH`   | High   | Yes   | Low    | IP address belongs to datacenter, hosting company, or IP transit.                                   |
| `CDN`   | High   | Yes   | Low    | IP address belongs to [CDN](https://en.wikipedia.org/wiki/Content_delivery_network) infrastructure. |
| `SES`   | Yes    | Yes   | Low    | Search engine spider.                                                                               |
| `GOV`   | Low    | Yes   | Low    | Government institution.                                                                             |
| `MIL`   | Low    | Yes   | Low    | Military facility.                                                                                  |
| `EDU`   | Low    | No    | Low    | University, college, school, or a similar educational institution.                                  |
| `LIB`   | Low    | No    | Low    | Public library.                                                                                     |
| `ANON`  | High   | Yes   | Low    | Public proxy, VPN service, or anonymizer.                                                           |
| `TOR`   | High   | Yes   | Low    | [Tor](https://www.torproject.org/) exit node.                                                       |
| `UNK`   | Yes    | Yes   | Low    | Unmapped IP address (bogon).                                                                        |
| `RSV`   | Yes    | Yes   | No     | Reserved IP address: local area network, class D, etc.                                              |
| `EMBED` | Medium | No    | No     | Embedding detected (`<iframe>`, `<embed>`, `<object>`, etc.)                                        |
| `TOUCH` | No     | No    | No     | Touchscreen browser support detected.                                                               |
| `FAKE`  | High   | Yes   | Low    | Browser is faking information about itself.                                                         |
| `DT`    | High   | Yes   | No     | DevTools protocol is in use (web automation or developer tools.)                                    |
| `AUTO`  | Yes    | Yes   | No     | Web automation.                                                                                     |
| `HW`    | Medium | Yes   | Medium | Highly suspicious hardware.                                                                         |
| `DATA`  | Low    | Yes   | No     | Missing or malformed fingerprint data (for [`POST` requests](#scanner).)                            |

### Paranoid Mode

Paranoid mode consists of extended filters and blocklists that provide better
cloaking at the expense of higher (sometimes dramatically) chances of false
positives.  In particular, it blocks known residential and mobile IP address
ranges that are known sources of reviewers, which also means blocking valid
visitors from those ranges.  Use with discretion.
