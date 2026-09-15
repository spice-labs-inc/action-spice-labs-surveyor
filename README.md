# action-spice-labs-surveyor

A composite GitHub Action that runs the Spice Labs Surveyor CLI to create an Artifact Dependency Graph (ADG) from a directory of built files (e.g., Rust binaries, OCI layout (unpacked) Docker images, JAR files), then uploads the resulting ADG to the Spice Labs servers.

It also supports **image mode**: survey an image from an OCI/Docker registry directly, no local files required. The CLI's `spice survey image` command pulls the image and surveys the pulled OCI layout.

---

### Features

- Surveys a directory with the `spicelabs/spice-labs-cli` container and produces an ADG
- Uploads the generated ADG to your Spice Labs project using a Spice Pass (JWT)
- Image mode: fetches an image from a public or private OCI/Docker registry using ORAS and surveys it

---

### Usage

Artifact files:

```yaml
- name: Build ADG
  uses: spice-labs-inc/action-spice-labs-surveyor@v5
  with:
    subject: my-app                           # Required — label shown on the dashboard
    input: target/release/                    # Optional; defaults to '.'
    spice_pass: ${{ secrets.SPICE_PASS }}     # Required
```

When `image` is set, the action surveys that image instead of local files (the `input` directory is ignored). The CLI expands bare references to their fully-qualified form (`nginx` → `docker.io/library/nginx:latest`, `user/app:tag` → `docker.io/user/app:tag`), pulls the image into an OCI layout with the `oras` baked into the CLI's container image, and surveys the layer blobs.


```yaml
- name: Build ADG
  uses: spice-labs-inc/action-spice-labs-surveyor@v5
  with:
    subject: imagename                        # Optional: defaults to image
    image: mycompany/imagename:tag            # Required
    spice_pass: ${{ secrets.SPICE_PASS }}     # Required
```

---

### Optional: pin or override the CLI image

```yaml
- name: Build ADG (pinned CLI image)
  uses: spice-labs-inc/action-spice-labs-surveyor@v5
  with:
    subject: wasabi
    input: ${{ github.workspace }}/target
    spice_pass: ${{ secrets.SPICE_PASS }}
    cli_image: spicelabs/spice-labs-cli:2.0.0   # tag embedded — the wrapper uses SPICE_IMAGE verbatim
```

---

For a private registry, log into it in a prior step (e.g. `docker/login-action`, which writes the runner's `~/.docker/config.json`); the action mounts that docker config read-only into the CLI container, so any registry the runner is already logged into just works. With no docker config on the runner, the pull is anonymous.

```yaml
- name: Log in to GHCR
  uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}

- name: Build ADG from a private registry image
  uses: spice-labs-inc/action-spice-labs-surveyor@v5
  with:
    subject: my-app
    image: ghcr.io/org/my-app:v1.2.3
    spice_pass: ${{ secrets.SPICE_PASS }}
```

---

### Inputs

| Name | Required | Default | Description |
|------|----------|---------|-------------|
| `subject` | Yes | *(none)* | Label identifying the system being surveyed (shown on the dashboard) |
| `input` | No | `.` | Path to local files to survey (ignored when `image` is set) |
| `image` | No | *(none)* | OCI/Docker registry image to survey instead of local files, e.g. `ghcr.io/org/app:v1` (bare names like `nginx` are expanded by the CLI). Private registries work via the runner's docker login (e.g. `docker/login-action`) |
| `spice_pass` | Yes | *(none)* | Spice Pass (JWT) from your Spice Labs project |
| `cli_image` | No | `spicelabs/spice-labs-cli` | Docker image to run the CLI |
| `cli_image_tag` | No | `latest` | Tag of the Docker image to run the CLI |
| `log_level` | No | `info` | Log level: `debug` \| `info` \| `warn` \| `error` |

---

### Requirements

- Docker must be available in the GitHub Actions runner.
- `spicelabs/spice-labs-cli` image must be publicly accessible.
- Image mode requires spice-labs-cli **v1.7.0+** (the `oras` binary and `spice survey image` command are baked into the CLI image since then).
