FROM    ubuntu:12.04
MAINTAINER Joe Button <joe@joebutton.co.uk>

RUN    sed -i 's/main/main universe/' /etc/apt/sources.list
RUN    echo 'deb http://download.opensuse.org/repositories/isv:ownCloud:community/xUbuntu_12.04/ /' >> /etc/apt/sources.list.d/owncloud.list
RUN    apt-get -y install curl
RUN    curl http://download.opensuse.org/repositories/isv:/ownCloud:/community/xUbuntu_12.04/Release.key | apt-key add -
RUN    apt-get update && apt-get -y install owncloud language-pack-en-base

ENV     APACHE_RUN_USER www-data
ENV     APACHE_RUN_GROUP www-data
ENV     APACHE_LOG_DIR /var/log/apache2

RUN     echo "127.0.0.1     localhost" >> /etc/hosts
RUN     echo "<? header('Location: /owncloud/'); ?>" > /var/www/index.php
RUN	sed -i s/^\ *upload_max_filesize.*/upload_max_filesize\ =\ 500\ MB\;/ /etc/php5/apache2/php.ini
RUN	sed -i s/^\ *post_max_filesize.*/post_max_filesize\ =\ 600\ MB\;/ /etc/php5/apache2/php.ini
RUN	sed -i s/^\ *max_execution_time.*/max_execution_time\ =\ 300\;/ /etc/php5/apache2/php.ini
RUN	sed -i s/^\ *max_input_time.*/max_input_time\ =\ 500\;/ /etc/php5/apache2/php.ini
EXPOSE  80
CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
