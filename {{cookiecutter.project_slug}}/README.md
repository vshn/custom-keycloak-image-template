# {{ cookiecutter.project_name }}

Custom, optimized [Keycloak](https://www.keycloak.org/) container image based on `keycloak-managed:{{ cookiecutter.keycloak_version }}`, with project-specific SPI providers and themes baked in.

The image is built using the [Keycloak optimized build](https://www.keycloak.org/server/containers) pattern (`kc.sh build` at image-build time, then `start --optimized` at runtime) for fast startup.

## Layout

```
.
├── Dockerfile              # multi-stage optimized build
├── Makefile                # build / push / tag / clean
├── extensions/             # put your SPI provider .jar files here
├── themes/                 # put your theme directories here
└── .github/workflows/      # CI: build on PR, push to GHCR on main + tags
```

## Adding extensions

Drop your SPI provider JARs into `extensions/`. They are copied to `/opt/keycloak/providers/` and processed by `kc.sh build`.

```
extensions/
└── my-event-listener-1.0.0.jar
```

## Adding themes

Put each theme as a subdirectory under `themes/` (matching Keycloak's theme layout). They are copied to `/opt/keycloak/themes/`.

```
themes/
└── my-theme/
    ├── login/
    │   └── theme.properties
    └── account/
        └── theme.properties
```

## Building locally

```sh
make build                                                                 # local build, image: {{ cookiecutter.project_slug }}:{{ cookiecutter.keycloak_version }}
make build TAG=dev                                                         # override tag
make push  IMAGE=ghcr.io/<owner>/{{ cookiecutter.project_slug }} TAG=dev   # buildx + push (run `docker login` first)
```

Defaults:

| Variable           | Default                               | Notes                                          |
|--------------------|---------------------------------------|------------------------------------------------|
| `IMAGE`            | `{{ cookiecutter.project_slug }}`     | No registry. Override for `push`.              |
| `TAG`              | `{{ cookiecutter.keycloak_version }}` |                                                |
| `KEYCLOAK_VERSION` | `{{ cookiecutter.keycloak_version }}` | Passed to Docker as `--build-arg`.             |
| `PLATFORMS`        | `linux/amd64`                         | Multi-arch list for `make push`.               |

## CI / publishing

`.github/workflows/build.yml` runs on:

- **Pull requests to `main`** → build only (no push)
- **Pushes to `main`** → build & push to `ghcr.io/<owner>/<repo>` with tags `main`, `sha-<short>`, `latest`
- **Git tags `v*`** → build & push semver tags (`v1.2.3`, `1.2`)

Authentication uses the workflow-scoped `GITHUB_TOKEN`; no extra secrets are needed for GHCR.

Make the resulting GHCR package public via GitHub UI (Packages → settings → change visibility) if you want unauthenticated pulls.

## Running

```sh
docker run --rm -p 8080:8080 \
  -e KEYCLOAK_ADMIN=admin \
  -e KEYCLOAK_ADMIN_PASSWORD=admin \
  -e KC_DB=dev-mem \
  ghcr.io/<owner>/{{ cookiecutter.project_slug }}:{{ cookiecutter.keycloak_version }} \
  start-dev
```

For production, see [Keycloak server docs](https://www.keycloak.org/server/configuration).

## Updating the base Keycloak version

Edit the `KEYCLOAK_VERSION` default in the `Dockerfile` (and `Makefile` if you changed it there too). The CI workflow picks it up automatically from the Dockerfile `ARG` default.
