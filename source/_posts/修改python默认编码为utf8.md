---
title: 修改python默认编码为utf8
tags: []
date: 2014-02-21 14:55:30
---

为了让python2.7更好的兼容汉字，将python的默认编码更改为utf8.

1.  打开配置文件

    > <div class="highlight"><pre>sudo vim /usr/lib/python2.7/site.py
> </pre></div>

2.  找到setencoding()函数，替换以下内容，保存即可

    > <div class="highlight"><pre>encoding = &quot;utf8&quot; # encoding = &quot;ascii&quot;
> </pre></div>