---
description: >-
  The base image for the bfren ecosystem, containing Alpine Linux installed with
  the S6 Overlay
---

# Alpine S6

[Source files](https://github.com/bfren/docker-alpine-s6) \(GitHub\)  
[Container images](https://hub.docker.com/r/bfren/alpine-s6) \(Docker Hub\)

## Acknowledgements

[Docker Alpine](https://github.com/alpinelinux/docker-alpine) is a tiny distribution of Linux designed specifically for use in containers.

The [S6 Overlay](https://github.com/just-containers/s6-overlay) is a version of the S6 supervisor which has various useful features for containers and managing services.

[esh](https://github.com/jirutka/esh) is a lightweight templating engine using POSIX-compatible syntax.

## Tags

_x.x and x.x.x refer to the bfren image versions._

| Alpine Version | Tags |
| :--- | :--- |
| 3.12 | alpine3.12, alpine3.12.8, alpine 3.12-x.x, alpine3.12-x.x.x |
| 3.13 | alpine3.13, alpine3.13.6, alpine 3.13-x.x, alpine3.13-x.x.x |
| 3.14 | latest, alpine3, alpine3.14, alpine3.14.2, alpine3.14-x.x, alpine3.14-x.x.x |
| edge | alpineedge, alpineedge-x.x, alpineedge-x.x.x |

In addition, you can add `-dev` and `-beta` suffixes to access development / test builds \(see Docker Hub for further details\).

## Environment Variables

| Name | Values | Description | Default |
| :--- | :--- | :--- | :--- |
| `BF_CRON_LOG_LEVEL` | 0-8 | Sets the cron log level \(0 is the most verbose\). | 8 |
| `BF_DEBUG` | 0 or 1 | Set to 0 to disable debug log output messages. | 1 |

## Packages

The image comes with the S6 Overlay and all its dependencies. If you need to build it with a different timezone to the default \("Europe/London"\), you can do so by setting the `TZ` ARG, or by installing the `tzdata` package.  \(Please note this is only possible if you are building the image yourself - otherwise you will need to use [bf-tz](executables.md#bf-tz).\)

As well as the standard repositories, the edge repos are tagged with `@edgemain` and `@edgecomm`. This means you can, for example, do `apk add curl@edgemain` or `apk add php8@edgecomm` to add the edge versions of packages.

## Layout

The most important concept in these images is the `/overlay/` directory, which is copied from the source over the top of the image. You can use whatever structure you like within that.

So, for example, the `edge` repositories are added via the `/overlay/etc/apk/repositories` file, which is copied over the top of the base image filesystem by the `Dockerfile` command `COPY ./overlay /`.

You can add any files you want to the image this way.

### S6 Startup

Every time the container starts, the S6 Overlay runs the same series of scripts. These allow you to ensure that there is a consistent environment for your service.

The sequence is as follows \(all should be contained within the `/overlay/` directory\):

1. `/etc/fix-attrs.d/` - set required permissions
2. `/etc/cont-init.d/` - run initialisation scripts
3. `/etc/services.d/` - register the services you want to be supervised

{% hint style="info" %}
My rule of thumb is that the first image uses 0-based files. Then the next uses 1-based files, then 2-based, etc. As S6 loads these files alphabetically, this practice ensures base permissions and configuration is always done first.
{% endhint %}

The S6 Overlay does more before and after all this - additional information can be found in [their documentation](http://www.skarnet.org/software/s6/). These three folders are where you will most often hook into the container processes.

### Permissions

For me, permissions are one the most powerful and most frustrating features of Linux. These are set by adding files to `/overlay/etc/fix-attrs.d/`. Good practice is to prefix these files with a number so you have control over the order in which they are loaded. For example:

```bash
# fix-attrs.d/00-bin
/usr/local/bin/healthcheck false root:root 0500 0500

# fix-attrs.d/01-user
/etc/user true www:www 0644 0755
```

* First we have the absolute path to the file or directory to set permissions for
* Next we have `true` or `false` whether or not to recurse \(obviously meaningless for a file\)
* Then we have the owner of the file or directory
* Finally we have the permissions, first `fmode` \(file permissions\) and then `dmode` \(directory permissions\).  I find the [chmod calculator](https://chmod-calculator.com) extremely useful for generating these.

You can have as many of these as you wish, and you can reapply them at any point by using [bf-fix-attrs](executables.md#bf-fix-attrs).

### Initialisation

After the permissions are set, the scripts in `/etc/cont-init.d/` are run. Here you might want to run installation routines, create configuration files, etc.

For example, in the [ClamAV](../base-images/clamav.md) image the following two scripts run in this stage:/etc/cont-init.d/10-initial \# downloads the latest virus definitions

```bash
/etc/cont-inid.d/11-updater # registers the freshclam update daemon
```

Notice that in this example the files begin `10-` and `11-` as mentioned earlier. 

\(It does mean there can't be more than ten initialisation scripts per image, but frankly if there are more we need to ask the question, is the image trying to do too much? A key principle of Docker is that an image should do one thing.\)

### Services

All the services you want to be managed by S6 go in `/etc/services.d/`. The name of the next directory will be the name of the service, e.g. `cron`. Within that the minimum you need is a file named `run`, which contains the script necessary to start your service.

Here is the `run` file from the `cron` service of this image:

```bash
#!/bin/sh
/usr/sbin/crond -f
```

As you can see, it doesn't have to be complex! The trick with this particular example is the `-f` flag which tells the cron daemon to run in the foreground. If it ran in the background, S6 wouldn't be able to supervise it.  This is something to watch out for when creating your own services.

You can have as many services as you want - however the main point of Docker is to separate services into their own 'contained' environments. Therefore I try to keep the discipline of one 'main' service, and only adding 'supporting' services beyond that.

So, for example, in the [Nginx](../base-images/nginx/) image, the main service is nginx itself, but there are also two services to manage logging.

Note that service directories are not given a number prefix - they will run in any order. S6 does have ways of making a service wait for another service to start, but it's complex and very fiddly. Plus, if something needs to happen before your service starts, it should probably go in `/etc/cont-init.d/` instead.

In your main service directory, I suggest also including a `finish` file:

```bash
#!/bin/sh
s6-svscanctl -t /var/run/s6/services
```

This is also from the `cron` service of this image, but you'll see it in the main service directory of all the bfren images. What this does is tell S6 to close down all the services gracefully when this one quits, and then stop the container. You don't have to do this, but it's good practice to ensure a safe closedown of your container services when one of them crashes.

It means you may want to set `restart` to `unless-stopped` instead of always, or you could end up with a never-ending loop of a container starting, crashing, restarting, crashing, etc.

### Container Environment

One of the most useful tricks when using S6 is to make use of the following directive to import all environment variables into the current script:

```bash
#!/usr/bin/with-contenv sh
freshclam -d -c ${FRESHCLAM_PER_DAY}
```

This is an example from the [ClamAV](../base-images/clamav.md) image \(in fact it is the contents of the `11-updater` file we looked at earlier\). One of that image's environment variables is `FRESHCLAM_PER_DAY` which allows you to define how many times a day you want `freshclam` to run.

You do not get Docker's environment variables in scripts by default however, so you need to use the S6 helper function `with-contenv` \(with container environment\). Then you can access all the environment variables you want.

## Cron

This image contains only one service: `cron`, which is enabled by default.  If you want to add scripts or executables to the cron you have two options.

### Option 1: `/etc/periodic/`

The simplest way to add tasks to the cron in Alpine Linux is to place an executable file in one of the directories under `/etc/periodic/` \(remember to set the executable attribute in `/etc/fix-attrs.d/`\):

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

The image comes pre-installed with [esh](https://github.com/jirutka/esh), a simple shell-based templating engine.  It is extremely lightweight, and very easy to use.

All dynamic configuration files in the bfren ecosystem are built using esh, and the default location for those templates is `/etc/bf/templates`.

## Have fun!

And that's it! There isn't much more to it - if you want to dive deeper into S6 then I suggest you read their documentation.

