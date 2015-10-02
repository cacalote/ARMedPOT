FROM armv7/armhf-ubuntu:14.04.3

MAINTAINER armedpot@norad.de

# update packet system

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update && apt-get -y upgrade && apt-get -y autoremove
RUN apt-get install -y supervisor iptables git build-essential autoconf libnetfilter-queue1 libnetfilter-queue-dev libtool libpq5 libpq-dev python-lxml python-mysqldb python-requests

# set timezone
RUN echo 'Europe/Berlin' > /etc/timezone && dpkg-reconfigure -f noninteractive tzdata

# honeytrap get/compile/install

#RUN cd /tmp && git clone git://git.carnivore.it/honeytrap.git && cd /tmp/honeytrap && autoreconf -fi && ./configure --with-stream-mon=nfq --with-logattacker --prefix=/opt/honeytrap && make && make install 
RUN cd /tmp && git clone https://github.com/armedpot/honeytrap.git && cd /tmp/honeytrap && autoreconf -fi && ./configure --with-stream-mon=nfq --with-logattacker --prefix=/opt/honeytrap && make && make install 
RUN cd /tmp && git clone https://github.com/rep/hpfeeds.git && cd hpfeeds && python setup.py install
RUN git clone https://github.com/armedpot/ewsposter.git /opt/ews

# setup user/dir/rights

RUN addgroup --gid 2001 armedpot && useradd --system --no-create-home --shell /sbin/nologin --uid 2001 --gid 2001 armedpot
RUN mkdir -p /armedpot/honeytrap/log /armedpot/honeytrap/attacks /armedpot/honeytrap/downloads
RUN mkdir -p /armedpot/ews/spool /armedpot/ews/log /armedpot/ews/json /armedpot/ews/cfg
RUN chmod 770 -R /armedpot && chown armedpot:armedpot -R /armedpot

# add configs to image

COPY honeytrap.conf /opt/honeytrap/etc/honeytrap/
COPY supervisord.conf /etc/supervisor/
COPY supervisor_honeytrap.conf /etc/supervisor/conf.d/honeytrap.conf

# cleaning

RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# starting honeytrap via supervisor

CMD ["/usr/bin/supervisord"]
