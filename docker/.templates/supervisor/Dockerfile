# Install supervisor
RUN apt-get install -y supervisor
RUN mkdir -p /var/log/supervisor

COPY ./supervisor/supervisord.conf /etc/supervisor/supervisord.conf
