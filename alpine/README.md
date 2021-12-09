---
description: >-
  The base image for the bfren ecosystem, containing Alpine Linux and various
  helper executables.
---

# Alpine

[Source files](https://github.com/bfren/docker-alpine) (GitHub)\
[Container images](https://hub.docker.com/r/bfren/alpine) (Docker Hub)

## Acknowledgements

[Docker Alpine](https://github.com/alpinelinux/docker-alpine) is a tiny distribution of Linux designed specifically for use in containers.

[esh](https://github.com/jirutka/esh) is a lightweight templating engine using POSIX-compatible syntax.

## Tags

_x.x and x.x.x refer to the bfren image versions._

| Alpine Version | Tags                                                                        |
| -------------- | --------------------------------------------------------------------------- |
| 3.12           | alpine3.12, alpine3.12.9, alpine3.12-x.x, alpine3.12-x.x.x                  |
| 3.13           | alpine3.13, alpine3.13.7, alpine 3.13-x.x, alpine3.13-x.x.x                 |
| 3.14           | alpine3.14, alpine3.14.3, alpine3.14-x.x, alpine3.14-x.x.x                  |
| 3.15           | latest, alpine3, alpine3.15, alpine3.15.0, alpine3.15-x.x, alpine3.15-x.x.x |
| edge           | alpineedge, alpineedge-x.x, alpineedge-x.x.x                                |

In addition, you can add `-dev` and `-beta` suffixes to access development / test builds (see Docker Hub for further details).

## Environment Variables

| Name       | Values | Description                                    | Default |
| ---------- | ------ | ---------------------------------------------- | ------- |
| `BF_DEBUG` | 0 or 1 | Set to 0 to disable debug log output messages. | 1       |

## Packages

If you need to build the image with a different timezone to the default ("Europe/London"), you can do so by setting the `TZ` ARG, or by installing the `tzdata` package. (Please note this is only possible if you are building the image yourself - otherwise you will need to use [bf-tz](../alpine-s6/executables.md#bf-tz).)

As well as the standard repositories, the edge repos are tagged with `@edgemain` and `@edgecomm`. This means you can, for example, do `apk add curl@edgemain` or `apk add php8@edgecomm` to add the edge versions of packages.

## Cron

This image contains only one service: `cron`, which is enabled by default. If you want to add scripts or executables to the cron you have two options.

### Option 1: `/etc/periodic/`

The simplest way to add tasks to the cron in Alpine Linux is to place an executable file in one of the directories under `/etc/periodic/` (remember to set the executable attribute in `/etc/fix-attrs.d/`):

```bash
$ ls /etc/periodic/
1min    15min    daily    hourly    monthly    weekly
```

It's pretty obvious how frequently they run! An example of this method can be found in the [Nginx PHP](../base-images/nginx-php/) image.

### Option 2: `/etc/crontabs/root`

If you want more control over your cron tasks you can create a normal crontab file:

```bash
$ head -n 1 /etc/crontabs/root
0 */8 * * * db-backup > /dev/null 2>&1
```

However, if you do this, remember you will be overriding the default file when your `/overlay/` is copied over the image files, so you need to include the [default directives](https://github.com/bfren/docker-alpine-s6/blob/main/overlay/etc/crontabs/root) if you don't want to break the cron further down the line.

## Templating with esh

The image comes pre-installed with [esh](https://github.com/jirutka/esh), a simple shell-based templating engine. It is extremely lightweight, and very easy to use.

All dynamic configuration files in the bfren ecosystem are built using esh, and the default location for those templates is `/etc/bf/templates`.
