# action-grinder-scan

A composite GitHub Action that runs the Spice Grinder scan against a directory of built files (e.g. Rust binaries), then uploads the resulting SBOM and OCI archive if present.

---

## Features

- Scans a directory using `spicelabs/grinder:latest` Docker image
- Exports SPDX SBOM (`/tmp/sbom.spdx.json`)
- Uploads `.oci.tar` image files if present in scan target directory
- Compatible with any containerized build output

---

## Usage

```yaml
- name: Run Spice Grinder Scan
  uses: spice-labs-inc/action-grinder-scan@main
  with:
    file_path: target/release/     # Optional, defaults to '.'
    spice_pass: ${{ secrets.GRINDER_JWT }}
```
## Inputs
| Name         | Required | Default | Description                                   |
| ------------ | -------- | ------- | --------------------------------------------- |
| `file_path`  | No       | `.`     | Path to local files to scan (read-only mount) |
| `spice_pass` | Yes      | —       | Secret passphrase for running grinder scan    |

## Output
This action uploads an artifact named grinder-artifacts that may include:
  *  `/tmp/sbom.spdx.json`
  *  `*.oci.tar` files from the scan target path

Set your downstream workflows to retrieve this as needed.

## Requirements
  *  Docker must be available in the GitHub Actions runner
  *  spicelabs/grinder:latest image must be publicly accessible
