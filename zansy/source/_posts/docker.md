title: Docker Notes(2021/07/09 updated)
author: zansy
toc: true
tags:
  - Docker
categories:
  - 唯有爱与工作不可辜负
date: 2021-07-09 17:32:00
---
Docker is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers. Containers are isolated from one another and bundle their own software, libraries and configuration files. Notes by Docker official website.
<!--more-->
# Get started
## Part 1: Getting started
### Start the tutorial
```bash
docker run -d -p 80:80 docker/getting-started
```
- -d - run the container in detached mode (in the background)
- -p 80:80 - map port 80 of the host to port 80 in the container
- docker/getting-started - the image to use

### What is a container?
Simply put, a container is simply another process on your machine that has been isolated from all other processes on the host machine. That isolation leverages kernel namespaces and cgroups, features that have been in Linux for a long time. 

### What is a container image?
When running a container, it uses an isolated filesystem. This custom filesystem is provided by a container image. Since the image contains the container’s filesystem, it must contain everything needed to run an application - all dependencies, configuration, scripts, binaries, etc. The image also contains other configuration for the container, such as environment variables, a default command to run, and other metadata.

## Part 2: Sample application