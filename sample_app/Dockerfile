FROM artburkart/docker-chicagoboss
MAINTAINER Arthur Burkart <artburkart@gmail.com>

# INSTALL APP
COPY ./app /app

# INSTALL SUPERVISOR
RUN apt-get update && apt-get install -y supervisor && mkdir /var/log/supervisor_logs
COPY ./supervisor.conf /etc/supervisor/conf.d/supervisor.conf

EXPOSE 8001

CMD ["/usr/bin/supervisord"]
