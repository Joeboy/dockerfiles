Dockerfiles
===========

This repository contains config files for building docker.io images. The
general intention is to make it easier to move away from centralized services
like Facebook, Gmail et al. Docker allows you to (relatively) easily run
services in isolated containers on cheap hosts. So far the repo contains docker
files for:

* http://pump.io/ - federated social network
* http://roundcube.net - webmail client

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

    docker run -p 8000:80 roundcube

The -p argument means "redirect port 8000 on the host to port 80 in the container".

You should now be able to access roundcube at http://localhost:8000/roundcube/


Is that it?
-----------

That's all you need to get a working docker image. To be actually useful,
unfortunately there's some other stuff you'll need to do.


Configuration
#############

The supplied configurations are minimal and inadequate. You should make sure
you edit the supplied files to suit your needs before building your image. Pay
particular attention to passwords / encryption keys.

Once you've built your image, you can do further configuration as follows::

    > docker run -t -i roundcube /bin/bash
    > (make changes)
    > exit
    > docker ps -a
    ID                  IMAGE               COMMAND                CREATED             STATUS              PORTS
    793a80f9a1e4        roundcube:latest    /bin/bash              31 seconds ago      Exit 0
    > docker commit 793a80f9a1e4
    0633b03960c9
    docker tag 0633b03960c9 roundcube

This will create a new roundcube image with whatever changes you made. However
it will also remove the default command, so in future you'll need to specify
the command to run when running the image.

(There must surely be some better way of doing this that I haven't thought of?)


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

