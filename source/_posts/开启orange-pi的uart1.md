---
title: 开启orange pi的uart1
tags: []
date: 2016-08-12 15:19:11
---

一、下载最新的sunxin-tools代码并编译

<div class="highlight"><pre>git clone https://github.com/linux-sunxi/sunxi-tools
<span class="nb">cd </span>sunxi-tools
make
</pre></div>

二、将script.bin转化成script.fex

<div class="highlight"><pre>cp /boot/script.bin .
./bin2fex script.bin &gt; script.fex
</pre></div>

三、编辑script.fex，文本中查找uart1位置，替换为如下字符串：

<div class="highlight"><pre>[uart1]
uart_used = 1
uart_port = 1
uart_type = 2
uart_tx = port:PG06&lt;2&gt;&lt;1&gt;&lt;default&gt;&lt;default&gt;
uart_rx = port:PG07&lt;2&gt;&lt;1&gt;&lt;default&gt;&lt;default&gt;
</pre></div>

四、将script.fex还原成script.bin，并替换原有的script.bin

<div class="highlight"><pre>./fex2bin script.fex &gt; script.bin
cp script.bin /boot
</pre></div>