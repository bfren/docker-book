---
description: .NET and ASP.NET runtimes pre-installed (versions 3.1, 5.0, and 6.0).
---

# ASP.NET

[Source files](https://github.com/bfren/docker-aspnet) (GitHub)\
[Container images](https://hub.docker.com/r/bfren/aspnet) (Docker Hub)

## Acknowledgements

Comes pre-installed with the [ASP.NET runtime](https://dotnet.microsoft.com) and all required dependencies.

## Tags

| .NET Version | Tags                           |
| ------------ | ------------------------------ |
| 3.1          | net3, net3.1, net3.1.21        |
| 5.0          | net5, net5.0, net5.0.12        |
| 6.0          | latest, net6, net6.0, net6.0.0 |

In addition, you can add `-dev` and `-beta` suffixes to access development / test builds (see Docker Hub for further details).

## Ports

| Port   | Description                                |
| ------ | ------------------------------------------ |
| `5000` | Serves HTTP content from your application. |

## Volumes

| Volume         | Purpose                                                                                                                                           |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/app/live`    | Contains live application files.                                                                                                                  |
| `/app/publish` | Publish updated files to this directory and use [aspnet-switch-terminate](scripts-and-executables.md#aspnet-switch-terminate) to make it go live. |

## Environment Variables

| Name                                    | Values            | Description                                                                                                                                                                   | Default                        |
| --------------------------------------- | ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| `ASPNET_ASSEMBLY`                       | string            | The filename of the assembly to execute.                                                                                                                                      | _None_ - **required**          |
| `ASPNETCORE_URLS`                       | string            | Default value binds web server to port 5000 - should not normally need to be changed.                                                                                         | [http://+:5000](http://+:5000) |
| `DOTNET_RUNNING_IN_CONTAINER`           | 'true' or 'false' | This should always be set to true - it tells dotnet that it is running in a container environment.                                                                            | 'true'                         |
| `DOTNET_SYSTEM_GLOBALIZATION_INVARIANT` | 'true' or 'false' | See [here](https://github.com/dotnet/runtime/blob/master/docs/design/features/globalization-invariant-mode.md) and [here](https://github.com/dotnet/announcements/issues/20). | 'true'                         |



