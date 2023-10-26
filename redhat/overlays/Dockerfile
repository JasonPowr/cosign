# Build stage
FROM registry.access.redhat.com/ubi9/go-toolset@sha256:7e49e105a854749d67e5e02fb4069b48bd50445098c780b7f808cc351dee2589 AS build-env

WORKDIR /cosign
COPY . .
USER root
RUN git config --global --add safe.directory /cosign
RUN make cross
RUN gzip cosign-darwin-amd64
RUN gzip cosign-linux-amd64
RUN gzip cosign-windows-amd64

# Install Cosign
FROM registry.access.redhat.com/ubi9/ubi-minimal@sha256:0dfa71a7ec2caf445e7ac6b7422ae67f3518960bd6dbf62a7b77fa7a6cfc02b1

LABEL description="Cosign is a container signing tool that leverages simple, secure, and auditable signatures based on simple primitives and best practices."
LABEL io.k8s.description="Cosign is a container signing tool that leverages simple, secure, and auditable signatures based on simple primitives and best practices."
LABEL io.k8s.display-name="Cosign container image for Red Hat Trusted Signer"
LABEL io.openshift.tags="cosign trusted-signer"
LABEL summary="Provides the cosign CLI binary for signing and verifying container images."
LABEL com.redhat.component="cosign"

COPY --from=build-env /cosign/cosign-darwin-amd64.gz /usr/local/bin/cosign-darwin-amd64.gz
COPY --from=build-env /cosign/cosign-linux-amd64.gz /usr/local/bin/cosign-linux-amd64.gz
COPY --from=build-env /cosign/cosign-windows-amd64.gz /usr/local/bin/cosign-windows-amd64.gz

RUN chown root:0 /usr/local/bin/cosign-darwin-amd64.gz && chmod g+wx /usr/local/bin/cosign-darwin-amd64.gz 
RUN chown root:0 /usr/local/bin/cosign-linux-amd64.gz && chmod g+wx /usr/local/bin/cosign-linux-amd64.gz
RUN chown root:0 /usr/local/bin/cosign-windows-amd64.gz && chmod g+wx /usr/local/bin/cosign-windows-amd64.gz

#Configure home directory
ENV HOME=/home
RUN chgrp -R 0 /${HOME} && chmod -R g=u /${HOME}

WORKDIR ${HOME}

# Makes sure the container stays running
CMD ["tail", "-f", "/dev/null"]