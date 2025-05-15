# 🔥 Grinder Scan GitHub Action

This composite GitHub Action runs the [Spice Grinder](https://github.com/spice-labs/grinder) container scanner against `.oci.tar` images and uploads any resulting SBOM and OCI artifacts.

## ✅ Features

- Runs `spicelabs/grinder:latest` scan in a Docker container
- Uploads:
  - SBOM: `/tmp/sbom.spdx.json` (if found)
  - OCI tarballs from the specified directory (if found)
- Will **not fail** if files are missing — it will warn instead

## 📦 Usage

```yaml
- name: Run Grinder Scan
  uses: spice-labs-inc/action-grinder-scan@main
  with:
    spice_pass: ${{ secrets.GRINDER_JWT }}
    file_path: ./target/release
```

## Inputs
| Name         | Required | Default | Description                                     |
| ------------ | -------- | ------- | ----------------------------------------------- |
| `spice_pass` | ✅ Yes    | –       | Secret used to authenticate the Grinder scanner |
| `file_path`  | ❌ No     | `.`     | Directory where `.oci.tar` files are located    |

## 📤 Upload Behavior
The action will upload:
 - SBOM file as artifact named sbom (if found at /tmp/sbom.spdx.json)
 - OCI images as artifact named oci-images (if *.oci.tar files are found in file_path)
If no files are found, uploads are skipped with a warning — the job will not fail.

## 🐳 Example: Save Docker Image for Scanning
Ensure your Docker image is saved as .oci.tar in the correct path:
```bash
mkdir -p target/release
docker save ghcr.io/your-org/your-image:latest > target/release/your-image.oci.tar
```

## 🔒 Permissions
Ensure your calling workflow includes:
```yaml
permissions:
  contents: write
```
