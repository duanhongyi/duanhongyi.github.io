---
title: centos下测试flannel的vxlan和udp模式
tags: []
date: 2016-09-22 15:17:50
---

分别使用flannel的udp和vxlan模式下生成两台docker容器，一台作为iperf服务器，另一台作为客户端，运行如下命令：

<div class="highlight"><pre>iperf -s -i <span class="m">1</span> -w 1M
iperf -c host -i <span class="m">1</span> -w 1M
</pre></div>

每次测试后需要执行命令:

<div class="highlight"><pre>etcdctl rm --recursive /atomic.io/network/subnets
</pre></div>

删除子网，并重启flannel和docker服务，生成新的网络配置，结果如下：

udp

<div class="highlight"><pre><span class="o">[</span> ID<span class="o">]</span> Interval       Transfer     Bandwidth
<span class="o">[</span>  3<span class="o">]</span>  0.0- 1.0 sec  13.6 MBytes   <span class="m">114</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  1.0- 2.0 sec  13.2 MBytes   <span class="m">111</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  2.0- 3.0 sec  9.25 MBytes  77.6 Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  3.0- 4.0 sec  11.4 MBytes  95.4 Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  4.0- 5.0 sec  13.5 MBytes   <span class="m">113</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  5.0- 6.0 sec  14.6 MBytes   <span class="m">123</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  6.0- 7.0 sec  13.4 MBytes   <span class="m">112</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  7.0- 8.0 sec  12.6 MBytes   <span class="m">106</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  8.0- 9.0 sec  15.0 MBytes   <span class="m">126</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  9.0-10.0 sec  12.4 MBytes   <span class="m">104</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  0.0-10.0 sec   <span class="m">129</span> MBytes   <span class="m">108</span> Mbits/sec
</pre></div>

vxlan

<div class="highlight"><pre><span class="o">[</span> ID<span class="o">]</span> Interval       Transfer     Bandwidth
<span class="o">[</span>  3<span class="o">]</span>  0.0- 1.0 sec  36.4 MBytes   <span class="m">305</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  1.0- 2.0 sec  34.8 MBytes   <span class="m">292</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  2.0- 3.0 sec  39.4 MBytes   <span class="m">330</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  3.0- 4.0 sec  35.9 MBytes   <span class="m">301</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  4.0- 5.0 sec  38.9 MBytes   <span class="m">326</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  5.0- 6.0 sec  38.5 MBytes   <span class="m">323</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  6.0- 7.0 sec  35.4 MBytes   <span class="m">297</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  7.0- 8.0 sec  37.2 MBytes   <span class="m">312</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  8.0- 9.0 sec  40.0 MBytes   <span class="m">336</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  9.0-10.0 sec  35.8 MBytes   <span class="m">300</span> Mbits/sec
<span class="o">[</span>  3<span class="o">]</span>  0.0-10.0 sec   <span class="m">372</span> MBytes   <span class="m">312</span> Mbits/sec
</pre></div>

实验了大约4次，udp模式比vxlan模式的性能确实差了好多，至少在虚拟机上是这样，可能物理机udp的性能会稍微好一些。

测试环境如下：

<div class="highlight"><pre>处理器： 2.59 GHz Intel Core i5
内存：16 GB 1600 MHz DDR3
操作系统: OSX 10.11
虚拟化软件: virtualbox
虚拟化操作系统: centos7
</pre></div>