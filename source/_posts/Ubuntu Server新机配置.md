---
title: Ubuntu Server新机配置
date: 2018-06-01 00:00:00
tags: [配置, Linux]
mathjax: true
comments: true
summary: 拿到/创建一台新的Ubuntu Server之后必做的配置工作
---
<h2 id="开启ssh"><a class="headerlink" href="#开启ssh" title="开启ssh"></a>开启ssh</h2><p>新的Ubuntu Server上没有sshd，要安装<code>openssh-server</code>：</p>
 hexo-inject:begin  hexo-inject:end <figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">sudo apt install openssh-server</span><br/></pre></td></tr></table></figure>
<h2 id="apt换源"><a class="headerlink" href="#apt换源" title="apt换源"></a>apt换源</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line">sudo sed -i 's/us.archive.ubuntu.com/mirrors.shu.edu.cn/g' /etc/apt/sources.list</span><br/><span class="line">sudo apt update</span><br/></pre></td></tr></table></figure>
<h2 id="一些必要软件包"><a class="headerlink" href="#一些必要软件包" title="一些必要软件包"></a>一些必要软件包</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">sudo apt install vim curl</span><br/></pre></td></tr></table></figure>
<h2 id="Docker环境"><a class="headerlink" href="#Docker环境" title="Docker环境"></a>Docker环境</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">sudo apt install docker.io</span><br/></pre></td></tr></table></figure>
<h3 id="加速器"><a class="headerlink" href="#加速器" title="加速器"></a>加速器</h3><p>见<a href="https://www.daocloud.io/mirror#accelerator-doc" rel="noopener" target="_blank">这个网址</a></p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://6b896b78.m.daocloud.io</span><br/></pre></td></tr></table></figure>
<h2 id="修改主机名"><a class="headerlink" href="#修改主机名" title="修改主机名"></a>修改主机名</h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">sudo vim /etc/hostname</span><br/></pre></td></tr></table></figure>
<p>将其中的<code>ubuntu</code>改成你希望的主机名。</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">sudo vim /etc/hosts</span><br/></pre></td></tr></table></figure>
<p>将</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">127.0.1.1       ubuntu</span><br/></pre></td></tr></table></figure>
<p>里的<code>ubuntu</code>改成你希望的主机名。</p>

