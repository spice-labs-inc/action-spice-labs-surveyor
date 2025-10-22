# action-spice-labs-surveyor

A composite GitHub Action that runs the Spice Labs Surveyor CLI to create an Artifact Dependency Graph (ADG) from a directory of built files (e.g., Rust binaries, OCI layout (unpacked) Docker images, JAR files), then uploads the resulting ADG to the Spice Labs servers.

---

### Features

- Surveys a directory with the `spicelabs/spice-labs-cli` container and produces an ADG
- Uploads the generated ADG to your Spice Labs project using a Spice Pass (JWT)

---

### Usage

```yaml
      - name: Index and Upload ADG
        uses: spice-labs-inc/action-spice-labs-surveyor@v4
        with:
          spice_pass: "${{ secrets.SPICE_PASS }}"     # required
          input: "./target"                           # Optional; defaults to "."
          tag: "${{ github.event.repository.name }}"  # required

```

---

### Optional: pin or override the CLI image
```yaml
      - name: Index and Upload ADG (pinned cli image)
        uses: spice-labs-inc/action-spice-labs-surveyor@v4
        with:
          spice_pass: "${{ secrets.SPICE_PASS }}"     # required
          input: "./target"                           # Optional; defaults to "."
          tag: "${{ github.event.repository.name }}"  # required
          cli_image: spicelabs/spice-labs-cli
          cli_image_tag: 1.2.3
```

---

### Inputs
| Name         | Required | Default                           | Description                                     |
| ------------ | -------- | --------------------------------- | ----------------------------------------------- |
| `input`      | No       | `.`                               | Path to local files to survey (mounted read-only) |
| `spice_pass` | Yes      | *(none)*                          | Spice Pass (JWT) from your Spice Labs project   |
| `tag`        | Yes      | *(none)*                          | Tag to associate with the survey         |
| `cli_image`  | No       | `spicelabs/spice-labs-cli`        | Docker image to run the CLI                     |
| `cli_image_tag` | No    | `latest`                          | Tag of the Docker image to run the CLI          |

---

### Requirements

- Docker must be available in the GitHub Actions runner.
- `spicelabs/spice-labs-cli` image must be publicly accessible.
