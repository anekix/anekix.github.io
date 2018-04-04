---
layout: post
title: Understanding Data alignment inside memeory!
categories:
- blog
---

We as programmers think of memory as a single array of bytes.But how do processors read & write to memory? processors do not read from and write to memory in byte-sized chunks, it accesses memory in 2/4/8/16.32 bytes chunks, which is called the memory access granualrity of processors.
