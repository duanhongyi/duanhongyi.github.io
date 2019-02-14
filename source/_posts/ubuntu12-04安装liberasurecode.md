---
title: ubuntu12.04安装liberasurecode
tags: []
date: 2015-09-24 15:35:51
---

ubuntu在14.04以后的版本才包含liberasurecode库，所以如果想在低版本的服务器上安装liberasurecode就需要自行编译，编译方法如下：

<div class="highlight"><pre>git clone https://bitbucket.org/tsg-/liberasurecode
sudo apt-get install autoconf automake libtool
<span class="nb">cd </span>liberasurecode
./autogen.sh
./configure
make
make install
</pre></div>