# action-spice-labs-surveyor

A composite GitHub Action that runs the Spice Labs Surveyor CLI to create an Artifact Dependency Graph (ADG) from a directory of built files (e.g., Rust binaries, OCI layout (unpacked) Docker images, JAR files), then uploads the resulting ADG to the Spice Labs servers.

---

### Features

- Surveys a directory with the `spicelabs/spice-labs-cli` container and produces an ADG
- Uploads the generated ADG to your Spice Labs project using a Spice Pass (JWT)

---

### Usage

```yaml
- name: Build ADG
  uses: spice-labs-inc/action-spice-labs-surveyor@v3
  with:
    file_path: target/release/            # Optional; defaults to '.'
    spice_pass: ${{ secrets.SPICE_PASS }} # Required
    tag: 'my-survey-tag'                  # Required

```

---

### Optional: pin or override the CLI image
```yaml
- name: Build ADG (pinned CLI image)
  uses: spice-labs-inc/action-spice-labs-surveyor@v3
  with:
    file_path: ${{ github.workspace }}/target
    spice_pass: ${{ secrets.SPICE_PASS }}
    tag: wasabi
    cli_image: spicelabs/spice-labs-cli:latest
```

---

### Inputs
| Name         | Required | Default                           | Description                                     |
| ------------ | -------- | --------------------------------- | ----------------------------------------------- |
| `file_path`  | No       | `.`                               | Path to local files to survey (mounted read-only) |
| `spice_pass` | Yes      | *(none)*                          | Spice Pass (JWT) from your Spice Labs project   |
| `tag`        | Yes      | *(none)*                          | Tag to associate with the survey         |
| `cli_image`  | No       | `spicelabs/spice-labs-cli:latest` | Docker image to run the CLI                     |

---

### Requirements

- Docker must be available in the GitHub Actions runner.
- `spicelabs/spice-labs-cli` image must be publicly accessible.
