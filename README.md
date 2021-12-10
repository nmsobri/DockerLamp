# DockerLamp
Lamp stack with docker compose


## Technical Details
* Apache 2 (mod_rewrite preconfigured, work out of the box)

* MySQL 8.0

* PHP 8.0 (xdebug preconfigured, work out of the box)

* PhpMyadmin

* Composer

* Mailhog (preconfigured, work out of the box)

* RabbitMq (preconfigured, work out of the box)

* Redis (preconfigured, work out of the box)

The web root is located in the project directory at `www` and you can install your files there

## Requirements
* Git <http://git-scm.com/>
* Docker <https://www.docker.com/>

## Usage


#### Startup
```
$ git clone git@github.com:slier81/DockerLamp.git
$ cd DockerLamp
$ mv .env.dist .env
$ docker-compose up -d
```

#### Shutdown
```
docker-compose down
```

That is pretty simple.


## Screenshot Of Localhost

![ScreenShot](https://i.imgur.com/m5QoNVg.png)
