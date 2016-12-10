---
title: "Combining Diacritical Marks"
layout: post
description: "Adventures in Unicode"
redirect_from: ["/2016/12/10/combiningmarks.html"]

---
While working on Mass Media Sync, I kept getting a weird bug that an album was not found, despite its presence. The comparison to find the album was being done with the Google Drive `'title'` metadata attribute and the name of the album's directory. On debugging, the two names _looked_ exactly identical.:

~~~
Sigur Ros - Ágætis byrjun
Sigur Ros - Ágætis byrjun
~~~

Yet, the comparison kept returning false. so I used [this website](http://online-toolz.com/tools/text-unicode-entities-convertor.php) to decode the two strings and see where they differed:

~~~
Unicode:
Sigur Ros - Ágætis byrjun vs Sigur Ros - Ágætis byrjun

Unicode Entities:
Sigur%20Ros%20-%20A%u0301g%E6tis%20byrjun%20vs%20Sigur%20Ros%20-%20%C1g%E6tis%20byrjun
~~~

More succinctly:

~~~
Ágætis---Ágætis

A%u0301g%E6tis---%C1g%E6tis
~~~

The first string begins with the character `A` followed by `%u0301` which is code for the "combining acute accent" character `  ́` (which, by the way, messes with MacDown (my markdown editor) quite a lot). The second string begins with `%C1` which is code for the actual "capital A with acute" character `Á`. 

There we have it. Upon further research, `%u0301` is part of a unicode block known as [Combining Diacritical Marks](https://en.wikipedia.org/wiki/Combining_Diacritical_Marks).

### `os.mkdir`

So why were the strings different in the first place? I tracked the difference in representation down to a call to `os.mkdir` in the Python Standard Library's `os` module. When the string with character `C1` was being passed to `os.mkdir`, the directory created had the string with `A` and the combining mark as its name. I'm not sure why this happens, but my guess is something in POSIX/UNIX or MacOS doesn't like having certain unicode characters as directory names. However, this doesn't explain why (seemingly, at least) more complicated unicode is used instead. 

### The Fix

Since I don't know exactly why (and thus when) the operating system changes the encoding of the string, my fix is somewhat of a hack. I simply check for the unicode characters that were causing issues. If the characters are present, the create a directory with that name, get the name of that directory, and use that name instead. In the future, I think I will expose this functionality through a command line switch that checks (via ad-hoc directory creation) the entire library for mismatching names/directories and uploads any changes. 
