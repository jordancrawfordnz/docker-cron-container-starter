**This repo is archived.** It's not being maintained and may no longer work. I recommend calling `docker start` or `docker run` from your system's crontab instead.

---

# Cron Container Starter

This is a simple Docker image that starts local Docker containers based on a cron schedule.

**This is based off [sillelien/tutum-cron](https://github.com/sillelien/tutum-cron) which does the same job but inside Tutum.**

## Why would I want this?
This is quite useful to run backups or other scheduled jobs. Rather than having each of these jobs in a container with their own cron schedules, you can use this image to keep all schedule job configuration in one place.

## How does it work?
The Docker socket must be provided to the container as a volume at ``/var/run/docker.sock``. Through this, the container can issue a start command.

``fcron`` is used to schedule this API call.

## Building
Pull the repository and run: ``docker build -t jordancrawford/cron-container-starter .``.

## Running
To run you must:

 * provide ``/var/run/docker.sock`` as a volume
 * provide your cron schedules as environment variables. These must be of the format: ``CONTAINER_<container name to start>=<cron schedule>``
 
## Cron Schedules
See the [fcron documentation](http://fcron.free.fr/doc/en/fcrontab.5.html) or [crontab-generator](http://crontab-generator.org/) for help.

## Docker Compose
The easiest way to run the image is within Docker Compose. Below an example Docker Compose file that runs a test container every minute.

```
cron-container-starter:
   container_name: cron-container-starter
   image: jordancrawford/cron-container-starter
   restart: always
   # Don't take a large portion of memory or CPU shares.
   cpu_shares: 128
   mem_limit: 64m
   # Setup container start rules.
   environment:
    CONTAINER_test: '* * * * *'
   # Setup the Docker socket as a volume so the container can control Docker.
   volumes:
    - /var/run/docker.sock:/var/run/docker.sock
test:
   image: alpine
   container_name: test
   command: 'echo On Schedule'
```

## ARM builds
There are automated `armhf` and `arm64` builds on the `armhf` and `arm64` tags respectively.

For Raspberry Pi's, you'll want to use the `armhf` tag.
