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
    subject: my-app                           # Required — label shown on the dashboard
    input: target/release/                    # Optional; defaults to '.'
    spice_pass: ${{ secrets.SPICE_PASS }}     # Required
```

---

### Optional: pin or override the CLI image

```yaml
- name: Build ADG (pinned CLI image)
  uses: spice-labs-inc/action-spice-labs-surveyor@v3
  with:
    subject: wasabi
    input: ${{ github.workspace }}/target
    spice_pass: ${{ secrets.SPICE_PASS }}
    cli_image: spicelabs/spice-labs-cli
    cli_image_tag: 2.0.0
```

---

### Inputs

| Name | Required | Default | Description |
|------|----------|---------|-------------|
| `subject` | Yes | *(none)* | Label identifying the system being surveyed (shown on the dashboard) |
| `input` | No | `.` | Path to local files to survey |
| `spice_pass` | Yes | *(none)* | Spice Pass (JWT) from your Spice Labs project |
| `cli_image` | No | `spicelabs/spice-labs-cli` | Docker image to run the CLI |
| `cli_image_tag` | No | `latest` | Tag of the Docker image to run the CLI |
| `log_level` | No | `info` | Log level: `debug` \| `info` \| `warn` \| `error` |

---

### Requirements

- Docker must be available in the GitHub Actions runner.
- `spicelabs/spice-labs-cli` image must be publicly accessible.
