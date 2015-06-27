# docker-chicagoboss

Dockerized Chicago Boss web framework

## What is it?

Docker container images that include [Chicago Boss](http://chicagoboss.org/)

## What can I use it for?

Use it to build and deploy web apps using Dockerized Chicago Boss.
Use it to develop an Erlang web app using Chicago Boss without worrying about any of the setup.

## Developing with an already existent Chicago Boss web app
###### (Scaffolding for the following exists in sample_app. It's meant to serve as an example, but IS NOT FUNCTIONAL, because I didn't want to include the 23MB of Chicago Boss web app in my Dockerfile repo.)

1.   Make a directory on your local machine that looks like the [sample_app](sample_app) directory in this repo

2.   Create a `supervisord.conf` file:

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

3.   Create a Dockerfile that looks like the following:

  ```
  FROM artburkart/docker-chicagoboss

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

4.   Move your existing project's code into the `app` directory

5.   Build the Dockerfile

  `docker build -t app-image .`

6.   Run the container

  `docker run -v /path/to/your/app:/app -p 8001:8001 -d --name app app-image`

7.   From here you can connect to the Docker container directly

  `docker exec -it app /bin/bash`

8.   Or you can edit the files in your `/path/to/your/app` directory and see that your changes are reflected in the container

## Developing an app from scratch
###### I assume we have a folder named after your project and it contains your Dockerfile and a folder named 'app' (your project's code)

1.   Follow steps 1-3 above

2.   Create an empty folder and name it `app`

3.   Build the Dockerfile

  `docker build -t app-image .`

4.   Initialize your app inside a throwaway container (this creates the base web server on your local machine without requiring Erlang or Chicago Boss to be installed on it)

  `docker run -v $(pwd)/app:/app --entrypoint /bin/bash app-image -c 'cd /ChicagoBoss && make app PROJECT=app'`

5.   Fire up a daemonized container named `app` with your web server running in it

  `docker run -v $(pwd)/app:/app -p 8001:8001 -d --name app app-image`

6.   If you go to [http://localhost:8001](http://localhost:8001), you should see a message that says "No routes matched the requested URL."

  - Cash money!

7.   Now that your container is running, you can open your favorite text editor and edit any file in the `app` folder on your local machine

  - All changes will be reflected in your container immediately, so you can develop just like you normally would if the web server was running directly on your local machine

8.   If, for some reason, you need to gain access to the container's terminal, you can run the following:

  `docker exec -it app bash`

9.   Unless you create a startup script to start your Docker container, you will need to turn it back on when you reboot your machine.

  `docker start app`

## If you're on OSX

1.   Since docker can't run natively in OSX, it runs inside a vagrant instance. I use Boot2Docker, but its setup is outside the scope of this README.

2.   If you have it running and all the above worked for you, then the last step is to run `boot2docker ip`

  - Let's say `boot2docker ip` returns 192.168.59.103, then your server is accessible from http://192.168.59.103:8001/







