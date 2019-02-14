---
title: redis实现事务锁
tags: []
date: 2018-08-20 15:43:46
---

watch 字面就是监视的意思，这里可以看做为数据库中乐观锁的概念，谁都可以读，谁都可以修改，但是修改的人必须保证自己watch的数据没有被别人修改过，否则就修改失败了,如果在watch后值被修改，在执行pipe.execute()的时候会报异常WatchError: Watched variable changed.
这里可以简单的实现基于redis中的锁库存的逻辑。

代码示例:

<div class="highlight"><pre><span></span><span class="kn">import</span> <span class="nn">time</span>
<span class="kn">import</span> <span class="nn">logging</span>

<span class="n">logger</span> <span class="o">=</span> <span class="n">logging</span><span class="o">.</span><span class="n">getLogger</span><span class="p">(</span><span class="vm">__name__</span><span class="p">)</span>

<span class="k">def</span> <span class="nf">do_sync_data</span><span class="p">():</span>
    <span class="k">print</span><span class="p">(</span><span class="s2">&quot;safe sync data&quot;</span><span class="p">)</span>

<span class="k">def</span> <span class="nf">lock_and_sync</span><span class="p">():</span>
    <span class="k">while</span> <span class="bp">True</span><span class="p">:</span>
        <span class="k">with</span> <span class="n">redis_conn</span><span class="o">.</span><span class="n">pipeline</span><span class="p">()</span> <span class="k">as</span> <span class="n">pipe</span><span class="p">:</span>
            <span class="k">try</span><span class="p">:</span>
                <span class="n">pipe</span><span class="o">.</span><span class="n">watch</span><span class="p">(</span><span class="s1">&#39;sync_last_modify_time’)  # 关注一个key</span>
                <span class="n">timestamp</span> <span class="o">=</span> <span class="nb">float</span><span class="p">(</span><span class="n">pipe</span><span class="o">.</span><span class="n">get</span><span class="p">(</span><span class="s1">&#39;sync_last_modify_time&#39;</span><span class="p">))</span>
                <span class="k">if</span> <span class="p">(</span><span class="n">time</span><span class="o">.</span><span class="n">time</span><span class="p">()</span> <span class="o">-</span> <span class="n">timestamp</span><span class="p">)</span> <span class="o">&gt;</span> <span class="mi">600</span><span class="p">:</span>  <span class="c1"># 大于5分钟</span>
                    <span class="n">pipe</span><span class="o">.</span><span class="n">multi</span><span class="p">()</span>  <span class="c1"># 事务开始</span>
                    <span class="n">pipe</span><span class="o">.</span><span class="n">set</span><span class="p">(</span><span class="s1">&#39;sync_last_modify_time&#39;</span><span class="p">,</span> <span class="n">time</span><span class="o">.</span><span class="n">time</span><span class="p">())</span>  <span class="c1"># 更新时间戳</span>
                    <span class="n">pipe</span><span class="o">.</span><span class="n">execute</span><span class="p">()</span>  <span class="c1"># 事务结束</span>
                    <span class="n">do_sync_data</span><span class="p">()</span>  <span class="c1"># do something</span>
            <span class="k">except</span> <span class="ne">Exception</span> <span class="k">as</span> <span class="n">e</span><span class="p">:</span>
                <span class="n">logger</span><span class="o">.</span><span class="n">exception</span><span class="p">(</span><span class="n">e</span><span class="p">)</span>
            <span class="k">except</span> <span class="n">WatchError</span> <span class="k">as</span> <span class="n">e</span><span class="p">:</span>
                <span class="n">logger</span><span class="o">.</span><span class="n">info</span><span class="p">(</span><span class="s2">&quot;Has been locked：</span><span class="si">%s</span><span class="s2">&quot;</span> <span class="o">%</span> <span class="n">e</span><span class="p">)</span>
        <span class="n">time</span><span class="o">.</span><span class="n">sleep</span><span class="p">(</span><span class="mi">5</span><span class="p">)</span>

<span class="k">if</span> <span class="vm">__name__</span> <span class="o">==</span> <span class="s1">&#39;__main__&#39;</span><span class="p">:</span>
    <span class="n">lock_and_sync</span><span class="p">()</span>
</pre></div>