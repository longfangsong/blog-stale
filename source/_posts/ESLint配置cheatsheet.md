---
title: ESLint配置cheatsheet
date: 2018-06-27 00:00:00
tags: [Javascript, 配置, cheatsheet]
mathjax: true
comments: true
summary: Vue-cli创建的项目的ESLint的默认配置有点令人抓狂，这种时候就要自己手动改配置了。
---
<p>配置文件为<code>.eslintrc.js</code>，规则放在<code>'rules'</code>下，我主要使用的规则是下面这些：</p>
    <figure class="highlight javascript"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">// 我喜欢空四格</span></span><br/><span class="line"><span class="string">"indent"</span>: [<span class="number">2</span>, <span class="number">4</span>],</span><br/><span class="line"><span class="comment">// 我还是喜欢有分号</span></span><br/><span class="line"><span class="string">"semi"</span>: [<span class="number">2</span>, <span class="string">"always"</span>]</span><br/></pre></td></tr></table></figure>

