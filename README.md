# IK Docker for Drupal
This repo is a collection of Docker files which deploy a Docker instance for PHP and Apache to host the Drupal site from your local filesystem. The Docker instance syncs with the filesystem and the Drupal site connects to the host database so mysql must be running on the host machine. I've also added SSL.

I created these files because I am getting tired of MAMP. Docker provides flexibility to test specific versions of PHP and Apache by simply editing the docker-compose and image Dockerfiles.
## Install
Unpack these files in the root directory of the Drupal 8 file system.
[Docker for Mac](https://docs.docker.com/docker-for-mac/) must be installed and running on the host machine.
## Configure
In `docker-compose.yml` set your desired URL for the local instance in the build:args:url setting. You may also change the ports. The port configuration is set like `host-machine`:`docker-machine`.

You must also edit your Drupal `.env` file and modify your database connection. Since the docker machine must connect back to the host MySQL instance you will need it to connect to `host.docker.internal`. I've also set this in my /etc/hosts file so that I can run drush commands from my local machine terminal.
## Start Docker
In the Drupal root directory run `docker-compose up`. Docker will begin downloading and building the container from scratch. This will be the longest the container takes to initalize. If you do not change the Docker settings in the future the files will be cached and take only seconds to build the container the next time.

To stop the container run `docker-compose down`.

If you make further changes any of the Docker configuration files then simply run `docker-compose build` to update your container cache.
## Further Reading
- [Docker Compose](https://docs.docker.com/compose/compose-file)
- [Dockerfile](https://docs.docker.com/engine/reference/builder/)
- [PHP Docker](https://hub.docker.com/_/php/)
- [Drupal Docker](https://hub.docker.com/_/drupal/)