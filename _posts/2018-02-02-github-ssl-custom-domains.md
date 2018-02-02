---
layout: post
title: "Enabling SSL on Github Pages custom domains with Cloudflare"
summary: "How to enable SSL on a Github pages custom domains with a free Cloudflare account."
date: 2018-02-02
comments: true
share: true
---

My blog is hosted by Github Pages with a custom domain. While Github Pages can be served over SSL, it won't be if you use a custom domain, which was not ideal for me. However, it is easy to set this up with Cloudflare.

Cloudflare is free for personal webpages like my own. Simply create an account and follow the instructions to copy your DNS settings to Cloudflare, then set your domain's nameservers to the ones provided by Cloudflare. Once done, I enabled "Always Use HTTPS" in the Crypto settings, which is applied to your entire domain. Additionally sometimes Cloudflare will require potentially malicious visitors to answer a CAPTCHA or become rate limited, but since I am not that concerned with that for my site, in my Firewall settings I changed my Security Level to "Essentially off." 

Since this requires you to use the DNS hosting provided by Cloudflare, this may or may not be ideal depending on your DNS needs. For me, Cloudflare supports everything I needed from the free DNS hosting provided by my registrar. Cloudflare lets you create as many MX, A, or other DNS records as you want. I was also using DDClient to periodically update an A record to the dynamic IP of my home network, and it was quick and painless to modify my DDClient configuration to work with Cloudflare.[^1] 

[^1]: By default, A records will instead point to a Cloudflare CDN server instead of the direct IP you entered. If you do want unhindered access directly to the IP address like I did, it is quite simple: click the button next to the A record to change it from a "DNS and HTTP Proxy (CDN)" record to a "DNS only" record.

There are limitations with the free Cloudflare subscription however. For starters you must use the Cloudflare-provided certificate for SSL, and you only get one of them for your entire domain, so if you have your own certs such as from Lets Encrypt you will not be able to use them. Secondly, you only get 3 Page Rules with the free account. Page Rules let you define URL patterns and change your Cloudflare settings for requests to those URLs; for instance, you may completely disable Security for requests to `/api/*` or disable SSL for a subdomain. Three rules doesn't sound like much but I currently use zero so YMMV.

If you wish to get SSL working with Github Pages and the limitations of the free Cloudflare account are not an issue, I would highly recommend it. The entire process only takes a few minutes, and it can even provide you with other benefits, such as enabling AMP with one click and Cloudflare's Always On CDN.
