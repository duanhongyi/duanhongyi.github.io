---
title: django自定义listfield并集成tastypie
tags: []
date: 2014-03-05 17:05:38
---

Django本身并不支持listfield，本文将使用`models.SubfieldBase'元类实现自定义的ListField功能。

<div class="highlight"><pre><span class="kn">import</span> <span class="nn">ast</span>
<span class="kn">import</span> <span class="nn">json</span>
<span class="kn">from</span> <span class="nn">django.db</span> <span class="kn">import</span> <span class="n">models</span>
<span class="kn">from</span> <span class="nn">django.utils.six</span> <span class="kn">import</span> <span class="n">with_metaclass</span>

<span class="k">class</span> <span class="nc">ListField</span><span class="p">(</span><span class="n">with_metaclass</span><span class="p">(</span><span class="n">models</span><span class="o">.</span><span class="n">SubfieldBase</span><span class="p">,</span> <span class="n">models</span><span class="o">.</span><span class="n">TextField</span><span class="p">)):</span>
    <span class="n">description</span> <span class="o">=</span> <span class="s">&quot;Stores a python list&quot;</span>

    <span class="k">def</span> <span class="nf">__init__</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="o">*</span><span class="n">args</span><span class="p">,</span> <span class="o">**</span><span class="n">kwargs</span><span class="p">):</span>
        <span class="nb">super</span><span class="p">(</span><span class="n">ListField</span><span class="p">,</span> <span class="bp">self</span><span class="p">)</span><span class="o">.</span><span class="n">__init__</span><span class="p">(</span><span class="o">*</span><span class="n">args</span><span class="p">,</span> <span class="o">**</span><span class="n">kwargs</span><span class="p">)</span>

    <span class="k">def</span> <span class="nf">to_python</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">value</span><span class="p">):</span>
        <span class="k">if</span> <span class="ow">not</span> <span class="n">value</span><span class="p">:</span>
            <span class="n">value</span> <span class="o">=</span> <span class="p">[]</span>

        <span class="k">if</span> <span class="nb">isinstance</span><span class="p">(</span><span class="n">value</span><span class="p">,</span> <span class="nb">list</span><span class="p">):</span>
            <span class="k">return</span> <span class="n">value</span>

        <span class="k">return</span> <span class="n">ast</span><span class="o">.</span><span class="n">literal_eval</span><span class="p">(</span><span class="n">value</span><span class="p">)</span>

    <span class="k">def</span> <span class="nf">get_prep_value</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">value</span><span class="p">):</span>
        <span class="k">if</span> <span class="n">value</span> <span class="ow">is</span> <span class="bp">None</span><span class="p">:</span>
            <span class="k">return</span> <span class="n">value</span>

        <span class="k">return</span> <span class="nb">unicode</span><span class="p">(</span><span class="n">value</span><span class="p">)</span>

    <span class="k">def</span> <span class="nf">value_to_string</span><span class="p">(</span><span class="bp">self</span><span class="p">,</span> <span class="n">obj</span><span class="p">):</span>
        <span class="n">value</span> <span class="o">=</span> <span class="bp">self</span><span class="o">.</span><span class="n">_get_val_from_obj</span><span class="p">(</span><span class="n">obj</span><span class="p">)</span>
        <span class="k">return</span> <span class="bp">self</span><span class="o">.</span><span class="n">get_prep_value</span><span class="p">(</span><span class="n">value</span><span class="p">)</span>
</pre></div>

tastypie默认的`ModelResource`并不能支持listfiled这个自定义字段，所以listfield会被转换为父类TextField方式处理，对此我们需要显示声明ListField字段。

<div class="highlight"><pre><span class="k">class</span> <span class="nc">PoiResource</span><span class="p">(</span><span class="n">ModelResource</span><span class="p">):</span>

    <span class="n">tags</span> <span class="o">=</span> <span class="n">fields</span><span class="o">.</span><span class="n">ListField</span><span class="p">(</span><span class="n">attribute</span><span class="o">=</span><span class="s">&#39;tag&#39;</span><span class="p">)</span>

    <span class="k">class</span> <span class="nc">Meta</span><span class="p">:</span>
        <span class="n">queryset</span> <span class="o">=</span> <span class="n">Poi</span><span class="o">.</span><span class="n">objects</span><span class="o">.</span><span class="n">all</span><span class="p">()</span>
        <span class="n">resource_name</span> <span class="o">=</span> <span class="s">&#39;poi&#39;</span>
        <span class="n">authorization</span> <span class="o">=</span> <span class="n">Authorization</span><span class="p">()</span>
</pre></div>