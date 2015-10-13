---
layout: post
title: "Please Add Us To Your Quorum Slice"
description: ""
category: 
tags: [XLM]
pinned: true
---

We are running 3 validators in stellar production network. You can add us in by direct using sample [stellar-core.cfg](https://github.com/stellar/docs/blob/master/other/stellar-core-validator-example.cfg) as your config file or add the following to corresponding sections in your config file.

## Peers
```
KNOWN_PEERS=[
"chiyou.strllar.org",
"confucius.strllar.org",
...
```

## Validators
```
[QUORUM_SET]
VALIDATORS=[
"GD5DJQDDBKGAYNEAXU562HYGOOSYAEOO6AS53PZXBOZGCP5M2OPGMZV3",  #lab1
"GBGGNBZVYNMVLCWNQRO7ASU6XX2MRPITAGLASRWOWLB4ZIIPHMGNMC4I",  #lab2
"GDPJ4DPPFEIP2YTSQNOKT7NMLPKU2FFVOEIJMG36RCMBWBUR4GTXLL57",  #lab3
...
```

## History
```
...
[HISTORY.chiyou]
get="curl -sf https://stellar.oss-cn-beijing.aliyuncs.com/xlm/{0} -o {1}"
[HISTORY.confucius]
get="curl -sf https://s3-ap-northeast-1.amazonaws.com/confucius.tome.strllar.org/xlm/{0} -o {1}"
```
