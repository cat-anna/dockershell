# Docker shell

Simple script to log user into container as shell

## Usage

Just replace shell in passwd file to `dockersh`. If user has `Dockerfile` in his home then it will be used to build image for container.
Each login starts new container which is removed after logout (the `--rm` flag is used). User inside container inherits all groups and home directory. Additioaly container hostname is set to match host system.

### Installation

Just clone somewhere. Script depends on pyyaml lib, make sure you have it installed.
Make sure target user is in docker group.

### Configuration

Behaviour of `dockersh` can be configured using config file. Default path is `/etc/dockershell.yaml`.
Example config:

```yaml
global:
  image: "ubuntu:latest"   # default image (if user does not have own Dockerfile in home)
  shell: "/usr/bin/bash"   # default shell (if not overriden by user)
  expose_docker: false     # don't expose docker host to users (False is default anyway)
  volumes:               # Mount additional volumes for all users
    - "/mnt/storage:/mnt/storage"
  # Following configs does not have default values, so paramter is not added if not defined
  memory: 1G               # Limit memory of containers, passed to --memory argument
  cpus: 1                  # Limit cpu, passed to --cpus argument
  network: none            # Select network to use, passed to --network argument
users:
  myuser:
    expose_docker: true    # overrides expose_docker from global
    volumes:               # extends volumes from global. myuser will have both binds
    - "/srv/www:/srv/www"
    network: host          # overrides network from global
    # memory and cpus will be inherited from global secion
  otheruser:
    shell: "/usr/bin/sh"   # overrides defaut shell
    image: "alpine:latest" # overrides defaut image
```

Using config file is optional. Config is not intended to be edited directly by users, but controlled by admin.

## Security

Purpose of this script is not to enchance security. Users can define own Dockerfiles so escaping container might be possible.
