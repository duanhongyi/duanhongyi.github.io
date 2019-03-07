---
title: 在国内通过kubespray安装kubernetes集群
tags: []
date: 2018-10-31 22:35:23
---

# 生成剧本

```
cp -rfp inventory/sample inventory/drycc
declare -a IPS=(10.1.80.51 10.1.80.52 10.1.80.53 10.1.80.54)
CONFIG_FILE=inventory/drycc/hosts.ini python3 contrib/inventory_builder/inventory.py
```

# 修改kubespray的docker registry

修改配置文件 `inventory/deis/group_vars/all/docker.yml`中的docker_registry_mirrors和docker_insecure_registries:

```
docker_insecure_registries:
  - gcr.io
  - quay.io

docker_registry_mirrors:
  - https://registry.docker-cn.com
```

# 添加科学安装kubernetes，需要在海外启动docker registry代理

可在阿里云香港云机上启动docker registry代理

```
git clone https://github.com/duanhongyi/docker-mirrors
./start.sh
```

修改DNS，将gcr.io和quay.io这两个域名劫持到该云主机
