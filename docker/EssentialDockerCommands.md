# Essential Docker Commands
## Docker pull
Used to pull images from the remote registries.

Syntax:

```sh
docker pull [OPTIONS] NAME[: TAG|@DIGEST]
```

Option | Short | Default | Description |
------:|------:|--------:|------------:|
--all-tags|-a|| Download all tagged images in the repository|
--disable-content-trust|true||Skip image verification
--platform|||API 1.32+ Set platform if server is multi-platform capable
--quiet|-q||Suppress verbose output

Examples:

```sh
# The :latest tag used as a default
docker pull debian
# Specify a specific tag
docker pull debian:bullseye
# Specify the digest of the image
docker pull debian@sha256:26c68657ccce2cb0a31b330cb0be2b5e108d467f641c62e13ab40cbec258c68d
# Specify the register path
docker pull myregistery.host:8080/debian:bullseye
# Pull all tagged images
docker pull --all-tags debian
```

## Docker push
Used to push images to the remote registries.

Syntax:

```sh
docker push [OPTIONS] NAME[:TAG]
```

Option | Short | Default | Description
-------|-------|---------|------------
--all-tags|-a||Upload all tagged images tp the repository
--disable-content-trust|true||Skipimage verification
--quiet|-q||Suppress verbose output

Examples:

```sh
# The :latest tag used as a default
docker push myimage
# Specify a specific tag
docker push myimage:demo
# Specify the register path
docker push myregistery.host:8080/myimage:demo
# Push all tagged images to the registry
docker push --all-tags myimage
```

## Docker build
Package an application into a Docker image by following the instructions specified in the Dockerfile

Syntax:
```sh
docker build [OPTIONS] PATH | URL | -
```
Commonly used options: 
Option | Short | Default | Description
-------|-------|---------|------------
--file|-f||Path to the Dockefile (Default is `PATH/Dockerfile`)
--tag|-t||Name and optionally tag the built image in the `name:tag` format
--add-host|||Add custom host-to-IP mappings in the `/etc/hosts` file of the containers
--build-arg|||Set the build-time variables
--cache-from|||Images to consider as cache sources
--compress|||Compress the build context using gzip
--cgroup-parrent|||Optional parent cgroup for the containers used in the build
--output|-o||Path to the directory for exporting files to
--rm|||Remove intermediate containers after a successful build
--force-rm|||Always remove the intermediate containers
--pull|||Always attempt to pull a newer version of the image
--label|||Set metadata for an image

