---
title: "Build on LXD or Docker"
---


In the near future, snapcraft will be offered as a snap, giving you the same experience regardless of Linux distribution or release. Until then, you can build snaps using LXD or Docker.

The following instructions are targeted at Ubuntu 14.04, but you should be able to easily adapt them to your own use case.

If you're already running Ubuntu 16.04 or later, you can skip this guide and [run snapcraft directly](/docs/build-snaps/your-first-snap).

### LXD

Using LXD, you'll map your home directory (or wherever your projects live) into the container, then build a snap from the current directory.

First, install LXD:

       sudo add-apt-repository ppa:ubuntu-lxc/lxd-stable
       sudo apt update
       sudo apt install lxd
       sudo lxd init

Once installed, you can either log into a new shell, or run `newgrp lxd`.

Next, create an Ubuntu 16.04 container for building snaps. We'll assume your projects live within your home directory and map it into this container. If your projects live elsewhere, substitute `source=/home/$USER` and `path=/home/ubuntu` below for your project directory.

       lxc launch ubuntu:16.04 snapcraft -c security.privileged=true
       lxc config device add snapcraft homedir disk source=/home/$USER path=/home/ubuntu

Finally, install snapcraft into the container:

       lxc exec snapcraft -- apt install snapcraft

You're all set. Any time you want to build a snap, type the following command to run snapcraft relative to the current directory:

       lxc exec snapcraft -- sh -c "cd $(pwd); snapcraft"

### Docker

Using Docker, you'll map the current directory into the container, then build a snap from that same directory.

First, install Docker using these abridged instructions. A more compherensive guide can be found on the [Docker website](https://docs.docker.com/engine/installation/linux/ubuntulinux/).

       echo "deb https://apt.dockerproject.org/repo ubuntu-trusty main" | sudo tee /etc/apt/sources.list.d/docker.list
       sudo apt-key adv \
               --keyserver hkp://ha.pool.sks-keyservers.net:80 \
               --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
       sudo apt update
       sudo apt install docker-engine

Next, make sure Docker is running:

       sudo service docker status

You're all set. Any time you want to build a snap, type the following command to run snapcraft relative to the current directory:

       sudo docker run -e TERM -v $PWD:/c -w /c snapcore/snapcraft sh -c "snapcraft"
