---
layout: page
title: Email me
permalink: /email/
---

The following is my email address, reversed then base64-encoded:

```
bW9jLmxpYW1nQGxlaWNhbWFrZXJlZA==
```

To reveal it, base-64 decode it then reverse it again. For example, in Python 2
and 3, you can simply do the following:

```python
from base64 import b64decode
msg = "bW9jLmxpYW1nQGxlaWNhbWFrZXJlZA=="
print(b64decode(msg)[::-1])
```
