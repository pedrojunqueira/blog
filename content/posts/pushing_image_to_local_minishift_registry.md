---
title: "Pushing image to local minishift registry"
date: 2020-08-09T07:52:24+09:30
draft: false
toc: false
images:
tags:
  - kubernetes
  - openshift
  - docker
  - DevOps
---

I am learning OpenShift as I am using it more and more at work and it has been great fun and I am really enjoying the DevOps space which is giving me new super powers as a developer.

It is not the intent of this post explain OpenShift but in summary is a RedHat product to manage a Kubernetes cluster. There are some peculiarities of OpenShift but it is pretty much Kubernetes in 98% of cases. It is just a matter of getting used to it.

OpenShift has a command line `oc` which most of the `kubectl` commands will work.

This post is a walk through on hot to push an image to a local registry.

I am testing a project to deploy airflow in openshift at work and here is my lab 😀.

To learn this I am using a template I found in [GitHub](https://github.com/kbristow/airflow-openshift)

This project is using a template and it is assuming that the images need to be already pushed to the project local registry.

What you need to do first is to make sure your Docker is activated in Minishift

Assuming you are starting from the absolute zero the steps are.

1. Start Minishift

`minishift start`

2. Configure [Minishift Docker Daemon](https://docs.okd.io/3.11/minishift/using/docker-daemon.html)

`minishift docker-env`

```bash
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.101:2376"
export DOCKER_CERT_PATH="/Users/john/.minishift/certs"
export DOCKER_API_VERSION="1.24"
```

Run this command to configure your shell:

`eval $(minishift docker-env)`

to test check processes

`docker ps`

You should see a lot of things running

Also list the images

`docker image ls`

you should see only image of your minishift projects and registry

3. Login a a developer

`oc login`

4. Login to your local minishift register

`docker login -u developer -p $(oc whoami -t) $(minishift openshift registry)`

5. Pull an existing image from a registry or build your image from a Docker file

This step depends what you wanted to do. 

In my case I like to keep my images in Docker Hub then what I do is to pull the image from Docker hub

`docker pull dockerhubid/image:tag`

6. Tag the image with your local minishift register

`docker tag dockerhubid/image:tag $(minishift openshift registry)/myproject/image:tag`

7. Push image

`docker push $(minishift openshift registry)/myproject/image:tag`







