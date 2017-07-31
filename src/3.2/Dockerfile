FROM openjdk:8-jre-alpine

RUN apk add --no-cache --quiet \
    bash \
    curl

ENV NEO4J_SHA256=%%NEO4J_SHA%% \
    NEO4J_TARBALL=%%NEO4J_TARBALL%%
ARG NEO4J_URI=%%NEO4J_DIST_SITE%%/%%NEO4J_TARBALL%%

COPY ./local-package/* /tmp/

RUN curl --fail --silent --show-error --location --remote-name ${NEO4J_URI} \
    && echo "${NEO4J_SHA256}  ${NEO4J_TARBALL}" | sha256sum -csw - \
    && tar --extract --file ${NEO4J_TARBALL} --directory /var/lib \
    && mv /var/lib/neo4j-* /var/lib/neo4j \
    && rm ${NEO4J_TARBALL} \
    && mv /var/lib/neo4j/data /data \
    && ln -s /data /var/lib/neo4j/data \
    && curl http://products.graphaware.com/download/framework-server-community/latest -Lo graphaware-server-community-all-3.2.1.51.jar \
    && curl http://products.graphaware.com/download/uuid/latest -Lo graphaware-uuid-3.2.1.51.14.jar \
    && mv graphaware*jar /var/lib/neo4j/plugins \
    && echo "com.graphaware.runtime.enabled=true" >> /var/lib/neo4j/conf/neo4j.conf \
    && echo "com.graphaware.module.UIDM.1=com.graphaware.module.uuid.UuidBootstrapper" >> /var/lib/neo4j/conf/neo4j.conf \
    && apk del curl

WORKDIR /var/lib/neo4j

VOLUME /data

COPY docker-entrypoint.sh /docker-entrypoint.sh

EXPOSE 7474 7473 7687

ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["neo4j"]
