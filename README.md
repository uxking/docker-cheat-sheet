# Docker Primer

This document will provide some quick info and commands to use once the Docker engine is installed on a machine.

## Docker Hub

Visit the docker hub website to find docker images for use. Many of the images have documentation you can follow to get the container up and running.

## Commands

### Start docker

If `docker` is not running on Ubuntu use `sudo service docker start`

### Download a container image

`docker pull python3` will pull the `latest` version  
`docker pull python3:<tag>` will pull the tagged version

### List images on your machine

`docker image ls`

```
REPOSITORY         TAG                     IMAGE ID       CREATED         SIZE
google/cloud-sdk   latest                  2c1fab49df09   3 months ago    2.87GB
enzcheck           v1                      fc432184a3ab   5 months ago    415MB
amazonlinux        latest                  c1c1e5b1da96   6 months ago    143MB
openjdk            18.0.1.1-jdk-bullseye   73415276e8bd   16 months ago   667MB
python             latest                  a5d7930b60cc   22 months ago   917MB
```

### Run container in interactive mode and leave container running

`docker run -id --name python_dev python3`  
The `--name` parameter will be used to more easily reference the container. If no name is provided, a randominzed name is assisgned.

### View all containers (running or not)

`docker ps -a`

```
CONTAINER ID   IMAGE                           COMMAND       CREATED         STATUS                      PORTS     NAMES
b74590299efe   google/cloud-sdk:latest         "bash"        3 months ago    Exited (0) 3 months ago               gcp
72330cfcfee4   amazonlinux                     "/bin/bash"   5 months ago    Exited (0) 3 months ago               aws
7740137bb556   openjdk:18.0.1.1-jdk-bullseye   "jshell"      16 months ago   Exited (0) 3 months ago               java_dev
61686bdb8d31   python                          "python3"     21 months ago   Exited (137) 27 hours ago             python_dev
```

### Stop a container (does not erase data or configuration)

`docker stop <name of container>`  
Example: `docker stop python_dev`

### Start a stopped container

`docker start python_dev`

### Remove a container (erases data and the container will have to be "run" again.)

`docker rm python_dev` or `docker rm <CONTAINER ID>`  
`CONTAINER ID` can be viewed by useing the `docker ps -a` command

### Remove an image from your machine

`docker rmi <IMAGE ID>`  
`<IMAGE ID>` can be found by running the `docker image ls` command

### Running a container with port forwarding

You can expose the containers ports by specifiying the `-p` switch. This will allow you to reach the container from your local host.
`docker run -id --name apache_web -p 80 httpd`

```
docker ps -a
CONTAINER ID   IMAGE                           COMMAND              CREATED         STATUS                      PORTS
   NAMES
a1e73b6c80f5   httpd                           "httpd-foreground"   7 seconds ago   Up 6 seconds                0.0.0.0:32768->80/tcp, :::32768->80/tcp   apache_web
```

**Notice how the PORTS are shown.**  
You can now use a browser to go to http://localhost:32768 and reach the containers exposed port **80**

If you like, you can also specify the local port.  
` docker run -id --name apache_web -p 8080:80 httpd`

```
CONTAINER ID   IMAGE                           COMMAND              CREATED          STATUS                      PORTS
  NAMES
2d706ad8dff3   httpd                           "httpd-foreground"   54 seconds ago   Up 53 seconds               0.0.0.0:8080->80/tcp, :::8080->80/tcp   apache_web
```

**Notice that port 8080 now maps to port 80 on the container.**  
https://localhost:8080 to reach the web server on the container.

## Advanced Docker

### Create an isolated network

`docker network create --driver bridge isolated_nw`  
`docker run -id --network=isolated_nw --name=apache_web httpd`

`isolated_nw` is the name of the network created earlier for sharing dns names
`name` is the container name as well as the `dns name` inside the container

### Map local directory to a directory on the container

`docker run -id --name=apache_web -v ~/apache_files:/tmp/ httpd`

The `-v` commmand (volume) will map your local home directory's `apache_files` to the `/tmp `
