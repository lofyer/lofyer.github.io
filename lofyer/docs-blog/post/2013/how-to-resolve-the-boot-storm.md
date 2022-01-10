---
title: "How to solve the 'boot storm' problem: BCACHE"
date: "2013-05-27"
categories: 
  - "cloud"
  - "linux-admin"
---

**Some solutions** Way 1. Add more cpus.(ABANDONED) Way 2. Add a SSD as "boot cache" Way 3. Sort the boot process

**POC:** **Way 2:** Sometimes we need cache the boot section of the OS into a SSD, since no SSD on hand, let's try to use a block device made in /dev/shm

**Way 3:** Considering that the parallelization of the "boot action", we have to predict the action in the near future

Experiment: Using BCACHE now...or flashcache
