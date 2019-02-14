---
title: nginx反向代理基本配置
tags: []
date: 2014-02-21 11:29:51
---

<div class="section" id="s-">

#### 配置实例:

> <div class="highlight"><pre><span class="k">upstream</span> <span class="s">backend</span> <span class="p">{</span>
>     <span class="kn">server</span> <span class="n">127.0.0.1</span><span class="p">:</span><span class="mi">18001</span> <span class="s">weight=5</span><span class="p">;</span>
>     <span class="kn">server</span> <span class="s">unix:/tmp/backend3</span><span class="p">;</span>
> <span class="p">}</span>
> 
> <span class="k">server</span> <span class="p">{</span>
>     <span class="kn">listen</span> <span class="mi">80</span><span class="p">;</span>
>     <span class="kn">location</span> <span class="s">/</span> <span class="p">{</span>
>         <span class="kn">underscores_in_headers</span> <span class="no">on</span><span class="p">;</span>
>         <span class="kn">proxy_pass</span> <span class="s">http://backend</span><span class="p">;</span>
>         <span class="kn">proxy_set_header</span> <span class="s">Host</span> <span class="nv">$host</span><span class="p">;</span>
>         <span class="kn">proxy_set_header</span> <span class="s">X-Url-Scheme</span> <span class="nv">$scheme</span><span class="p">;</span>
>         <span class="kn">proxy_set_header</span> <span class="s">X-Host</span> <span class="nv">$http_host</span><span class="p">;</span>
>         <span class="kn">proxy_set_header</span> <span class="s">X-Real-IP</span> <span class="nv">$remote_addr</span><span class="p">;</span>
>         <span class="kn">proxy_set_header</span> <span class="s">X-Forwarded-For</span> <span class="nv">$proxy_add_x_forwarded_for</span><span class="p">;</span>
>         <span class="kn">proxy_set_header</span> <span class="s">Proxy-host</span> <span class="nv">$proxy_host</span><span class="p">;</span>
>         <span class="kn">proxy_redirect</span> <span class="s">http://</span> <span class="nv">$scheme://</span><span class="p">;</span>
>         <span class="kn">client_max_body_size</span> <span class="mi">400m</span><span class="p">;</span>
>         <span class="kn">client_body_buffer_size</span> <span class="mi">128k</span><span class="p">;</span>
>         <span class="kn">proxy_buffering</span> <span class="no">off</span><span class="p">;</span>
>         <span class="kn">proxy_connect_timeout</span> <span class="mi">1200</span><span class="p">;</span>
>         <span class="kn">proxy_send_timeout</span> <span class="mi">1200</span><span class="p">;</span>
>         <span class="kn">proxy_read_timeout</span> <span class="mi">1200</span><span class="p">;</span>
>         <span class="kn">proxy_buffers</span> <span class="mi">8</span> <span class="mi">32k</span><span class="p">;</span>
>     <span class="p">}</span>
> <span class="p">}</span>
> </pre></div>
</div>