FROM    ubuntu:12.10
MAINTAINER Joe Button <joe@joebutton.co.uk>
# NB: You should change your db passwords in the selections file

RUN     echo "127.0.0.1  localhost" > /etc/hosts
RUN     sed -i 's/main/main universe/' /etc/apt/sources.list
RUN     dpkg-divert --local --rename --add /sbin/initctl && ln -s /bin/true /sbin/initctl
RUN     apt-get update && apt-get upgrade && apt-get -y install debconf-utils
ADD     ./selections /selections
RUN     cat /selections | debconf-set-selections && rm /selections
ENV     APACHE_RUN_USER www-data
ENV     APACHE_RUN_GROUP www-data
ENV     APACHE_LOG_DIR /var/log/apache2
RUN     apt-get -y install mysql-server mysql-server-5.5 python-pip apache2-mpm-prefork
RUN     mysqld & sleep 5; apt-get -y install roundcube roundcube-plugins #roundcube-plugins-extra
RUN     pip install supervisor && echo_supervisord_conf > /etc/supervisord.conf
ADD     ./Supervisorfile /etc/
RUN     echo "[include]\nfiles = /etc/Supervisorfile\n" >> /etc/supervisord.conf
RUN     ln -s /var/lib/roundcube /var/www/roundcube
RUN     sed -i 's/ar_SA/en_US/' /var/lib/roundcube/config/main.inc.php
EXPOSE  80
CMD ["/usr/local/bin/supervisord", "-n", "-c", "/etc/supervisord.conf"]

