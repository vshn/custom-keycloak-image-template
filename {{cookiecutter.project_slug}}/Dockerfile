# syntax=docker/dockerfile:1

ARG KEYCLOAK_VERSION={{ cookiecutter.keycloak_version }}
ARG BASE_IMAGE=docker-registry.inventage.com:10121/keycloak-competence-center/keycloak-managed

FROM ${BASE_IMAGE}:${KEYCLOAK_VERSION} AS builder

COPY --chown=1000:0 extensions/ /opt/keycloak/providers/
COPY --chown=1000:0 themes/     /opt/keycloak/themes/

RUN /opt/keycloak/bin/kc.sh build -- --spi-events-listener-jboss-logging-success-level=info --spi-events-listener-jboss-logging-error-level=warn

FROM ${BASE_IMAGE}:${KEYCLOAK_VERSION}

COPY --from=builder /opt/keycloak/ /opt/keycloak/

ENTRYPOINT ["/opt/keycloak/bin/kc-with-setup.sh"]
CMD ["start", "--optimized"]
