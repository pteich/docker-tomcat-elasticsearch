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

#download tomcat
RUN apk update && apk add curl \
    openssl \ 
    ca-certificates \ 
 	&& curl -o /opt/tomcat.tar.gz \
 	http://mirror2.shellbot.com/apache/tomcat/tomcat-7/v7.0.70/bin/apache-tomcat-7.0.70.tar.gz \
	&& tar -C /opt/ -xzf /opt/tomcat.tar.gz \
	&& rm /opt/tomcat.tar.gz \
	&& ln -s /opt/apache-tomcat-7.0.70 /opt/tomcat \
    && rm /opt/tomcat/bin/*.bat

ADD ./tomcat-users.xml /opt/tomcat/conf/

ENV CATALINA_HOME /opt/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH

RUN addgroup tomcat && adduser tomcat -S -G tomcat && chown -R tomcat:tomcat /opt/apache-tomcat-7.0.70

EXPOSE 8080

CMD ["gosu", "tomcat", "catalina.sh", "run"]