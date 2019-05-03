FROM openjdk:8-jre-alpine

RUN addgroup -S neo4j && adduser -S -H -h /var/lib/neo4j -G neo4j neo4j

ENV NEO4J_SHA256=568c711a5f7addb54be92bd8504c1ebf5e34d0dd4fb617a8aef2d5b3549f2f4b \
    NEO4J_TARBALL=neo4j-enterprise-3.5.0-unix.tar.gz \
    NEO4J_EDITION=enterprise
ARG NEO4J_URI=http://dist.neo4j.org/neo4j-enterprise-3.5.0-unix.tar.gz

COPY ./local-package/* /tmp/
COPY ./neo4j-enterprise-3.5.0-unix.tar.gz /${NEO4J_TARBALL}

RUN apk add --no-cache --quiet \
    bash \
    curl \
    tini \
    su-exec \
#    && curl --fail --silent --show-error --location --remote-name ${NEO4J_URI} \
    && echo "${NEO4J_SHA256}  ${NEO4J_TARBALL}" | sha256sum -csw - \
    && tar --extract --file ${NEO4J_TARBALL} --directory /var/lib \
    && mv /var/lib/neo4j-* /var/lib/neo4j \
    && rm ${NEO4J_TARBALL} \
    && mv /var/lib/neo4j/data /data \
    && chown -R neo4j:neo4j /data \
    && chmod -R 777 /data \
    && chown -R neo4j:neo4j /var/lib/neo4j \
    && chmod -R 777 /var/lib/neo4j \
    && ln -s /data /var/lib/neo4j/data \
    && apk del curl

ENV PATH /var/lib/neo4j/bin:$PATH

COPY ./plugins/apoc-3.5.0.1-all.jar /var/lib/neo4j/plugins/apoc-3.5.0.1-all.jar
COPY ./plugins/graph-algorithms-algo-3.5.0.1.jar /var/lib/neo4j/plugins/graph-algorithms-algo-3.5.0.1.jar
COPY ./conf/neo4j.conf /var/lib/neo4j/conf/neo4j.conf

WORKDIR /var/lib/neo4j

VOLUME /data

COPY docker-entrypoint.sh /docker-entrypoint.sh

EXPOSE 7474 7473 7687
RUN chmod +x /docker-entrypoint.sh

ENTRYPOINT ["/sbin/tini", "-g", "--", "/docker-entrypoint.sh"]
CMD ["neo4j"]

#########################################################
#
# 1. 构建Docker Image 文件：docker build -t idu/neo4j:3.5.0-enterprise .
# 2. 创建Docker Container
#    docker run \
#    -d \
#    --restart=always \
#    --publish=7474:7474 --publish=7687:7687 \
#    --env=NEO4J_AUTH=none \
#    --env=NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
#    --volume=/data/neo4j/data:/data \
#    --name neo4j-enterprise \
#    idu/neo4j:3.5.0-enterprise
#
#
#########################################################
#
# 如何使用apoc 从neo4j导出数据，gephi导入数据
# https://blog.csdn.net/the_conquer_zzy/article/details/75665617