[Reference](https://docs.docker.com/engine/reference/commandline/build/#options)

Examples:
```sh
# Package application in the . context into image name myapp:latest
docker build -t myapp .

# Suppose that, you have a Dockerfile that contains instructions to install packages from a custom repository, and that repo is identified by a custom hostname (not a public DNS record). 
# You can use --add-host to ensure that hostname is resolved to the correct IP during the build process
docker build --add-host=myrepo.example.com:192.168.1.100 -t myapp .

# Suppose that, you have a Dockerfile as below
# Dockerfile
# ARG my_arg
# RUN echo "The value of my_arg is: ${my_arg}" ${myarg}
docker build --build-arg myarg=value -t myapp .
# Or
export myarg=value
docker build --build-arg myarg .

# Specify that Docker should use myapp:v1 as a cache source during the build of myapp:v2
# NOTE: To use an image as a cache source, cache metadata needs to be written into the image on creation.
# This can be done by setting --build-arg BUILDKIT_INLINE_CACHE=1 when building the image.
docker build -t myapp:v1 --build-arg BUILDKIT_INLINE_CACHE=1 .
docker build --cache-from myapp:v1 -t myapp:v2 .

# This will clone the github repo and use the cloned repo as context.
# The Dockerfile at the root of the repo is used as Dockerfile
docker build github.com/creack/docker-firefox

# This sends the URL `http://server/ctx.tar.gz` to the Docker daemon, which downloads and extracts the referenced tarball.
# The `-f ctx/Dockerfile` parameter specifies a path inside `ctx.tar.gz` to the Dockerfile that is used to build the image.
# Any `ADD` commands in that `Dockerfile` that refers to local paths musts be relative to the root of the contents inside `ctx.tar.gz`.
docker build -f ctx/Dockerfile http://server/ctx.tar.gz

# This will read a Dockerfile from `STDIN` without context.
# Due to the lack of a context, no contents of any local directory will be sent to the Docker daemon.
# Since there is no context, a Dockerfile `ADD` instruction only works if it refers to a remote URL
docker build - < Dockerfile

# This will build an image for a compressed context read from `STDIN`.
# (Supported formats are: bzip2, gzip and xz)
docker build - < context.tar.gz

# When you building a Dockerfile with multiple build stages.
# --target can be used to specify an intermediate build stage by name as a final stage for the resulting image.
# Dockerfile
# FROM debian AS build-env
# ...
# FROM alpine AS production-env
# ...
docker build -t myapp --target build-env

# Build an image using the current directort (.) as build context.
# Exports the files to a directory named `out` in the current directory.
docker build -o out .
# Equivalent to
docker build --output type=local,dest=out .
# Use the tar type to export the files as a .tar archive
docker build --outout type=var,dest=out.tar .
```

## Docker run
Create and run a container from an image. (Pulling the image if needed and starting the container)

Syntax:
```sh
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

Commonly used options: 
Option | Short | Default | Description
-------|-------|---------|------------
--name|||Assign a name to the container
--interactive|-i||Keep the stdin open even if not detached
--tty|-t||Allocate a pseudo-TTY (terminal) for interact
--priviledged|||Give extended privileges to the container
--workdir|-w||Specify the working directory inside the container
--volume|-v||Mount a directory or a file from host system into a specific location within the container
--mount|||Attach a host file, a directory, or a volume to the container
--tmpfs|||Mount the empty tmpfs (a temporary, in-memory filesystem) into the container
--read-only|||Mount the container's root filesystem as read-only
--restart||no|Restart policy to apply when a container exits (`no`, `on-failure[:max-retries]`, `unless-stopped`, `always`)
--publish|-p||Publish a container's port to the host
--publish-all|-P||Publish all exposed ports to random ports
--pull||missing|Pull image before running (`always`, `missing`, `never`)
--env|-e||Set environment variables
--env-file|||Read in a file of environment variables
--label|-l||Set metadata on a container
--label-file|||Read in a file of labels
--network|||Connect a container to a network
--network-alias|||Add a network-scoped alias for the container
--volumes-from|||Mount volumes from a specified container(s)
--attach|-a|||Attach to STDIN, STDOUT, STDERR
--detach-keys|||Override the key sequence for detaching the container


Examples:
```sh
# The `-it` instructs Docker to allocate a pseudo-TTY connected to the stdin; creating an interactive bash shell in the container.
docker run --name test -it debian
# The `--privileged` flag gives all capabilities to the container, and it also lifts all the limitations enforced by the `device` cgroup controller.
# In other words, the container can do almost everything that the host can do.
# This flag exists to allow special use-cases, like running Docker within container.
docker run -t -i --privileged alpine sh
# The `-w` runs the commands executed inside directory specified, in this example `/path/to/dir`.
# If the path does not exist, Docker creates it inside the container 
docker run -it -w /path/to/dir alpine pwd
# Mount the `/path/on/host` on the host into the container at `/path/on/container`, sets it as the working directory.
# When the host bind-mount directory in the host does not exist, the Docker creates it before starting the container.
docker run -v /path/on/host:/path/on/container --volume -it alpine pwd
# Or you can do the same thing with `--mount`
docker run --mount type=bind,src=/path/on/host,dest=/path/on/container -it alpine pwd
# Mounts a tmpfs into the container with the rw, noexec, nouid, and size = 65536k options
docker run -d --tmpfs /run:rw,noexec,nouid,size=65536k alpine
# You can use volumes in combination with `--read-only` to control where the container can write files.
docker run -d --read-only -v /writable busybox touch /wriable/here
# Or you can do the same thing with `--mount`
docker run -d --read-only --mount type=volume,target=/writable touch /wriable/here
# By bind-mouting the Docker Unix socket and statically linked the Docker binary, you give the container the full access to create and manipulate the host's Docker daemon
docker run -d -it -v /var/run/docker.sock:/var/run/docker.sock -v /path/to/static-docker-binary:/usr/bin/docker busybox sh
# This will expose the port 80 of the container without publishing the port to the host system's interfaces.
docker run -p 80 alpine sh
# This will always pull the newest version of myapp:latest image before starting the container.
# By default, pulling images only if they are not present in the daemon's image cache. (Equivalent to: --pull=missing)
# If you want to disable the pulling images, specify the --pull=never. This will tell the Docker only use the local images that are available in the image cache.
docker run --pull=always myapp
# Use the `-e` or `--env` and `--env-file` to set simple environment variables in the container you're running.
# If there is no value for the key provided, Docker will use the exported variables in your local machine to pass it to the container.
# If there is no value provided and that variable not exported on your local machine, that variable is not set in the container.
# env.list
# MYVAR3=bar
docker run -e MYVAR1 --env MYVAR2=foo --enf-file ./env.list alpine sh
# To assign an IP to the container use --ip or --ip6 flag.
# To assign a static ip to containers, you must specify subnet block for the network.
# docker network create --subnet 192.0.2.0/24 my-net
docker run -itd --network=my-net --ip=192.0.2.69 busybox
# Set the metadata on the container.
# If you do not specify the value so the label defaults to the empty string ""
docker run -l my.label1 --label my.label.foo=bar --label-file ./label.list
# Adds the container to the `my-net` network.
# Note that you need to create the specified network before adding it to the container.
docker run -itd --network=my-net busybox
# This will bind the port 80 of the container to the TCP port 8080 on 127.0.0.1 of the host machine.
# You can also specify UDP and SCTP ports.
# Note that ports whitch are not bound to the host are exterally accessible. (i.e., -p 8080:80 instead of -p 127.0.0.1:8080)
docker run -p 127.0.0.1:8080:80/tcp alpine sh
# Mounts all the defined volumes from the referenced container with ID `777f7dc92da7` and `ba8c0c54f0f2`.
# You can specify the surfixes such as `:ro` and `:wo` after the container ID to mount the volumes in read-only and write-only mode.
docker run -it --volumes-from 777f7dc92da7 --volumes-from ba8c0c54f0f2:ro alpine sh
# This pipes data into a container and prints the container's ID by attaching only to the container's STDIN. 
echo "test" | docker run -a stdin alpine cat -    
# This pipes data into a container and prints the piped data because the container's STDOUT is attached.
echo "test" | docker run -a stdout volume cat -
# This will print nothing unless there is an error because only the container' STDERR is attached
docker run -a stderr alpine echo "Hello"
# `--detach-keys` is used to override the detach key sequence can be used to detach from a running container when you are attached to use it.
# 
docker run --detach-keys="ctrl-q" alpine sh
```

## Docker ps
List containers

Syntax:
```sh
docker ps [OPTIONS]
```

Commonly used options: 
Option | Short | Default | Description
-------|-------|---------|------------
--all|-a||Show all the containers (by default, show only running containers)
--filter|-f||Filter output based on the conditions provided
--last|-n|-1|Show n last created containers (includes all states)
--latest|||Show the latest created container (includes all states)
--quiet|-q||Only display container's IDs
--size|-s||Display total file sizes


Examples:
```sh
# The `--no-trunc` flag is used to display full, untruncated ouput of the container information.
docker ps --no-trunc
# The `--all` or `-a` flag is used to display all container.
# By default `the docker` ps command only shows running containers.
docker ps -a
# The `--size` or `-s` is used to display 2 different on-disk-sizes for each container.
# - "size" information shows the amount of data (on disk) that is used for writable layer of each container.
# - "virtual size" is the total amount of disk-space used for read-only image data used by the container and the writable layer.
docker ps -s
# The following filter matches all the containers with label foo with bar value.
# If you do not care about the value of the lable, just remove the =bar
# For more filter: https://docs.docker.com/engine/reference/commandline/ps/#filter
docker ps --filter "label=foo=bar"
```

## Docker logs
Fetch logs of the containers

Syntax:
```sh
docker logs [OPTIONS] CONTAINER
```

Commonly used options: 
Option | Short | Default | Description
-------|-------|---------|------------
--detail|||Show extra details provided to logs
--follow|-f||Follow log output
--since|||Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
--until|||Show log until timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
--timestamp|-t||Show timestamps


Examples:
```sh
docker run --name test -d busybox sh -c "while true;do $(echo date); sleep 1; done"
docker logs -f --until=2s test
```

## Docker images
List images

Syntax:
```sh
docker images [OPTIONS] [REPOSITORY[:TAG]]
```

Option | Short | Default | Description
-------|-------|---------|------------
--all|-a||Shows all images (Default hides intermediate images)
--digest|||Add digests in the output
--filter|-f||Filter the output based on the condition provided
--no-trunc|||Do not trunc the output
--quiet|-q||Only show the images ID

Examples:
```sh
# Used the `--all` or `-a` to list all images (includes intermediate images)
docker images --all
# Show images information without truncating the output
docker images --no-trunc
# Display only ID of the images
docker images --quiet
# Use `--filter` to filter the ouput whose reference matches the specified pattern
docker images --filter="foo=bar" 
```

## Docker start
Start one or more stopped containners

Syntax:
```sh
docker start [OPTIONS] CONTAINER [CONTAINER...]
```

Commonly used options: 
Option | Short | Default | Description
-------|-------|---------|------------
--attach|-a||Attach STDOUT/STDERR and forward signals
--checkpoint|||Restore from this checkpoint
--checkout-dir|||Use a custom checkpoint storage directory
--detach-keys|||Override the key sequence for detaching a container
--interactive|-i||Attach STDIN

Examples:
```sh
# Start the conatiner name myapp and attach the stdin to the terminal
docker start -i myapp
```

## Docker stop
Stop one or more containers

Syntax:
```sh
docker stop [OPTIONS] CONTAINER [CONTAINER...]
```

Option | Short | Default | Description
-------|-------|---------|------------
--signal|-s||Signal to send to the container
--time|-t||Seconds to wating before killing the container

Examples:
```sh
docker stop myapp1 myapp2
```

## Docker exec
Execute a command in a running container.

The commands started with the `docker exec` only runs while the container's primary procces (PID 1) is running, and it is not restarted when the container is restarted.

COMMAND runs in the default directory of the container. If the underlying image has a custom directory specified by the WORKDIR directive in its Dockerfile, this directory is used instead.

COMMAND must be an executable. A chained or quoted command does not work.

For example, `docker exec -it my_container sh -c "echo a && echo b"` does work, but `docker exec -it my_container "echo a && echo b"` does not.

Syntax:
```sh
docker exec [OPTIONS] CONTAINER COMMAND [AGR...]
```

Commonly used options: 
Option | Short | Default | Description
-------|-------|---------|------------
--detach|-d||Detached mode: run a command in the background
--detach-keys|||Override the key sequence for detaching the conatiner
--env|-e||Set environment variables
--env-file|||Read in a file of the environment variables
--interactive|-i||Keep STDIN open even if not attached
--priviledge|||Give extended priviledges to the command
--tty|-t||Allocate a pseudo-TTY
--user|-u||Username or UID (format: <name|uid>[:<group|gid>])
--workdir|-w||Working directory inside the container

Examples:
```sh
# Start a container.
# The `-t` allocates a pseudo-TTY.
# the `-i` keeps the stdin attached, which prevent the `/bin/sh` from existing immediately.
# The `-d` helps run the container in the background (in detached mode).
docker run --name mycontainer -i -t -d alpine /bin/sh
# This creates a new file `/tmp/execTest` inside the mycontainer, in the background.
docker exec -d mycontainer touch /tmp/execTest
# Use the `--env` or `-e` to override global enviroment variables, or to set additional environment variables for the process started by the `docker exec`.
docker exec -e MYVAR1=VALUE1 --env MYVAR2=VALUE2 mycontainer env
# You can use `--workdir` or `-w` to specify an alternative working directory for the command to execute.
docker exec -w /root mycontainer pwd
```

<!-- ## <command>
<description>

Syntax:
```sh
<syntax>
```

Commonly used options: 
Option | Short | Default | Description
-------|-------|---------|------------

Examples:
```sh
<examples>
``` -->