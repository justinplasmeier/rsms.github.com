---
title: "Mass Music Sync Pivot"
layout: post
description: "Like all good projects, I have had to redefine scope after digging in."
redirect_from: ["/2016/08/16/2016"]
---

## Migrating Music to Google Drive

While working on a sync utility for USB Mass Storage Music Players (like the FiiO X1), I realized that I had a problem. My desktop computer is now in storage, so I can't readily sync against it. So, I uploaded all of my files to Google Drive. Google Drive will now function as my master copy of music files, which is great because I can now access them from anywhere with an internet connection. However, a new problem manifested.

## Google Drive to USB Device

Google makes a desktop client for Drive so you can sync your Drive files to your local machine. This works great on my work laptop, which has ample free storage space. On my personal laptop, a 2013 Macbook Air, I almost never have more than 10Gb free. My music collection is somewhere around 60Gb so this would never work. Additionally, Google Drive currently does not support syncing to USB Devices. Google probably has a good reason for this, but there's no good reason for me to sync 60Gb of files manually. 

So, what if there was a hack around this? Google Drive provides [an API](https://developers.google.com/drive/v3/reference/). From there the process is such: Connect to Google Drive. Find/select the Music directory. Get the size of said folder. Get the free space of the local machine. Get the free space of the USB Device. Create a cache on the local machine for passing data through to the USB Device. Start downloading chunks of files into the cache. Upon completion, sync to USB Device. Unzip. Verify that directory structure, file integrity, etc. is preserved. Possibly check Last.fm/Sonemic for tagging information. 

I have never done anything like this, so hopefully I don't accidentally delete all of my Google Drive files...
