FROM    ubuntu:14.04
MAINTAINER Joe Button <joe@joebutton.co.uk>

ENV DEBIAN_FRONTEND noninteractive
RUN     apt-get update
RUN     apt-get -y upgrade
RUN     apt-get -y install curl
RUN     echo 'deb http://download.opensuse.org/repositories/isv:/ownCloud:/community/xUbuntu_14.04/ /' > /etc/apt/sources.list.d/owncloud.list
RUN     curl http://download.opensuse.org/repositories/isv:/ownCloud:/community/xUbuntu_14.04/Release.key | apt-key add -
RUN     apt-get update
RUN     apt-get install -y apache2 php5 php5-ldap libapache2-mod-rpaf php-apc

ENV     APACHE_RUN_USER www-data
ENV     APACHE_RUN_GROUP www-data
ENV     APACHE_LOG_DIR /var/log/apache2
RUN     service apache2 start && apt-get install -y owncloud && service apache2 stop

RUN     echo "127.0.0.1     localhost" >> /etc/hosts
RUN     echo "<? header('Location: /owncloud/'); ?>" > /var/www/index.php
RUN     sed -i s/^\ *upload_max_filesize.*/upload_max_filesize\ =\ 500\ MB\;/ /etc/php5/apache2/php.ini
RUN     sed -i s/^\ *post_max_filesize.*/post_max_filesize\ =\ 600\ MB\;/ /etc/php5/apache2/php.ini
RUN     sed -i s/^\ *max_execution_time.*/max_execution_time\ =\ 300\;/ /etc/php5/apache2/php.ini
RUN     sed -i s/^\ *max_input_time.*/max_input_time\ =\ 500\;/ /etc/php5/apache2/php.ini
EXPOSE  80
CMD     ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
