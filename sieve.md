# Adspect Sieve: Enterprise Anti-fraud System


## Usage

```
# adspectd [options] --account=<aid> --apikey=<key>
# adspectd --help
```

In order to start `adspectd` you will need to specify an Adspect account ID via
the `--account` CLI option.  The daemon will run regardless of account status
or existence, but a valid active account is required to enable traffic analysis.

Invalid or inactive account will result in HTTP 401 Unauthorized errors upon
click processing.


## Operation

The daemon starts a plain HTTP service (either on `127.0.0.1:8003` by default or
on address and port specified via the `--listen` command line option) and listens
for incoming POST requests.

POST request body is parsed as JSON produced by Adspect browser fingerprint collector.
The daemon then analyzes the fingerprint and builds an array of mnemonic tags
each indicating a failed anti-fraud check.  An empty array means that a particular
fingerprint was determined valid.


## Modes

Two modes of operation are supported: simple and handover.  Mode is selected on
per-request basis based on whether the `Handover` request header exists.  These
modes differ in the mechanism of returning tags to a consumer application.

### Simple Mode

Simple mode is selected for requests that lack the `Handover` header.  Tags are
returned as a JSON-encoded array in response body with MIME type `application/json`.

Example NGINX configuration may be found in the `doc/simple.conf` file.

### Handover Mode

Handover mode is selected for requests that specify the `Handover` header, using
its value as an URI template for `X-Accel-Redirect` response header which in turn
"hands over" request processing to a different NGINX location (it is assumed that
`adspectd` is used behind an NGINX reverse proxy.)  For each request, the daemon
replaces the `<tags>` macro in the URI template with a comma-separated tag list,
appends request query string to it, and returns in the `X-Accel-Redirect` header
in a 200 OK response with no response body.

Example NGINX configuration may be found in the `doc/handover.conf` file.


## JavaScript

Adspect Sieve distribution comes with several data collecting JavaScript scripts.
All of them implement a single `collect()` function in the global scope that is
used to collect and optionally submit a browser fingerprint.  Function signatures
differ between files and are detailed below.

### ajax.min.js

```
collect(String postUrl, callback(Object))
```

Collect a fingerprint, submit it to `postUrl` via asynchronous `XMLHttpRequest`,
decode the result (array of tags as returned by simple mode), and call `callback`,
passing the decoded result to it as a sole argument.

### beacon.min.js

```
collect(String postUrl)
```

Collect a fingerprint and submit it to `postUrl` via the `Navigator.sendBeacon()`
method, falling back to asynchronous `XMLHttpRequest` if Beacon API is not available.

### return.min.js

```
Object collect()
```

Collect a fingerprint and return it as a JavaScript object without submitting.

### sync.min.js

Collect a fingerprint and submit it to the current URL (`location.href`) via synchronous
form submit procedure.  Code is run immediately.  This script is meant to be used with
intermediate party such as a tracker that will decode the form and relay fingerprint
data to `adspectd`.


## System Status

Any GET request sent to the daemon will produce a JSON-encoded object with some
internal counters.  It may be used for monitoring `adspectd` status.
