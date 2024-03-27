# REST API

Adspect provides a REST API for automating various tasks:

* Creating, editing, and deleting streams;
* Downloading integration PHP files;
* Managing guest access to reporting.

The API supports JSON and XML data encoding.  Examples below use JSON encoding for simplicity.

## Authentication

Adspect REST API employs
[HTTP Basic authentication scheme](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication#Basic_authentication_scheme)
in which API key is supplied as username and password is left blank.  You may find your API key in profile.

Each API request must contain two mandatory headers:

1. `Authorization: Basic <authKey>` to authenticate yourself with Adspect;
2. `Content-Type` for selecting content data encoding:
   * `Content-Type: application/json` for JSON data encoding;
   * `Content-Type: application/xml` for XML data encoding.

The `<authKey>` field in the Authorization header is formed by [base64-encoding](https://en.wikipedia.org/wiki/Base64)
a string composed of the API key and a colon appended to it.

Example code in PHP:

```php
<?php
$apiKey = 'SEbMw152aoe2ArffS7yjEJzJ_MFnd33e';
$authKey = base64_encode($apiKey . ':');
```

:::{tip}
For convenience, there's a readily encoded value of `<authKey>` available in your profile in the Authorization Header field.
:::

Some HTTP clients offer native support for HTTP Basic authentication scheme which will set the `Authorization` header for you (e.g.
[Python Requests](https://docs.python-requests.org/en/latest/) provides the `requests.auth.HTTPBasicAuth` class.)  In this case pass
your API key for username and leave password blank.  Example for [php-curl](https://www.php.net/manual/en/book.curl.php):

```php
<?php
$apiKey = 'SEbMw152aoe2ArffS7yjEJzJ_MFnd33e';
$curl = curl_init();
curl_setopt($curl, CURLOPT_USERPWD, $apiKey . ':');
```

## Endpoints

The base URL for all API endpoints is `https://api.adspect.net/v1/`.  Endpoint descriptions below specify
paths relative to this base URL.  HTTP method (verb) comes first, followed by the relative endpoint path,
e.g. the description `GET /streams` means making a GET request to the `https://api.adspect.net/v1/streams` URL.

## Collections

Certain object properties may take values only from concrete finite sets--collections.  The table below lists collection endpoints:

| Endpoint                          | Description |
|:----------------------------------|:------------|
| `GET /collections/presets`        | Presets for various traffic sources when creating streams. |
| `GET /collections/os`             | Operating systems for targeting and reporting. |
| `GET /collections/browsers`       | Browsers for targeting and reporting. |
| `GET /collections/engines`        | Browser engines for targeting and reporting. |
| `GET /collections/countries`      | Country codes for targeting and reporting. |
| `GET /collections/languages`      | Language codes for targeting and reporting. |
| `GET /collections/time-zones`     | Time zones for targeting and reporting. |
| `GET /collections/stream-modes`   | Stream modes. |
| `GET /collections/stream-actions` | Actions for money and safe pages (see below.) |
| `GET /collections/query-group-by` | Reporting funnel groupings. |

### Stream Actions

The following table details available actions for money and safe pages:

| Action    | Description |
|:----------|:------------|
| `local`   | Local file ("zero redirect"). |
| `proxy`   | Reverse proxy. |
| `fetch`   | Insert HTML code. |
| `iframe`  | Display in iframe. |
| `301`     | HTTP 301 redirect. |
| `302`     | HTTP 302 redirect. |
| `303`     | HTTP 303 redirect. |
| `noop`    | No action. |
| `refresh` | HTTP Refresh redirect. |
| `meta`    | HTML meta refresh redirect. |
| `return`  | Custom HTTP response code. |
| `php`     | Execute PHP code. |
| `js`      | Execute JavaScript code. |
| `xar`     | `X-Accel-Redirect` header. |
| `xsf`     | `X-Sendfile` header. |

## Managing Streams

A stream is represented as an object with the following properties:

:::{list-table} Stream Properties
:header-rows: 1

* - Property
  - Type
  - Description

* - `stream_id`
  - String
  - Stream identifier.

* - `account_id`
  - String
  - Account identifier.  Read-only.

* - `name`
  - String
  - Stream name.

* - `tags`
  - Array of strings
  - Stream tags (up to 32.)

* - `mode`
  - String
  - Mode, one of: `Filter`, `Review`, `Money`, or `White`.

* - `notes`
  - String
  - Any notes you may want to write down.

* - `money_pages`
  - Array of objects
  - Array of money pages, up to 254, each having the following properties:<br>
    `page` -- URL / file path / code (depending on action), string;<br>
    `action` -- action to perform for the visitor, string;<br>
    `arg_passthru` -- whether to perform URL parameters passthru, boolean;<br>
    `weight` -- weight for rotation, integer;<br>
    `enabled` -- whether this money page is enabled, boolean.

* - `rotator`
  - String
  - Money page rotator, either `Split` or `Timer`.

* - `safe_pages`
  - Array of exactly one object
  - Safe page.  Array of exactly one object with the following properties:<br>
    `page` -- URL / file path / code (depending on action), string;<br>
    `action` -- action to perform for the visitor, string;<br>
    `arg_passthru` -- whether to perform URL parameters passthru, boolean.

* - `filter_level`
  - Integer or string
  - Filtering level, one of:<br>
    0 or `Off`<br>
    1 or `Low`<br>
    2 or `Medium`<br>
    3 or `High`<br>
    4 or `Paranoid`

* - `enable_fp`
  - Boolean
  - Enable JavaScript fingerprinting and VLA™ model training.

* - `enable_ua`
  - Boolean
  - Enable built-in user agent-based filters.

* - `require_unique`
  - Boolean
  - Allow only unique visitors.

* - `require_touch`
  - Boolean
  - Require touchscreen support (needs JS fingerprinting.)

* - `allow_apps`
  - Boolean
  - Allow traffic from mobile apps.

* - `allow_embed`
  - Boolean
  - Allow traffic from frames, iframes, and embedded objects.

* - `countries`
  - Array of strings
  - Allowed country codes in [two-letter format](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2).

* - `os`
  - Array of strings
  - Allowed operating systems.

* - `browsers`
  - Array of strings
  - Allowed browsers.

* - `engines`
  - Array of strings
  - Allowed browser engines.

* - `languages`
  - Array of strings
  - Allowed browser language codes.

* - `timezones`
  - Array of integers
  - Allowed time zones as integer hour offsets from UTC.

* - `tz_match_ip`
  - Boolean
  - Match browser time zone with IP address time zone.

* - `skipClicks`
  - Integer
  - Number of first clicks to block (delayed start.)

* - `skip_clicks_mode`
  - String
  - Skip clicks mode, one of:<br>
    `all` -- decrease the skip clicks counter for all clicks;<br>
    `money` -- decrease the counter only for legitimate visitors;<br>
    `safe` -- decrease the counter only for bots, moderators, etc.

* - `ip_list_mode`
  - String
  - IP/ASN list mode, one of:<br>
    `black` -- block IP addresses/ASN in the blacklist except those in the whitelist;<br>
    `white` -- block IP addresses/ASN in the blacklist or not in the whitelist;<br>
    `special` -- block IP addresses/ASN in the blacklist, allow IP addresses/ASN in the whitelist.

* - `ip_on_review`
  - Boolean
  - Blacklist all IP addresses in Review mode.

* - `extrapolate_ipv4`
  - Integer between 0 and 255
  - IPv4 extrapolation.

* - `extrapolate_ipv6`
  - Integer between 0 and 64
  - IPv6 extrapolation.

* - `cost_parameter`
  - String
  - Click cost parameter name.

* - `sid_parameter`
  - String
  - Subaccount ID parameter name.

* - `cid_parameter`
  - String
  - Click ID parameter name.

* - `url_rules`
  - Array of objects
  - URL rules.  See [URL rule object structure description](#url-rule-object).

* - `schedule`
  - Array of objects
  - Schedule (dayparting.)  See [Schedule rule object structure description](#schedule-rule-object).

* - `ua_list_mode`
  - String
  - User agent list mode, one of:<br>
    `black` -- block user agents in the blacklist except those in the whitelist;<br>
    `white` -- block user agents in the blacklist or not in the whitelist;<br>
    `special` -- block user agents in the blacklist, allow user agents in the whitelist.

* - `ua_blacklist`
  - String or array of strings
  - User agent blacklist.

* - `ua_whitelist`
  - String or array of strings
  - User agent whitelist.

* - `referer_regex`
  - String
  - Regular expression for filtering by referrer.
:::

Example:

```json
{
  "stream_id": "1eacc6d0-875f-6f5c-bff8-00162501c2b4",
  "account_id": "1eaa2ce5-d4dd-63ec-b8a4-00162501c2b4",
  "name": "Example Stream",
  "tags": ["Tag1", "Tag2"],
  "mode": "Filter",
  "notes": "This is an example comment.",
  "money_pages": [
    {
      "page": "https://example.com/offer1?clid={clickid}",
      "action": "302",
      "arg_passthru": true,
      "weight": 10,
      "enabled": true
    },
    {
      "page": "https://example.com/offer2?clid={clickid}",
      "action": "302",
      "arg_passthru": true,
      "weight": 20,
      "enabled": true
    }
  ],
  "rotator": "Split",
  "safe_pages": [
    {
      "page": "safe.html",
      "action": "local",
      "arg_passthru": true
    }
  ],
  "filter_level": "High",
  "enable_fp": true,
  "enable_ua": true,
  "require_unique": true,
  "require_touch": false,
  "allow_apps": false,
  "allow_apps": true,
  "countries": ["AE", "HK"],
  "os": ["iOS", "macOS"],
  "browsers": ["Google Chrome"],
  "engines": ["Blink"],
  "languages": ["en", "fr", "es"],
  "timezones": [-5, -6, -7],
  "tz_match_ip": true,
  "ip_on_review": true,
  "cost_parameter": "cost",
  "sid_parameter": "zoneid",
  "cid_parameter": "clickid",
  "url_rules": [
    {
      "param": "zoneid",
      "op": "REGEX",
      "arg": "[[:alnum:]-_]{8,}",
      "enabled": true
    }
  ],
  "schedule": [
    {
      "days": ["Sun", "Sat"],
      "since": "15:30:00",
      "till": "22:15:00"
    }
  ]
}
```

### URL Rule Object

An URL rule object has the following structure:

```json
{
  "param": "gclid",
  "op": "REGEX",
  "arg": "[[:alnum:]-_]{55,}",
  "enabled": true
}
```

:::{list-table} URL Rule Properties
:header-rows: 1

* - Property
  - Type
  - Description

* - `param`
  - String
  - Name of the URL parameter that this rule operates on.

* - `op`
  - String
  - Rule operator.  See the operator table below for details.

* - `arg`
  - String
  - Operator argument.  See the operator table below for details.

* - `enabled`
  - Boolean
  - Rule is enabled flag.
:::

:::{list-table} URL Rule Operators
:header-rows: 1

* - Operator
  - Argument
  - Description

* - `ASSIGN`
  - Optional
  - Sets parameter to the value supplied in the rule argument.

* - `RENAME`
  - Optional
  - Renames parameter to the name supplied in the rule argument.

* - `DELETE`
  - Ignored
  - Deletes parameter.

* - `EXISTS`
  - Ignored
  - Checks that parameter exists.

* - `NEXISTS`
  - Ignored
  - Checks that parameter does not exist.

* - `REGEX`
  - Optional
  - Checks that parameter value matches regular expression supplied in the rule argument.

* - `IREGEX`
  - Optional
  - Checks that parameter value matches regular expression supplied in the rule argument (case-sensitive).

* - `NREGEX`
  - Optional
  - Checks that parameter value does not match regular expression supplied in the rule argument.

* - `NIREGEX`
  - Optional
  - Checks that parameter value does not match regular expression supplied in the rule argument (case-insensitive).

* - `EQ`
  - Optional
  - Checks that parameter value equals the rule argument.

* - `NEQ`
  - Optional
  - Checks that parameter value does not equal the rule argument.

* - `GT`
  - Optional
  - Checks that parameter value is greater than the rule argument.

* - `GE`
  - Optional
  - Checks that parameter value is greater than or equal to the rule argument.

* - `LT`
  - Optional
  - Checks that parameter value is less than the rule argument.

* - `LE`
  - Optional
  - Checks that parameter value is less than or equal to the rule argument.
:::

### Schedule Rule Object

A schedule (dayparting) rule object has the following structure:

```json
{
  "days": ["Sun", "Sat"],
  "since": "15:30:00",
  "till": "22:15:00"
}
```

:::{list-table} Schedule Rule Properties
:header-rows: 1

* - Property
  - Type
  - Description

* - `days`
  - Array of strings
  - Days of week the rule applies to.  Day is one of:<br>
    `Sun` -- Sunday<br>
    `Mon` -- Monday<br>
    `Tue` -- Tuesday<br>
    `Wed` -- Wednesday<br>
    `Thu` -- Thursday<br>
    `Fri` -- Friday<br>
    `Sat` -- Saturday

* - `since`
  - String
  - Allow visitors since HH:MM:SS, e.g. `15:30:00`.

* - `till`
  - String
  - Allow visitors till HH:MM:SS, e.g. `22:15:00`.
:::

### List Streams

```
GET /streams
```

This endpoint returns a list of streams configured in the account.  The list is paginated and ordered
by stream name in ascending [lexicographic order](https://en.wikipedia.org/wiki/Lexicographic_order).

Supported URL parameters:

* `page=1` -- sets the number of page to display, defaults to 1;
* `per-page=20` -- sets the number of streams per page, from 1 to 100, defaults to 20;
* `name=substr` -- lists only streams whose name contains `substr` (case-insensitive.)

### Stream List Metadata

```
HEAD /streams
```

This endpoint returns several headers with useful pagination information:

* `X-Pagination-Total-Count: 1000` -- total number of streams;
* `X-Pagination-Page-Count: 50` -- total number of pages.

Supported URL parameters:

* `per-page=20` -- sets the number of streams per page, from 1 to 100, defaults to 20.

### Get Stream

```
GET /streams/<id>
```

This endpoint returns the stream specified by `<id>`.

### Create Stream

```
POST /streams
```

This endpoint creates and returns a new stream.  Send a stream object in request body.
All of the stream object properties have default values, so you need to specify only those properties
that you want to assign concrete values to, e.g. `{"name":"My Stream"}`.

### Update Stream

```
PATCH /streams/<id>
```

This endpoint updates the stream specified by `<id>`.  Send a stream object in request body.
You need to specify only those properties that you want to change; the rest of them will be left unchanged.

### Copy Stream

```
COPY /streams/<id>
```

This endpoint copies the stream specified by `<id>`.  Optionally you may send a stream object in
request body--its properties will overwrite properties in the copy, saving you an extra `PATCH` call.

### Delete Stream

```
DELETE /streams/<id>
```

This endpoint deletes the stream specified by `<id>`.

## Integration PHP Files

You may obtain `index.php`, `filter.php`, and `ajax.php` files for any stream via the following requests:

* `index.php` and `filter.php` (both are the same file):<br>
  `GET https://clients.adspect.ai/getindex.php?sid=<id>`
* `ajax.php`:<br>
  `GET https://clients.adspect.ai/getindex.php?sid=<id>&mode=ajax`

Replace `<id>` with an actual Adspect stream ID.

## Guest Access to Reporting

Guest access to reporting lets third parties view pre-defined portions of your statistics without having to
log into the system.  Guess access management is centered around saved report queries:

1.  You create a saved query that specifies allowed report parameters (dates and filters);
2.  The API returns an ID of the saved query;
3.  A guest may then execute this query in the [Reporting section](https://clients.adspect.ai/reporting)
    by entering the saved query ID in the corresponding field, or by navigating to a link like
   `https://clients.adspect.ai/reporting?query_id=<id>`, where `<id>` should be replaced with the saved query ID.

:::{note}
At most 100 saved queries may be created.
:::

Saved query is represented by an object with the following properties:

:::{list-table} Saved Query Properties
:header-rows: 1

* - Property
  - Type
  - Description

* - `query_id`
  - String
  - Saved query ID.  Read-only.

* - `owner_id`
  - String
  - Saved query owner's account ID.  Read-only.

* - `date_from`
  - Integer or string
  - Minimum Unix timestamp since which the report will be pulled.

* - `date_to`
  - Integer or string
  - Maximum Unix timestamp until which the report will be pulled.

* - `time_zone`
  - String
  - Default time zone for displaying date and time.

* - `group_by`
  - Array of strings
  - Default sales funnel breakdown.

* - `stream_id`
  - Array of strings
  - Filter by stream IDs.

* - `ip_address`
  - Array of strings
  - Filter by IP addresses.

* - `asn`
  - Array of integers or strings
  - Filter by [autonomous system numbers (ASN)](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)).
    ASNs may be specified as strings, e.g. `AS65536` or `AS1.10`.

* - `country_code`
  - Array of strings
  - Filter by country codes in [two-letter format](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2).

* - `os`
  - Array of strings
  - Filter by operating systems.

* - `browser`
  - Array of strings
  - Filter by browsers.

* - `engine`
  - Array of strings
  - Filter by browser engines.

* - `sub_id`
  - Array of strings
  - Filter by subaccounts.

* - `click_id`
  - Array of strings
  - Filter by click IDs.

* - `mode`
  - Array of strings
  - Filter by stream modes.

* - `target`
  - Array of integers
  - Filter by displayed page:<br>
    0 -- safe page;<br>
    1--255 -- money page with the corresponding ordinal number.
:::

All of the properties are optional.  If a particular property is not needed, then you may either omit it or set it to `null`
(or an empty array `[]` where array values are expected.)

Example:

```json
{
  "query_id": "878efbf1-0fb9-4c69-ad36-40e714b0eeeb",
  "owner_id": "1eb5991f-a25b-68f4-b171-00162501c2b4",
  "date_from": 1577836800,
  "date_to": null,
  "time_zone": "Asia/Dubai",
  "group_by": [],
  "stream_id": ["6792f6ce-f153-439f-b223-f58749f1210f"],
  "ip_address": [],
  "asn": [],
  "country_code": ["HK", "AE"],
  "os": ["iOS", "macOS"],
  "browser": ["Apple Safari"],
  "engine": [],
  "sub_id": [],
  "click_id": [],
  "mode": ["Filter"],
  "target": []
}
```

### Query List

```
GET /reports
```

This endpoint returns a paginated list of created saved queries in the account.

Supported URL parameters:

* `page=1` -- sets the number of page to display, defaults to 1;
* `per-page=20` -- sets the number of queries per page, from 1 to 100, defaults to 20;

### Query List Metadata

```
HEAD /reports
```

This endpoint returns several headers with useful pagination information:

* `X-Pagination-Total-Count: 1000` -- total number of queries;
* `X-Pagination-Page-Count: 50` -- total number of pages.

Supported URL parameters:

* `per-page=20` -- sets the number of queries per page, from 1 to 100, defaults to 20.

### Get Query

```
GET /reports/<id>
```

This endpoint returns the query specified by `<id>`.

### Create Query

```
POST /reports
```

This endpoint creates and returns a new query.  Send a query object in request body.

### Update Query

```
PATCH /reports/<id>
```

This endpoint updates the query specified by `<id>`.  Send a query object in request body.
You need to specify only those properties that you want to assign values to;  the rest of the properties
will remain unchanged.

### Copy Query

```
COPY /reports/<id>
```

This endpoint copies the query specified by `<id>`.  Optionally you may send a query object in
request body--its properties will overwrite properties in the copy, saving you an extra `PATCH` call.

### Delete Query

```
DELETE /reports/<id>
```

This endpoint deletes the query specified by `<id>`.
