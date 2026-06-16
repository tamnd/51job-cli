---
title: "Installation"
description: "Install job51 from a release, with go install, or from source."
weight: 20
---

## Prebuilt binaries

Every [release](https://github.com/tamnd/51job-cli/releases) carries archives for Linux, macOS,
and Windows on amd64 and arm64, plus deb, rpm, and apk packages for Linux.
Download, unpack, put `job51` on your `PATH`, done. The `checksums.txt`
on each release is signed with keyless [cosign](https://docs.sigstore.dev/) if
you want to verify before running.

## With Go

```bash
go install github.com/tamnd/51job-cli/cmd/job51@latest
```

That puts `job51` in `$(go env GOPATH)/bin`, which is `~/go/bin` unless
you moved it. Make sure that directory is on your `PATH`.

## From source

```bash
git clone https://github.com/tamnd/51job-cli
cd 51job-cli
make build        # produces ./bin/job51
./bin/job51 version
```

## Container image

```bash
docker run --rm ghcr.io/tamnd/job51:latest --help
```

## Checking the install

```bash
job51 version
```

prints the version and exits.
