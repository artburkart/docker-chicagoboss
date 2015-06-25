# docker-chicagoboss

Dockerized Chicago Boss web framework

## What is it?

Docker container images that include [Chicago Boss](http://chicagoboss.org/)

## What can I use it for?

Use it to build and deploy web apps using Dockerized Chicago Boss.
Use it to develop an Erlang web app using Chicago Boss without worrying about any of the setup.

## Usage
###### (Scaffolding for the following exists in sample_app. It's meant to serve as an example, but IS NOT FUNCTIONAL, because I didn't want to include the 23MB of Chicago Boss web app in my Dockerfile repo.)

1.   The idea is that you use this image as the foundation for another image that runs your app

1.   Create a `supervisord.conf` file:

  ```
  [supervisord]
  nodaemon=true

  [program:app]
  command=/app/init-dev.sh
  autostart=true
  autorestart=true
  priority=0
  stderr_logfile=/var/log/supervisor_logs/app.err.log
  stdout_logfile=/var/log/supervisor_logs/app.out.log
  ```
  <sub>NOTE: This is probably not ideal, but I'm not sure how else to keep the instance alive throughout development.</sub>

1.   Create a Dockerfile that looks like the following:

  ```
  FROM cb

  # INSTALL APP
  COPY ./app /app  # `make app PROJECT=app`

  WORKDIR /app

  EXPOSE 8001

  # INSTALL SUPERVISOR
  RUN apt-get update \
    && apt-get install -y supervisor \
    && mkdir /var/log/supervisor_logs
  COPY ./supervisor.conf /etc/supervisor/conf.d/supervisor.conf

  CMD ["/usr/bin/supervisord"]
  ```

1.   Build the Dockerfile

  `docker build -t app-image .`

1.   Run the container

  `docker run -v /path/to/your/app:/app -p 8001:8001 -d --name app app-image`

1.   From here you can connect to the Docker container directly

  `docker exec -it app /bin/bash`

1.   Or you can edit the files in your `/path/to/your/app` directory and see that your changes are reflected in the container


