---
title: Flexbox cheatsheet
date: 2018-04-09 00:00:00
tags: [cheatsheet, 前端, CSS, Flexbox]
mathjax: true
comments: true
summary: Flexbox是现代web页面布局的一大利器，但是相关属性众多，为了方便查阅，本文列出了最常用的几个Flexbox布局相关属性。
---
<p>Flexbox是现代web页面布局的一大利器，但是相关属性众多，本人每次使用都要查文档，而且查了多次（≥20）居然一点都没记住。</p>
 hexo-inject:begin  hexo-inject:end <p>所以就写了这个cheatsheet，不是因为写了就记住了，而是从自己写的文档里翻东西比较快……</p>
<h2 id="display-flex"><a class="headerlink" href="#display-flex" title="display: flex;"></a><code>display: flex;</code></h2><p>在某个元素上设置，这个元素内部使用Flexbox布局。</p>
<h2 id="flex-direction"><a class="headerlink" href="#flex-direction" title="flex-direction"></a><code>flex-direction</code></h2><p>主轴方向，取值如下：</p>
<ul>
<li><code>row</code></li>
<li><code>row-reverse</code></li>
<li><code>column</code></li>
<li><code>column-reverse</code></li>
</ul>
<h2 id="justify-content"><a class="headerlink" href="#justify-content" title="justify-content"></a><code>justify-content</code></h2><p>控制主轴方向上元素的排列，取值如下：</p>
<ul>
<li><code>center</code></li>
<li><code>start</code></li>
<li><code>flex-start</code></li>
<li><code>end</code></li>
<li><code>flex-end</code></li>
<li><code>space-between</code></li>
<li><code>space-around</code></li>
<li><code>space-evenly</code></li>
</ul>
<p><code>start</code>/<code>flex-start</code>和<code>end</code>/<code>flex-end</code>之间的差别在于是否只对flex元素生效。</p>
<h2 id="align-items"><a class="headerlink" href="#align-items" title="align-items"></a><code>align-items</code></h2><p>控制次轴方向上元素的排列，取值如下：</p>
<ul>
<li><p><code>stretch</code></p>
</li>
<li><p><code>flex-start</code></p>
</li>
<li><p><code>flex-end</code></p>
</li>
<li><p><code>center</code></p>
</li>
</ul>
<h2 id="flex"><a class="headerlink" href="#flex" title="flex"></a><code>flex</code></h2><figure class="highlight css"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="selector-tag">flex</span>: 数字</span><br/></pre></td></tr></table></figure>
<p>代表元素在flexbox中占的比例。</p>

