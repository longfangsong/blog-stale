---
title: PostgreSQL cheatsheet
date: 2018-03-28 00:00:00
tags: [cheatsheet, PostgreSQL]
mathjax: true
comments: true
summary: PostgreSql开发环境搭建常用命令。
---
<h2 id="安装"><a class="headerlink" href="#安装" title="安装"></a>安装</h2><p>Mac：<br/><figure class="highlight bash"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">brew install postgresql</span><br/></pre></td></tr></table></figure></p>
 hexo-inject:begin  hexo-inject:end <h2 id="初始化"><a class="headerlink" href="#初始化" title="初始化"></a>初始化</h2><figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">initdb 数据库文件位置 -E utf8</span><br/></pre></td></tr></table></figure>
<h2 id="开启数据库服务器"><a class="headerlink" href="#开启数据库服务器" title="开启数据库服务器"></a>开启数据库服务器</h2><figure class="highlight bash"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">pg_ctl -D 数据库文件位置 -l logfile start</span><br/></pre></td></tr></table></figure>
<h2 id="重启数据库服务器"><a class="headerlink" href="#重启数据库服务器" title="重启数据库服务器"></a>重启数据库服务器</h2><figure class="highlight bash"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">pg_ctl restart -D 数据库文件位置</span><br/></pre></td></tr></table></figure>
<h2 id="关闭数据库服务器"><a class="headerlink" href="#关闭数据库服务器" title="关闭数据库服务器"></a>关闭数据库服务器</h2><figure class="highlight bash"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">pg_ctl -D 数据库文件位置 stop -s -m fast</span><br/></pre></td></tr></table></figure>
<h2 id="创建一个-PostgreSQL-用户"><a class="headerlink" href="#创建一个-PostgreSQL-用户" title="创建一个 PostgreSQL 用户"></a>创建一个 PostgreSQL 用户</h2><figure class="highlight bash"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">createuser username -P</span><br/></pre></td></tr></table></figure>
<h2 id="创建数据库"><a class="headerlink" href="#创建数据库" title="创建数据库"></a>创建数据库</h2><figure class="highlight bash"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">createdb 库名 -O PostgreSQL用户名 -E UTF8 -e</span><br/></pre></td></tr></table></figure>
<h2 id="打开数据库命令行"><a class="headerlink" href="#打开数据库命令行" title="打开数据库命令行"></a>打开数据库命令行</h2><figure class="highlight bash"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">psql -U PostgreSQL用户名 -d 库名 -h 127.0.0.1</span><br/></pre></td></tr></table></figure>
<h2 id="默认端口"><a class="headerlink" href="#默认端口" title="默认端口"></a>默认端口</h2><p><code>5432</code> </p>
<h2 id="配置"><a class="headerlink" href="#配置" title="配置"></a>配置</h2><h3 id="监听端口"><a class="headerlink" href="#监听端口" title="监听端口"></a>监听端口</h3><p>数据库文件夹下，编辑<code>postgresql.conf</code>文件的<code>listen_addresses</code>字段。</p>
<h3 id="允许连接的IP"><a class="headerlink" href="#允许连接的IP" title="允许连接的IP"></a>允许连接的IP</h3><p><code>pg_hba.conf</code>中添加相关信息，例如：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">host    all             all             192.168.1.0/24          md5</span><br/></pre></td></tr></table></figure>

