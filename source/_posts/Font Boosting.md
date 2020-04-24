---
title: Font Boosting
date: 2018-03-17 00:00:00
tags: [前端, CSS]
mathjax: true
comments: true
summary: Font Boosting导致移动端字体大小设置怪异行为的解决方案
---
<p>就在做这个Blog的时候发现一个很interesting的问题：在用CSS设置了<code>font-size</code>之后，在浏览器中看到的样式表中的样式和计算出来的样式不同，而且同样选择器选到的几个元素字体大小也不一样。</p>
 hexo-inject:begin  hexo-inject:end <p>开始以为是相对大小的问题（没道理呀，<code>pt</code>、<code>px</code>都是绝对单位呀），调了半天无果，非常崩溃。</p>
<p>后来甚至怀疑是webkit的bug</p>
<p>结果发现It is not a bug, but a feature.</p>
<p>具体可以见这个文档<a href="https://docs.google.com/document/d/1PPcEwAhXJJ1TQShor29KWB17KJJq7UJOM34oHwYP3Zg/edit" rel="noopener" target="_blank">Chromium’s Text Autosizer</a>，计算规则则可以在这里看到<a href="https://cs.chromium.org/chromium/src/third_party/WebKit/Source/core/layout/TextAutosizer.cpp" rel="noopener" target="_blank">TextAutosizer.cpp</a>。</p>
<p>最简单的解决方法是：在对应元素上添加</p>
<figure class="highlight css"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="selector-tag">-webkit-text-size-adjust</span>: <span class="selector-tag">none</span>;</span><br/></pre></td></tr></table></figure>

