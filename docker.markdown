## Install on Ubuntu 14.04 LTS

[document](https://docs.docker.com/installation/ubuntulinux/#ubuntu-trusty-1404-lts-64-bit)

```
If you'd like to try the latest version of Docker:

First, check that your APT system can deal with https URLs: the file /usr/lib/apt/methods/https should exist. If it doesn't, you need to install the package apt-transport-https.

[ -e /usr/lib/apt/methods/https ] || {
  apt-get update
  apt-get install apt-transport-https
}
Then, add the Docker repository key to your local keychain.

$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
Add the Docker repository to your apt sources list, update and install the lxc-docker package.

You may receive a warning that the package isn't trusted. Answer yes to continue installation.

$ sudo sh -c "echo deb https://get.docker.io/ubuntu docker main\
> /etc/apt/sources.list.d/docker.list"
$ sudo apt-get update
$ sudo apt-get install lxc-docker

```


