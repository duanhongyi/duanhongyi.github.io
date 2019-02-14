---
title: SSL behind an Nginx Reverse Proxy in Django 1.4
tags: []
date: 2014-08-12 16:41:01
---

1、Have the proxy server (nginx in our case) set the X-Forwarded-Protocol HTTP header. In the nginx config:

<div class="highlight"><pre><span class="k">proxy_set_header</span> <span class="s">X-Forwarded-Protocol</span> <span class="nv">$scheme</span><span class="p">;</span>
</pre></div>

2、Tell Django to use the X-Forwarded-Protocol HTTP header to determine if the request is secure. We define SECURE_PROXY_SSL_HEADER in settings.py:

<div class="highlight"><pre><span class="n">SECURE_PROXY_SSL_HEADER</span> <span class="o">=</span> <span class="p">(</span><span class="s">&#39;HTTP_X_FORWARDED_PROTOCOL&#39;</span><span class="p">,</span> <span class="s">&#39;https&#39;</span><span class="p">)</span>
</pre></div>