---
title: ubuntu12.04升级到postgis2.0数据库
tags: []
date: 2014-02-19 23:50:31
---

PostGIS 使得 PostgreSQL 数据库支持对地理数据的存储。2.0 版本包含大量新特性，如支持栅格数据和栅格/矢量分析，支持Postgres9.1扩展系统，允许在共享的边界上处理对象，支持 3D 和 4D 索引等等。本文主要内容是如何在ubuntu12.04上将Postgis升级到2.0版本。

1.  停止关联程序

    > <div class="highlight"><pre>supervisorctl stop uuboard
> </pre></div>

2.  升级数据库

    > <div class="highlight"><pre>sudo apt-get install python-software-properties
> sudo apt-add-repository ppa:ubuntugis/ppa
> sudo apt-get update
> sudo apt-get install postgresql-9.1-postgis
> sudo apt-get install postgis
> </pre></div>

3.  备份数据

    > <div class="highlight"><pre>pg_dump -h localhost -p 5432 -U postgres -Fc -b -v -f &quot;olddb.backup&quot; uuboard
> </pre></div>

4.  创建新数据库

    > <div class="highlight"><pre>CREATE DATABASE uuboard_new WITH OWNER = postgres ENCODING = &#39;UTF8&#39; TABLESPACE = pg_default LC_COLLATE = &#39;zh_CN.UTF-8&#39; LC_CTYPE = &#39;zh_CN.UTF-8&#39; CONNECTION LIMIT = -1;
> </pre></div>

5.  创建扩展

    > <div class="highlight"><pre>psql -d uuboard_new -c &quot;CREATE EXTENSION postgis;&quot;
> psql -d uuboard_new -c &quot;CREATE EXTENSION postgis_topology;&quot;
> psql -d uuboard_new -f /usr/share/postgresql/9.1/contrib/postgis-2.0/legacy.sql
> </pre></div>

6.  恢复数据

    > <div class="highlight"><pre>perl /usr/share/postgresql-9.1-postgis/utils/postgis_restore.pl &quot;olddb.backup&quot; | psql -h localhost -p 5432 -U postgres uuboard_new 2&gt; errors.txt
> </pre></div>

7.  清理升级数据

    > <div class="highlight"><pre>psql -d uuboard_new -f /usr/share/postgresql/9.1/contrib/postgis-2.0/uninstall_legacy.sql
> </pre></div>

8.  重命名数据库

    > <div class="highlight"><pre>psql -c &quot;ALTER DATABASE uuboard RENAME TO uuboard_backup&quot;
> psql -c &quot;ALTER DATABASE uuboard_new RENAME TO uuboard&quot;
> </pre></div>

9.  查询是否升级成功

    > <div class="highlight"><pre>psql -d uuboard -c &quot;SELECT postgis_full_version();&quot;
> </pre></div>