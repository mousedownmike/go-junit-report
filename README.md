# go-junit-report

go-junit-report is a tool that converts [`go test`] output to a JUnit compatible
XML report, suitable for use with applications such as [Jenkins].

[![Build status](https://github.com/jstemmer/go-junit-report/actions/workflows/main.yml/badge.svg)](https://github.com/jstemmer/go-junit-report/actions)
[![Go Reference](https://pkg.go.dev/badge/github.com/jstemmer/go-junit-report/v2.svg)](https://pkg.go.dev/github.com/jstemmer/go-junit-report/v2)
[![Go Report Card](https://goreportcard.com/badge/github.com/jstemmer/go-junit-report/v2)](https://goreportcard.com/report/github.com/jstemmer/go-junit-report/v2)

## Install from package (recommended)

Pre-built packages for Windows, macOS and Linux are found on the [Releases]
page.

## Install from source

Download and install the latest stable version from source by running:

```bash
go install github.com/jstemmer/go-junit-report/v2@latest
```

## Usage

By default, go-junit-report reads `go test -v` output generated by the standard
library [testing] package from `stdin` and writes a JUnit XML report to
`stdout`.

Go build and runtime errors are also supported, but this requires that `stderr`
is redirected to go-junit-report as well.

Typical use looks like this:

```bash
go test -v 2>&1 ./... | go-junit-report -set-exit-code > report.xml
```

### More examples

JSON produced by `go test -json` is supported by the `gojson` parser. Note that
`stderr` still needs to be redirected to go-junit-report in order for build
errors to be detected. For example:

```bash
go test -json 2>&1 ./... | go-junit-report -parser gojson > report.xml
```

Go benchmark output is also supported. The following example runs benchmarks for
the package in the current directory and uses the `-out` flag to write the
output to a file called `report.xml`.

```bash
go test -v -bench . -count 5 2>&1 | go-junit-report -out report.xml
```

The `-iocopy` flag copies `stdin` directly to `stdout`, which is helpful if you
want to see what was sent to go-junit-report. The following example reads test
input from a file called `tests.txt`, copies the input to `stdout` and writes
the output to a file called `report.xml`.

```bash
go-junit-report -in tests.txt -iocopy -out report.xml
```

### Flags

Run `go-junit-report -help` for a list of all supported flags.

| Flag                  | Description                                                                     |
| --------------------  | -----------                                                                     |
| `-in file`            | read go test log from `file`                                                    |
| `-iocopy`             | copy input to stdout; can only be used in conjunction with -out                 |
| `-no-xml-header`      | do not print xml header                                                         |
| `-out file`           | write XML report to `file`                                                      |
| `-package-name name`  | specify a default package name to use if output does not contain a package name |
| `-parser parser`      | specify the parser to use, available parsers are: `gotest` (default), `gojson`  |
| `-p key=value`        | add property to generated report; properties should be specified as `key=value` |
| `-set-exit-code`      | set exit code to 1 if tests failed                                              |
| `-subtest-mode`       | set subtest `mode`, modes are: `ignore-parent-results`, `exclude-parents`       |
| `-version`            | print version and exit                                                          |

## Go packages

The test output parser and JUnit XML report generator are also available as Go
packages. This can be helpful if you want to use the `go test` output parser or
create your own custom JUnit reports for example. See the package documentation
on pkg.go.dev for more information:

- [github.com/jstemmer/go-junit-report/v2/parser/gotest]
- [github.com/jstemmer/go-junit-report/v2/junit]

## Changelog

### v2.0.0

- Support for parsing `go test -json` output.
- Distinguish between build/runtime errors and test failures.
- JUnit report now includes output for all tests and benchmarks, and global output that doesn't belong to any test.
- Use full Go package name in generated report instead of only last path segment.
- Add support for reading skipped/failed benchmarks.
- Add `-subtest-mode` flag to exclude or ignore results of subtest parent tests.
- Add `-in` and `-out` flags for specifying input and output files respectively.
- Add `-iocopy` flag to copy stdin directly to stdout.
- Add `-prop` flags to set key/value properties in generated report.
- Add `-parser` flag to switch between regular `go test` (default) and `go test -json` parsing.
- Output in JUnit XML is written in `<![CDATA[]]>` tags for improved readability.
- Add `hostname`, `timestamp` and `id` attributes to JUnit XML.
- Improve accuracy of benchmark time calculation and update formatting in report.
- No longer strip leading whitespace from test output.
- The `formatter` and `parser` packages have been replaced with `junit` and `parser/gotest` packages respectively.
- Add support for parsing lines longer than 64KiB.
- The JUnit errors/failures attributes are now required fields.
- Drop support for parsing pre-Go1.13 test output.
- Deprecate `-go-version` flag.

## Contributing

See [CONTRIBUTING.md].

[`go test`]: https://pkg.go.dev/cmd/go#hdr-Test_packages
[Jenkins]: https://www.jenkins.io/
[github.com/jstemmer/go-junit-report/v2/parser/gotest]: https://pkg.go.dev/github.com/jstemmer/go-junit-report/v2/parser/gotest
[github.com/jstemmer/go-junit-report/v2/junit]: https://pkg.go.dev/github.com/jstemmer/go-junit-report/v2/junit
[Releases]: https://github.com/jstemmer/go-junit-report/releases
[testing]: https://pkg.go.dev/testing
[CONTRIBUTING.md]: https://github.com/jstemmer/go-junit-report/blob/master/CONTRIBUTING.md
