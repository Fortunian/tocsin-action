# Tocsin check

Fail a pull request when a lockfile dependency has a known security advisory, and comment the upgrade.

[Tocsin](https://tocsin.sh) is a free feed of security advisories built for agents: 12 package ecosystems (npm, PyPI, Go, Packagist, crates.io, RubyGems, Maven, NuGet, Hex, Pub, Alpine, Debian) and the Node.js, PHP, Python, Deno, Bun and Go runtimes, with NVD severity, the CISA KEV exploited flag and EPSS. New advisories land within 10 minutes.

This action runs the Tocsin check on every pull request. It fails the job when a lockfile dependency has a known advisory, writes a job summary, and posts one PR comment (updated on each push) with the fixed version, the action to take and the upgrade command.

## Usage

Save this as `.github/workflows/tocsin.yml`:

```yaml
name: tocsin
on: [pull_request]
permissions:
  contents: read
  pull-requests: write
jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: Fortunian/tocsin-action@v1
```

## Inputs

| Input | Default | What it does |
|---|---|---|
| `min-severity` | `low` | The lowest severity that counts: low, medium, high, critical. |
| `fail-on` | `affected` | `affected` fails on any counted advisory, `exploited` only on a CISA KEV one, `never` reports only. |
| `fail-on-error` | `false` | Fail the step when the check could not run. |
| `comment` | `true` | Post or update one PR comment with the affected dependencies. |
| `working-directory` | `.` | The directory to scan for lockfiles. |
| `api-key` | | An optional Tocsin API key for higher rate limits (pass it from a secret). |
| `base-url` | `https://tocsin.sh` | Self-hosters point it at their own worker. |

Output `affected` is the number of affected dependencies at or above `min-severity`.

## Badge

```markdown
[![tocsin](https://github.com/<owner>/<repo>/actions/workflows/tocsin.yml/badge.svg)](https://github.com/<owner>/<repo>/actions/workflows/tocsin.yml)
```

## Source

This repository is a thin wrapper so the action can be listed on the GitHub Marketplace. The implementation is in [Fortunian/tocsin](https://github.com/Fortunian/tocsin/tree/main/action) (Apache-2.0). Issues go there.
