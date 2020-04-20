# Overview

Adspect is an easy-to-use cloud-based service for protecting affiliate campaigns (offers, landing pages) from
"bad" traffic. By bad traffic we mean:

* click fraud, ubiquitous in display ads and popunder;
* moderators and policy teams of ad networks;
* spy services;
* [content scrapers](https://en.wikipedia.org/wiki/Web_scraping);
* [credential stuffing bots](https://en.wikipedia.org/wiki/Credential_stuffing);
* bots of antivirus companies;
* and other similar flavors of unwanted visitors.

This is done via a special `index.php` file that you place in your landing page directory or elsewhere accessible
via HTTP. This file acts as an entry point for web traffic and is wired to our servers that do the filtering.
Depending on filtering decision a visitor may be directed to your actual page or to a "white page",
that is, a page that contains no sensitive content. In other words, Adspect acts as an intermediary stage in your
traffic flow, actively filtering unwanted traffic from legitimate visitors.

## index.php

`index.php` is a PHP script that serves the purpose of a bridge between your premises and our backend servers.
The file name `index.php` is just a convention that we use throughout the system, however, you may rename it as
you like. The fact that we use a PHP script to filter traffic naturally implies that you need a PHP-enabled web
hosting or a tracker with support for landing pages written in PHP. For this reason, Keitaro TDS in particular
is not suited for hosting `index.php` as it lacks PHP support for landing pages.

The script is carefully written to be compatible with a wide variety of web hosting environments, ranging from
virtual hosting and VPS to dedicated servers and Amazon AWS. Both Windows and Unix-like operating systems are
supported, to the extent supported by PHP. PHP 7 is recommended, PHP 5 is supported, PHP 4 *may* probably work
but hasn't been tested and will be left out of this list eventually. Use the latest available version of PHP.

The only requirement is that PHP has to be built with [cURL support](https://www.php.net/manual/en/book.curl.php).
You may check if cURL is supported by examining [phpinfo](https://www.php.net/manual/en/function.phpinfo.php),
but cURL is supported by almost every PHP build out there.

## Workflow

The common workflow with Adspect for affiliate marketing campaigns consists of the following steps:

1. Create an Adspect stream for your campaign and place it in "On Review" mode.
2. Download an `index.php` file associated with that stream.
3. Place `index.php` into the root directory of your landing page or host it standalone.
4. Create an ad campaign using the link to the `index.php` file.
5. Wait for campaign approval to switch the stream into "Filtering" mode.
6. Run traffic and explore statistics in the "Reports" section.

We will describe caveats of these steps in detail in the next chapters.
