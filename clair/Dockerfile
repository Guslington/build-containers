ARG CLAIR_VERSION='2.0.8'
FROM quay.io/coreos/clair:v${CLAIR_VERSION} as clair

#FROM postgres:11.1-alpine
FROM arminc/clair-db:latest

ARG KLAR_VERSION='2.4.0'
ENV PGDATA /var/lib/postgresql/clair
ENV CLAIR_ADDR localhost

RUN apk add --no-cache git rpm xz ca-certificates bzr groff less python py-pip && \
  pip install awscli && \
  wget https://github.com/optiopay/klar/releases/download/v${KLAR_VERSION}/klar-${KLAR_VERSION}-linux-amd64 -O /bin/klar && \
  chmod +x /bin/klar && \
  adduser -h ${PGDATA} -u 1000 -D -s /bin/bash clair && \
  chown -R clair:clair ${PGDATA} && \
  chown clair:clair -R /var/run/postgresql && \
  touch /var/log/clair && \
  chown clair:clair /var/log/clair

COPY --from=clair /clair /bin/clair
COPY config/config.yaml /etc/clair/config.yaml
COPY scripts/start-clair.sh scripts/wait-for-update.sh /bin/

RUN sed -i 's/interval: 2h/interval: 0/g' /etc/clair/config.yaml

USER clair
#RUN sh /bin/start-clair.sh
#RUN sh /bin/wait-for-update.sh
