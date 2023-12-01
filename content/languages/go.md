---
title: Go
tags:
 - go
 - google
---

[Go](https://go.dev/) is a statically typed, compiled high-level programming language designed at Google. 
<!--more-->
It is syntactically similar to C, but also has memory safety, garbage collection, structural typing, and CSP-style concurrency.

## Installation (Windows)

There are a number of [installation methods for windows](https://gohugo.io/installation/windows/).

My preference is to download the `.msi` file from the [Go releases page](https://go.dev/dl/) and install.


## Should I commit my 'go.sum' file as well as my 'go.mod' file?
Yes. Ensure your `go.sum` file is committed along with your `go.mod` file. See the github FAQ below for more details and rationale.

From the [FAQ](https://github.com/golang/go/wiki/Modules#should-i-commit-my-gosum-file-as-well-as-my-gomod-file):

### Should I commit my 'go.sum' file as well as my 'go.mod' file?
Typically, your module's `go.sum` file should be committed along with your `go.mod` file.

* go.sum contains the expected cryptographic checksums of the content of specific module versions.
* If someone clones your repository and downloads your dependencies using the go command, they will receive an error if there is any mismatch between their downloaded copies of your dependencies and the corresponding entries in your go.sum.
* In addition, go mod verify checks that the on-disk cached copies of module downloads still match the entries in go.sum.
* Note that `go.sum` is not a lock file as used in some alternative dependency management systems. (`go.mod` provides enough information for reproducible builds).