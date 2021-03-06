FROM ubuntu:14.04.2

ENV CONF_VERSION  5.8.6
ENV CONF_DATA_DIR /var/atlassian/application-data
ENV CONF_HOME_DIR $CONF_DATA_DIR/confluence
ENV CONF_INSTALL_DIR /opt/atlassian/confluence

### system update
RUN apt-get update
RUN apt-get upgrade -y

### additional package
RUN apt-get install -y curl wget git vim xmlstarlet

### ja_JP.UTF-8 Locale
RUN apt-get install -y language-pack-ja && \
    update-locale LANG=ja_JP.UTF-8

### Confluence Install
ADD https://www.atlassian.com/software/confluence/downloads/binary/atlassian-confluence-${CONF_VERSION}-x64.bin /confluence.bin
RUN chmod +x /confluence.bin
ADD response.varfile /
RUN /confluence.bin -q -varfile /response.varfile
RUN rm -f /confluence.bin

### MySQL JDBC Driver
ENV MYSQL_CONNECTOR_J_VERSION 5.1.36
RUN curl -L --silent http://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-${MYSQL_CONNECTOR_J_VERSION}.tar.gz | tar -xz --strip=1 -C /tmp && \
    cp /tmp/mysql-connector-java-${MYSQL_CONNECTOR_J_VERSION}-bin.jar ${CONF_INSTALL_DIR}/confluence/WEB-INF/lib && \
    cp /tmp/mysql-connector-java-${MYSQL_CONNECTOR_J_VERSION}-bin.jar ${CONF_INSTALL_DIR}/lib

### Change TZ
RUN ln -sf /usr/share/zoneinfo/Japan /etc/localtime
RUN dpkg-reconfigure --frontend noninteractive tzdata

### add Japanese Font
RUN apt-get install -y unzip
RUN cd /tmp && wget --quiet  http://ipafont.ipa.go.jp/ipafont/IPAfont00303.php && \
    unzip IPAfont00303.php && \
    mkdir /opt/atlassian/confluence/jre/lib/fonts/fallback && \
    cp IPAfont00303/*.ttf /opt/atlassian/confluence/jre/lib/fonts/fallback/ && \
    mkdir /usr/share/fonts/ && \
    cp IPAfont00303/*.ttf /usr/share/fonts/

ADD start /
RUN chmod +x /start

EXPOSE 8090
VOLUME $CONF_HOME_DIR
WORKDIR $CONF_INSTALL_DIR

CMD ["/start"]
