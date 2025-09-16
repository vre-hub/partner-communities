ARG BASEIMAGE=rucio/rucio-clients
ARG BASETAG=release-35.7.0

FROM ${BASEIMAGE}:${BASETAG}

USER root

# EGI trust anchors
RUN curl -Lo /etc/yum.repos.d/egi-trustanchors.repo https://repository.egi.eu/sw/production/cas/1/current/repo-files/egi-trustanchors.repo \
    && yum update -y \
    && dnf -y install ca-certificates ca-policy-egi-core

USER user

WORKDIR /home/user

# ET Rucio client default configuration
ADD --chown=user:user rucio.default.cfg.et /opt/user/rucio.default.cfg

ENTRYPOINT ["/bin/bash"]
