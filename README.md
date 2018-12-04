# IK Docker for Drupal
This repo is a collection of Docker files which deploy a Docker instance for PHP and Apache to host the Drupal site from your local filesystem. The Docker instance syncs with the filesystem and the Drupal site connects to the host database so mysql must be running on the host machine. I've also added SSL.

I created these files because I am getting tired of MAMP. Docker provides flexibility to test specific versions of PHP and Apache by simply editing the docker-compose and image Dockerfiles.
## Install
Unpack these files in the root directory of the Drupal 8 file system.
[Docker for Mac](https://docs.docker.com/docker-for-mac/) must be installed and running on the host machine.
## Configure
In `docker-compose.yml` set your desired URL for the local instance in the build:args:url setting. You may also change the ports. The port configuration is set like `host-machine`:`docker-machine`.

You must also edit your Drupal `.env` file and modify your database connection. Since the docker machine must connect back to the host MySQL instance you will need it to connect to `host.docker.internal`.
## Start Docker
In the Drupal root directory run `docker-compose up`. Docker will begin downloading and building the container from scratch. This will be the longest the container takes to initalize. If you do not change the Docker settings in the future the files will be cached and take only seconds to build the container the next time.

To stop the container run `docker-compose down`.

If you make further changes any of the Docker configuration files then simply run `docker-compose build` to update your container cache.
## Use Drupal
Connect to your site at `https://localhost` or another alias defined in your hosts file. SSL is enabled by default and you may need to authorize the self-signed certificate in your browser. If you navigate to the non-SSL HTTP site you will see __Docker Localhost__ static HTML page. I've added `http://localhost/phpinfo.php`  to display the Docker PHP version and current configuration.
## Using Drush and command-line utilities
Because this current implementation syncs the local and docker file system and uses the local database we can configure a cli like drush to run locally.

Edit the /etc/hosts files. Add `127.0.0.1 host.docker.internal` so that requests to that URL will loopback to your local machine.

Now you may run `drush` from your local filesystem. It will read the database URL in settings.local.php and the system hosts file will route it directly to the current database.
## TODOS
1. Easily support drush and drupal console commands from ~~the host machine~~ or the container.
1. Make the database available from a container.
1. Consider using PHP-FPM.
## Further Reading
- [Docker Compose](https://docs.docker.com/compose/compose-file)
- [Dockerfile](https://docs.docker.com/engine/reference/builder/)
- [PHP Docker](https://hub.docker.com/_/php/)
- [Drupal Docker](https://hub.docker.com/_/drupal/)