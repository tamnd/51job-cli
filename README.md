# job51

51job recruitment platform CLI

`job51` is a single pure-Go binary. It reads public 51job data
over plain HTTPS, shapes it into clean records, and prints output that pipes
into the rest of your tools. No API key, nothing to run alongside it.

The same package is also a [resource-URI driver](#use-it-as-a-resource-uri-driver),
so a host program like [ant](https://github.com/tamnd/ant) can address
51job as `51job://` URIs.

## Install

```bash
go install github.com/tamnd/51job-cli/cmd/job51@latest
```

Or grab a prebuilt binary from the [releases](https://github.com/tamnd/51job-cli/releases), or run
the container image:

```bash
docker run --rm ghcr.io/tamnd/job51:latest --help
```

## Usage

```bash
job51 page <path>                      # fetch one page as a record
job51 page <path> -o json              # as JSON, ready for jq
job51 page <path> --template '{{.Body}}'  # just the readable body text
job51 links <path>                     # the pages it links to, one per line
job51 --help                           # the whole command tree
```

Every command shares one output contract: `-o table|json|jsonl|csv|tsv|url|raw`,
`--fields` to pick columns, `--template` for a custom line, and `-n` to limit.
The default adapts to where output goes (a table on a terminal, JSONL in a
pipe), so the same command reads well by hand and parses cleanly downstream.

This is a fresh scaffold. It ships one example resource type, `page`, wired end
to end. Model the real 51job records in `51job/` and declare their
operations in `51job/domain.go`; each one becomes a command, an HTTP
route, and an MCP tool at once.

## Serve it

The same operations are available over HTTP and as an MCP tool set for agents,
with no extra code:

```bash
job51 serve --addr :7777    # GET /v1/page/<path>  returns NDJSON
job51 mcp                   # speak MCP over stdio
```

## Use it as a resource-URI driver

`job51` registers a `51job` domain the way a program registers a
database driver with `database/sql`. A host enables it with one blank import:

```go
import _ "github.com/tamnd/51job-cli/51job"
```

Then [ant](https://github.com/tamnd/ant) (or any program that links the package)
dereferences `51job://` URIs without knowing anything about 51job:

```bash
ant get 51job://page/<path>   # fetch the record
ant cat 51job://page/<path>   # just the body text
ant ls  51job://page/<path>   # the pages it links to, each addressable
ant url 51job://page/<path>   # the live https URL
```

## Development

```
cmd/job51/   thin main: hands cli.NewApp to kit.Run
cli/                 assembles the kit App from the 51job domain
51job/                the library: HTTP client, data models, and domain.go (the driver)
docs/                tago documentation site
```

```bash
make build      # ./bin/job51
make test       # go test ./...
make vet        # go vet ./...
```

## Releasing

Push a version tag and GitHub Actions runs GoReleaser, which builds the
archives, Linux packages, the multi-arch GHCR image, checksums, SBOMs, and a
cosign signature:

```bash
git tag v0.1.0
git push --tags
```

The Homebrew and Scoop steps self-disable until their tokens exist, so the first
release works with no extra secrets.

## License

Apache-2.0. See [LICENSE](LICENSE).
