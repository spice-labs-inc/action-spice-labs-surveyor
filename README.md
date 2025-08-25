# action-spice-labs-cli-scan

A composite GitHub Action that runs the Spice Labs CLI to create an artifact dependency
graph from a directory of built files (e.g. Rust binaries, OCI-layout unpacked docker
continers, jar files, etc), then uploads the resulting ADG to the Spice Labs servers.

---

## Features

- Scans a directory using `spicelabs/spice-labs-cli` container and creates an artifact
  dependency graph.

---

## Usage

```yaml
- name: Build ADG
  uses: spice-labs-inc/action-spice-labs-cli-scan@v2
  with:
    file_path: target/release/ # Optional, defaults to '.'
    spice_pass: ${{ secrets.SPICE_PASS }}
    tag: 'my-scan-tag' # Optional
```

## Inputs

| Name         | Required | Default | Description                                    |
| ------------ | -------- | ------- | ---------------------------------------------- |
| `file_path`  | No       | `.`     | Path to local files to scan (used read-only).  |
| `spice_pass` | Yes      |         | Spice Pass (JWT) from your Spice Labs project. |
| `tag`        | Yes      |         | Tag for the scan.                              |

## Requirements

- Docker must be available in the GitHub Actions runner.
- `spicelabs/spice-labs-cli` image must be publicly accessible.
