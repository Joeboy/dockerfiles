FROM    ubuntu:12.04
MAINTAINER Joe Button <joe@joebutton.co.uk>

RUN     apt-key adv --keyserver keyserver.ubuntu.com --recv 7F0CEB10
RUN     echo "deb http://archive.ubuntu.com/ubuntu precise universe" | tee -a /etc/apt/sources.list
RUN     echo "deb http://downloads-distro.mongodb.org/repo/ubuntu-upstart dist 10gen" | tee -a /etc/apt/sources.list.d/10gen.list
RUN     apt-get -y install python-software-properties
RUN     add-apt-repository ppa:chris-lea/node.js
RUN     apt-get update
RUN     apt-get -y install nodejs python make g++ mongodb-10gen python-pip git
RUN     pip install supervisor
RUN     npm install databank-mongodb
RUN     mkdir /pumpio && git clone https://github.com/e14n/pump.io.git /pumpio/src
RUN     cd /pumpio/src && git checkout 2fc3518a5282da8144a5ce6e090ea42f392bb281 && npm install
RUN     echo_supervisord_conf > /etc/supervisord.conf
ADD     ./Supervisorfile /pumpio/
ADD     ./pump.io.json /etc/
RUN     echo "[include]\nfiles = /pumpio/Supervisorfile\n" >> /etc/supervisord.conf
RUN     echo "127.0.0.1     localhost" >> /etc/hosts
RUN     mkdir -p /data/db
EXPOSE  80
CMD ["/usr/local/bin/supervisord", "-n", "-c", "/etc/supervisord.conf"] 
