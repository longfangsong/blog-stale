---
title: Mocha with typescript环境配置
date: 2018-12-25 00:00:00
tags: [测试, cheatsheet, mocha, typescript]
mathjax: true
comments: true
summary: Mocha+typescript 的单测环境爆炸难配，网上找了半天才拼凑出这样的方案。
---
<p>需要包：</p>
    <figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line">mocha</span><br/><span class="line">typescript</span><br/><span class="line">ts-node</span><br/></pre></td></tr></table></figure>
<p>mocha命令额外的选项：</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">--require ts-node/register</span><br/></pre></td></tr></table></figure>
<p>mocha不会检查<code>tsconfig.json</code>，需要使用环境变量手动指定<code>compilerOptions</code>，例如：</p>
<figure class="highlight"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/></pre></td><td class="code"><pre><span class="line">TS_NODE_COMPILER_OPTIONS={</span><br/><span class="line">    "target": "es5",     </span><br/><span class="line">    "lib": ["es2015","es6"],</span><br/><span class="line">    "downlevelIteration": true,</span><br/><span class="line">    "sourceMap": true,</span><br/><span class="line">    "declaration": true,</span><br/><span class="line">    "experimentalDecorators": true</span><br/><span class="line">}</span><br/></pre></td></tr></table></figure>

