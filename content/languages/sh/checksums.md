---
title: File Hashes
tags:
 - unix
 - linux
 - file
 - checksum
 - hash
---

Commands and examples for calculating file checksums (a.k.a. file hashes) in Linux.
<!--more-->

## Installing tools

```shell
# Ubuntu
sudo apt install hashalot
```

## Generate md5sum

```shell
$ md5sum /tmp/my-file.txt
80bffb4ca7cc62662d951326714a71be  /tmp/my-file.txt
```

## Generate sha256sum 

```shell
$ sha256sum freshtomato-E3000-K26MIPSR2_RTN-USB-NVRAM60K-2024.2-VPN.zip
fc54a3182f8a58060029fb0d8e879856824637ee119f94fce82f9e9792d333ee  freshtomato-E3000-K26MIPSR2_RTN-USB-NVRAM60K-2024.2-VPN.zip
```

## Generate a hash file

```shell
$ md5sum /tmp/duplicate.txt >> hashes.txt
$ cat hashes.txt
80bffb4ca7cc62662d951326714a71be  /tmp/duplicate.txt
```

## Read hashes from file and check them

```text
80bffb4ca7cc62662d951326714a71be  original.txt
80bffb4ca7cc62662d951326714a71be  /tmp/duplicate.txt
1f59bbdc4e80240e0159f09ecfe3954d  /tmp/duplicate.txt
```

```shell
$ md5sum --check hashes.txt
original.txt: OK
/tmp/duplicate.txt: FAILED
/tmp/duplicate.txt: OK
md5sum: WARNING: 1 computed checksum did NOT match
```