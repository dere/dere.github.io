---
layout: page
title: Email me
permalink: /email/
---

I don't want to put my email in plain-text or I'll be spammed to death, and
[Cloudflare email address obfuscation](https://support.cloudflare.com/hc/en-us/articles/200170016-What-is-Email-Address-Obfuscation-)
requires JavaScript enabled which I am not fond of, so... the following is my
email address, reversed then base64-encoded:

```
bW9jLmtpaGNydWdAc2Vpcml1cW5p
```

To reveal it, base-64 decode it using one of the many base-64 decoders online
([here's one](https://www.base64decode.org)), then re-reverse the string. Or if
you have Python 2 or 3 installed you can simply paste this into a terminal:

```
$ python -c "from base64 import b64decode as d; print(d('bW9jLmtpaGNydWdAc2Vpcml1cW5p')[::-1])"
```
