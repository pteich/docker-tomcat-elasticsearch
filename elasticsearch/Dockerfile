#oracle jre8 on alpine :)
FROM anapsix/alpine-java

#install gosu
ENV GOSU_VERSION 1.9
RUN set -x \
    && apk add --no-cache --virtual .gosu-deps \
        dpkg \
        gnupg \
        openssl \
    && dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')" \
    && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch" \
    && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \
    && rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu \
    && gosu nobody true \
    && apk del .gosu-deps

#download elastic
RUN apk update && apk add curl \ 
 	&& curl -o /opt/elastic.tar.gz \
 	https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.3.4/elasticsearch-2.3.4.tar.gz \
	&& tar -C /opt/ -xzf /opt/elastic.tar.gz \
	&& rm /opt/elastic.tar.gz \
	&& ln -s /opt/elasticsearch-2.3.4 /opt/elasticsearch


RUN addgroup elasticsearch && adduser elasticsearch -S -G elasticsearch

#pteich/elasticsearch
ENV ES_HOME=/opt/elasticsearch MAX_OPEN_FILES=65535 PATH=/opt/elasticsearch/bin:$PATH

ADD config /config

RUN rm /config/scripts/.gitignore

RUN ulimit -n $MAX_OPEN_FILES

# Expose ports
#   - 9200: HTTP
#   - 9300: transport
EXPOSE 9200
EXPOSE 9300

# mountable directory
VOLUME ["/config","/data"]

ADD run.sh /run.sh
RUN chmod +x /run.sh

ENTRYPOINT ["/run.sh"]

CMD ["elasticsearch", "-Des.network.host=0.0.0.0", "--default.path.conf=/config", "--default.path.home=/opt/elasticsearch"]