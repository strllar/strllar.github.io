---
layout: post
title: "XLM Network Upgraded To v2"
description: ""
category: 
tags: [Tech, XLM]
pinned: false
---

Today we upgraded all our validator nodes to [v0.2-12-g005dc0b](https://github.com/stellar/stellar-core/commit/005dc0).

Windows binary is available at [downloads](/downloads) page

---

## Endnote
When we stopped our first node to upgrade, the network was paused at ledger 239766. it seems some of nodes in our quorum slice were also upgrading. So we shrinked our quorum slice to make advance. Everything is ok then.

This makes us aware that we need a dashboard showing validators status, including nodeid, name, last closed ledger, current status, quorum slice, ip address/gelocation etc.