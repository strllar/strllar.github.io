---
layout: post
title: "A Possible Way To Lose History Wholely When Upgrading"
description: ""
category: 
tags: [Tech]
pinned: false
---

Suppose we are upgrading our nodes with stellar-core which need reset db in such configuration:

* NodeA (Get History from NodeB, Get History from outside NodeOut1, Get/Put History to NodeA)
* NodeB (Get History from NodeA, Get History from outside NodeOut1, Get/Put History to NodeB)

And if we are using a buggy stellar-core will publish corrupted history archive, which has made all NodeOut* history not accessable.
NodeA will publish bad archives as any other outside after catch up. NodeB will successfully catchup by using itself history archive. once it catches up, the last good archive is overwriten.

## Preventive Upgrade Strategy

There are two obvious methods to avoid such tragedy.

* Backup history archive before uprading process overwrite it
* Don't config get from a put archive

