## Problem

```
$ docker

The command $ docker could not be found in this WSL 1 distro.
We recommend to convert this distro to WSL 2 and activate the WSL integration in Docker Desktop settings.

See https://docs.docker.com/docker-for-windows/wsl/ for details.
```

## Solution

Run in `cmd.exe`

```
> wsl --list --verbose
  NAME             STATE           VERSION
  Ubuntu           Running         1
```

Then to switch it with `wsl --set-version <your proc> 2`[^1]

```
> wsl --set-version Ubuntu 2
Conversion in progress, this may take a few minutes...
For information on key differences with WSL 2 please visit https://aka.ms/wsl2
Conversion complete.
```

Also you need to go to the docker desktop settings, and enable integration with your distro in "Resources -> WSL Integration".

## Reference

[^1]: [Ubuntu WSL with docker could not be found - Stack Overflow](https://stackoverflow.com/questions/63497928/ubuntu-wsl-with-docker-could-not-be-found)