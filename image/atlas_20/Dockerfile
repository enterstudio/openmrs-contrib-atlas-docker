FROM openmrs/atlas:base
MAINTAINER Alexis DUQUE "alexis.duque@openmrs.org"

RUN useradd atlas -s /bin/bash -m -U &&\
    mkdir /opt/atlas && cd /opt &&\
    git clone https://github.com/openmrs/openmrs-contrib-atlas.git atlas &&\
    cd atlas &&\
    chown -R atlas:atlas /opt/atlas &&\
    chown -R www-data:www-data /opt/atlas/app/storage &&\
    chmod -R a+rwx /opt/atlas/app/storage

RUN mkdir /opt/auth && cd /opt &&\
    git clone https://github.com/alexisduque/atlas-mock-id.git auth
ADD auth.conf /etc/apache2/sites-available/auth.conf
RUN a2ensite auth.conf

RUN mkdir /etc/apache2/ssl
RUN a2enmod rewrite ssl

ADD supervisord.conf /etc/supervisor/conf.d/supervisord.conf
ADD mysql-setup.sh /tmp/mysql-setup.sh
ADD laravel-setup.sh /tmp/laravel-setup.sh
ADD auth-setup.sh /tmp/auth-setup.sh
ADD atlas.cfg /tmp/atlas.cfg
ADD atlas.sql /tmp/atlas.sql
ADD ssl.sh /tmp/ssl.sh
ADD 000-default.conf /tmp/000-default.conf
RUN chmod a+x /tmp/*.sh

ADD crontab /etc/crontab
RUN crontab /etc/crontab

RUN /bin/bash /tmp/ssl.sh
RUN /bin/sh /tmp/auth-setup.sh
RUN /bin/sh /tmp/mysql-setup.sh

RUN cd /opt/atlas && sudo -u atlas composer install

RUN /bin/sh /tmp/laravel-setup.sh

RUN sed -i -e"s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" /etc/mysql/my.cnf

EXPOSE 22 80 443 8888

CMD ["/usr/bin/supervisord"]