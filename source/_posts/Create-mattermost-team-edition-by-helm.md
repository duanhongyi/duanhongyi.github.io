---
title: Create mattermost-team-edition by helm
date: 2019-02-21 10:03:49
tags: k8s helm mattermost mattermost-team-edition
---

Mattermost 不仅运用灵活，更是杰出的协作工具，可让部门或团队成员进行公开或私人对话、文件传输，并运用更多帮助提高生产力的实用功能。

```
helm install stable/mattermost-team-edition \
  --name mattermost-team-edition \
  --namespace mattermost-team-edition \
  --set persistence.data.enabled=false \
  --set mysql.enabled=false \
  --set service.type=NodePort \
  --set externalDB.enabled=true \
  --set externalDB.externalDriverType="postgres" \
  --set externalConnectionString="postgres://user:passwd@host:port/database?sslmode=disable&connect_timeout=10"
```
