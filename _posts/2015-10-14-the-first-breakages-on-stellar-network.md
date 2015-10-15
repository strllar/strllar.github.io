---
layout: post
title: "The First Breakages On Stellar Network"
description: ""
category: 
tags: [XLM, Tech]
pinned: false
---

Ledger 1 of stellar live network was closed at @1443631614 (seconds from [epoch](https://en.wikipedia.org/wiki/Unix_time)) according to history [archive](https://s3-eu-west-1.amazonaws.com/history.stellar.org/prd/core-live/core_live_001/ledger/00/00/00/ledger-0000003f.xdr.gz).
As the network was very small and fragile at early days, it paused for a few times.

## Breakages
If sort ledger sequence by close time on a node with postgresql fully catched up,

```sql
SELECT * FROM (select ledgerseq,closetime, (lead(closetime) OVER (ORDER BY ledgerseq) - closetime) AS duration FROM ledgerheaders ORDER BY ledgerseq) AS et ORDER BY duration DESC LIMIT 10;
```

you may get results like this:

```
 ledgerseq | closetime  |  duration
-----------+------------+------------
    224595 | 1444811694 |
         1 |          0 | 1443631614
     43952 | 1443878587 |      13117
     14605 | 1443706755 |       6245
     14606 | 1443713000 |       3292
     17575 | 1443732324 |       1832
         2 | 1443631614 |       1740
     30294 | 1443801533 |         79
    151400 | 1444438084 |         77
     29247 | 1443795636 |         67
(10 rows)
```

Omitting latest unclosed ledger 224595 and ledger 1,2. The breakages happened on ledger 14605, 17575, 43952.

### Ledger 14605 (Oct  1 13:39:15 UTC 2015)

There were only four validators in our quorum slice when we spinned network up.

```
UNSAFE_QUORUM=false
FAILURE_SAFETY=1
[QUORUM_SET]
VALIDATORS=[
"GD5DJQDDBKGAYNEAXU562HYGOOSYAEOO6AS53PZXBOZGCP5M2OPGMZV3", #chiyou
"GBGGNBZVYNMVLCWNQRO7ASU6XX2MRPITAGLASRWOWLB4ZIIPHMGNMC4I", #yan
"GDPJ4DPPFEIP2YTSQNOKT7NMLPKU2FFVOEIJMG36RCMBWBUR4GTXLL57", #pangu
"GB6REF5GOGGSEHZ3L2YK6K4T4KX3YDMWHDCPMV7MZJDLHBDNZXEPRBGM"  #donovan
]
```

Then chiyou triggered an [assert](https://github.com/stellar/stellar-core/pull/816) when trying push history archive to [OSS](http://oss.aliyun.com/) across [something nonexistent](https://en.wikipedia.org/wiki/Great_Firewall).
![oops! lab's node was down](/img/quote_lab_oct1.png)

While the failure safety was used up, donovan restarted his node
![oops! donovan's node was down](/img/quote_donovan_oct1.png)

Then the network paused.

### Ledger 17575 (Oct  1 20:45:24 UTC 2015)

we extended quorum slice to 7 validator while recovering from ledger 14605 for 1 more failure safty.

```
[QUORUM_SET]
VALIDATORS=[
"GD5DJQDDBKGAYNEAXU562HYGOOSYAEOO6AS53PZXBOZGCP5M2OPGMZV3",
"GBGGNBZVYNMVLCWNQRO7ASU6XX2MRPITAGLASRWOWLB4ZIIPHMGNMC4I",
"GDPJ4DPPFEIP2YTSQNOKT7NMLPKU2FFVOEIJMG36RCMBWBUR4GTXLL57",
"GBGR22MRCIVW2UZHFXMY5UIBJGPYABPQXQ5GGMNCSUM2KHE3N6CNH6G5",
"GAOO3LWBC4XF6VWRP5ESJ6IBHAISVJMSBTALHOQM2EZG7Q477UWA6L7U",
"GDVFVU7KURZ22MO56MP7IWX7Q4PEMHFQCOBTJVD2IR4OITEAUN64CCT2",
"GB6REF5GOGGSEHZ3L2YK6K4T4KX3YDMWHDCPMV7MZJDLHBDNZXEPRBGM"
]
```
chiyou node trigger the same assert again and restarted in catching up state when closing ledger 17575. Meanwhile, some other nodes met connectivity issue, then network paused again. It not noticable becaused network automatically recovered after about half an hour when connectivity restored.

### Ledger 43952 (Oct  3 13:23:07 UTC 2015)

nelisky's node was out of service first,
![oops! nelisky's node was down](/img/quote_nelisky_oct4.png)

one of our nodes was crashed,
![oops! our node was down](/img/quote_lab_oct4.png)

another one of our nodes was crashed also,
![oops! our node was down](/img/quote_lab_oct4b.png)

Then network was paused because 3 nodes are out of service, more than failure safty which is 2. The root cause of crashing is still under investigation. [#818](https://github.com/stellar/stellar-core/issues/818) [#840](https://github.com/stellar/stellar-core/issues/840)


## Endnote

[SCP](https://www.stellar.org/papers/stellar-consensus-protocol.pdf "Stellar Consensus Protocol") prefer safty than availability. We are happy there was no ledger fork after all. And [v0.2](https://github.com/stellar/stellar-core/releases/tag/v0.2) improved robustness in some aspects.

We also noticed connectivity issues between data center of node running and that of storage may cause history archive files missing, so we created a [script](https://github.com/strllar/stellar_history_sync) can check and sync history archives.