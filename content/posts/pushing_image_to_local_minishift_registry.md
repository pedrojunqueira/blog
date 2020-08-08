---
title: "Pushing_image_to_local_minishift_registry"
date: 2020-08-09T07:52:24+09:30
draft: true
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



