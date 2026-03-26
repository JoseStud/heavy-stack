FROM ubuntu:24.04

ARG KOBOLDCPP_RELEASE=v1.110

RUN apt-get update \
    && apt-get install -y --no-install-recommends ca-certificates curl libgomp1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /opt/koboldcpp

# Build image contents from the selected KoboldCpp GitHub release artifact.
RUN set -eux; \
    arch="$(dpkg --print-architecture)"; \
    case "$arch" in \
      amd64) asset="koboldcpp-linux-x64" ;; \
      *) echo "Unsupported architecture: $arch"; exit 1 ;; \
    esac; \
    curl -fL "https://github.com/LostRuins/koboldcpp/releases/download/${KOBOLDCPP_RELEASE}/${asset}" -o /usr/local/bin/koboldcpp; \
    chmod +x /usr/local/bin/koboldcpp

EXPOSE 5001

ENTRYPOINT ["/usr/local/bin/koboldcpp"]
