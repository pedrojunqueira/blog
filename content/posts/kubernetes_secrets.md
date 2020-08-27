---
title: "How to create a Kubernetes Secret"
date: 2020-08-26T06:38:52+09:30
draft: false
toc: false
images:
tags:
  - kubernetes
---

Creating secret imperatively

```bash
kubectl create secret generic \
    <secret name> --from-literal=<key>=<value>
```

`kubectl explain pods --recursive | less `

`kubectl explain pods --recursive | grep -A8 envFrom` 

create secrete base64

```
$ echo -n "secret" | base64
c2VjcmV0
```

decode

```
$ echo -n "c2VjcmV0" | base64 --decode
secret
```

Secret definition file

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
  
```

injecting in through a volume in a pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-test-pod
spec:
  containers:
    - name: test-container
      image: nginx
      volumeMounts:
        # name must match the volume name below
        - name: secret-volume
          mountPath: /etc/secret-volume
  # The secret data is exposed to Containers in the Pod through a Volume.
  volumes:
    - name: secret-volume
      secret:
        secretName: mysecret        
```
Injecing as environment variable

```yaml

apiVersion: v1
kind: Pod
metadata:
  name: envfrom-secret
spec:
  containers:
  - name: envars-test-container
    image: nginx
    envFrom:
    - secretRef:
        name: test-secret
```

