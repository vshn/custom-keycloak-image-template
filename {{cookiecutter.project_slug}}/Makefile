PROJECT          ?= {{ cookiecutter.project_slug }}
KEYCLOAK_VERSION ?= {{ cookiecutter.keycloak_version }}

# Local image name. No registry by default so `make build` works in a fresh
# project. For pushing, override IMAGE with a fully-qualified registry path,
# e.g.:  make push IMAGE=ghcr.io/my-org/{{ cookiecutter.project_slug }}
IMAGE ?= $(PROJECT)
TAG   ?= $(KEYCLOAK_VERSION)

PLATFORMS ?= linux/amd64

.PHONY: build push clean help

help:
	@echo "Targets:"
	@echo "  build   Build local image: $(IMAGE):$(TAG)"
	@echo "  push    Build & push multi-arch image $(IMAGE):$(TAG) (buildx)"
	@echo "  clean   Remove local image $(IMAGE):$(TAG)"
	@echo ""
	@echo "Vars (override on command line):"
	@echo "  IMAGE=$(IMAGE)"
	@echo "  TAG=$(TAG)"
	@echo "  KEYCLOAK_VERSION=$(KEYCLOAK_VERSION)"
	@echo "  PLATFORMS=$(PLATFORMS)"

build:
	docker build \
		--build-arg KEYCLOAK_VERSION=$(KEYCLOAK_VERSION) \
		-t $(IMAGE):$(TAG) \
		.

push:
	docker buildx build \
		--platform $(PLATFORMS) \
		--build-arg KEYCLOAK_VERSION=$(KEYCLOAK_VERSION) \
		-t $(IMAGE):$(TAG) \
		--push \
		.

clean:
	-docker rmi $(IMAGE):$(TAG) 2>/dev/null || true
