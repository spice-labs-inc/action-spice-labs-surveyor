# action-spice-labs-cli-scan

A composite GitHub Action that runs the Spice Labs CLI scan against a directory of built files (e.g. Rust binaries), then uploads the resulting SBOM and OCI archive if present.

---

## Features

- Scans a directory using `spicelabs/spice-labs-cli:latest` Docker image
- Exports SPDX SBOM (`/tmp/sbom.spdx.json`)
- Uploads `.oci.tar` image files if present in scan target directory
- Compatible with any containerized build output

---

## Usage

```yaml
- name: Run Spice Labs CLI Scan
  uses: spice-labs-inc/action-spice-labs-cli-scan@main
  with:
    file_path: target/release/     # Optional, defaults to '.'
    spice_pass: ${{ secrets.SPICE_PASS_JWT }}
```
## Inputs
| Name         | Required | Default | Description                                   |
| ------------ | -------- | ------- | --------------------------------------------- |
| `file_path`  | No       | `.`     | Path to local files to scan (read-only mount) |
| `spice_pass` | Yes      | —       | Secret passphrase for running spice-labs-cli scan    |

## Output
This action uploads an artifact named spice-labs-cli-artifacts that may include:
  *  `/tmp/sbom.spdx.json`
  *  `*.oci.tar` files from the scan target path

Set your downstream workflows to retrieve this as needed.

## Requirements
  *  Docker must be available in the GitHub Actions runner
  *  spicelabs/spice-labs-cli:latest image must be publicly accessible
