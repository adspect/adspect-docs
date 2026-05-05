# Streams

Traffic management in Adspect is organized through **streams**. A stream is a traffic channel managed as a whole, similar to a campaign in an ad network or a scheme in a TDS (traffic distribution system). You can manage streams in the Streams section of the clients area. Use the **+ Create Stream** button there to add a new stream. Below, we'll go through each stream setting in detail.

When creating a stream, you'll need to choose your use case. It's important to pick the one that best matches your scenario, as this directly affects performance. Default settings are already optimized for the chosen use case. You don't have to fill in every field--usually it's enough to set up [money and safe pages](#money-and-safe-pages) and let Adspect handle the rest.

(basic-settings)=
## Basic Settings

(name)=
### Name

The stream name is a human-readable identifier that helps you distinguish between different streams.  It's a good idea
to match stream names with ad campaigns on a one-to-one basis to keep your traffic sources consistent and clear.

(tags)=
### Tags

You can assign up to 32 mnemonic tags to a stream to label it meaningfully, e.g. "gambling", "betting", "nutra", "test", etc.
These tags also let you quickly search streams using the Search field in the top-right corner of the streams list.

(mode)=
### Mode

The stream mode is the main setting that turns traffic filtering on or off.

:::{list-table} Stream Modes
:header-rows: 1

* - Mode
  - Description

* - Filtering
  - Primary working mode--clicks are checked in real time and sent to either the money or safe page.
    All Adspect technologies, including [VLA™ machine learning](how-it-works.md#machine-learning), operate in this mode.

* - On&nbsp;Review
  - Special mode that you **must use** when your ad campaign is under review--it guarantees that Adspect will have no impact
    on your ad campaign's approval.  All visitors land on the safe page.<br><br>
    **Do not follow your stream's link while this mode is active, or you may get blocked automatically.**

* - All&nbsp;Money
  - Auxiliary mode where all visitors land on the money page.  Useful for testing the money page settings.

* - All&nbsp;Safe
  - Auxiliary mode where all visitors land on the safe page.  Useful for testing the safe page settings.
    Set this mode when your ad campaign is paused.
:::

"On Review" is the default mode when creating a new stream.  **You must always use it** when submitting campaigns for review.
Once a campaign is approved, change the stream mode to "Filtering" before actual traffic starts arriving.

(notes)=
### Notes

You can write down arbitrary notes in this field.  It serves a purely informational purpose.

(macros)=
## Macros

Adspect provides a variety of macros (also known as placeholders or tokens) that you can use in different stream fields
to automatically insert values of various attributes for each click.  Below is a table of available macros with examples:

:::{list-table} Adspect Macros
:header-rows: 1

* - Macro
  - Description
  - Examples

* - `{aid}`
  - ID of the account that received the click.
  - `d1577686-0f44-4b17-82e6-e61b71e4f086`

* - `{sid}`
  - ID of the stream that received the click.
  - `d550554f-bf5c-43c9-af38-ffa1e4445ece`

* - `{ip}`
  - IP address of the visitor.
  - `192.0.2.1`<br>
    `2001:db8::1`

* - `{asn}`
  - [Autonomous system number (ASN)](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) of the visitor's ISP.
  - `65536`

* - `{scheme}`
  - Protocol part of the click URL: `https` or `http`.<br><br>
    *May differ from the protocol shown in the browser's address bar when a CDN is used.*
  - If click URL is `https://example.com/`:<br><br>
    `{scheme}` → `https`

* - `{host}`
  - Domain name in the click URL.
  - If click URL is `https://example.com/`:<br><br>
    `{host}` → `example.com`

* - `{path}`
  - Path part of the click URL (after the domain).
  - If click URL is `https://example.com/posts/42`:<br><br>
    `{path}` → `/posts/42`

* - `{path:N}`
  - N-th part of the click URL path, separated by slashes.
  - If click URL is `https://example.com/posts/42`:<br><br>
    `{path:1}` → `posts`<br>
    `{path:2}` → `42`<br>
    `{path:-1}` → `42`<br>
    `{path:-2}` → `posts`

* - `{query}`
  - Full URL parameters of the click URL (query string after `?`).
  - If click URL is `https://example.com/?utm_campaign=Dating&utm_medium=banner`:<br><br>
    `{query}` → `utm_campaign=Dating&utm_medium=banner`

* - `{p:parameter}`
  - Value of the click URL parameter named `parameter`.
  - If click URL is `https://example.com/?utm_campaign=Dating&utm_medium=banner`:<br><br>
    `{p:utm_campaign}` → `Dating`<br>
    `{p:utm_medium}` → `banner`

* - `{h:header}`
  - Value of the click HTTP header named `header` (name is case-insensitive).
  - `{h:accept-language}` → `en;q=0.7`

* - `{c:cookie}`
  - Value of the click cookie named `cookie` (name is case-insensitive).
  - If request header is `Cookie: username=guest; language=en-US`:<br><br>
    `{c:username}` → `guest`<br>
    `{c:language}` → `en-US`

* - `{useragent}`
  - [User agent string](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) of the visitor's browser.
  - `Mozilla/5.0 (iPhone; CPU iPhone OS 18_6 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Mobile/15E148 Safari/604.1`

* - `{referrer}`
  - [Referrer URL](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Referer) of the click.
  - `https://www.google.com/search?q=cat`

* - `{country}`
  - [Two-letter country code](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) of the visitor.
  - `AE`

* - `{os}`
  - Operating system of the visitor (with version for Windows and Android).
  - `iOS`

* - `{browser}`
  - Name of the visitor's browser.
  - `Apple Safari`

* - `{engine}`
  - Name of the browser's rendering engine.
  - `WebKit`

* - `{language}`
  - Primary language of the visitor's browser.
  - `en-US`

* - `{languages}`
  - All browser languages, space-separated.
  - `en-US en`

* - `{clickid}`
  - [Click ID](#click-id)--external or generated internally.
  - If [Click ID](#click-id) is `{p:gclid}` and click URL has `gclid=EAIaIQobChMI&hellip;BwE`:<br><br>
    `{clickid}` → `EAIaIQobChMI&hellip;BwE`

* - `{cost}`
  - [Cost](#click-cost) of the click.
  - If [Click Cost](#click-cost) is `{p:cost}` and click URL has `cost=0.015`:<br><br>
    `{cost}` → `0.015`

* - `{subid}`
  - [Sub ID](#sub-id) of the click.
  - If [Sub ID](#sub-id) is `{p:utm_campaign}` and click URL has `utm_campaign=Dating`:<br><br>
    `{subid}` → `Dating`

* - `{tags}`
  - Space-separated [click tags](reporting.md#tags) (triggered filters).
  - `BOT GEO REF`

* - `{unixtime}`
  - [Unix timestamp](https://en.wikipedia.org/wiki/Unix_time) of the click.
  - `946674000`
:::

## Money and Safe Pages

Money and safe pages determine how traffic is routed--which pages are shown to real visitors and which to bots.

Both fields support [macros](#macros), for example:
```
https://example.com/offer?clickid={clickid}&geo={country}&os={os}
```
When using macros in money/safe page URLs, their values are
[URL-encoded](https://developer.mozilla.org/en-US/docs/Glossary/Percent-encoding) before being inserted into the link,
since they are intended for substituting URL parameter values.  If you need to disable URL encoding for a specific macro,
add an underscore `_` immediately after the opening curly brace, e.g. `{_subid}`.  In all other stream fields that
support macros, URL encoding is not applied.

### Money Page

This is your actual landing page or offer that you are going to advertise. The "money" word is intended
to indicate that this is the page that makes you money. You can specify up to 32 money pages for A/B testing.
Traffic will be distributed among them according to the rules of the selected [rotator](#rotator).

Depending on the selected [action](#action), this field can contain various values such as URLs, paths to local files,
PHP or JavaScript code, etc.  The two most common value types are URLs and paths.

* **A URL** is a link that you normally see in your browser's address bar, e.g. `https://example.com/page.php`.
  This may be your offer in a CPA network, a smartlink, a tracking link in a third party tracker, a TDS stream, etc.
  URLs *must* start with `http://` or `https://`, otherwise they will be treated as paths.

  With redirect actions, you can also use various non-HTTP URLs to do special tasks on your visitors' devices.
  Some of the more common examples:

  * `mailto:user@example.com` will open up a default e-mail program in compose mode;
  * `tel:+08001234567` will dial the number on mobile devices and some desktops with installed telephony software;
  * `market://details?id=app` will bring the visitor to a particular app's page in Google Play.

  This is particularly useful with the so called "deep links" that link to mobile in-app content.

* **A path to a local file or directory,** e.g. `page.php` or `/landers/page.html`.  The word "local" here means
  that the file or directory the path points to is supposed to reside on the same server where you put our PHP file
  (more on PHP files will be described in the [Intergration](integration.md) chapter), i.e. on the same domain
  that will be used for your final link.

(action)=
#### Action

This is the action to peform for a visitor.  Adspect supports many different types of actions.
You will normally use just two or three of the most common actions.

(parameter-passthrough)=
#### PT (Parameter Passthrough)

PT stands for "URL parameter passthrough." When enabled, parameters passed in the incoming URL will be
appended to money page URL or file.

For example, consider the stream's money page is configured as follows:
```
https://example.com/?utm_campaign=sweeps
```

A visitor accesses `index.php` of the stream using the following URL:
```
https://tracker.test/lander/index.php?utm_medium=ppc&utm_source=search
```

If the visitor is considered legitimate, they will be redirected to the money URL with URL parameters combined
from both of the above:
```
https://example.com/?utm_campaign=sweeps&utm_medium=ppc&utm_content=search
```

#### Weight

Each money page has a "weight" parameter, which defaults to 10.  It is used only for A/B testing when you have more than one
money page.  The exact effect of "weight" depends on the selected [rotator](#rotator).  If you have only one money page,
this parameter is ignored.

#### "On" Checkbox

The "On" checkbox lets you turn individual money pages on or off.  This is useful for removing poorly performing offers or
landing pages from A/B testing without deleting them from the list.

### Rotator

The rotator controls how multiple money pages are rotated--that is, how Adspect decides which money page to show to each visitor.
If only a single money page is specified, the rotator has no effect.

#### Split Rotator

This is the default rotator that splits traffic across all enabled money pages according to their weights (A/B testing).
The more the weight is, the more traffic that money page will receive, proportionally.

For example, if you have three money pages with weights 10, 15, and 25, the first page will receieve about
20% of all human traffic, the second page will receieve 30%, and the third page will get around 50%.

Since this rotator is based on a pseudorandom number generator (PRNG), there may be distribution bias on small scale.
However, mathematical properties of the PRNG guarantee that distribution will reach target weights on distance.

#### Timer Rotator

Timer rotator cycles through enabled money pages, using weight as a number of seconds that a money page is active for.

For instance, if you have three money pages with weights 60, 120, and 180, the first page will be shown to visitors
for 1 minute, then the rotator will cycle to the second page and display it to incoming clicks for 2 minutes, then go to
the third page and use it for 3 minutes, then cycle back to the first one, and so on.

This rotator is useful for automatic time-based switching of domains.

### Safe Page

This is the safe page to show to moderators, robots, scrapers, etc.  It should not contain any sensitive content
that may put your affiliate campaign in danger or in violation of any rules.  Everything described above for
the money page fields also applies to the safe page.

:::{important}
In most cases, your safe page **must** be displayed without redirects!
Consider using either the local file or the reverse proxy actions.
:::

## Actions

This section explains the mechanics of page actions.  Each action has its own use cases and serves a specific purpose.

:::{important}
The same action may have different behaviors with PHP and JavaScript integrations.  In such cases, JavaScript integration behavior
is explained in a standalone paragraph after the general description which applies to PHP integration only.
:::

(local-file)=
### Local File ("Zero Redirect")

This action displays the specified local file directly without redirect.

:::{tip}
This is the most secure action, and we strongly advise to use it wherever possible.
:::

There are several ways to specify a local file:

:::{list-table}
:header-rows: 1

* - Syntax
  - Examples
  - Description

* - Relative path
  - `page.php`<br>
    `folder/page.html`
  - A path relative to the location of the Adspect integration PHP file.<br><br>
    For example, if you specify the path `landers/page.html` and upload the Adspect PHP file at `https://example.com/ads/index.php`, Adspect will display the page at `https://example.com/ads/landers/page.html`.

* - Absolute path
  - `/var/www/html/index.html`
  - An exact file path specified from the root of the server's file system tree.

* - URL
  - `https://example.com/`<br>
    `//example.com/page.php`
  - The path part of the URL is used relative to the visited domain's root.  The scheme and domain parts of the URL are ignored.<br><br>
    For example, if you specify the URL `https://dummy.test/landers/page.html` and upload the Adspect PHP file at `https://example.com/ads/index.php`, Adspect will display the page at `https://example.com/landers/page.html`.
:::

:::{important}
Usually this action is used to display an HTML or PHP file.  In this case, **you should** put the Adspect PHP file in the same directory.  Otherwise all relative links on the page will be broken because the visitor's browser will not be aware that the page's resources (images, styles, scripts) should be loaded from a different directory--there's no redirect to inform it of the actual location of those files.
:::

What exactly happens depends on the file name suffix (extension):

:::{list-table}
:header-rows: 1

* - Suffix
  - Examples
  - Action

* - `/` (directory)
  - `folder/`<br>
    `folder`
  - Adspect will display the file `index.php`, `index.html`, or `index.htm` inside the directory, in that order.

* - `.php`<br>
    `.phtml`<br>
    `.php5`<br>
    `.php4`<br>
    `.php3`
  - `page.php`
  - The file will be executed as a PHP script using the [`require`](https://www.php.net/manual/en/function.require.php) PHP directive.

* - `.html`<br>
    `.htm`
  - `page.html`
  - The file will be displayed as a static HTML page.

* - Anything else
  - `app.apk`
  - The file will be downloaded by the browser.
:::

When using the local file action, you can add parameters with [macros](#macros) after the file name of your page, and they will be parsed and made available in PHP code in the [`$_GET` superglobal variable](https://www.php.net/manual/en/reserved.variables.get.php). For example:
```
page.php?cid={clickid}
```
The value of this macro can be accessed in the `page.php` code like this:
```php
<a href="https://example.com/offer?clickid=<?= $_GET['cid'] ?>">
  This is a link to the offer
</a>
```

:::{important}
With JavaScript integration, this action loads the page via synchronous `XMLHttpRequest` and replaces the safe page content with it
without redirecting.  This will only work if your money and safe pages are on the same domain, or if your money page is served with
a proper [Access-Control-Allow-Origin header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)
that allows [cross-origin resource sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).
:::

(reverse-proxy)=
### Reverse Proxy

[Reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy) displays a third-party website on your domain via smart
HTTP request proxying.  It creates a fully dynamic, navigatable replica of another website.
Most websites are proxied correctly, however, in some edge cases the result may appear broken or even blank.

This action is best suited for displaying remote safe pages as if they were located on your own domain.
**Not recommended for displaying money pages as it may break internal page logic.**

:::{important}
With JavaScript integration, this action loads the page via synchronous `XMLHttpRequest` and replaces the safe page content with it
without redirecting.  This will only work if your money and safe pages are on the same domain, or if your money page is served with
a proper [Access-Control-Allow-Origin header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)
that allows [cross-origin resource sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).
:::

(insert-html-code)=
### Insert HTML Code

This action is a simpler version of reverse proxy that fetches a remote page and inserts
its HTML code into the current page without performing the more complex link substituion to achieve seamless
navigation.  This action may be used to display self-contained pages from a remote repository without redirects.

:::{important}
With JavaScript integration, this action loads the page via synchronous `XMLHttpRequest` and replaces the safe page content with it
without redirecting.  This will only work if your money and safe pages are on the same domain, or if your money page is served with
a proper [Access-Control-Allow-Origin header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)
that allows [cross-origin resource sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).
:::

(iframe)=
### Display in Iframe

Displays a web page using an [`<iframe>` HTML tag](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe)
without changing the URL in the browser's address bar.

:::{attention}
Websites may forbid displaying their content inside an iframe by using the
[X-Frame-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options) response header, so this action
may not work.
:::

:::{warning}
Contrary to what is widely believed, iframing is considered a redirect by many ad networks because it emits
a trackable HTTP request.  It is not as safe as it may seem to be.  Consider using reverse proxy instead.
:::

(redirect-301)=
### HTTP 301 Moved Permanently

[HTTP 301 Moved Permanently](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/301) is the permanent redirect.
These redirect responses may be cached by browsers, which means that if a visitor goes by the same cloaked link again,
then the browser may instantly redirect them to where they were redirected before (the cached link), bypassing the cloaker.

:::{attention}
This behavior is at the sole discretion of a particular browser implementation and thereby must not be relied upon if security
is at stake.
:::

:::{important}
With JavaScript integration, this action performs a [JavaScript replace() redirect](streams.md#js-replace).
:::

(redirect-302)=
### HTTP 302 Found

[HTTP 302 Found](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/302) is the usual redirection as you know it,
also known as a temporary redirect.  These redirect responses are not cached by browsers, hence accessing the same
cloaked link again will lead to re-scanning the visitor by Adspect.

:::{tip}
If you don't know which redirect type to choose, go with HTTP 302 Found redirect.
:::

:::{important}
With JavaScript integration, this action performs a [JavaScript replace() redirect](streams.md#js-replace).
:::

(redirect-303)=
### HTTP 303 See Other

[HTTP 303 See Other](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/303) is a type of redirect similar to
HTTP 302 Found.

:::{important}
With JavaScript integration, this action performs a [JavaScript replace() redirect](streams.md#js-replace).
:::

(redirect-307)=
### HTTP 307 Temporary Redirect

[HTTP 307 Temporary Redirect](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/307) is a type of redirect similar to
HTTP 302 Found.

:::{important}
With JavaScript integration, this action performs a [JavaScript replace() redirect](streams.md#js-replace).
:::

(redirect-308)=
### HTTP 308 Permanent Redirect

[HTTP 308 Permanent Redirect](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/308) is a type of redirect similar to
HTTP 301 Moved Permanently.

:::{important}
With JavaScript integration, this action performs a [JavaScript replace() redirect](streams.md#js-replace).
:::

(no-action)=
### No Action

Nothing will be done; the visitor will be left where they are.  This action is normally only used together with
[reverse PHP integration](integration.md#reverse-php-integration).  It may be used to implement passive tracking
without any traffic filtering by integrating a stream into your website with both money and safe page actions
set to No Action.

(http-refresh)=
### HTTP Refresh Header

A special type of HTTP redirect that is performed with an HTTP 200 OK response code.  When used in combination with
[reverse PHP integration](integration.md#reverse-php-integration), it also returns content of the page Adspect was
integrated into like No Action does.

:::{important}
With JavaScript integration, this action performs a [JavaScript replace() redirect](streams.md#js-replace).
:::

(meta-refresh)=
### HTML Meta Refresh

An HTML-only variant of the previous HTTP Refresh redirect performed with
a [`<meta>` HTML tag](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta).  Use cases are similar.

:::{note}
Some types of "dumb" bots do not follow these redirects.
:::

:::{important}
With JavaScript integration, this action performs a [JavaScript replace() redirect](streams.md#js-replace).
:::

(form-submit)=
### Form Submit

A redirect triggered by submitting an HTML form using JavaScript.

:::{note}
"Dumb" bots without JavaScript support will not follow this redirect because of their inability to execute the script.
:::

(js-assign)=
### JavaScript assign() Redirect

A JavaScript redirect that is done via the [`location.assign()` method](https://developer.mozilla.org/en-US/docs/Web/API/Location/assign).

:::{note}
"Dumb" bots without JavaScript support will not follow this redirect because of their inability to execute the script.
:::

(js-replace)=
### JavaScript replace() Redirect

A JavaScript redirect that is done via the [`location.replace()` method](https://developer.mozilla.org/en-US/docs/Web/API/Location/replace).
Unlike the JavaScript `assign()` redirect, it does not save the current page in the browser session history, so the visitor will not
be able to navigate back to the page they were redirected from.

:::{note}
"Dumb" bots without JavaScript support will not follow this redirect because of their inability to execute the script.
:::

(frame-break)=
### Break Out of Frame

A JavaScript redirect that opens a link in the parent browser window even if the cloaked link is displayed in a frame or iframe,
thereby making the browser navigate out ("break out") of the frame.
If there's no parent frame, this redirect is equivalent to the [JavaScript `assign()` redirect](streams.md#js-assign).

:::{important}
This feat will not work with [sandboxed iframes](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/iframe#sandbox).
:::

:::{note}
"Dumb" bots without JavaScript support will not follow this redirect because of their inability to execute the script.
:::

(http-response-code)=
### Custom HTTP Response Code

Returns a [custom HTTP response code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes) specified
in the page field, e.g. `404` to display a "404 Not Found" error page.  This action may be used to simulate
a server-side error with 50x codes or explicitly block access to a resource with a 403 Forbidden response.

:::{important}
With JavaScript integration, this action does nothing.
:::

(execute-php-code)=
### Execute PHP Code

Executes PHP code specified in the page field, for example:
```php
echo '<h1>Hello, world!</h1>';
```

:::{important}
With JavaScript integration, this action does nothing.
:::

(execute-js-code)=
### Execute JavaScript Code

Executes JavaScript code specified in the page field, for example:

```js
document.write("<h1>Hello, world!</h1>");
```

This is a special purpose action that may be used to implement complex click processing logic like adding or
removing safe page content to turn it into money page, changing element styles, attaching scripts or pixels, etc.
It is most useful with [JavaScript integration](integration.md#javascript-integration).

:::{note}
"Dumb" bots without JavaScript support will not execute the code.
:::

(x-accel-redirect)=
### X-Accel-Redirect Header

This action allows Adspect to interoperate with non-PHP web applications.  It returns 200 OK with
[X-Accel-Redirect header](https://www.nginx.com/resources/wiki/start/topics/examples/x-accel/) -- a server-side redirect mechanism
supported by NGINX and Cherokee web servers.

Usage is best explained by example.  Suppose you have an NGINX `location` block that serves a Node.js web application (or proxies
a remote website, delivers static files, anything really) which you want to protect with Adspect as your money page:

```nginx
location /app {
  internal;
  proxy_pass http://127.0.0.1:8080/;
}
```

You should add another `location` block to serve the `index.php` file of Adspect which will protect your web application:

```nginx
location ~ \.php$ {
  root /var/www/html;
  include fastcgi.conf;
  fastcgi_pass unix:/run/php/php-fpm.sock;
}
```

Then you configure your stream to use your web application as the money page:

* Money page field: `/app`
* Money page action: **X-Accel-Redirect header**

With this setup, if the visitor is legitimate, Adspect will transfer control to your web application inside NGINX seamlessly
for the visitor without any visible redirection.

:::{important}
With JavaScript integration, this action performs a [JavaScript replace() redirect](streams.md#js-replace).
:::

(x-sendfile)=
### X-Sendfile Header

Returns 200 OK with [X-Sendfile header](https://tn123.org/mod_xsendfile/) --
a server-side redirect mechanism supported by Apache, Cherokee, and Lighttpd web servers.

:::{important}
With JavaScript integration, this action performs a [JavaScript replace() redirect](streams.md#js-replace).
:::

## Delayed Start

Delayed start allows you to filter out a given number of first clicks in the stream.  For example, if you have observed
that the first 10--15 clicks in each campaign in your ad network belong to ad reviewers and malware checkers, you can
set your stream to send the first 20 clicks to the safe page (just a little more to stay on the safe side).

:::{attention}
Delayed start works only when the stream is in Filter mode.
:::

Delayed start has three modes that differ in click counter logic:

:::{list-table}
:header-rows: 1

* - Mode
  - Description

* - All
  - Counts all clicks.

* - Money
  - Counts only those clicks that would otherwise be admitted to money page, passing all other filters save JS fingerprinting.

* - Safe
  - Counts only invalid clicks that would be blocked by any other filter.
:::

:::{tip}
Delayed start is useful if you launch many ad campaigns and don’t want to manually switch each stream from "On Review" to "Filter".
:::

## Filtering Settings

This group of settings controls most of the built-in traffic filters of Adspect.

(filtering-level)=
### Filtering Level

Filtering level lets you adjust aggressiveness of traffic filtering by selecting one of several pre-defined levels.

:::{list-table} Available Filtering Levels
:header-rows: 1

* - Level
  - Description

* - 0.  Essentially&nbsp;off
  - All built-in and third-party filtering databases are disabled.  VLA™ machine learning is not used.  Do not use use this level
    unless you have a compelling reason.

* - 1.  Low
  - Only the most blatant offenders are blocked: big corporations, largest datacenters and hosting providers, antivirus companies,
    etc.  VLA™ machine learning is not used.  This level fits best for in-app and WebView cloaking.

* - 2.  Medium
  - All the IT corporations, datacenters, hosting providers, and IP transit networks are blocked.  VLA™ machine learning is enabled.
    All clicks are also checked with the top twelve competing cloaking services.

* - 3.  High
  - Same as Medium, plus IP network ownership history of up to 3 years back is checked for all the blocked organizations.  Third-party
    filtering databases and risk scoring companies are also queried.  This level is safe for most scenarios.

* - 4.  Paranoid
  - Same as High, plus all the government, military, scientific, and educational organizations are blocked, as well as certain high
    risk Internet service providers.  This level is the safest, but may result in high bleed rate.
:::

:::{tip}
If you don't know which filtering level to select, go with level **High**.
:::

(js-fingerprinting)=
### Enable JavaScript Fingerprinting and VLA™ Model Training

This setting controls JavaScript fingerprinting--one of our strongest cutting edge filters.  When enabled,
visitors that pass all the naïve checks (by IP address, user agent, referrer, targeting, URL rules, etc.) will
be given a JavaScript challenge: a tiny script that will collect extensive information about the browser's
internals--a fingerprint--and submit it back to us.

Upon reception, Adspect will analyze the fingerprint with more than a hundred know-how heuristics looking for
signatures of bots, web automation software that some bots are built on, fake (spoofed) information about OS
and browser version, and many other tell-tale technical pieces of information.  Additionally, this fingerprint
will undergo probabilistic analysis by our [VLA™ machine learning model](how-it-works.md#machine-learning),
if available in your plan.

:::{note}
This feature is not implemented in [native Keitaro integration](keitaro.md) due to technical limitations of the Keitaro custom filter framework.
:::

(filter-by-user-agents)=
### Enable Built-in User Agent-based Filters

This setting lets you enable or disable internal user agent-based filtering.  Just like with IP address blocklists,
these filters are developed and maintaned by Adspect and always kept up to date.

:::{attention}
It is strongly recommended to enable this setting in all use cases except in-app cloaking.
:::

(require-unique)=
### Allow Only Unique Visitors

This setting blocks non-unique visitors.  If enabled, only the first click with any particular IP address or click ID will be allowed, and all repeated clicks will be blocked.

(require-touchscreen)=
### Allow Only Touchscreen Devices

This setting allows only those visitors that use devices with a touchscreen (mobile phones and tablets).
It may be useful for mobile-only campaigns that target visitors with contemporary mobile phones and tablets:
lots of security bots that use desktop browser automation will be cut off by this setting alone.

:::{note}
This check only works when [JavaScript fingerprinting](#js-fingerprinting) is enabled.
:::

(require-web3)=
### Allow Only Visitors With In-browser Web3 Wallets

This setting allows only those visitors that have a Web3 wallet installed in their browser.

:::{note}
This check only works when [JavaScript fingerprinting](#js-fingerprinting) is enabled.
:::

(allow-apps)=
### Allow Traffic From Mobile Apps

This setting allows traffic that originates from inside mobile applications, e.g. from the WebView browser.
While natural for certain niche ad formats, such traffic is widely seen as click fraud in other formats
(automated clicks generated by mobile malware) and should normally be disabled unless your ad format is somehow
based on mobile applications.

(allow-embed)=
### Allow Traffic From Frames, Iframes, and Embedded Objects

This setting allows traffic that originates from inside embedded environments such as `<iframe>`,
`<embed>`, `<object>`, etc.  Similarly to traffic from mobile apps, this setting should be set or cleared
depending on your particular traffic format and source.  Keep it enabled if unsure.

:::{note}
This check only works when [JavaScript fingerprinting](#js-fingerprinting) is enabled.
:::

(allow-google-proxy)=
### Allow Google IP Protection Traffic (Chrome Privacy Sandbox)

This setting allows visitors that use [Google IP Protection](https://privacysandbox.google.com/protections/ip-protection) (also known as Chrome Privacy Sandbox) to hide their real IP addresses behind Google-operated privacy proxies.  Enable it if you observe high volumes of blocked traffic coming from Google-owned IP addresses.

(allow-apple-proxy)=
### Allow Apple iCloud Private Relay Traffic

This setting allows visitors that use Apple's [iCloud Private Relay](https://support.apple.com/en-us/102602) to hide their real IP addresses behind Apple-operated privacy proxies.  Enable it if you observe high volumes of blocked traffic coming from Apple iOS devices.

## Countries and Devices

These fields allow you to allow visitors only from the specified countries, using specified operating systems, browsers, browser
engines, and browser language preferences.  If a field is empty, no check will be made for that attribute (allows all).

## Tracking Settings

These settings are used to assign Sub ID, Click ID, and cost to individual clicks.  Adspect takes the value of each field below,
replaces macros in it, and assigns the result to the corresponding attribute of each click.

Filling these fields is not required, but may be useful if you want to track individual clicks, conversions, expenses, revenue,
and per-subaccount statistics in the [Reporting](reporting.md) section.

### Sub ID

Sub ID is a click attribute that you can group reports by in [Reporting](reporting.md) by choosing Sub ID in the breakdown field.
The concept is best described by example. Suppose your ad network has a notion of zones for dividing different publishers or ad
placements into numbered groups. You would use a macro like `{zoneid}` to put zone identifiers into your campaign URL, for example:
```
https://example.com/?subid={zoneid}
```
For each click, the ad network will replace the `{zoneid}` macro with an actual zone ID which can then be taken
out of the click link and tracked individually. In this example, `subid` is name of the parameter used to track
zone IDs. If you specify `{p:subid}` for the Sub ID stream setting, you will be able to pull per-zone reports
in [Reporting](reporting.md). This may come in very handy for building blacklists of bot-ridden publishers, zones, placements, etc.

### Click ID

Click ID works the same way as Sub ID, but for assigning unique identifiers to individual clicks, which are then used for signalling
conversions to the tracker or ad network via pixel or S2S postback mechanism.

Clicks often already have unique identifiers automatically added by the ad platform, e.g. in the `gclid` URL parameter in Google Ads
or `fbclid` in Facebook. The Click ID field lets you extract that identifier from that URL parameter and assign it to each
individual click in Adspect reports. To do this, enter `{p:gclid}` (Google Ads example) or `{p:fbclid}` (Facebook example) there.

Click ID may be added to money or safe page links via the `{clickid}` macro.

If the Click ID field is empty, Adspect will generate its own unique click IDs.

### Click Cost

This field sets cost of each click which is used to calculate expenses in [Reporting](reporting.md).  You can enter a fixed number
here, but some ad networks support adding cost to each click's URL in a URL parameter.  For example, suppose a click comes by a link
like this:
```
https://example.com/?cost=0.15
```
If you enter `{p:cost}` in the Click Cost field, Adspect will take 0.15 from the `cost` URL parameter and use that as the cost
of that particular click.

If the Click Cost field is empty, clicks will have zero cost in reports.

## IP Address Filtering

This block of settings lets you filter traffic by lists of IP addresses, IP address ranges, [CIDR prefixes](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#CIDR_notation), and/or [autonomous system numbers (ASN)](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)).  Each stream has two lists: black and white.  Individual list entries should be delimited by newlines or whitespace.

Entry format examples:

:::{list-table}
:header-rows: 1

* - Format
  - Description

* - `192.0.2.1`
  - Single IPv4 address.

* - `192.0.2.0/24`
  - IPv4 CIDR prefix.

* - `192.0.2.0–192.0.2.255`
  - IPv4 address range.

* - `2001:db8::1`
  - Single IPv6 address.

* - `2001:db8::/112`
  - IPv6 CIDR prefix.

* - `2001:db8::-2001:db8::ffff`
  - IPv6 address range.

* - `65536`
  - Autonomous system number.

* - `AS65536`
  - Autonomous system number.

* - `1.10`
  - Autonomous system number in dot+ format.

* - `AS1.10`
  - Autonomous system number in dot+ format.
:::

### IP/ASN List Mode

The IP/ASN list mode controls how blacklist and whitelist work together.

:::{list-table}
:header-rows: 1

* - Mode
  - Visitor in blacklist
  - Visitor in whitelist
  - Visitor not in lists
  - Notes

* - **Black**
  - Blocked if not whitelisted
  - Checked by other filters
  - Checked by other filters
  - The whitelist has priority over the blacklist.

* - **White**
  - Blocked
  - Checked by other filters if not blacklisted
  - Blocked
  - The blacklist has priority over the whitelist.

* - **Special**
  - Blocked if not whitelisted
  - Goes to the money page
  - Checked by other filters
  - Whitelisted visitors skip all checks and go to the money page.
:::

### Blacklist All IP Addresses in Review Mode

If enabled, this setting instructs Adspect to add IP addresses of all incoming visitors to the IP blacklist if
the stream is in Review mode. Since the Review mode is meant to be used only when your ad campaigns are under
review by moderators, it is safe to assume that every visitor in this mode is a moderator and should be barred.
We recommend you to always enable this setting, but pay attention to the moment your campaign is approved,
to switch the stream mode to Filtering in time lest you blacklist IP addresses of legitimate visitors when
your campaign goes live.

We recommended to enable this setting.

### IP Extrapolation

IP extrapolation allows you to control fuzzy matching of IP addresses against our internal blocklists.
Larger values result in banning of more addresses adjacent to already blacklisted ones, which results in
better protection at the expense of higher chances of false positives.

(user-agent-and-referrer)=
## User Agent Filtering

The black and white user agent lists let you filter visitors by their [user agents](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/User-Agent).  The lists should contain [Perl-compatible regular expressions (PCRE)](https://www.pcre.org/original/doc/html/pcrepattern.html) each on its own line.  They are checked in order until the first match.  Regular expression matching is case-sensitive.

### User Agent List Mode

The user agent list mode controls how blacklist and whitelist work together.

:::{list-table}
:header-rows: 1

* - Mode
  - Visitor in blacklist
  - Visitor in whitelist
  - Visitor not in lists
  - Notes

* - **Black**
  - Blocked if not whitelisted
  - Checked by other filters
  - Checked by other filters
  - The whitelist has priority over the blacklist.

* - **White**
  - Blocked
  - Checked by other filters if not blacklisted
  - Blocked
  - The blacklist has priority over the whitelist.

* - **Special**
  - Blocked if not whitelisted
  - Goes to the money page
  - Checked by other filters
  - Whitelisted visitors skip all checks and go to the money page.
:::

(referrer-filter)=
## Referrer Filtering

The black and white referrer lists let you filter visitors by their [referrers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Referer).  The lists should contain [Perl-compatible regular expressions (PCRE)](https://www.pcre.org/original/doc/html/pcrepattern.html) each on its own line.  They are checked in order until the first match.  Regular expression matching is case-sensitive.

Common use cases:

- Blocking visitors with empty referrers.  Add this line to the blacklist:
  ```
  ^$
  ```
- Blocking visitors that did not come from ads.  Set the list mode to **White** and add allowed domains to the whitelist.  Example for Google Ads:
  ```
  google\.com
  ```

### Referrer List Mode

The referrer list mode controls how blacklist and whitelist work together.

:::{list-table}
:header-rows: 1

* - Mode
  - Visitor in blacklist
  - Visitor in whitelist
  - Visitor not in lists
  - Notes

* - **Black**
  - Blocked if not whitelisted
  - Checked by other filters
  - Checked by other filters
  - The whitelist has priority over the blacklist.

* - **White**
  - Blocked
  - Checked by other filters if not blacklisted
  - Blocked
  - The blacklist has priority over the whitelist.
:::

(url-rules)=
## URL Rules

URL rules let you check, add, modify, or remove URL parameters in the links of incoming clicks.  You can use them to block
a click if it has or doesn't have a specific parameter, or to check whether a parameter matches or doesn't match a regular
expression, etc.

Rules are applied in order--from top to bottom, as listed in the stream settings.  Each rule works with the link as it was
modified by the previous rules.  For example, if one rule removes a parameter from the link, the following rules won't be able
to check or change its original value.  If a rule adds a new parameter, the next ones can work with it.

Rules work with the link the click originally came through--not the final link to your money or safe page.  So any changes to
parameters (adding, modifying, deleting) won't affect the final link unless you enable [parameter passthrough](#parameter-passthrough) for it.

:::{note}
If there are several parameters with the same name, a rule with that parameter name will apply to all of them.
:::

Each rule consists of the following fields:

:::{list-table} URL Rule Fields
:header-rows: 1

* - Rule Field
  - Description

* - URL Parameter
  - The name of the URL parameter that the rule applies to.
    For example, if the link contains the parameter `utm_medium=banner`, its name is `utm_medium`.

* - Operator
  - Defines what kind of check or operation will be performed on the URL parameter.
    See the [operator table](#url-rule-operators) below.

* - Argument
  - Additional data for the rule, required by certain operators (see the [operator table](#url-rule-operators) below).
    This field supports [macros](#macros).

* - "On" Checkbox
  - Enables or disables the rule.
:::

(url-rule-operators)=
:::{list-table} URL Rule Operators
:header-rows: 1

* - Operator
  - Description

* - exists
  - Checks if the parameter exists.  The rule argument is not used.

* - does not exist
  - Checks if the parameter does not exist.  The rule argument is not used.

* - matches regexp
  - Checks if the parameter value matches a [Perl-compatible regular expression (PCRE)](https://www.pcre.org/original/doc/html/pcrepattern.html) in the rule argument (case-sensitive).

* - matches regexp (no case)
  - Checks if the parameter value matches a regular expression in the rule argument (case-insensitive).

* - does not match regexp
  - Checks if the parameter value does not match a regular expression in the rule argument (case-sensitive).

* - does not match regexp (no case)
  - Checks if the parameter value does not match a regular expression in the rule argument (case-insensitive).

* - equal to<br>
    not equal to<br>
    greater than<br>
    greater than or equal to<br>
    less than<br>
    less than or equal to
  - Checks if the parameter value compares with the rule argument accordingly.  Integers and real values are compared as numbers,
    and strings are compared according to their [lexicographical order](https://en.wikipedia.org/wiki/Lexicographical_order).

* - assign value
  - Sets the parameter's value to the rule argument.  Existing parameters with the same name will be deleted.

* - append value
  - Appends a new parameter with the rule argument as its value.  Existing parameters with the same name will remain.

* - rename to
  - Renames the parameter to the name specified in the rule argument.  Existing parameters with the same name will remain.

* - replace
  - Renames the parameter to the name specified in the rule argument.  Existing parameters with the same name will be deleted.

* - delete
  - Deletes the parameter.  The rule argument is not used.
:::

## Filtering by Time

This section allows you to filter visitors by time and time zones.

### Schedule (Dayparting)

Schedule allows you to specify dayparts and optionally days of week during which traffic filtering is on.
All visits on time and days not explicitly listed will be blocked. Schedule is active if at least one daypart
is specified. If a daypart does not specify days of week, it is applied to all days.

### Time Zones

This field lets you allow visitors only from the specified time zones.

Time zone settings are restricted to full hour offsets from UTC. If a visitor's time zone is not offset by
full hours, the offset will be rounded.

(match-time-zone-to-ip)=
### Match Browser Time Zone to Location Time Zone

If this setting is enabled, Adspect will check whether the time zone reported by visitor's browser matches
the time zone of the visitor as determined by our geolocation. This check may slightly increase the rate of false
positives, but it significantly boosts protection against moderators and bots that use VPN or proxies.

We recommended to enable this setting.

:::{note}
This check only works when [JavaScript fingerprinting](#js-fingerprinting) is enabled.
:::
