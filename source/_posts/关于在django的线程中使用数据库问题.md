---
title: 关于在django的线程中使用数据库问题
tags: []
date: 2016-07-25 17:12:35
---

大家都知道在django使用数据库不需要做关闭操作，因为django中间件会帮助销毁相关的数据库连接；
但是大家在程序中可能操作数据库是在线程或进程里进行的，这样的连接因为不在view中生成，所以django框架不会进行销毁，最终导致数据库连接泄漏问题。

详情请看：
[https://github.com/django/django/blob/master/django/db/__init__.py](https://github.com/django/django/blob/master/django/db/__init__.py) 的最后一行代码：

<div class="highlight"><pre><span class="k">def</span> <span class="nf">close_old_connections</span><span class="p">(</span><span class="o">**</span><span class="n">kwargs</span><span class="p">):</span>
    <span class="k">for</span> <span class="n">conn</span> <span class="ow">in</span> <span class="n">connections</span><span class="o">.</span><span class="n">all</span><span class="p">():</span>
        <span class="n">conn</span><span class="o">.</span><span class="n">close_if_unusable_or_obsolete</span><span class="p">()</span>
<span class="n">signals</span><span class="o">.</span><span class="n">request_started</span><span class="o">.</span><span class="n">connect</span><span class="p">(</span><span class="n">close_old_connections</span><span class="p">)</span>
<span class="n">signals</span><span class="o">.</span><span class="n">request_finished</span><span class="o">.</span><span class="n">connect</span><span class="p">(</span><span class="n">close_old_connections</span><span class="p">)</span>
</pre></div>

可以看到数据库的连接和关闭监听的是request_started和request_finished事件；即所有没有经过request产生的数据库连接，django框架都不销毁。

解决方法有两种：

1、在进程/线程结束时手工调用close_old_connections。

2、在进程/线程开始和结束时分别调用request_started、request_finished这两个事件。

两种方法互有利弊，希望大家辩证的看待选型，通常为了简单，可在项目中统一采用一种方式，如下所示：

<div class="highlight"><pre><span class="kn">import</span> <span class="nn">logging</span>
<span class="kn">import</span> <span class="nn">queue</span>
<span class="kn">import</span> <span class="nn">threading</span>

<span class="kn">from</span> <span class="nn">django.core</span> <span class="kn">import</span> <span class="n">signals</span>
<span class="kn">from</span> <span class="nn">.helper</span> <span class="kn">import</span> <span class="n">recognize_task</span>

<span class="n">logger</span> <span class="o">=</span> <span class="n">logging</span><span class="o">.</span><span class="n">getLogger</span><span class="p">(</span><span class="s">&quot;default&quot;</span><span class="p">)</span>

<span class="k">class</span> <span class="nc">RecognizeTask</span><span class="p">(</span><span class="nb">object</span><span class="p">):</span>

    <span class="k">def</span> <span class="nf">__init__</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">queue</span> <span class="o">=</span> <span class="n">queue</span><span class="o">.</span><span class="n">Queue</span><span class="p">()</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">started</span> <span class="o">=</span> <span class="bp">False</span>

    <span class="k">def</span> <span class="nf">add_task</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="o">*</span><span class="n">args</span><span class="p">,</span> <span class="o">**</span><span class="n">kwargs</span><span class="p">):</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">queue</span><span class="o">.</span><span class="n">put</span><span class="p">((</span><span class="n">args</span><span class="p">,</span> <span class="n">kwargs</span><span class="p">))</span>

    <span class="k">def</span> <span class="nf">task</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="k">while</span> <span class="bp">self</span><span class="o">.</span><span class="n">started</span><span class="p">:</span>
            <span class="k">try</span><span class="p">:</span>
                <span class="n">args</span><span class="p">,</span> <span class="n">kwargs</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">queue</span><span class="o">.</span><span class="n">get</span><span class="p">()</span>
                <span class="k">if</span> <span class="ow">not</span> <span class="n">args</span> <span class="ow">and</span> <span class="ow">not</span> <span class="n">kwargs</span><span class="p">:</span>
                    <span class="k">break</span>
                <span class="k">try</span><span class="p">:</span>
                    <span class="n">signals</span><span class="o">.</span><span class="n">request_started</span><span class="o">.</span><span class="n">send</span><span class="p">(</span><span class="bp">self</span><span class="p">)</span>
                    <span class="n">recognize_task</span><span class="p">(</span><span class="o">*</span><span class="n">args</span><span class="p">,</span> <span class="o">**</span><span class="n">kwargs</span><span class="p">)</span>
                <span class="k">finally</span><span class="p">:</span>
                    <span class="n">signals</span><span class="o">.</span><span class="n">request_finished</span><span class="o">.</span><span class="n">send</span><span class="p">(</span><span class="bp">self</span><span class="p">)</span>
            <span class="k">except</span> <span class="ne">BaseException</span> <span class="k">as</span> <span class="n">e</span><span class="p">:</span>
                <span class="n">logger</span><span class="o">.</span><span class="n">exception</span><span class="p">(</span><span class="n">e</span><span class="p">)</span>

    <span class="k">def</span> <span class="nf">start</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="k">if</span> <span class="ow">not</span> <span class="bp">self</span><span class="o">.</span><span class="n">started</span><span class="p">:</span>
            <span class="bp">self</span><span class="o">.</span><span class="n">started</span> <span class="o">=</span> <span class="bp">True</span>
            <span class="n">thread</span> <span class="o">=</span> <span class="n">threading</span><span class="o">.</span><span class="n">Thread</span><span class="p">(</span><span class="n">target</span><span class="o">=</span><span class="bp">self</span><span class="o">.</span><span class="n">task</span><span class="p">)</span>
            <span class="n">thread</span><span class="o">.</span><span class="n">setDaemon</span><span class="p">(</span><span class="bp">True</span><span class="p">)</span>
            <span class="n">thread</span><span class="o">.</span><span class="n">start</span><span class="p">()</span>

    <span class="k">def</span> <span class="nf">stop</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">started</span> <span class="o">=</span> <span class="bp">False</span>
        <span class="bp">self</span><span class="o">.</span><span class="n">queue</span><span class="o">.</span><span class="n">put</span><span class="p">((</span><span class="bp">None</span><span class="p">,</span> <span class="bp">None</span><span class="p">))</span>
</pre></div>

在调用方法recognize_task的前后分别发送request_started和request_finished事件。