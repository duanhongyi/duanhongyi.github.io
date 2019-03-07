---
title: Install NFS and Minio on k8s
date: 2019-03-07 08:55:09
tags:
---

# Install nfs-client-provisioner

```
helm install --name k8s-nfs \
  --set nfs.server=180.76.112.88 \
  --set nfs.path=/nas/k8s-nfs/baike \
  stable/nfs-client-provisioner
```

# Installing Minio in nasgateway mode

```
helm install --name minio \
  --set persistence.storageClass=nfs-client \
  --set persistence.size=100G \
  --set service.type=NodePort \
  --set service.nodePort=31053 \
  --set nasgateway.enabled=true \
  --set nasgateway.replicas=3 \
  stable/minio
```
