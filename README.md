### Action for Spice Grinder Scan

A reusable GitHub Action to scan artifacts with [Spice Grinder](https://github.com/spice-labs-inc/grinder) and upload the results.

This action supports:
- Scanning local files
- Running Spice Grinder inside a secure container
- Uploading results using your Spice Pass

---

### Inputs

| Name              | Description                                                                 | Required | Default |
|-------------------|-----------------------------------------------------------------------------|----------|---------|
| `file_path`       | Path to local files to scan                                                 | true     | `""`    |
| `spice_pass`      | Spice pass for uploading results.  Get this from the Spice Labs Dashboard.  | true     | —       |

---

### Example Usage

```yaml
name: Scan with Spice Grinder

on:
  workflow_dispatch:

jobs:
  scan-and-upload:
    uses: spice-labs-inc/action-grinder-scan/.github/workflows/grinder-scan.yml@main
    with:
      file_path: "./deploy"
    secrets:
      SPICE_PASS: ${{ secrets.SPICE_PASS }}

```
