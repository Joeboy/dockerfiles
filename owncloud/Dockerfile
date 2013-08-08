FROM    ubuntu:12.04
MAINTAINER Joe Button <joe@joebutton.co.uk>

RUN    sed -i 's/main/main universe/' /etc/apt/sources.list
RUN    echo 'deb http://download.opensuse.org/repositories/isv:ownCloud:community/xUbuntu_12.04/ /' >> /etc/apt/sources.list.d/owncloud.list
RUN    apt-get -y install curl
RUN    curl http://download.opensuse.org/repositories/isv:/ownCloud:/community/xUbuntu_12.04/Release.key | apt-key add -
RUN    apt-get update && apt-get -y install owncloud

ENV     APACHE_RUN_USER www-data
ENV     APACHE_RUN_GROUP www-data
ENV     APACHE_LOG_DIR /var/log/apache2

RUN     echo "127.0.0.1     localhost" >> /etc/hosts
RUN     mkdir /owncloud
EXPOSE  80
CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
