---
date: '2025-04-10T19:39:54-05:00'
draft: true
title: 'Go Language'
tags: ["go", "programming"]
---
I really like [Go](https://go.dev).

Let me count some ways:

- Simple object system
- Easy to understand minimal sytax
- Great docoumentation
- Go routines for concurrency
  - [Java Virtual threads](https://docs.oracle.com/en/java/javase/21/core/virtual-threads.html) are copying go
- One way to do things including networking and go routines
- All functions are [one color](https://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/)
- High quality http client/server built in
- AOT compilation, single file executable, static linking, no runtime dependencies
- Crazy fast builds
- Go modules
- Cross compilation
- One way to write tests/documentation/examples
- Built in benchmarking
- Crazy low memory usage compared with JVM
- Crazy fast GC compared with JVM
  - https://go.dev/blog/ismmkeynote
    - Two <500 microsecond STW pauses per GC
  - https://www.oracle.com/technical-resources/articles/java/g1gc.html
    - Default max pause time 200 milliseconds