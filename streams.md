# Configuring Streams

Traffic management in Adspect is organized in terms of streams. A stream is a traffic channel that is managed
as a whole, much like a campaign in an ad network or a scheme in TDS. Streams are managed in the Streams section
of the clients area. Use the New Stream button to create new streams. Below we will visit each stream setting in detail.

**Please note** that default settings are generally adequate for most traffic sources and use cases. You are
by no means required to fill in all the available fields; normally, it is enough to configure just money and white
pages and leave the rest to Adspect.

## Name

Stream name is just a human-readable identifier that lets you distinguish between different streams. It is a good
idea to match stream names with ad campaigns on one-to-one basis to maintain consistency and clarity across your
traffic sources and Adspect. We also recommend that you create one stream per GEO (country) to make obtaining per-GEO
statistics easier.

## Mode

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
to indicate that this is the page that makes you money. You may specify up to 254 money pages for A/B testing.
Traffic will be distributed across them according to rules of a particular rotator; see the [Rotator paragraph](#rotator) below.

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

### Param Setting

Param stands for "URL parameters passthrough." When enabled, parameters passed in the incoming URL will be
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

### Weight Setting

Each money page has associated abstract "weight" that defaults to 10. This setting is taken into account when
you have more than one money page in A/B testing. Exact meaning of this parameter depends on the rotator used
by the stream; see the [Rotator paragraph](#rotator) below.

### On Setting

The On checkbox allows you to turn individual money pages on or off.

### URL Macros

Adspect supports several URL macros that you can use in money and white page fields:

* `{ip}` -- IP address of the visitor;
* `{asn}` -- [autonomous system](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) number of the visitor;
* `{agent}` -- [user agent string](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent) of the visitor;
* `{referrer}` -- [referrer](https://developer.mozilla.org/en-US/docs/Web/API/Document/referrer) of the visitor;
* `{clickid}` -- unique click ID (external ID from URL parameter or generated by Adspect);
* `{country}` -- [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code of the visitor;
* `{os}` -- operating system of the visitor with version for Windows and Android;
* `{browser}` -- name of the browser of the visitor;
* `{engine}` -- name of the browser engine of the visitor;
* `{epoch}` -- [Unix timestamp](https://en.wikipedia.org/wiki/Unix_time) of the visit;
* `{tags}` -- [click processing tags](reporting.html#raw-report-columns), if any;
* `{p:parameter}` -- value of the named parameter in the request URL.

In zero redirect (file-based) display mechanism, you may still add parameters with macros after the file name of your
money/white page, and they will be parsed and made available in PHP in the
[`$_GET` superglobal variable](https://www.php.net/manual/en/reserved.variables.get.php).

Redirect example:
```
https://example.com/offer?clickid={clickid}&geo={country}&os={os}
```

Zero redirect example:
```
page.php?clickid={clickid}&geo={country}&os={os}
```
```php
<!-- Inside page.php file -->
<a href="https://example.com/offer?clickid=<?= $_GET['clickid'] ?>">Offer</a>
```

## Rotator

Rotator controls how several money pages are rotated, i.e. how the system decides which money page to display to each
particular visitor. If there is only a single money page specified, then rotator choice does not affect anything.
Currently Adspect supports two rotators: Split and Timer.

### Split Rotator

This is the default rotator that splits traffic across all enabled money pages according to their weights (A/B testing.)
The more the weight is, the more traffic that money page will receive, proportionally.

For example, if you have three money pages with weights 10, 15, and 25, then the first page will receieve about
20% of all human traffic, the second page will receieve 30%, and the third page will get around 50%.

Since this rotator is based on a pseudorandom number generator (PRNG), there may be distribution bias on small scale.
However, mathematical properties of the PRNG guarantee that distribution will reach target weights on distance.

### Timer Rotator

Timer rotator cycles through enabled money pages, using weight as a number of seconds that a money page is active for.

For instance, if you have three money pages with weights 60, 120, and 180, then the first page will be shown to visitors
for 1 minute, then the rotator will cycle to the second page and display it to incoming clicks for 2 minutes, then go to
the third page and use it for 3 minutes, then cycle back to the first one, and so on.

This rotator is useful for automatic time-based switching of domains.

## White Page

This is the safe page to show to moderators, robots, scrapers, etc. It should not contain any sensitive content
that may put your affiliate campaign in danger or in violation of any rules. Everything described above for
the money page also applies to the white page--you may use an URL or a landing page file. In the latter case,
if your money page is also a self-hosted landing page, you will need to effectively merge two landing page
directories together to meet the requirement that both page files are located in the root of the common directory.

We strongly advise using a landing page file over external URL for white page. This has to do with suspicion and
increased scrutiny from compliance teams of certain ad networks that discourage or outright prohibit the use of
redirects in traffic flow.

## Pass URL Parameters to White URL

When enabled, parameters passed in the incoming URL will be appended to white page URL or file. This setting works
the same way like the Param setting of money pages.

## VLA™

VLA™ stands for Virtual Learning Appliance, the trademark of the machine learning system at the heart of Adspect.
It is discussed in detail in the [VLA chapter](vla.md). 95% is a good VLA precision value to begin with.

## HyperLogLog

[HyperLogLog](https://en.wikipedia.org/wiki/HyperLogLog) is an advanced algorithm used to estimate cardinality
of large sets. It is used in the eponymous state of the art filter that we invented to perform pattern-based
filtering in real time based on our entire operation history.  Lower values result in more aggressive filtering
and thereby better protection at the expense of higher chances of false positives.

**We recommend to set it to 1 when working with Google Ads and TikTok!**

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
Then it may be communicated to the money or white page via the `{clickid}` URL macro.

## Paranoid Mode

The paranoid mode enables additional strict fingerprint checks and vast IP address blacklists (counting up to 2 billion
IPv4 addresses) that are considered "paranoid", that is, with higher false positives chance, but at the same time
providing equally higher chance of busting moderators.

**We recommend to enable this mode when working with Google Ads and TikTok!**

## Allow Traffic From Mobile Apps

This setting tells us to allow traffic that originates from inside mobile applications, e.g. from WebView Android
browser. While natural for certain niche ad formats, such traffic is widely seen as click fraud in other formats
(automated clicks generated by mobile malware) and should normally be disabled unless your ad format is somehow
based on mobile applications.

## Countries, Operating Systems, Browsers, Engines, Languages, and Time Zones

These manual targeting options allow you to further restrict your stream to only allow visitors from specified
countries, using specified operating systems, browsers, browser engines, browser language preferences, or time zones.
You would normally set them to match your campaign targeting settings. If any of these settings is omitted (the list
is empty), then no check will be made for that setting.

Note: time zone settings are restricted to full hour offsets from UTC. If a visitor's time zone is not offset by
full hours, then the offset will be rounded.

## Match Browser Time Zone to Location Time Zone

If this setting is enabled, then Adspect will check whether the time zone reported by visitor's browser matches
the time zone of the visitor as determined by our geolocation. This check may slightly increase the rate of false
positives, but it significantly boosts protection against moderators and bots that use VPN or proxy services.
If enabled, the manual time zone list described above is ignored. It is recommended to enable this setting.

## Schedule

Schedule allows you to specify dayparts and optionally days of week during which traffic filtering is on.
All visits on time and days not explicitly listed will be blocked. Schedule is active if at least one daypart
is specified. If a daypart does not specify days of week, then it is applied to all days.

## Click Limit

This is the maximum number of clicks per each IP address that will be allowed. 0 means no limit. Visitors from
IP addresses that have exceeded the limit will be filtered out. The "Reset" button may be used to reset all click
counters for the stream.

## Blacklist IP Addresses upon Hitting the Limit

If this setting is enabled, then all IP addresses that exceed the click limit will be added to the IP/ASN blacklist
(see below.)

## URL Rules

This section allows you to create up to 30 custom rules for checking and manipulating URL parameters. Each rule
consists of:

* Parameter name -- this is the name of the URL parameter that will be checked or altered;
* Operator -- specific check or operation that will be executed;
* Argument -- argument of the operator, if applicable (string interpolation macros supported);
* "On" checkbox -- toggle that allows you to turn rules on or off.

The following operators are supported:

* `EXISTS` -- checks if parameter exists (rule argument ignored);
* `! EXISTS` -- checks if parameter does not exist (rule argument ignored);
* `REGEX` -- checks if parameter value matches a
  [Perl-compatible regular expression (PCRE)](https://www.pcre.org/original/doc/html/pcrepattern.html)
  in rule argument (case-sensitive);
* `REGEX (no case)` -- checks if parameter value matches a regular expression in rule argument (case-insensitive);
* `! REGEX` -- checks if parameter value does not match a regular expression in rule argument (case-sensitive);
* `! REGEX (no case)` -- checks if parameter value does not match a regular expression in rule argument (case-insensitive);
* =, ≠, >, ≥, <, ≤ -- compare parameter value with rule argument; integers and real values are compared as numbers,
  strings are compared according to [lexicographical order](https://en.wikipedia.org/wiki/Lexicographical_order);
* `ASSIGN` -- assigns rule argument as parameter value;
* `RENAME` -- renames parameter to rule argument;
* `DELETE` -- deletes parameter (rule argument ignored).

The order of rule execution is as follows:

1. Checks: `EXISTS` and `REGEX` rules, =, ≠, >, ≥, <, ≤ -- failed check sends to white page;
2. `ASSIGN` rules;
3. `RENAME` rules;
4. `DELETE` rules.

Rule argument supports all the same string interpolation macros available for money/white page settings.

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

## IP Extrapolation

IP extrapolation allows you to control fuzzy matching of IP addresses against our internal blocklists.
Larger values result in banning of more addresses adjacent to already blacklisted addresses, which results in
better protection at the expense of higher chances of false positives.

**It is recommended to set higher values when working with Google Ads and TikTok!** Start in the 128--255 range
and lower gradually if you suspect high false positives.

## IP/ASN List Mode

Adspect supports traffic filtering using lists of IP addresses, IP address ranges, and/or
[autonomous system numbers (ASN)](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)). Two such lists
exist: blacklist and whitelist.

The IP/ASN list mode controls how blacklist and whitelist combine to determine whether a particular visitor
should be blocked. Two modes are supported:

* Black: a visitor will be blocked only if their IP address or ASN is in the blacklist and is not in the whitelist.
  As such, the whitelist may be used to override the blacklist. This is the default mode.

* White: a visitor will be blocked unless their IP address or ASN is in the whitelist and is not in the blacklist.
  As such, the blacklist may be used to override the whitelist.

Please note that whitelists do not override IP/ASN blocklists built into the system.

## IP/ASN Blacklist

This is the blacklist. Both IPv4 and IPv6 addresses are supported, as well as CIDR and range notations. Examples:

* 192.0.2.1
* 192.0.2.0/24
* 192.0.2.0–192.0.2.255
* 2001:db8::1
* 2001:db8::/112
* 2001:db8::-2001:db8::ffff
* 721
* AS721

Individual entries should be delimited by newlines or whitespaces. Please note that the system will automatically
merge adjacent or overlapping ranges in order to optimize storage space and lookup speed.

## Blacklist All IP Addresses in Review Mode

If enabled, this setting instructs Adspect to add IP addresses of all incoming visitors to the IP blacklist if
the stream is in Review mode. Since the Review mode is meant to be used only when your ad campaigns are under
review by moderators, it is safe to assume that every visitor in this mode is a moderator and should be barred.
We recommend you to always enable this setting, but pay attention to the moment your campaign is approved,
to switch the stream mode to Filtering in time lest you blacklist IP addresses of legitimate visitors when
your campaign goes live.

## IP/ASN Whitelist

This is the whitelist. It has exactly the same input format as the blacklist.
