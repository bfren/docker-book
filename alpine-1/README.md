---
description: >-
  The base image for the bfren ecosystem, containing Alpine Linux and various
  helper executables.
---

# Debian

[Source files](https://github.com/bfren/docker-debian) (GitHub)\
[Container images](https://hub.docker.com/r/bfren/debian) (Docker Hub)

## Acknowledgements

[Debian Slim](https://hub.docker.com/\_/debian) is a cut-down distribution of [Debian](https://www.debian.org) designed specifically for use in containers.

[esh](https://github.com/jirutka/esh) is a lightweight templating engine using POSIX-compatible syntax.

## Tags

_x.x and x.x.x refer to the bfren image versions._

| Debian Version | Tags                                                   |
| -------------- | ------------------------------------------------------ |
| 10 (buster)    | debian10, debian10-x, debian10-x.x, debian10-x.x.x     |
| 11 (bullseye)  | debian11, debian11-x, debian11-x.x, debian11-x.x.x     |
| 12 (bookworm)  | debian12, debian12-x, debian12-x.x, debian12-x.x.x     |
| sid            | debiansid, debiansid-x, debiansid-x.x, debiansid-x.x.x |

In addition, you can add `-dev` and `-beta` suffixes to access development / test builds (see Docker Hub for further details).

## Environment Variables, Packages, Cron & Templating

See [Alpine](../alpine/).
