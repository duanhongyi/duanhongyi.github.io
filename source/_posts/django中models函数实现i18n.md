---
title: django中models函数实现i18n
tags: []
date: 2014-02-20 11:14:37
---

我们在项目中经常会在django的models中添加一些方法，用来作只读属性，但是这些方法怎么作国际化处理呢？请参考下面代码中的is_published属性。

<div class="section" id="s-">

#### 代码实例：

<div class="highlight"><pre><span class="k">class</span> <span class="nc">Entry</span><span class="p">(</span><span class="n">models</span><span class="o">.</span><span class="n">Model</span><span class="p">):</span>
    <span class="n">is_active</span> <span class="o">=</span> <span class="n">models</span><span class="o">.</span><span class="n">BooleanField</span><span class="p">(</span><span class="n">_</span><span class="p">(</span><span class="s">&quot;is active&quot;</span><span class="p">),</span> <span class="n">help_text</span><span class="o">=</span><span class="n">_</span><span class="p">(</span>
        <span class="s">&quot;Tick to make this entry live (see also the publication date).&quot;</span>
        <span class="s">&quot; Note that administrators (like yourself) are allowed to preview &quot;</span>
        <span class="s">&quot;inactive entries whereas the general public aren&#39;t.&quot;</span><span class="p">),</span>
        <span class="n">default</span><span class="o">=</span><span class="bp">False</span><span class="p">,</span>
        <span class="n">db_index</span><span class="o">=</span><span class="bp">True</span><span class="p">,</span>
    <span class="p">)</span>
    <span class="n">pub_date</span> <span class="o">=</span> <span class="n">models</span><span class="o">.</span><span class="n">DateTimeField</span><span class="p">(</span><span class="n">verbose_name</span><span class="o">=</span><span class="n">_</span><span class="p">(</span><span class="s">&quot;Publication date&quot;</span><span class="p">),</span>
        <span class="n">help_text</span><span class="o">=</span><span class="n">_</span><span class="p">(</span><span class="s">&quot;For an entry to be published, &quot;</span>
        <span class="s">&quot;it must be active and its publication date must be in the past.&quot;</span><span class="p">),</span>
        <span class="n">default</span><span class="o">=</span><span class="n">now</span><span class="p">(),</span>
        <span class="n">db_index</span><span class="o">=</span><span class="bp">True</span><span class="p">,</span>
    <span class="p">)</span>

    <span class="k">def</span> <span class="nf">is_published</span><span class="p">(</span><span class="bp">self</span><span class="p">):</span>
        <span class="sd">&quot;&quot;&quot;</span>
<span class="sd">        Return True if the entry is publicly accessible.</span>
<span class="sd">        &quot;&quot;&quot;</span>
        <span class="k">return</span> <span class="bp">self</span><span class="o">.</span><span class="n">is_active</span> <span class="ow">and</span> <span class="bp">self</span><span class="o">.</span><span class="n">pub_date</span> <span class="o">&lt;=</span> <span class="n">now</span><span class="p">()</span>
    <span class="n">is_published</span><span class="o">.</span><span class="n">boolean</span> <span class="o">=</span> <span class="bp">True</span>
    <span class="n">is_published</span><span class="o">.</span><span class="n">short_description</span> <span class="o">=</span> <span class="n">ugettext</span><span class="p">(</span><span class="s">&#39;is published&#39;</span><span class="p">)</span>  <span class="c"># i18n</span>
</pre></div>
</div>