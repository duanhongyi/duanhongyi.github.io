---
title: install normandy vampires web to z83xx
tags: []
date: 2018-08-17 17:18:30
---

# update nss

<div class="highlight"><pre><span></span>sudo apt-get update
sudo apt-get install libnss3
</pre></div>

# sudo nopasswd

<div class="highlight"><pre><span></span>chmod u+w /etc/sudoers
echo &quot;zhumengyuan        ALL=(ALL)       NOPASSWD: ALL&quot; &gt;&gt; /etc/sudoers
chmod u-w /etc/sudoers
</pre></div>

# install python3-setuptools vlc

<div class="highlight"><pre><span></span>sudo apt-get install python3-setuptools
sudo apt-get install vlc
</pre></div>

# install intel driver

<div class="highlight"><pre><span></span>wget https://download.01.org/gfx/ubuntu/16.04/main/pool/main/i/intel-graphics-update-tool/intel-graphics-update-tool_2.0.2_amd64.deb
sudo dpkg -i intel-graphics-update-tool_2.0.2_amd64.deb
sudo intel-graphics-update-tool 
sudo apt-get install -f
sudo intel-graphics-update-tool 
wget $(echo &quot;https://download&quot;.01.org/gfx/RPM-GPG-GROUP-KEY-ilg) -O - | sudo apt-key add -
sudo apt-get update
</pre></div>

# install realtek driver

<div class="highlight"><pre><span></span>wget https://az695102.vo.msecnd.net/rtdrivers/cn/nic/0012-r8168-8.046.00.tar.bz2
sudo apt-get install make gcc libelf-dev
sudo tar -xvzf 0012-r8168-8.046.00.tar.bz2
sudo ./autorun.sh
</pre></div>