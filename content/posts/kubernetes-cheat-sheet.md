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

`kubectl run nginx --image=nginx`

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


## Creating objects imperatively

`kubectl run nginx --image=nginx`

With Labels

`kubectl run redis --image=redis:alpine -l tier=db`

Create a service

`kubectl expose pod redis --port 6370 --name redis-service -l tier=db`

Deployment

`kubectl create deployment webapp --image=kodekloud/webapp-color`

Scale deployement

`kubectl scale deployment/webapp --replicas=3`

Create a pod and expose a port

`kubectl run custom-nginx --image=nginx --port=8080`

Create a namespace

`kubectl create ns <name>`

Pipe deployment to YAML file

`kubectl create deployment redis-deploy --namespace=dev-ns --image redis --dry-run=client -o yaml > deploy.yaml`


### Replica Sets

`kubectl get replicasets`

use the same commands as pod for describe, edit, delete and also apply manifests

### Deployments

`kubectl create -f deployment.yml`

a deployment automatically creates a replicaset

to see all objects running in the cluster write

`kubectl get all`

### Output format

the kubectl output can be specified with the `-o` argument other than receiving text only

Json 

`-o json`

Just the name

`-o name`

text with a few extra detail

`-o wide`

Yaml

`-o yaml`

## Namespaces

Create a name space

`kubectl create namespace <name>`

get resources in a particular namespace

`kubectl get pods --namespace <namespace name>`

Change namespace context to list resources on the set namespace

`kubectl config set-context $(kubectl config current-context) --namespace=dev`

get resources in all namespaces

`kubectl get pods --all-namespaces`

## Secrets

Create a secret

```bash
kubectl create secret generic \
    <secret name> --from-literal=<key>=<value>
```
