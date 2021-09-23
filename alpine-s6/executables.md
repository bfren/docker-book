---
description: >-
  Various helper executables for providing standard output and simplifying
  common tasks.
---

# Alpine S6: Executables

## Output

### bf-debug

Echoes "`$1`: `$2`" to `stdout` in grey with prefix \(see [bf-e](executables.md#bf-e)\) - but only if [environment variable](./#environment-variables) `BF_DEBUG` is set to "1".

#### Arguments

1. Text to output
2. \[Optional\] Script / executable name

#### Usage

```bash
$ bf-debug "Hi"
[bf] 2021-09-19 18:00:00 | Hi # (in grey)

$ bf-debug "Hi" "alpine-s6/docs"
[bf] 2021-09-19 18:00:00 | alpine-s6/docs: Hi # (in grey)
```

### bf-done

Echoes 'done.' to `stdout` in green with prefix \(see [bf-ok](executables.md#bf-ok)\).

#### Arguments

1. \[Optional\] Script / executable name

#### Usage

```bash
$ bf-done
[bf] 2021-09-19 18:00:00 | done. # (in green)

$ bf-done "alpine-s6/docs"
[bf] 2021-09-19 18:00:00 | alpine-s6/docs: done. # (in green)
```

### bf-e

Echoes `$3` to `stdout` in colour `$2` with prefix:  
"\[`$1`\] %Y-%m-%d %H:%M:%S".

#### Arguments

1. Namespace \(default value for other output helper executables is 'bf'\)
2. ANSI colour code \(see e.g. [here](https://gist.github.com/Prakasaka/219fe5695beeb4d6311583e79933a009)\)
3. Text to output
4. \[Optional\] Script / executable name

#### Usage

```bash
$ bf-e "docs" "\e[91m" "Hi"
[docs] 2021-09-19 18:00:00 | Hi # (in red)

$ bf-e "docs" "\e[92m" "Hi" "alpine-s6"
[docs] 2021-09-19 18:00:00 | alpine-s6: Hi # (in green)
```

### bf-echo

Echoes "`$1`: `$2`" to `stdout` in default colour with prefix \(see [bf-e](executables.md#bf-e)\).

#### Arguments

1. Text to output
2. \[Optional\] Script / executable name

#### Usage

```bash
$ bf-echo "Hi"
[bf] 2021-09-19 18:00:00 | Hi # (in black / white)

$ bf-echo "Hi" "docs"
[bf] 2021-09-19 18:00:00 | docs: Hi # (in black / white)
```

### bf-error

Echoes "`$1`: `$2`" to `stdout` in red with prefix \(see [bf-e](executables.md#bf-e)\), and returns 1.  This will end execution of the calling script if you use, e.g. `set -euo pipefail` at the top of your scripts \(recommended\).

#### Arguments

1. Text to output
2. \[Optional\] Script / executable name

#### Usage

```bash
$ bf-error "Die"
[bf] 2021-09-19 18:00:00 | Die # (in red)

$ bf-error "Die" "docs"
[bf] 2021-09-19 18:00:00 | docs: Die # (in red)
```

### bf-notok

Like [bf-error](executables.md#bf-error) echoes "`$1`: `$2`" to `stdout` in red with prefix, but doesn't end execution of calling script.

#### Arguments

1. Text to output
2. \[Optional\] Script / executable name

#### Usage

```bash
$ bf-notok "Error"
[bf] 2021-09-19 18:00:00 | Error # (in red)

$ bf-notok  "Error" "docs"
[bf] 2021-09-19 18:00:00 | docs: Error # (in red)
```

### bf-ok

Echoes "`$1`: `$2`" to `stdout` in green with prefix.

#### Arguments

1. Text to output
2. \[Optional\] Script / executable name

#### Usage

```bash
$ bf-ok "Hi"
[bf] 2021-09-19 18:00:00 | Hi # (in green)

$ bf-ok  "Hi" "docs"
[bf] 2021-09-19 18:00:00 | docs: Hi # (in green)
```

### bf-svc-finish

Outputs service closing down debug message and optionally terminates _all_ running services.  Usually used in `/etc/services.d/xxx/finish` file to show

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

### bf-adduser

Create user with id `$2`, group with id `$3`, both with name `$1`, and no password.

#### Arguments

1. User and group name
2. \[Optional\] User ID \(default: 1000\)
3. \[Optional\] Group ID \(default: UID\)

#### Usage

```bash
$ bf-adduser "fred"
[bf] 2021-09-19 18:00:00 | bf-adduser: Adding user and group 'fred'...
[bf] 2021-09-19 18:00:00 | bf-adduser: done. 'fred'... # (in green)
$ cat /etc/passwd
...
fred:x:1000:1000:Linux User,,,:/home/fred:/bin/ash

$ bf-adduser "fred" 1001
[bf] 2021-09-19 18:00:00 | bf-adduser: Adding user and group 'fred'...
[bf] 2021-09-19 18:00:00 | bf-adduser: done. 'fred'... # (in green)
$ cat /etc/passwd
...
fred:x:1001:1001:Linux User,,,:/home/fred:/bin/ash

$ bf-adduser "fred" 1002 1003
[bf] 2021-09-19 18:00:00 | bf-adduser: Adding user and group 'fred'...
[bf] 2021-09-19 18:00:00 | bf-adduser: done. 'fred'... # (in green)
$ cat /etc/passwd
...
fred:x:1002:1003:Linux User,,,:/home/fred:/bin/ash
```

### bf-ch

Changes ownership and/or permissions of file / directory glob.

#### Arguments

Required \(one or both\):

* `-o X` use `chown` to set ownership to "X"
* `-m Y` use `chmod` to set permissions to "Y"

Optional:

* `-t f|d` apply only to files \("f"\) or directories \("d"\)
* `-r`if `-t` is not specified, applies action recursively to all files and directories

#### Usage

```bash
$ bf-ch -o "fred" -m 777 -t f /tmp
[bf] 2021-09-19 18:00:00 | bf-ch: Applying chown fred to /tmp.
[bf] 2021-09-19 18:00:00 | bf-ch:  .. type f.
[bf] 2021-09-19 18:00:00 | bf-ch: Applying chmod 777 to /tmp.
[bf] 2021-09-19 18:00:00 | bf-ch:  .. type f.

$ bf-ch -o "fred" -m 777 -r /tmp
[bf] 2021-09-19 18:00:00 | bf-ch: Applying chown fred to /tmp.
[bf] 2021-09-19 18:00:00 | bf-ch:  .. recursively.
[bf] 2021-09-19 18:00:00 | bf-ch: Applying chmod 777 to /tmp.
[bf] 2021-09-19 18:00:00 | bf-ch:  .. recursively.
```

### bf-clear

Clears contents of `/tmp` and `apk` cache.

#### Usage

```bash
$ bf-clear
[bf] 2021-09-19 18:00:00 | bf-clear: Clearing /tmp...
[bf] 2021-09-19 18:00:00 | bf-clear: done.
[bf] 2021-09-19 18:00:00 | bf-clear: Clearing caches...
[bf] 2021-09-19 18:00:00 | bf-clear: done.
```

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

Re-applies attributes and permissions defined in `/etc/fix-attrs.d`.  Based on code from [S6 Overlay](https://github.com/just-containers/s6-overlay/blob/master/builder/overlay-rootfs/etc/s6/init/init-stage2).

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

### bf-install

Run install `/tmp/install` and then perform cleanup \(see [bf-clear](executables.md#bf-clear)\).  Within the ecosystem this would normally be used in a `Dockerfile`

#### Usage

_This is the basic structure of Dockerfiles based on the Alpine S6 image, copying overlay and then running the standard installation._

```text
FROM bfren/alpine-s6:alpine3.14

COPY ./overlay /

RUN bf-install
```

### bf-rmrf

Runs `rm -rf $1` safely: doing nothing if `$1` is empty.  This is handy if the path you are deleting is contained in a variable \(it effectively stops `rm -rf /`!\).

#### Arguments

1. Folder path / file glob to delete.

#### Usage

```bash
$ bf-rmrf ""
[bf] 2021-09-19 18:00:00 | bf-rmrf: Cannot remove ''.

bf-rmrf /tmp/*
[bf] 2021-09-19 18:00:00 | bf-rmrf: Removing /tmp/*.
```

### bf-rnd

Generates a string of random letters and numbers of length `$1`.

#### Arguments

1. \[Optional\] Number of characters to return

#### Usage

```bash
$ bf-rnd
7ceeezFaFzbuaHujA2ST2s3ACPAnAEoUqqVXBcdk

$ bf-rnd 10
D8ORzTX56m

$ RND=$(bf-rnd)
$ echo ${RND}
cm1HuNlkQwbqd5TeApoHsxfYE6X1MWxKnuiBbjFr
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

Terminates all running services - usually used in `/etc/services.d/xxx/finish` file - which also closes down \(and potentially restarts\) the whole container.

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

### bf-test-url

Uses `wget` to test URL `$1`.

#### Arguments

1. URL to test

#### Usage

```bash
$ bf-test-url https://fake.url
[bf] 2021-09-19 18:00:00 | bf-test-url: Loading https://fake.url.
wget: bad address 'fake.url'

$ bf-test-url https://bbc.co.uk
[bf] 2021-09-19 18:00:00 | bf-test-url: Loading https://bbc.co.uk.
Connecting to bbc.co.uk (151.101.0.81:443)
Connecting to www.bbc.co.uk (212.58.237.254:443)
remote file exists
```

### bf-tz

Sets the container's timezone to `$1`.  \(Installs `tzdata` package, changes timezone and then removes `tzdata`\).

#### Arguments

1. Valid timezone \(e.g. Europe/London\)

#### Usage

_Note the change in time on line 12._

```bash
$ bf-tz UTC
[bf] 2021-09-19 18:00:00 | bf-tz: Installing tzdata packages.
fetch https://dl-cdn.alpinelinux.org/alpine/v3.14/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.14/community/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/edge/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/edge/community/x86_64/APKINDEX.tar.gz
(1/2) Installing tzdata (2021a-r0)
(2/2) Installing .tz (20210923.092854)
Executing busybox-1.33.1-r3.trigger
OK: 9 MiB in 17 packages
[bf] 2021-09-19 18:00:00 | bf-tz: Setting timezone to UTC...
[bf] 2021-09-19 17:00:00 | bf-tz: Removing tzdata packages.
WARNING: Ignoring https://dl-cdn.alpinelinux.org/alpine/v3.14/main: No such file or directory
WARNING: Ignoring https://dl-cdn.alpinelinux.org/alpine/v3.14/community: No such file or directory
WARNING: Ignoring https://dl-cdn.alpinelinux.org/alpine/edge/main: No such file or directory
WARNING: Ignoring https://dl-cdn.alpinelinux.org/alpine/edge/community: No such file or directory
(1/2) Purging .tz (20210923.092854)
(2/2) Purging tzdata (2021a-r0)
Executing busybox-1.33.1-r3.trigger
OK: 6 MiB in 15 packages
[bf] 2021-09-19 17:00:00 | bf-tz: done.

$ cat /etc/localtime
TZif2UTCTZif2UTC
UTC0
```

