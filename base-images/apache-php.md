---
description: Base Apache + PHP image with MySQL enabled
---

# Apache PHP

[Source files](https://github.com/bfren/docker-apache-php) (GitHub)\
[Container images](https://hub.docker.com/r/bfren/apache-php) (Docker Hub)

## Acknowledgements

Based on the [official PHP image](https://hub.docker.com/\_/php) for PHP 5.6.40 with Apache and `mysql` extension installed.

## Ports

| Port | Description                                                                       |
| ---- | --------------------------------------------------------------------------------- |
| `80` | Serves HTTP content (Apache requires permissions for user 1000, e.g. `www-data`). |

## Volumes

| Volume           | Purpose                                                  |
| ---------------- | -------------------------------------------------------- |
| `/var/www/html/` | _From base image: files are served from this directory._ |

