---
date: '2025-05-04T10:39:20-05:00'
draft: false
title: 'I Like Go'
tags: ["go", "programming"]
---
I like [Go](https://go.dev).

It is definitely not perfect but there is much to like.

Let me count some things I like:

# Simplicity and Correctness

Go values simplicity and correctness, both for the language itself and its tooling.

Unlike many other languages where the choices of build tools, networking, threading models, memory management, and event loops are up to the user, Go takes the opposite route.

Go has one simple, high-quality way to do things.

# Concurrency Model

Goroutines and channels allow concurrency in the Go language.

The idea of using goroutines as an asynchronous function might seem simple but it actually is very powerful.  

A developer can write simple code that looks like it blocks but it doesn't.  

Go will automatically schedule and run goroutines on a small number of operating system threads.  It manages preemption and blocking for I/O or other resources automatically.

Each goroutine starts with a tiny stack size that can grow as needed, so the memory usage of go programs tends to be extremely small.  1 to 2 orders of magnitude smaller memory footprint vs a similar JVM application is common.

Goroutines are the only way to do threading in the go language.  All goroutines are equal.  

All go functions are [one color](https://journal.stuffwithstuff.com/2015/02/01/what-color-is-your-function/), the developer does not have to worry about marking functions as async or suspend.

Goroutines and channels have been copied by many other languages from Kotlin Coroutines to Rust's Tokio library.

Java's [Virtual Threads](https://docs.oracle.com/en/java/javase/21/core/virtual-threads.html) are an attempt to unify many many different threading models in the JVM and are copying go's threading model.

# High Quality Standard Libraries

Go has high quality standard libraries.

One example is the net/http client and server library.  This library transparently supports HTTP/1.x and HTTP/2.0.  HTTP/3.0 and QUIC are not in the standard library but the [quic-go](https://github.com/quic-go/quic-go) module is easy to use with net/http.

Go is still adding to the standard library after many releases, some recent examples are [slog](https://go.dev/blog/slog) for structured logging and [enhancing net/http.ServeMux](https://go.dev/blog/routing-enhancements).

# Fast Runtime, Low Memory Usage

Go is well optimized at runtime.

Because it uses ahead of time (AOT) compilation there is no worry for just in time (JIT) compilation slowing things down, or requiring warmups which are common in the JVM world.

Go automatically creates a fixed number of threads based on available CPUs at startup and runs goroutines there.

Go has one garbage collection algorithm with only [2 knobs](https://tip.golang.org/doc/gc-guide) (GOGC and GOMEMLIMIT), compared with the JVM's myriad of GC algorithms and options.

As of 2018 (7 years ago) Go's garbage collector has [2x 500 microsecond pauses](https://go.dev/blog/ismmkeynote) per GC cycle.  Often the pauses are smaller in the 100-200 microsecond range.  Meanwhile in 2025 the default GC pause time of the JVM is [200 milliseconds](https://www.oracle.com/technical-resources/articles/java/g1gc.html).  Go's GC has 2-3 orders of magnitude smaller pauses vs the JVM.

Because the JVM's pause times are big (200 milliseconds) a common tactic even for stateless applications is to use multiple GBs of heap space.  2-4GB might be considered small for high-performance JVM apps so the GC does not have to run often.  

With go heap usage of 10-20 megabytes is common for a stateless application.

# Go Modules

Go has something like Gradle built-in to the language.  It manages dependency versions, transitive dependencies, and embedding version info to an app automatically.  

Go modules do not rely on a central repository server like Gradle/Maven.  They take a simple approach of checking out code from a public code repo (e.g. github), building it locally, and caching the compiled library locally.

I find go modules defaults to be more sane than Gradle, for example with just one `go build` command I can build my app and produce an executable.  

With Gradle we must worry which plugin to use (shadowjar? application? spring boot fatjar?) and arcane details like whether a jar's manifest file contains the correct main class name.  For libraries we must worry about complex processes to upload to private or public maven/gradle repositories.  None of this is needed in go.

In just 3 commands and a few milliseconds we can update go itself and all modules to their latest versions for a project, while respecting semver:

```sh
go get go@latest
go get -u ./...
go mod tidy
```

In the JVM world it is not uncommon for updating libraries to take days/weeks/months of effort.

Meanwhile go will automatically download new versions of itself as needed at build-time.  We are ensured [forward](https://go.dev/blog/toolchain) and [backward](https://go.dev/blog/compat) compatibility betwen go versions.

# Fast Builds

Go's builds are extremely fast.

Running tests and builing often takes less than 1 second even for large projects.

Meanwhile a JVM app might take many seconds or minutes to build and startup, and we must worry about the costs of JIT when the app starts up.

Go's compiler is self-hosting.  Builds will automatically use all available CPUs.

Go is in fact so fast that [Typescript is rewriting their compiler in go](https://devblogs.microsoft.com/typescript/typescript-native-port/) for a 10x performance improvement vs the self-hosting compiler.

# Cross Compilation

As go uses AOT compilation, we have to consider what operating system and cpu architecture we want to run the executable on.  By default `go build` produces an executable for the local OS and CPU.

Fortunately cross-compiling is built-in we just set 2 environment variables at build-time: GOOS and GOARCH

By setting these variables we can run the build on any platform and produce an executable for any platform go supports (which is quite wide-ranging and includes some more obscure options):

```sh
GOOS=linux  GOARCH=arm64  go build
GOOS=linux  GOARCH=amd64  go build
GOOS=darwin GOARCH=arm64  go build
GOOS=js     GOARCH=wasm   go build
GOOS=aix    GOARCH=ppc64  go build
GOOS=linux  GOARCH=s390x  go build
```

# Single File Executable

Go's builds produce a single file executable with no runtime dependencies.

The entire go-runtime is statically linked into the executable.  Executable sizes less than 10MB are common.

We do not have to worry about installing a runtime, or having runtime dependencies available as in the JVM and many other languages (python/ruby/node/perl/etc)

We can even [embed files](https://pkg.go.dev/embed) into the executable, this is useful for HTTP servers with static assets.
