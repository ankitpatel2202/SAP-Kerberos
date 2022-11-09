FROM openjdk:11-jre-slim
MAINTAINER ssokerberos@kerbrose.org

#############################################
# ApacheDS installation
#############################################

ENV APACHEDS_VERSION 2.0.0.AM26
ENV APACHEDS_ARCH amd64

ENV APACHEDS_ARCHIVE apacheds-${APACHEDS_VERSION}-${APACHEDS_ARCH}.deb
ENV APACHEDS_USER apacheds
ENV APACHEDS_GROUP apacheds

ADD run.sh /run.sh

RUN apt-get update \
    && apt-get install -y ldap-utils \ 
    procps \
    curl \
    jq \
    && groupadd -g 1001 apacheds \
    && useradd -rm -d /home/apacheds -s /bin/bash -g 1001 -G sudo -u 1001 apacheds \
    && curl https://downloads.apache.org/directory/apacheds/dist/${APACHEDS_VERSION}/${APACHEDS_ARCHIVE} > ${APACHEDS_ARCHIVE} \
    && dpkg -i ${APACHEDS_ARCHIVE} \
    && rm ${APACHEDS_ARCHIVE} \
        && mkdir -p /bootstrap \
        && ln -s /var/lib/apacheds-${APACHEDS_VERSION}/default/partitions /data \
        && chmod +x /run.sh \
        && chown -R $APACHEDS_USER.$APACHEDS_GROUP /data \
        && chown -R $APACHEDS_USER.$APACHEDS_GROUP /var/lib/apacheds-${APACHEDS_VERSION}/default/partitions

VOLUME /data
VOLUME /bootstrap

EXPOSE 10389 10636 60088 60464 8080

#############################################
# ApacheDS bootstrap configuration
#############################################

ENV APACHEDS_INSTANCE default
ENV APACHEDS_BOOTSTRAP /bootstrap

ADD instance/krb5.conf /etc/
ADD instance/config.ldif /bootstrap/
RUN apt -y install python3-ldap3

#############################################
# ApacheDS wrapper command
#############################################
USER $APACHEDS_USER
ENTRYPOINT ["/run.sh"]

