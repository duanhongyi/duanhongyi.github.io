---
title: 在国内通过kubespray安装kubernetes集群
tags: []
date: 2018-10-31 22:35:23
---

# 生成剧本

<div class="highlight"><pre><span></span>cp -rfp inventory/sample inventory/deis
declare -a IPS=(10.1.80.51 10.1.80.52 10.1.80.53 10.1.80.54)
CONFIG_FILE=inventory/deis/hosts.ini python3 contrib/inventory_builder/inventory.py <span class="cp">${</span><span class="n">IPS</span><span class="p">[</span><span class="err">@</span><span class="p">]</span><span class="cp">}</span>
</pre></div>

# 修改kubespray的docker registry

修改配置文件 `inventory/deis/group_vars/all/docker.yml`中的docker_registry_mirrors节点

<div class="highlight"><pre><span></span>docker_registry_mirrors:
  - https://registry.docker-cn.com
</pre></div>

# 添加海外代理科学安装kubernetes

修改配置文件`inventory/deis/group_vars/all/all.yaml`中的`http_proxy`和`https_proxy`以及`no_proxy`

<div class="highlight"><pre><span></span>http_proxy: &quot;http://203.189.234.212:3128&quot;
https_proxy: &quot;https://203.189.234.212:3128&quot;
no_proxy: &quot;localhost,127.0.0.1,registry.docker-cn.com&quot;
</pre></div>