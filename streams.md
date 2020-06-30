# Configuring Streams

Traffic management in Adspect is organized in terms of streams. A stream is a traffic channel that is managed
as a whole, much like a campaign in an ad network or a scheme in TDS.

Streams are managed in the Streams section of the clients area. Use the New Stream button to create new streams.
Please be advised that the total number of streams per account is limited to 50. If there's no New Stream button,
then you have reached your streams limit. Please contact us if you need more streams, and we will resolve your issue
individually.

Each stream has its own `index.php` and `adspect.php` files wired to it that have the stream ID encoded inside.
However, you may override that encoded stream ID and send a click to a different stream by putting the destination
full stream ID into the `__sid` URL parameter, e.g:

```
https://example.com/index.php?__sid=1ea85c7c-b977-6804-8e69-00162501c2b4
```

Click the short stream ID in the streams list to copy the full stream ID into clipboard.

Below we will visit each stream setting in detail.

## Name

Stream name is just a human-readable identifier that lets you distinguish between different streams. It is a good
idea to match stream names with ad campaigns on one-to-one basis to maintain consistency and clarity across your
traffic sources and Adspect. We also recommend that you create one stream per GEO (country) to make obtaining per-GEO
statistics easier.

## Filter Mode

This is the mode that streams currently operates in. There are four modes:

* **Filtering** -- this is primary working mode in which we actively inspect every click coming in the stream and filter
  legitimate visitors from moderators, click fraud, and other unwanted types of traffic. All filtering technologies
  of Adspect, including [VLA™](vla.md), work only in this mode.

* **On review** -- this mode is meant to be used when ad campaign that points to the stream is on
  review by ad network moderators. Every visitor in this mode will be directed to the white page. There are additional
  settings that apply in this mode, they will be described below.

* **All money** -- auxiliary mode in which all visitors are directed to the money page. Useful for
  testing accessibility of the money page.

* **All white** -- auxiliary mode in which all visitors are directed to the white page. Useful for
  testing accessibility of the white page. It is also a good idea to put streams into this mode whenever campaigns
  are paused in ad networks, to prevent unauthorized access to your landing pages or offers during inactivity periods.

On review is the default mode when creating a new stream. You *should* always use it when sending
campaigns to moderation. After a campaign is approved, you should change its stream mode to Filtering
before actual traffic starts coming.

## Money Page

This is your actual landing page or offer that you are going to advertise. The "money" word is intended
to indicate that this is the page that makes you money.

There are two types of values that may be specified: page file name or an URL. Page file name is the advised way
of specifying money page--it is the name of an HTML or PHP file of your real landing page that *must* be
located in the same directory as where you put `index.php` after stream creation, i.e. in the root directory of
your landing page.

The file name should not be easily guessable because it lets determined moderators or competitors figure out
the URL of your real landing page. Pick a random long file name.

*Do not* name your money page `index.html` or `index.htm`! Apart from being easily guessable (i.e. trivially uncloakable),
those file names may be in conflict with your existing web server configuration, leading to unforeseen problems.

To put it all together: if you have a landing page directory and the actual landing page file inside named `index.php`
(as is most often the case), then you should first rename that `index.php` file to something hard-to-guess like `re3NBX1XtH.php`,
then put our special `index.php` file next to `re3NBX1XtH.php` in the same directory after stream creation.
Our `index.php` will then display the real file `re3NBX1XtH.php` to approved visitors.

Alternatively, you may use an URL instead, for instance a direct offer URL taken from an affiliate network.
This may be optimal for some campaigns, however, external URL implies an additional HTTP redirect, with associated
latency and traffic loss considerations, especially on low-quality ad formats like popunder.

You may also use various non-HTTP URLs to achieve specialized tasks on your visitors' devices.
Some of the more common examples:

* `mailto:user@example.com` will open up a default e-mail program in compose mode;
* `tel:+08001234567` will dial the number on mobile devices and some desktops with installed telephony software;
* `market://details?id=app` will bring the visitor to a particular app's page in Google Play.

This is particularly useful with the so called "deep links" that link to mobile in-app content.

### URL Macros and PHP Variables

Adspect supports several URL macros and their PHP variable counterparts that you can use in redirection URLs and
PHP landing pages, respectively:

* `{clickid}` and `$_SERVER["ADSPECT_CLICK_ID"]`: unique click ID–external ID from URL parameter, if specified,
  or generated by Adspect;
* `{country}` and `$_SERVER["ADSPECT_COUNTRY"]`: [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2)
  country code of the visitor;
