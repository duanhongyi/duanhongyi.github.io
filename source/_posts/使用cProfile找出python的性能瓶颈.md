---
title: 使用cProfile找出python的性能瓶颈
tags: []
date: 2018-10-10 10:50:09
---

Python自带了几个性能分析的模块：profile、cProfile；cProfile基于lsprof的用C语言实现的扩展应用，运行开销比较合理，适合分析运行时间较长的程序；profile纯Python实现的性能分析模块，接口和cProfile一致。但在分析程序时增加了很大的运行开销。不过，如果你想扩展profiler的功能，可以通过继承这个模块实现；本文以cProfile为例。

# 运行如下命令：

<div class="highlight"><pre><span></span>python -m cProfile -s cumulative {CMD} &gt; profile.log
</pre></div>

其中{CMD}为正常使用程序的原始命令，以gunicorn为例：

<div class="highlight"><pre><span></span>python -m cProfile ../env/bin/gunicorn -c gunicorn_config.py &gt; profile.log
</pre></div>

# 分析profile.log结果：

<div class="highlight"><pre><span></span>5132124 function calls (5012662 primitive calls) in 17.813 seconds
Ordered by: cumulative time
ncalls  tottime  percall  cumtime  percall filename:lineno(function)
     1    0.000    0.000   16.727   16.727 server.py:52(serve_forever)
1980/2    0.012    0.000    7.679    3.839 query.py:171(_get_records)
1000/1    0.007    0.000    7.477    7.477 socketserver.py:610(process_request_thread)
1000/1    0.007    0.000    7.477    7.477 socketserver.py:342(finish_request)
1000/1    0.011    0.000    7.477    7.477 socketserver.py:667(__init__)
1000/1    0.015    0.000    7.477    7.477 server.py:126(handle)
1000/1    0.015    0.000    7.477    7.477 server.py:156(get_reply)
3673/6    0.030    0.000    7.477    1.246 query.py:99(_recursive_query)
1000/1    0.012    0.000    7.476    7.476 server.py:26(resolve)
</pre></div>

ncalls：表示函数调用的次数

tottime：函数的总的运行时间，除掉函数中调用子函数的运行时间

percall：第一个percall，等于 tottime/ncalls

cumtime：函数及其所有子函数的调用运行的时间

percall：第二个percall，函数运行一次的平均时间，等于 cumtime/ncalls

filename:lineno(function)：每个函数调用的具体信息