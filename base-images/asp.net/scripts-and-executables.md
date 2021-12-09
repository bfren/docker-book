---
description: Various helper executables for interacting with your .NET application.
---

# ASP.NET: Executables

## Helpers

### aspnet-restart

Restarts ASP.NET application.

#### Usage

```
$ aspnet-restart
[bf] 2021-09-19 18:00:00 | restart: Restarting application.
[bf] 2021-09-19 18:00:00 | bf-svc: Restarting service 'aspnet'.
```

### aspnet-start

Starts ASP.NET application.

{% hint style="warning" %}
If you try to start an application that is already running you will get an error as it won't be able to bind to port 5000.  You should never need to use this executable as it is called when the container starts.
{% endhint %}

#### Usage

```
$ aspnet-start
[bf] 2021-09-19 18:00:00 | start: Starting ASP.NET application.
[bf] 2021-09-19 18:00:00 | start:  .. /app/live/ASPNET_ASSEMBLY.
```

### aspnet-switch-terminate

Stops ASP.NET application, switches live and published code, and terminates the container (you will need to have the restart policy set to 'always').

#### Usage

```
$ aspnet-switch-terminate
[bf] 2021-09-19 18:00:00 | bf-svc: Disabling service 'aspnet'.
[bf] 2021-09-19 18:00:00 | switch: Switching code...
[bf] 2021-09-19 18:00:00 | switch:  .. moving live files to src.OCPFfa
[bf] 2021-09-19 18:00:00 | switch:  .. moving published files to /app/live
[bf] 2021-09-19 18:00:00 | switch:  .. moving live files to /app/publish
[bf] 2021-09-19 18:00:00 | switch: done.
[bf] 2021-09-19 18:00:00 | bf-env: BF_TERMINATING=1.
[bf] 2021-09-19 18:00:00 | bf-svc-terminate: Terminating all services.
```

### healthcheck

Uses [bf-test-url](../../alpine/alpine-executables.md#bf-test-url) to request the URL set by `ASPNETCORE_URLS` and exits with code 0 if response is `HTTP 200 OK`.

#### Usage

```
$ healthcheck
[bf] 2021-09-19 18:00:00 | healthcheck: Loading http://localhost:5000. (bf-test-url)
Connecting to localhost:5000 (127.0.0.1:5000)
remote file exists
```