* `{os}` and `$_SERVER["ADSPECT_OS"]`: operating system of the visitor with version for Windows and Android
  (*PHP value may contain special characters and should be encoded for use in URLs with* `rawurlencode()`);
* `{browser}` and `$_SERVER["ADSPECT_BROWSER"]`: name of the browser of the visitor
  (*PHP value may contain special characters and should be encoded with* `rawurlencode()`);

URL example:

```
https://example.com/?clickid={clickid}&geo={country}&os={os}
```

PHP landing page example:

```php
<a href="https://example.com/offer?clickid=<?= $_SERVER["ADSPECT_CLICK_ID"] ?>">
	Link to CPA offer with embedded Adspect click ID
</a>
```

## White Page

This is the safe page to show to moderators, robots, scrapers, etc. It should not contain any sensitive content
that may put your affiliate campaign in danger or in violation of any rules. Everything described above for
the money page also applies to the white page--you may use an URL or a landing page file. In the latter case,
if your money page is also a self-hosted landing page, you will need to effectively merge two landing page
directories together to meet the requirement that both page files are located in the root of the common directory.

We strongly advise using a landing page file over external URL for white page. This has to do with suspicion and
increased scrutiny from compliance teams of certain ad networks that discourage or outright prohibit the use of
redirects in traffic flow.

## Pass URL Parameters to Money/White URL

These settings are applied only when the corresponding money/white page is specified as an URL. When enabled,
URL parameters passed to the `index.php` file will be appended to the corresponding money/white URL.

These settings work the same as similar settings available in most affiliate tracking software. For example,
consider the stream's money page is configured as follows:

```
https://cpanetwork.test/offer?id=1234
```

A good (non-robot) visitor accesses `index.php` of the stream using the following URL:

```
https://tracker.test/lander/index.php?utm_medium=cpc&utm_content=mycampaign
```

After inspecting the visitor and allowing them through to the money page, they will be redirected to the money URL
with URL parameters combined from both of the above:

```
https://cpanetwork.test/offer?id=1234&utm_medium=cpc&utm_content=mycampaign
```

## VLA™

VLA™ stands for Virtual Learning Appliance, the trademark of the machine learning system at the heart of Adspect.
It is discussed in detail in the [VLA chapter](vla.md). 95% is a good VLA precision value to begin with.

## Sub ID

Sub ID refers to an URL parameter that you want to use for per-subaccount reports, available in the Reporting
section by selecting the Sub ID grouping. Please refer to the [Reporting](reporting.md) chapter for details.

The concept is best described by example. Suppose your ad network has a notion of zones for dividing different
publishers or ad placements into numbered groups. You would use some form of macro, e.g. `{zoneid}`, to put zone
identifiers into your click URL. Your campaign tracking link might look like this:

```
https://tracker.test/lander/index.php?subid={zoneid}
```

For each click, the ad network will replace the `{zoneid}` macro with an actual identifier which can then be taken
out of the click link and tracked individually. In this example, `subid` is name of the parameter used to track
zone IDs. If you specify `subid` for the Sub ID stream setting, then you will be able to pull per-zone reports
in the Reporting section of the clients area as mentioned above. This may come in very handy for building
blacklists of bot-ridden publishers, zones, placements, etc.

Sub ID may also be anything else: GEO, hardware platform, OS version, any URL-trackable parameter. You can also
combine several parameters into a compound subaccount by using more than one macro in the same parameter:

```
https://tracker.test/lander/index.php?subid={zoneid}-{platform}
```

In the example above each subaccount will be a combination of a zone and a device platform seen within that zone.

## Click ID

Click ID works the same way as Sub ID, but for tracking unique click identifiers often supplied by ad networks
or affiliate trackers. If the Click ID setting is specified, click IDs are taken out of that link parameter and
recorded in statistics along with other click data. This allows you to find and examine individual clicks in raw
CSV reports. One use case would be compiling lists of bot clicks as a proof of click fraud.

If the Click ID setting is omitted, then Adspect will generate its own click IDs for use with its [tracker](tracker.md).
Depending on how your money/white pages are displayed, click IDs may be put into money/white page URLs via the
`{clickid}` URL macro or embedded into money/white page files via the `$_SERVER["ADSPECT_CLICK_ID"]` PHP variable.

## Paranoid Mode

The paranoid mode enables additional strict fingerprint checks and vast IP address blacklists (counting up to 2 billion
IPv4 addresses) that are considered "paranoid", that is, with higher false positives chance, but at the same time
providing equally higher chance of busting moderators.

**We recommend to enable this mode when working with Facebook or Google Ads.**

## Allow Traffic From Mobile Apps

