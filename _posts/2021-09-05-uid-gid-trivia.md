---
title: UID/GID trivia
tags:
- osx
- system
---

<hr>

Root user's uid/gid is 0:0.
As os gives privilege based on uid / gid, hacker may try to acquire root privilege with changing it's uid/gid with 0:0.

<hr>

Usually, uid is bigger than or equal to 0, but "nobody" user in osx has uid/gid of -2:-2.
In HP-UX, -2 is actually a minimum value that uid / gid can have. It seems that os gives "nobody" user's uid/gid minimum value.

<hr>

In osx or in linux, general user's uid is more than or equal to 500. System account's uid is less than 500.
However if you try to find user in osx's /etc/passwd file, you cannot find uid more than 500.
That is, you only can find system accounts in /etc/passwd file.

Osx manages general user not by /etc/passwd file or /etc/shadow file, but with user management service : DirectoryService.
You can see user information with "dscl" command. 

<hr>

In osx, hidden group or hidden user has name starts with underscore. 
Therefore, when you see /etc/passwd file, most of the system account's name starts with underscore.
