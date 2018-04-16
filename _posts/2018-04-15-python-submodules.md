---
layout: post
title: "Importing Python Submodules"
summary: "For the longest time I have been making a small mistake with the Python import system"
date: 2018-04-15
comments: true
share: true
---

For the longest time I have been making a small mistake with the Python import system: when using a submodule in the standard library, oftentimes I only import the parent module. For example, if I need to use the `isfile()` method in the `path` submodule of the `os` module, like so:

```python3
import os

# ...

if os.path.isfile("/foo/bar"):
  # ...
```

However, this does not always work. I found this out when trying to instantiate a `Message()` object in the `message` submodule of the `email` module. On my machine (Python 3.5.2) I ran into this strange behavior:

```
>>> import email
>>> email.message.Message()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: module 'email' has no attribute 'message'
>>> email.message.Message()
>>>
```

Many Python developers know that importing a parent module will only import the child submodule if the parent's `__init__.py` imports the child for you. As a mistake on my part, I thought perhaps this wasn't necessary for the standard library, but thankfully the language is consistent for this case! Indeed, since `email` is a module in pure Python, the source is quite easy to read, and we can see in [`__init__.py`](https://github.com/python/cpython/blob/master/Lib/email/__init__.py) for that module that this behavior is deliberate (ostensibly for performance reasons):

> Don't import Parser and Message as side-effects of importing email since those cascadingly import most of the rest of the email package.

Interestingly, this behavior does not occur on Python 3.6, even though the source for this file is identical in that version. Perhaps there was a change made to the import system itself that causes the submodule to be intelligently imported in this case?

The takeaway is that you should always import a submodule when you need it, even if the parent module is already imported, even if not doing so would work 9 times out of 10. Indeed, in spite of Stack Overflow and other resources making this mistake often, the official Python documentation rarely makes this mistake, so it is better to be safe than sorry!
