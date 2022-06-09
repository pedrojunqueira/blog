---
title: "Basic CLI commands for Openshift-Minishift"
date: 2020-07-02T20:56:05+09:30
draft: false
toc: false
images:
tags:
  - openshift
  - minishift
  - kubernetes
  - DevOps
  - CLI
---

## Here is a post with the absolute basic commands that I always forget

### Getting the export Path of oc CLI tool

`minishift oc-env`

`export PATH="/Users/pedrojunqueira/.minishift/cache/oc/v3.11.0/darwin:$PATH"`

### Logging in 

to log in

`oc login -u <user> -p <password>`

### Deploy an application from git

`oc new-app https://github.com/sclorg/nodejs-ex -l name=myapp`

## login as sys:admin

`oc login -u system:admin`

get users 

`oc get users`

make an user called administrator a system admin

`oc adm policy add-cluster-role-to-user cluster-admin administrator`

now the administrator user is adm and see all the projects

