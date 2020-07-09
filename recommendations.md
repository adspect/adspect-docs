# Recommendations

Below you may find a compiled list of general recommendations that we give to all of our customers.
We highly encourage you to follow them in order to achieve the best results with Adspect.

1. Domains and hosting

   1. **Do not use** domain names in cheap zones like `.site`, `.club`, `.world`, etc. as they attract more
      thorough and frequent scrutiny from antivirus and ad scoring companies, effectively being tainted
      from the beginning. **Only use** domains in `.org`, `.net`, and `.com` zones, in the order of priority.

   2. **Do not use** domain names that contain questionable or stop words: "sex", "xxx", "win", "diet",
      "health", "meet", "date", and so forth. Be creative and choose domain names that look and sound like brands.

   3. **Always use** [Cloudflare](https://www.cloudflare.com/) to hide IP addresses of your servers. Many ad
      networks flag IP addresses behind domains in banned accounts, however, they will never flag addresses
      that belong to a huge CDN company serving 10% of the World Wide Web. Adspect fully supports Cloudflare
      in proxy mode.

   4. **Do not use** technical domains of hosting companies. They both raise suspicion and uniquely identify
      particular servers of the hoster.

   5. **Do not use** Namecheap virtual hosting because they employ WAF (web application firewall) that by
      default blocks POST requests that we rely upon, causing 403 Forbidden errors.

   6. **Do not name** any of your money or white page files `index.html` because it is likely to take priority
      over our `index.php` if the file name is omitted in the URL after `/`, thus exposing your real pages.
      Always use distinct and hard to guess names for money and white pages.

   7. **Make sure** that your white pages do not have broken links, non-loading images, or scripts with errors.
      Check the Console and Network tabs of developer tools in your browser, it will highlight potential
      problems red.

   8. **Do not use** web resources (images, styles, scripts) from third party domains unless they are well-known
      CDNs (content delivery networks) like those of jQuery, Bootstrap, Font Awesome, Google Fonts, etc.
      Better download them and host locally.

   9. **Always set up** your pages for HTTPS. Cloudflare provides free SSL/TLS certificates for domains in proxy
      mode. [Let's Encrypt](https://letsencrypt.org/) also provides an infrastructure for obtaining free
      SSL/TLS certificates.

   10. **It is recommended** to use hosting providers in proximity to your target audience, ideally in the same
       country. This is especially important when working with popunder ad format.

3. Cloaking tips
   1. **Never reuse** domain names in the same network. If an account is banned, then write down all the domains
      used by its campaigns and don't use them again. Register new domain names for new accounts.

   2. **Always use** the most strict manual filters by country, OS, browser, and languages. Match them to
      targeting settings of your campaigns.

   3. **Always use** zero redirect (file-based) display mechanism for white pages, if possible. This rule
      is **mandatory in Facebook and Google Ads** with PHP integration!

   4. **Always make sure** that your white pages are convincing and relevant to your ad campaigns (creatives,
      languages, targeting options.) **Never use** obviously bogus white pages like redirections to Google.

   5. **Use site constructors** like [Shopify](https://www.shopify.com/), [Wix](https://wix.com/), [Tilda](https://tilda.cc/),
      etc. for white pages when working with Facebook or Google Ads as they come with highly trusted domains.
      We support JavaScript integration for them. **One exception is Tilda**: Google seems to preemptively
      flag sites in the `tilda.ws` zone for cloaking, which has been reported several times recently.

   6. When using site constructors, **check availability** of your site periodically--it is not uncommon
      for such services to ban sites for suspected use in various cloaking schemes.

   7. **Put links** to legitimate-looking terms of use, privacy policy, and cookies policy on your white pages
      when working with the more strict ad networks like Facebook, Google Ads, Microsoft Advertising, etc.
      **The EU Cookie Law also requires** websites to obtain informed consent from visitors if using cookies.

   8. **Put** `robots.txt` and `sitemap.xml` files into the root of your domain, especially when working with
      search engine-based ad networks (Google Ads, Microsoft Advertising, Verizon Media Native, etc.)

   9. **Do not use** copied landing pages as is, always alter them in one way or another to make them overall
      unique. This practice helps with disarming signature-based flagging.

   10. **Do not use** copyrighted materials on your white pages, they may be detected and rejected.

   11. **Do not use** overly simple, single page, skewed/broken, non-mobile-optimized, or low quality white pages.
       Always remember that a white page must look like a legitimate, useful website with authentic content.

   12. **Do not use** landing pages of supposedly "white" offers as white pages--advertising networks tend to
       have a wildly different idea of "white". **Never use** direct affiliate links as white pages.
