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
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run Spice Grinder Scan
        uses: spice-labs-inc/grinder-scan-action@main
        with:
          file_path: "./deploy"
          spice_pass: ${{ secrets.SPICE_PASS }}
```
