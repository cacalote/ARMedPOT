FROM armv7/armhf-ubuntu:14.04.3

MAINTAINER armedpot@norad.de

# update packet system

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update && apt-get -y upgrade && apt-get -y autoremove
RUN apt-get install -y supervisor python git python-twisted python-pycryptopp mysql-server python-mysqldb python-lxml python-requests

# set timezone
RUN echo 'Europe/Berlin' > /etc/timezone && dpkg-reconfigure -f noninteractive tzdata

# kippo, hpfeed, ewsposter  get/compile/install

RUN git clone https://github.com/desaster/kippo.git /opt/kippo
RUN cd /tmp && git clone https://github.com/rep/hpfeeds.git && cd hpfeeds && python setup.py install
RUN git clone https://github.com/armedpot/ewsposter.git /opt/ews

# setup user/dir/rights

RUN addgroup --gid 2001 armedpot && useradd --system --no-create-home --shell /sbin/nologin --uid 2001 --gid 2001 armedpot
RUN mkdir -p /armedpot/kippo/log/tty/ /armedpot/kippo/downloads/ /armedpot/kippo/keys/ /armedpot/kippo/misc/ /var/run/kippo
RUN mkdir -p /armedpot/ews/spool /armedpot/ews/log /armedpot/ews/json /armedpot/ews/cfg
RUN chmod 770 -R /armedpot && chown armedpot:armedpot -R /armedpot && chown armedpot:armedpot /var/run/kippo && chown armedpot:armedpot -R /opt/kippo

# add configs to image

COPY kippo.cfg /opt/kippo/
COPY userdb.txt /armedpot/kippo/misc/userdb.txt
COPY setup.sql /tmp/
COPY supervisord.conf /etc/supervisor/
COPY supervisor_kippo.conf /etc/supervisor/conf.d/kippo.conf

# setup mysql

RUN sed -i 's/127.0.0.1/0.0.0.0/g' /etc/mysql/my.cnf
RUN service mysql start && /usr/bin/mysqladmin -u root password "gr8p4$w0rd" && /usr/bin/mysql -u root -p"gr8p4$w0rd" < /tmp/setup.sql

# cleaning

RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# starting kippo via supervisor

CMD ["/usr/bin/supervisord"]
