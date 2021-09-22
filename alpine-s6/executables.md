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

### bf-clear-src

### bf-env

### bf-fix-attrs

### bf-forward

### bf-install

### bf-rmrf

### bf-rnd

### bf-svc

### bf-svc-finish

### bf-svc-terminate

### bf-test-url

### bf-tz

