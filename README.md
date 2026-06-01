# Custom Keycloak Image Template

[Cookiecutter](https://www.cookiecutter.io/) template for building an optimized, custom Keycloak container image with your own extensions (SPI providers) and themes.

The generated project ships with:

- A multi-stage `Dockerfile` that runs `kc.sh build` to produce an optimized Keycloak image
- A `Makefile` with `build`, `push`, `tag`, and `clean` targets
- A GitHub Actions workflow that builds on every push/PR and publishes to **GitHub Container Registry (`ghcr.io`)** on pushes to `main` and on `v*` git tags

## Requirements

- [`cookiecutter`](https://www.cookiecutter.io/) >= 2.0 (`pipx install cookiecutter` or `pip install cookiecutter`)
- Docker (with `buildx`) for local image builds

## Usage

```sh
cookiecutter gh:vshn/custom-keycloak-image-template
# or from a local clone:
cookiecutter ./custom-keycloak-image-template
```

You will be prompted for:

| Variable           | Description                                                     | Example                |
|--------------------|-----------------------------------------------------------------|------------------------|
| `project_name`     | Human-readable project name                                     | `Acme Keycloak`        |
| `project_slug`     | Repo / image name (auto-derived from `project_name`)            | `acme-keycloak`        |
| `keycloak_version` | Tag of the base Keycloak image                                  | `26.0.7`               |

The generated directory is `<project_slug>/`. Drop your providers into `extensions/` and your themes into `themes/`, then `make build`.

See the generated project's `README.md` for full end-user instructions.
