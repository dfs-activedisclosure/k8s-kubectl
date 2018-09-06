# Kubernetes Client

## Supported tags and respective `Dockerfile` links

* `v1.11.1`, `latest`    [(v1.11.1/Dockerfile)](https://github.com/dfs-activedisclosure/k8s-kubectl/blob/v1.11.1/Dockerfile)

## Overview

This container provides the Kubernetes client kubectl which can be used to interact with a Kubernetes cluster

## Build

`make docker_build`

## Run

`docker run --rm adacr.azurecr.io/utilities/k8s-kubectl:``git rev-parse --abbrev-ref HEAD`` --server=http://<server-name>:8080 get pods`

## Data Container

In order to get kube spec files accessible via the kubectl container please use the following data container that exposes a data volume under /data. It dumps everything under cwd in the data container.

``` shell
cat ~/bin/mk-data-container
#!/usr/bin/env sh

WORKDIR="$1"

if [ -z $WORKDIR ]; then
    WORKDIR='.'
fi

cd $WORKDIR
echo "FROM debian:jessie\n\nVOLUME [ '/data' ]\n\nCOPY * /data/" > ./Dockerfile.data-container
docker rm data
docker build -f ./Dockerfile.data-container -t temp/data .
docker run --name data temp/data
rm ./Dockerfile.data-container
```

## Data container with kubectl container

``` shell
docker run --rm -it --volumes-from data k8s/kubectl:<tag> --server=http://<server-name>:8080 create -f /data/controller.yml
```