This setting tells us to allow traffic that originates from inside mobile applications, e.g. from WebView Android
browser. While natural for certain niche ad formats, such traffic is widely seen as click fraud in other formats
(automated clicks generated by mobile malware) and should normally be disabled unless your ad format is somehow
based on mobile applications.

## Countries, Operating Systems, Browsers, Languages, and Time Zones

These manual targeting options allow you to further restrict your stream to only allow visitors from specified
countries, using specified operating systems, browsers, browser language preferences, or time zones. You would normally
set them to match your campaign targeting settings. If any of these settings is omitted (the list is empty), then
no check will be made for that setting.

Note: time zone settings are restricted to full hour offsets from UTC. If a visitor's time zone is not offset by
full hours, then the offset will be rounded.

## Match Browser Time Zone to Location Time Zone

If this setting is enabled, then Adspect will check whether the time zone reported by visitor's browser matches
the time zone of the visitor as determined by our geolocation. This check may slightly increase the rate of false
positives, but it significantly boosts protection against moderators and bots that use VPN or proxy services.
If enabled, the manual time zone list described above is ignored. It is recommended to enable this setting.

## User Agent Filter

This setting allows you to specify a custom [Perl-compatible regular expression (PCRE)](https://www.pcre.org/original/doc/html/pcrepattern.html)
for filtering visitors by their [user agent string](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent).
Regular expression matching is case-sensitive. By default, the search is done in any part of the user agent string;
you may use [anchors](https://www.pcre.org/original/doc/html/pcrepattern.html#SEC6) to bind matching to the start
or the end of the string (see examples below.)

PCRE syntax is very rich and powerful and is well out of scope of this document. Regular expressions can be combined
using various syntax constructs to create arbitrarily complex patterns, but please note that the current implementation
limits regular expression length by 1023 characters.

Some examples:

```
Firefox|Nexus|Miui
```

This regex will match any user agent that contains words "Firefox", "Nexus", or "Miui", and can be used to filter
out visitors that use Mozilla Firefox, Google Nexus, or Xiaomi built-in browser.

```
^Mozilla/4[.]0
```

This regex will match any user agent that begins with "Mozilla/4.0", banning shady visitors that report themselves
to be very old browsers yet support contemporary JavaScript features (implied by being able to run our fingerprint
collecting code.)

```
^Mozilla/5[.]0$
```

This regex will match user agents that are exactly "Mozilla/5.0", blocking visitors without concrete browser,
HTML engine, and platform information, which is very uncommon and suspicious.

All of the expressions above can be combined using logical "or" (i.e. to match the first expression *or* the second
*or* the third) this way:

```
Firefox|Nexus|Miui|^Mozilla/4[.]0|^Mozilla/5[.]0$
```

**Please be careful! Improperly formed regular expression can lead to erroneous matching and filtering of
vast amounts of legitimate traffic. Use this setting only if you know what you are doing.**

## Referer Filter

This setting works similarly to the user agent filter described above, but deals with [HTTP referer](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referer)
instead. It also takes a Perl-compatible regular expression and filters out all visitors whose referers match it.
Regular expression matching is case-sensitive.

One common use case is filtering empty or non-existent referers. This can be achieved with the following regex:

```
^$
```

**Please be careful! Improperly formed regular expression can lead to erroneous matching and filtering of
vast amounts of legitimate traffic. Use this setting only if you know what you are doing.**

## IP/ASN Blacklist

This is the list of IP addresses, IP address ranges, and/or [autonomous system numbers (ASN)](https://en.wikipedia.org/wiki/Autonomous_system_(Internet))
that should always be shown the white page. Both IPv4 and IPv6 addresses are supported, as well as CIDR and range notations. Examples:

* 192.0.2.1
* 192.0.2.0/24
* 192.0.2.0–192.0.2.255
* 2001:db8::1
* 2001:db8::/112
* 2001:db8::-2001:db8::ffff

Individual entries should be delimited by newlines or whitespaces. Please note that the system will automatically
merge adjacent or overlapping ranges in order to optimize storage space and lookup speed.

## Blacklist All IP Addresses In Review Mode

If enabled, this setting instructs Adspect to add IP addresses of all incoming visitors to the IP blacklist if
the stream is in Review mode. Since the Review mode is meant to be used only when your ad campaigns are under
review by moderators, it is safe to assume that every visitor in this mode is a moderator and should be barred.
We recommend you to always enable this setting, but pay attention to the moment your campaign is approved,
to switch the stream mode to Filtering in time lest you blacklist IP addresses of legitimate visitors when
your campaign goes live.
