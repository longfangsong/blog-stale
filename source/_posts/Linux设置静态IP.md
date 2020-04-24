---
title: Linux设置静态IP
date: 2018-06-01 00:00:00
tags: [配置, cheatsheet, Linux]
mathjax: true
comments: true
summary: Linux如何配置静态IP
---
<p>（我lfs就是饿死，死外边，也不会背一句配置！！！）</p>
 hexo-inject:begin  hexo-inject:end <p>以Ubuntu系统为例。</p>
<h2 id="网卡"><a class="headerlink" href="#网卡" title="网卡"></a>网卡</h2><p><code>ipconfig</code>找到你的网卡，以VMWare下Ubuntu Server的<code>ens33</code>为例。</p>
<h2 id="网关"><a class="headerlink" href="#网关" title="网关"></a>网关</h2><p>用<code>route</code>找到你的网关地址。</p>
<h2 id="修改-etc-network-interfaces"><a class="headerlink" href="#修改-etc-network-interfaces" title="修改/etc/network/interfaces"></a>修改<code>/etc/network/interfaces</code></h2><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">sudo vim /etc/network/interfaces</span><br/></pre></td></tr></table></figure>
<p>将</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">iface ens33 inet dhcp</span><br/></pre></td></tr></table></figure>
<p>改为</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/></pre></td><td class="code"><pre><span class="line">iface ens33 inet static</span><br/><span class="line">address 你想要的静态IP    </span><br/><span class="line">netmask 子网掩码（一般是255.255.255.0）</span><br/><span class="line">gateway 用route看到的网关地址</span><br/><span class="line">dns-nameserver dns服务器地址</span><br/></pre></td></tr></table></figure>
<p>然后重启即可。</p>

