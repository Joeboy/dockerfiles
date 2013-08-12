Dockerfiles
===========

This repository contains config files for building docker.io images. These
files are intended as a starting point for creating your own customized images.
The general intention is to make it easier to move away from centralized
services like Facebook, Gmail et al. Docker allows you to (relatively) easily
run services in isolated containers on cheap hosts. So far the repo contains
docker files for:

* http://owncloud.org - online file storage  / dropbox replacement + calendar
  + contacts + tasks etc
* http://roundcube.net - webmail client
* http://pump.io/ - federated social network

Feel free to raise a ticket if there's anything else you'd particularly like
to see dockerized.

NB this is a work in progress, don't expect things to be complete, correct or
sensible.


Installing Docker
-----------------

See http://www.docker.io/gettingstarted/


Downloading the repo
--------------------

::

    git clone https://github.com/Joeboy/dockerfiles.git


Building an image
-----------------

::

    cd dockerfiles/roundcube (or whatever)

(Edit the files as necessary)

::

    docker build -t roundcube .

Expect to wait maybe half an hour or so while the build completes.


Running your image
------------------

::

    docker run -p 8000:80 -d roundcube

The -p argument means "redirect port 8000 on the host to port 80 in the
container". The -d means "run detached", ie. as a daemon in the background.

You should now be able to access roundcube at http://localhost:8000/roundcube/


Is that it?
-----------

That's all you need to get a working docker image. To be actually useful,
unfortunately there's some other stuff you'll need to do.

Persistent data
###############

Docker's design is such that if you stop an image and run it again, your new
container will lose any changes that happened during the previous run. That's
potentially a big problem. To avoid that happening, make folders on your host
for anything you don't want to lose between runs and tell docker to bind mount
them when you run your images. For example, to persist owncloud's config and
data directories, do something like:

::

    mkdir -p /dockerdata/owncloud/config /dockerdata/owncloud/data
    docker run -v /dockerdata/owncloud/config:/var/www/owncloud/config -v /dockerdata/owncloud/data:/var/www/owncloud/data -p 8001:80 -d owncloud



Configuration
#############

The supplied configurations are minimal and inadequate. You should make sure
you edit the supplied files to suit your needs before building your image. Pay
particular attention to passwords / encryption keys.


Hosting your docker image
#########################

You probably want to build / run your image on a server rather than your local
PC. Fortunately hosting requirements are not too onerous. You could either host
it on a dedicated box or a cheap VPS. See http://www.lowendbox.com/ for deals on
VPSes. You should be OK with any x64 host that supports Ubuntu 13.04, although I
can't guarantee anything. You should easily be able to find something beefy
enough to handle a few docker containers in the "few bucks a month" range.

See also:

* http://kencochrane.net/blog/2013/06/running-docker-on-digital-ocean/
* http://kencochrane.net/blog/2013/05/running-docker-on-rackspace-cloud/


Proxying from the host to the docker container
##############################################

If you're running more than one containerized web app, you'll want to proxy
requests to the host to the container. One fairly straightforward way of doing
this is with nginx:

::

    apt-get install nginx

You'll want to amend ``/etc/nginx/sites-available/default`` with something like
this:

::

    server {
        listen      80;
        location /roundcube/ {
            proxy_pass http://127.0.0.1:8000;
        }
    }

That'll pass requests to the host on port 80 to port 8000, which is hopefully
the port we're passing onto the roundcube container (see the -p flag to
``docker run`` above).



SSL 
###

You should create an SSL certificate and ensure that your docker images are
being served over SSL. You can create a self-signed certificate using openssl,
although web browsers will present scary warnings when people visit your site.
Unfortunately you'll need to pay for a certificate from a commercial
Certificate Authority if that's not acceptable.

TODO: find a friendly link

