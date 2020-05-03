# Use Cases

Adspect has a few well-defined use cases that have proven to be consistent and useful. Remember that Adspect
has two intertwined but still distinct functions: cloaking and bot filtering. The latter helps a lot in
achieving the former. We will describe the benefits of both just below.

## Cloaking

Cloaking is the practice of hiding the real web page, be it a landing page or a CPA offer, from those who should
not see it, at the discretion of the one who is in control of that page. We at Adspect firmly believe that if
you do not wish to expose your content to a certain party, then you should be able to limit access to it,
regardless of your reasons. We give you the power to do so. In particular, this means hiding your landing pages from
ad network moderators, spy services, and antivirus robots. Those visitors will never make you any money.

## Detecting Bot Zones

Popular purely web-based ad formats like banners, teasers, native ads, and popunders all suffer from
[click fraud](https://en.wikipedia.org/wiki/Click_fraud).
Technology they are based on--HTTP, HTML, and JavaScript--allows for relatively easy and cheap automated clicking,
just pick any of the programmable [headless browsers](https://en.wikipedia.org/wiki/Headless_browser) out there.
No wonder these browsers, initially meant for website testing automation, are now widely used to forge clicks
in ad networks and make advertisers pay for what will never bring them any revenue.

Adspect can detect all of them with ease. All you have to do is configure a stream to parse the subaccount ID
out of the tracking URL as [described in the chapter on streams](streams.html#sub-id). If you pass a publisher,
site, or zone ID (let's call it a *source* from now on) to Adspect via a link parameter, then you will be able
to pull per-source reports with exact figures of human ratio in the traffic. The rightmost "Quality" column in
reports will let you evaluate and compare different sources, showing the percentage of legitimate traffic
among the whole. Just select "Sub ID" in the grouping list to the left of the timezone picker.

Drawing a line at, say, 80% of humans in traffic, you can easily find sources that meet the requirement--just
click the "Quality" column header to sort the table by that column. Sources with quality above 80% will give
you a whitelist; conversely, sources with quality below 80% will be your blacklist. You will find this simple
method invaluable for determining converting traffic sources in display ads and popunder without spending
fortunes on filtering sources by sheer CR (conversion rate.) Filter out bot-ridden sources first, then filter
the rest by CR as you would normally do.

## Hiding Traffic Sources

Many affiliate networks have internal media buying teams that would be all too happy to discover your traffic
sources and use this information to steal your campaigns. Therefore you would normally want to hide your sources
from affiliate networks, or any other parties, for that matter. Adspect does this for you by removing
the [Referer HTTP request header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referer)
from all clicks, making sure that observers on the traffic redirect chain will not see the source of your traffic
in web server logs.
