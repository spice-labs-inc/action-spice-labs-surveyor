# action-spice-labs-surveyor

A composite GitHub Action that runs the Spice Labs Surveyor CLI to create an Artifact Dependency Graph (ADG) from a directory of built files (e.g., Rust binaries, OCI layout (unpacked) Docker images, JAR files), then uploads the resulting ADG to the Spice Labs servers.

It also supports **image mode**: survey an image from an OCI/Docker registry directly, no local files required. The action runs `spice survey inventory` with a `docker://` image, which pulls the image and surveys its contents.

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
    subject: my-app                           # Required for local files: label shown on the dashboard
    input: target/release/                    # Optional; defaults to '.'
    spice_pass: ${{ secrets.SPICE_PASS }}     # Required
```

When `image` is set, the action surveys that image instead of local files (the `input` directory is ignored). It runs `spice survey inventory [subject] docker://<image>`; an `image` that already starts with `docker://` or `oci://` is passed as is. The CLI expands bare references to their fully-qualified form (`nginx` → `docker.io/library/nginx:latest`, `user/app:tag` → `docker.io/user/app:tag`), pulls the image with the `oras` baked into the CLI's container image, and surveys its contents.

Without `subject`, the survey is labeled with the image name without its tag or digest (`ghcr.io/org/app:v1` → `ghcr.io/org/app`, `nginx:1.27` → `nginx`), so every version of the image lands on one subject.


```yaml
- name: Build ADG
  uses: spice-labs-inc/action-spice-labs-surveyor@v5
  with:
    subject: imagename                        # Optional: defaults to the image name without its tag
    image: mycompany/imagename:tag            # Required
    spice_pass: ${{ secrets.SPICE_PASS }}     # Required
```

---

### Optional: pin or override the CLI image

By default the action runs the `latest` CLI image. To pin the CLI version, name the image with its tag in `cli_image`:

```yaml
- name: Build ADG (pinned CLI version)
  uses: spice-labs-inc/action-spice-labs-surveyor@v5
  with:
    subject: wasabi
    input: ${{ github.workspace }}/target
    spice_pass: ${{ secrets.SPICE_PASS }}
    cli_image: spicelabs/spice-labs-cli:1.9.3
```

To run the CLI from another image, such as a copy in your own registry, name that image the same way:

```yaml
    cli_image: registry.example.com/mirror/spice-labs-cli:1.9.3
```

`cli_image_tag` adds a tag to `cli_image` when it has none; a tag or digest in `cli_image` wins, with a warning.

---

For a private registry, log into it in a prior step (e.g. `docker/login-action`, which writes the runner's `~/.docker/config.json`); the CLI mounts that docker config read-only into its container, so any registry the runner is already logged into just works. A login kept in a credential helper is read for that one registry. With no docker config on the runner, the pull is anonymous.

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
| `subject` | For local files | *(none)* | Label identifying the system being surveyed (shown on the dashboard). For an image it defaults to the image name without its tag or digest |
| `input` | No | `.` | Path to local files to survey (ignored when `image` is set) |
| `image` | No | *(none)* | OCI/Docker registry image to survey instead of local files, e.g. `ghcr.io/org/app:v1` (bare names like `nginx` are expanded by the CLI). Private registries work via the runner's docker login (e.g. `docker/login-action`) |
| `spice_pass` | Yes | *(none)* | Spice Pass (JWT) from your Spice Labs project, passed from a secret. When it is empty (for example, the secret is missing or this workflow cannot read it), the action stops at its first step with an error naming `spice_pass`. See [Generate a Spice Pass](https://docs.spicelabs.io/docs/topographer/credentials/#generate-a-spice-pass) |
| `cli_image` | No | `spicelabs/spice-labs-cli` | Docker image to run the CLI, with a tag or digest to pin it, e.g. `spicelabs/spice-labs-cli:1.9.3` |
| `cli_image_tag` | No | *(none)* | A tag added to `cli_image` when it has no tag or digest, e.g. `1.9.3` |
| `log_level` | No | `info` | Log level: `debug` \| `info` \| `warn` \| `error` |

---

### Requirements

- Docker must be available in the GitHub Actions runner.
- `spicelabs/spice-labs-cli` image must be publicly accessible.
- Image mode requires a Surveyor CLI release with `docker://` inputs for `spice survey inventory`. Older CLI images only have `spice survey image`.
