# Playbook

This chapter provides recipes for the most frequent use cases, based on the best collective experience of Adspect customers.

:::{attention}
For cloaking, you will need to subscribe to the Cloaking plan.  Please do not try to cloak with the Anti-fraud plan
subscription--it does not include cloaking-specific filters, so you'll only waste your time, nerves, and accounts!

All cloaking setups must adhere to all the general [best practices](recommendations.html).
:::

## Cloaking Google (Search, GDN)

1.  Create a stream by selecting the Google Ads preset;
2.  Safe page **must** be displayed without redirect;
3.  Perform [any type of integration](integration.html), depending on your needs.
    :::{important}
    With [PHP integration](integration.md#php-integration), make sure the
    [Enable JavaScript fingerprinting and VLA™ model training](streams.md#enable-javascript-fingerprinting-and-vla-model-training)
    checkbox is not checked in stream settings.
    With [JavaScript integration](integration.md#javascript-integration), you may check it.
    :::

## Cloaking Google (Tracking Template)

1.  Create a stream by selecting the Google Ads (Tracking Template) preset;
2.  Fill in money and safe page URLs and set both actions to HTTP 302 Redirect;
3.  Perform [forward PHP integration](integration.md#forward-php-integration);
4.  In your campaign's tracking template, add the `url={lpurl}`
    [ValueTrack parameter](https://support.google.com/google-ads/answer/6305348) to the link, e.g:
    `https://tracker.link/?url={lpurl}`

## Cloaking Facebook

1.  Create a stream by selecting the Facebook preset;
2.  Safe page **must** be displayed without redirect;
3.  Perform [any type of integration](integration.html), depending on your needs.

## Cloaking TikTok

1.  Create a stream by selecting the TikTok preset;
2.  While not strict, it is **advised** to display both money and safe pages without redirect;
3.  In your campaign, add all of the TikTok Ads URL macros (`__CAMPAIGN_ID__`, `__PLACEMENT__`, etc.) to the advertised URL;
4.  Perform [any type of integration](integration.html), depending on your needs.

## Cloaking X (Twitter Ads)

1.  Create a stream by selecting the X (Twitter Ads) preset;
2.  Safe page **must** be displayed without redirect;
3.  Perform [any type of integration](integration.html), depending on your needs.

## Cloaking WebView Apps

Cloaking WebView apps is essentially the same as cloaking regular web pages.

1.  Create a stream by selecting the WebView preset;
2.  Perform PHP integration;
3.  In your app, open the cloaked link with WebView.

## In-app Cloaking

Adspect provides an easy way to do in-app cloaking, i.e. choosing which app elements (paywalls, prices, rebill conditions,
WebView, etc.) to display to legitimate users or moderators.

1.  Create a stream by selecting an appropriate preset for your traffic source: Google Play, App Store, or the generic Apps API;
2.  Perform forward PHP integration by uploading the `index.php` file to your domain--the URL of this file
    will be your simple HTTP API that responds `200 OK` to legitimate users and `403 Forbidden` to moderators;
3.  In your app, make a simple `GET` HTTP request to the API URL and decide what do to based on response code:
    - 200 -- the user is legitimate, display sensitive content;
    - any other code or error -- assume the user is a moderator, don't display anything sensitive.

You may customize API responses by selecting different actions for the money and safe pages in stream settings.
For example, return some JSON instead of empty 200 response for valid users:

- Action: execute PHP code
- Page field: `echo json_encode(['user_is_legit' => true]);`

## Blacklist Bot Zones

Popular advertising formats like banners, teasers, native ads, and popunders all suffer from ubiquitous
[click fraud](https://en.wikipedia.org/wiki/Click_fraud).  Adspect detects all of that with ease, so you can
quickly build blacklists of bot-ridden publishers/placements/sites/spots (hereafter referred to as "zones"):

1.  In stream settings, set the [Sub ID field](streams.md#sub-id) to the name of the URL parameter that
    will contain zone identifier, e.g. `subid`;
2.  In ads campaign, add that parameter to your advertised URL with any appropriate macro supported by your
    particular ad network, e.g. `subid={zoneid}` or `subid=__PLACEMENT__` (contact ad network support if unsure
    which macro to use);
3.  In the [Reporting section](reporting.html), select funnel grouping by Sub ID;
4.  Inspect the Quality column for different zones and decide which zones to blacklist based on that, e.g.
    zones with quality 75% or lower (that is, zones with at least ¼ of all traffic being bots.)

Blacklist fraudulent zones first (and save a fortune on paying for their bots), then proceed to single out
best converting zones by CR or ROI as you would normally do.

## Hide Traffic Source

Traffic source may often be discovered by examining a click's
[Referer HTTP header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referer).
If you'd like to hide it from those who receive your traffic, then add the following code after the `<head>` tag
inside your Adspect PHP file (`index.php` or `filter.php`):

```html
<meta name="referrer" content="no-referrer">
```

If you are using JavaScript integration, then add this code to your safe page instead.
