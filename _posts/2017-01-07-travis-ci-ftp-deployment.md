---
layout: single
title:  "FTP deployment with Travis CI and lftp"
date:   2018-01-17 13:47:32 +0100
categories: travis ftp deployment
---
We're in 2018 now and it couldn't be so hard to upload my generated Jeykll via FTP to my favorite hoster
but it took me several hours to get this working...

Travis CI has [a not so nice example](https://docs.travis-ci.com/user/deployment/custom/#FTP) for a FTP 
deployment which basically uploads just one file. I needed something more sophisticated like `lftp` which 
is able to upload a whole directory. I wrote this little script which uploads my `_site` directory to a remote host:
```
#!/usr/bin/env bash
set -e

LOCALPATH='./_site'
REMOTEPATH='/'

lftp -f "
open ftp://$FTP_HOST
user $FTP_USER $FTP_PASSWORD
mirror --continue --reverse --delete $LOCALPATH $REMOTEPATH
bye
" 
```
Unfortunately I always got this dirty little error message:
```
mirror: Cannot assign requested address
```
After several hours of headbanging I figured out that this is a ipv6 related issue und I simply added
this line before `open ftp://$FTP_HOST`:
```
set dns:order "inet"
```
and my files got uploaded without any errors (even with TLS encryption)!
