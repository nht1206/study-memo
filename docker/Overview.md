# What is Docker?
Docker is a powerful tool that allow developers, sys-admins to easily develop, package, and deploy their applications in sandboxs (called containers). Docker is a container service management. The keywords of Docker are `develop`, `ship`, and `run anywhere`.
## Feature of Docker
- Has ability to reduce the size of development by providing a smaller footprint of the OS vie containers.
- With containers, it becomes easier for teams across different units, such as Development, QA, and Operation to work seamlessly across applications.
- You can deploy Docker containers anywhere, on any physical machine, virtual machine or even on the cloud.
- Since Docker container are pretty lightweight, they are very easily scalable.
## What is a Container?
A container is a sandboxed proccess running on a host machine that is isolated from all other processes running on that host machine. That isolation leverage kernel `namespaces` and `cgroups`, features that have been in Linux for a long time.
- Is a runnable instance of an image. You can create, start, stop, move, or delete a container using the docker API or CLI.
- Can be run on a local machine, virtual machine, or deployed on cloud.
- Is portable (and can be run on any OS).
- Is isolated from other containers and runs its own softwares, binaries, configurations, etc.
## What is an Image?
A running container uses an isolated filesystem. This isolated filesystem is provided by an image, and an image must contain everything needed to run an application - all dependencies, configurations, scripts, binaries, etc. The image also contains other configurations for the container, such as environment variables, a default command to run, and other metadata.
- Is a file, comprised multiple layers, used to run an application in the container.
- Is a set of instructions used to create containers.
## What is a Dockerfile?
A `Dockerfile` is a text document that contains all the commands a user could call on the command line to assemble an image.
## What is a Docker Engine?
A Docker Engine is a software used for building and containerizing your applications.
- Acts as an client-server application.
- Docker Engine contains 3 main components:
    - A Server with a long-running deamon process (called `Dockerd`)
    - APIs which specify interfaces that programs can use to talk to the and instruct the deamon.
    - A command line interface (CLI) (called `Docker`)

## Architecture of Docker

![test](./images/Architecture-of-Docker.png)
