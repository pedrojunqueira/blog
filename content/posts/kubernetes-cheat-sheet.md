---
title: "Kubernetes Cheat Sheet"
date: 2020-07-10T06:02:26+09:30
draft: false
toc: false
images:
tags:
  - kubernetes
  - containers
  - DevOps
---

## This is the most handy Kubernetes kubectl commands

### Pods

List all pods

`kubectl get pods`

Create a pod from CLI

`kubectl run nginx image=nginx`

Describe the pod details

`kubectl describe pod <name>`

Get pods listed with more details e.g. which nodes are running

`kubectl get pods -o wide`

Open a vim editor to edit the pod yaml definition file

`kubeclt edit pod <podname>`

Create/Reconfigure a pod based on a manifest file

`kubectl apply -f manifest.yaml`

Display the contents of a running pod yaml file

`kubectl get pod <pod-name> -o yaml`

Create a definition file from an existing one

`kubectl get pod <pod-name> -o yaml > pod-definition.yaml`

### Replica Sets


