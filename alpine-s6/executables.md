---
description: >-
  Various helper executables for interacting with S6 and the container
  environment.
---

# Alpine S6: Executables

## Output

### bf-svc-finish

Outputs service closing down debug message and optionally terminates _all_ running services. Usually used in `/etc/services.d/xxx/finish` file to show

#### Arguments

* `-s X` outputs a message that service "X" is shutting down
* `-t` terminates all other services as well

#### Usage

```bash
$ bf-svc-finish -s cron
[bf] 2021-09-23 10:21:52 | cron/finish: Service closing down.

$ bf-svc-finish -s cron -t
[bf] 2021-09-19 18:00:00 | cron/finish: Service closing down.
[bf] 2021-09-19 18:00:00 | bf-env: BF_TERMINATING=1.
[bf] 2021-09-19 18:00:00 | bf-svc-terminate: Terminating all services.
[bf] 2021-09-19 18:00:00 | cron-log/finish: Service closing down.
[cont-finish.d] executing container finish scripts...
[cont-finish.d] 00-clear: executing...
[bf] 2021-09-19 18:00:00 | 00-clear: Clearing /tmp... (bf-clear)
[bf] 2021-09-19 18:00:00 | 00-clear: done. (bf-clear)
[bf] 2021-09-19 18:00:00 | 00-clear: Clearing caches... (bf-clear)
[bf] 2021-09-19 18:00:00 | 00-clear: done. (bf-clear)
[cont-finish.d] 00-clear: exited 0.
[cont-finish.d] done.
[s6-finish] waiting for services.
[s6-finish] sending all processes the TERM signal.
[s6-finish] sending all processes the KILL signal and exiting.
```

## Helpers

### bf-clear-src

Clears contents of `/etc/bf/src`.

#### Usage

```bash
$ bf-clear-src
[bf] 2021-09-19 18:00:00 | bf-clear-src: Clearing /etc/bf/src...
[bf] 2021-09-19 18:00:00 | bf-clear-src: done.
```

### bf-env

Adds a container environment variable called `$1` with value `$2`.

#### Arguments

1. Variable name
2. Variable value

#### Usage

```bash
$ bf-env "DOCS" "Docker Docs"
[bf] 2021-09-19 18:00:00 | bf-env: DOCS=Docker Docs.
```

### bf-fix-attrs

Re-applies attributes and permissions defined in `/etc/fix-attrs.d`. Based on code from [S6 Overlay](https://github.com/just-containers/s6-overlay/blob/master/builder/overlay-rootfs/etc/s6/init/init-stage2).

#### Usage

```bash
$ bf-fix-attrs
[bf] 2021-09-19 18:00:00 | bf-fixattrs: Applying ownership & permissions fixes...
[bf] 2021-09-19 18:00:00 | bf-fixattrs: 00-alpine-s6: applying...
[bf] 2021-09-19 18:00:00 | bf-fixattrs: 00-alpine-s6: exited 0.
[bf] 2021-09-19 18:00:00 | bf-fixattrs: done.
```

### bf-forward

Forwards errors logged in `$2` to Docker logs; if `$2` is not set, disables service `$1`.

#### Arguments

1. Service name.
2. Absolute path to log file.

#### Usage

```bash
$ bf-forward cron-log /var/log/cron.log
[bf] 2021-09-19 18:00:00 | [bf] 2021-09-23 09:57:59 | cron-log/run: Forwarding cron-log - /var/log/cron.log. (bf-forward)
```

### bf-svc

Uses S6 to control the service named `$2`.

#### Arguments

1. Control action: 'disable', 'restart', 'start', 'stop'
2. Service name

#### Usage

```bash
$ bf-svc restart cron
[bf] 2021-09-19 18:00:00 | bf-svc: Restarting service 'cron'.
[bf] 2021-09-19 18:00:00 | cron/finish: Service closing down.
[bf] 2021-09-19 18:00:00 | cron/run: Starting cron.
crond: crond (busybox 1.33.1) started, log level 8
```

### bf-svc-terminate

Terminates all running services - usually used in `/etc/services.d/xxx/finish` file - which also closes down (and potentially restarts) the whole container.

#### Usage

```bash
$ bf-svc-terminate
[bf] 2021-09-19 18:00:00 | bf-env: BF_TERMINATING=1.
[bf] 2021-09-19 18:00:00 | bf-svc-terminate: Terminating all services.
[bf] 2021-09-19 18:00:00 | cron/finish: Service closing down.
[bf] 2021-09-19 18:00:00 | cron-log/finish: Service closing down.
[cont-finish.d] executing container finish scripts...
[cont-finish.d] 00-clear: executing...
[bf] 2021-09-19 18:00:00 | 00-clear: Clearing /tmp... (bf-clear)
[bf] 2021-09-19 18:00:00 | 00-clear: done. (bf-clear)
[bf] 2021-09-19 18:00:00 | 00-clear: Clearing caches... (bf-clear)
[bf] 2021-09-19 18:00:00 | 00-clear: done. (bf-clear)
[cont-finish.d] 00-clear: exited 0.
[cont-finish.d] done.
[s6-finish] waiting for services.
[s6-finish] sending all processes the TERM signal.
[s6-finish] sending all processes the KILL signal and exiting.
```
