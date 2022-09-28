FROM postgres:13-bullseye as builder

ENV PLV8_VERSION=3.0.0

RUN set -ex \
  && apt-get update \
  && apt-get install -y build-essential curl python ninja-build git wget postgresql-server-dev-${PG_MAJOR} libtinfo5 pkg-config clang binutils libc++-dev libc++abi-dev libc++1 libc++abi1 \
  && git clone https://github.com/plv8/plv8 \
  && cd plv8 \
  && git checkout v${PLV8_VERSION} \
  && make DOCKER=1 install \
  && strip /usr/lib/postgresql/${PG_MAJOR}/lib/plv8-${PLV8_VERSION}.so


FROM postgres:13-bullseye

COPY --from=builder /usr/lib/postgresql/${PG_MAJOR}/lib/plv8* /usr/lib/postgresql/${PG_MAJOR}/lib/
COPY --from=builder /usr/share/postgresql/${PG_MAJOR}/extension/plv8* /usr/share/postgresql/${PG_MAJOR}/extension/
COPY --from=builder /usr/share/postgresql/${PG_MAJOR}/extension/plls* /usr/share/postgresql/${PG_MAJOR}/extension/
COPY --from=builder /usr/share/postgresql/${PG_MAJOR}/extension/plcoffee* /usr/share/postgresql/${PG_MAJOR}/extension/
COPY --from=builder /usr/lib/postgresql/${PG_MAJOR}/lib/bitcode/plv8* /usr/lib/postgresql/${PG_MAJOR}/bitcode/

RUN set -ex \
  && apt-get update \
  && apt-get install -y libc++1 libtinfo5 libc++abi1 \
  && mkdir -p /var/log/postgres \
  && touch /var/log/postgres/log /var/log/postgres/log.csv \
  && chown -R postgres /var/log/postgres

USER postgres

RUN ln -fs /dev/stderr /var/log/postgres/log
