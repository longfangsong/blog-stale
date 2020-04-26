---
title: 代数结构 cheatsheet
date: 2019-04-09 00:00:00
tags: [抽象代数]
mathjax: true
comments: true
summary: （我学会的）抽象代数中代数结构的整理。
---
<h2 id="运算"><a class="headerlink" href="#运算" title="运算"></a>运算</h2><h3 id="二元运算"><a class="headerlink" href="#二元运算" title="二元运算"></a>二元运算</h3><blockquote>
<p>给定集合$A$，二元函数$F: A \times A → A$称为集合A上的<strong>二元运算</strong>。</p>
</blockquote>
<p>本文中的二元运算使用 $\circ$ 、$\diamond$  <sup><a href="#fn_1" id="reffn_1">1</a></sup>表示，同时，集合$A$中的元素用 $a$ ，$b$ ，$c$ 等表示。</p>
<h3 id="可能使集合具有的特殊元素"><a class="headerlink" href="#可能使集合具有的特殊元素" title="可能使集合具有的特殊元素"></a>可能使集合具有的特殊元素</h3><h4 id="幺元-单位元-（identity-element-neutral-element）"><a class="headerlink" href="#幺元-单位元-（identity-element-neutral-element）" title="幺元/单位元 （identity element/neutral element）"></a>幺元/单位元 （identity element/neutral element）</h4><p>若对 $A$ 中每个元素 $a$</p>
<script type="math/tex; mode=display">
i\ \circ\ a = a</script><p>则 $i$ 是 $\circ$ 在 $A​$ 上的左幺元。</p>
<p>若对 $A$ 中每个元素 $a$</p>
<script type="math/tex; mode=display">
a\ \circ\ i = a</script><p>则 $i$ 是 $\circ$ 在 $A​$ 上的右幺元。</p>
<p>幺元 = 左幺元 &amp; 右幺元。</p>
<h4 id="逆元-（inverse-element）"><a class="headerlink" href="#逆元-（inverse-element）" title="逆元 （inverse element）"></a>逆元 （inverse element）</h4><p>在已经存在幺元 $i​$ 时，</p>
<p>若</p>
<script type="math/tex; mode=display">
a\ \circ\ b = i</script><p>则 $a$ 是 $b$ 在 $\circ$ 上的左逆元，同时 $b$ 也是 $a$ 在 $\circ$ 下的右逆元。</p>
<p>逆元 = 左逆元 &amp; 右逆元</p>
<h4 id="零元（zero-element）"><a class="headerlink" href="#零元（zero-element）" title="零元（zero element）"></a>零元（zero element）</h4><p>若对 $A​$ 中每个元素 $a​$</p>
<script type="math/tex; mode=display">
z\ \circ\ a = z</script><p>则 $z$ 是 $\circ$ 在 $A$ 上的左零元。</p>
<p>若对 $A​$ 中每个元素 $a​$</p>
<script type="math/tex; mode=display">
a\ \circ\ z = z</script><p>则 $z​$ 是 $\circ​$ 在 $A​$ 上的右零元。</p>
<p>零元 = 左零元 &amp; 右零元</p>
<h4 id="零因子（Zero-divisor）"><a class="headerlink" href="#零因子（Zero-divisor）" title="零因子（Zero divisor）"></a>零因子（Zero divisor）</h4><p>在已经存在零元 $z$ 时：</p>
<p>若对 $A​$ 中存在不是零元的元素 $b​$</p>
<script type="math/tex; mode=display">
a\ \circ\ b = z</script><p>则 $a$ 是 $\circ$ 在 $A$ 上的左零因子。</p>
<p>若对 $A$ 中存在不是零元的元素 $b$</p>
<script type="math/tex; mode=display">
b\ \circ\ a = z</script><p>则 $a$ 是 $\circ$ 在 $A$ 上的右零因子。</p>
<p>零因子 = 左零因子 &amp; 右零因子（也有说法是 零因子 = 左零因子 | 右零因子）</p>
<h3 id="可能具有的性质"><a class="headerlink" href="#可能具有的性质" title="可能具有的性质"></a>可能具有的性质</h3><h4 id="交换律"><a class="headerlink" href="#交换律" title="交换律"></a>交换律</h4><p>若对 $A$ 中任意元素 $a$ 、$b​$</p>
<p>$a\ \circ\ b = b\ \circ\ a​$</p>
<h4 id="结合律"><a class="headerlink" href="#结合律" title="结合律"></a>结合律</h4><p>若对 $A$ 中任意元素 $a$ 、$b$</p>
<p>$(a\ \circ\ b)\ \circ\ c =a\ \circ\ (b\ \circ\ c) $</p>
<h4 id="分配率"><a class="headerlink" href="#分配率" title="分配率"></a>分配率</h4><p>若对 $A$ 中任意元素 $a$ 、$b$、$c$</p>
<p>若</p>
<script type="math/tex; mode=display">
a\ \circ\ (b\ \diamond\ c) = a\ \circ\ b\ \diamond\ a\ \circ\ b</script><p>称 $\circ$ 对 $\diamond$ 具有左分配率。</p>
<p>若</p>
<script type="math/tex; mode=display">
(b\ \diamond\ c) \circ\ a = b\ \circ\ a\ \diamond\ c\ \circ\ a</script><p>称 $\circ$ 对 $\diamond​$ 具有右分配率。</p>
<p>分配率 = 左分配率 &amp; 右分配率</p>
<h4 id="幂等率"><a class="headerlink" href="#幂等率" title="幂等率"></a>幂等率</h4><p>对 $A$ 中每个元素 $a$</p>
<script type="math/tex; mode=display">
a\ \circ\ a = a</script><h4 id="幂幺率"><a class="headerlink" href="#幂幺率" title="幂幺率"></a>幂幺率</h4><p>对 $A$ 中每个元素 $a​$</p>
<script type="math/tex; mode=display">
a\ \circ\ a = i</script><h4 id=""><a class="headerlink" href="#" title=" "></a> </h4><p>其中 $i$ 为幺元。</p>
<h4 id="幂零率"><a class="headerlink" href="#幂零率" title="幂零率"></a>幂零率</h4><p>对 $A$ 中每个元素 $a$</p>
<script type="math/tex; mode=display">
a\ \circ\ a = z</script><h4 id="-1"><a class="headerlink" href="#-1" title=" "></a> </h4><p>其中 $z$ 为零元。</p>
<h4 id="吸收率"><a class="headerlink" href="#吸收率" title="吸收率"></a>吸收率</h4><p>对 $A$ 中每个 $a$ 、$b​$</p>
<script type="math/tex; mode=display">
a\ \circ\ (a\ \diamond\ b) = a\ \diamond\ (a\ \circ\ b) = a</script><p>则这两个运算服从吸收率，两种运算被称为对偶对。</p>
<h2 id="序"><a class="headerlink" href="#序" title="序"></a>序</h2><h3 id="偏序关系"><a class="headerlink" href="#偏序关系" title="偏序关系"></a>偏序关系</h3><p>待补充……</p>
<h3 id="全序关系"><a class="headerlink" href="#全序关系" title="全序关系"></a>全序关系</h3><p>待补充……</p>
<h3 id="上-下界"><a class="headerlink" href="#上-下界" title="上/下界"></a>上/下界</h3><p>待补充……</p>
<h3 id="上-下确界（全上-下界）"><a class="headerlink" href="#上-下确界（全上-下界）" title="上/下确界（全上/下界）"></a>上/下确界（全上/下界）</h3><p>待补充……</p>
<h2 id="代数结构"><a class="headerlink" href="#代数结构" title="代数结构"></a>代数结构</h2><p>来自维基百科（许多东西没有中文译名……）：</p>
<p><img alt="img1" src="./img1.png"/></p>
<p><img alt="Lattice_v4" src="./Lattice_v4.png"/></p>
<h3 id="原群（magma-groupoid）"><a class="headerlink" href="#原群（magma-groupoid）" title="原群（magma/groupoid）"></a>原群（magma/groupoid）</h3><p>有一个集合和一个集合上的二元运算就是原群了。</p>
<h3 id="拟群（quasigroup）"><a class="headerlink" href="#拟群（quasigroup）" title="拟群（quasigroup）"></a>拟群（quasigroup）</h3><p>对任意元素 $a$ 和 $b$，有唯一的 $x$ 和 $y$，使得</p>
<script type="math/tex; mode=display">
a\ \circ\ x = b \\
y\ \circ\ a = b</script><p>我们可以从此定义出 $\circ$ 的逆运算：</p>
<script type="math/tex; mode=display">
x = a\ \grave{\circ}\ b\\
y = b\ \acute{\circ}\ a\\</script><p>$\grave{\circ}$ 常写作 \ ，常被称为左除。</p>
<p>$\acute{\circ}$ 常写作 /，被称为右除。</p>
<h3 id="半群（semigroup）"><a class="headerlink" href="#半群（semigroup）" title="半群（semigroup）"></a>半群（semigroup）</h3><p>原群 加</p>
<ul>
<li>运算<ul>
<li>结合律</li>
</ul>
</li>
</ul>
<h3 id="幺拟群（圈）（loop）"><a class="headerlink" href="#幺拟群（圈）（loop）" title="幺拟群（圈）（loop）"></a>幺拟群（圈）（loop）</h3><p>拟群 加上：</p>
<ul>
<li>特殊元素<ul>
<li>幺元</li>
</ul>
</li>
</ul>
<h3 id="幺半群（独异点）（monoid）"><a class="headerlink" href="#幺半群（独异点）（monoid）" title="幺半群（独异点）（monoid）"></a>幺半群（独异点）（monoid）</h3><p>半群 加上：</p>
<ul>
<li>特殊元素<ul>
<li>幺元</li>
</ul>
</li>
</ul>
<h3 id="可交换幺半群（阿贝尔幺半群）"><a class="headerlink" href="#可交换幺半群（阿贝尔幺半群）" title="可交换幺半群（阿贝尔幺半群）"></a>可交换幺半群（阿贝尔幺半群）</h3><p>幺半群 加上：</p>
<ul>
<li>运算<ul>
<li>交换律</li>
</ul>
</li>
</ul>
<h3 id="群（group）"><a class="headerlink" href="#群（group）" title="群（group）"></a>群（group）</h3><p>幺半群 加上：</p>
<ul>
<li>特殊元素<ul>
<li>（对每个元素）逆元</li>
</ul>
</li>
</ul>
<h3 id="可交换群（阿贝尔群）（Abelian-group）"><a class="headerlink" href="#可交换群（阿贝尔群）（Abelian-group）" title="可交换群（阿贝尔群）（Abelian group）"></a>可交换群（阿贝尔群）（Abelian group）</h3><p>群 + 可交换幺半群</p>
<h3 id="环（ring）"><a class="headerlink" href="#环（ring）" title="环（ring）"></a>环（ring）</h3><p>对一种运算（$\circ$）构成可交换群 + 对另一种运算（$\diamond$）构成半群 + $\circ$ 对$\diamond$有分配率</p>
<h3 id="幺环"><a class="headerlink" href="#幺环" title="幺环"></a>幺环</h3><p>环 + 其中那个半群是含幺半群</p>
<h3 id="交换环"><a class="headerlink" href="#交换环" title="交换环"></a>交换环</h3><p>环 + 其中那个半群是交换半群</p>
<h3 id="无零因子环"><a class="headerlink" href="#无零因子环" title="无零因子环"></a>无零因子环</h3><p>环 + 没有零因子</p>
<h3 id="整环"><a class="headerlink" href="#整环" title="整环"></a>整环</h3><p>交换环 + 幺环 + 无零因子环</p>
<h3 id="除环"><a class="headerlink" href="#除环" title="除环"></a>除环</h3><p>所有元素都在 $\circ$ 上有逆元</p>
<h3 id="域（field）"><a class="headerlink" href="#域（field）" title="域（field）"></a>域（field）</h3><p>交换环 + 除环</p>
<h3 id="偏序集（partially-ordered-set）"><a class="headerlink" href="#偏序集（partially-ordered-set）" title="偏序集（partially ordered set）"></a>偏序集（partially ordered set）</h3><p>定义了偏序的集合</p>
<h3 id="半格"><a class="headerlink" href="#半格" title="半格"></a>半格</h3><h4 id="序理论定义"><a class="headerlink" href="#序理论定义" title="序理论定义"></a>序理论定义</h4><p>偏序集 + (最小上界 | 最大上界)</p>
<h4 id="抽象代数定义"><a class="headerlink" href="#抽象代数定义" title="抽象代数定义"></a>抽象代数定义</h4><p>集合 加上 </p>
<ul>
<li>运算<ul>
<li>交换律</li>
<li>结合律</li>
</ul>
</li>
</ul>
<h3 id="格（Lattice）2"><a class="headerlink" href="#格（Lattice）2" title="格（Lattice）2"></a>格（Lattice）<sup><a href="#fn_2" id="reffn_2">2</a></sup></h3><h4 id="序理论定义-1"><a class="headerlink" href="#序理论定义-1" title="序理论定义"></a>序理论定义</h4><p>偏序集 + 最小上界 + 最大上界</p>
<h4 id="抽象代数定义-1"><a class="headerlink" href="#抽象代数定义-1" title="抽象代数定义"></a>抽象代数定义</h4><p>两个定义在同一个集合上，但运算不同的半格，加上</p>
<ul>
<li>两个运算<ul>
<li>吸收律</li>
</ul>
</li>
</ul>
<h3 id="分配格"><a class="headerlink" href="#分配格" title="分配格"></a>分配格</h3><p>格 加上</p>
<ul>
<li>运算<ul>
<li>分配率</li>
</ul>
</li>
</ul>
<h3 id="完全格"><a class="headerlink" href="#完全格" title="完全格"></a>完全格</h3><p>偏序集 + 所有子集都有上确界和下确界</p>
<h3 id="有界格"><a class="headerlink" href="#有界格" title="有界格"></a>有界格</h3><p>格 加上</p>
<ul>
<li>有全下界和全上界</li>
</ul>
<h3 id="有补格"><a class="headerlink" href="#有补格" title="有补格"></a>有补格</h3><p>有界格 加上</p>
<ul>
<li>对任意元素均有补元</li>
</ul>
<h4 id="补元"><a class="headerlink" href="#补元" title="补元"></a>补元</h4><script type="math/tex; mode=display">
a\ \wedge\ b=0</script><p>且</p>
<script type="math/tex; mode=display">
a\ \vee\ b = 1</script><p>则 $b$ 是 $a$ 的补元。</p>
<h3 id="海廷代数"><a class="headerlink" href="#海廷代数" title="海廷代数"></a>海廷代数</h3><p>有界格 加上</p>
<p>对于所有 $a$ 和 $b$，集合中存在最大的 $x$，使得$a \wedge x \leq b$。</p>
<p>其中 $x$ 称为 $a$ 对 $b$ 的相对伪补元。</p>
<h3 id="完全海廷代数"><a class="headerlink" href="#完全海廷代数" title="完全海廷代数"></a>完全海廷代数</h3><p>海廷代数 + 完全格</p>
<h3 id="布尔代数"><a class="headerlink" href="#布尔代数" title="布尔代数"></a>布尔代数</h3><p>有补格 + 分配格</p>
<p>或</p>
<p>海廷代数 将 相对伪补元改为真正的补元。</p>
<h3 id="完全布尔代数"><a class="headerlink" href="#完全布尔代数" title="完全布尔代数"></a>完全布尔代数</h3><p>布尔代数 + 完全格</p>
<h3 id="逻辑代数"><a class="headerlink" href="#逻辑代数" title="逻辑代数"></a>逻辑代数</h3><p>布尔代数 + 二元（集合中元素只有两个）</p>
<blockquote id="fn_1">
<sup>1</sup>. 各种材料常常用常见的乘号和加号代表运算，个人并不喜欢这样做，”不同”的东西应该用”不同”的记号<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_2">
<sup>2</sup>. 格在群论和序理论中代表两个不同的东西，英文名称都叫Lattice，中文名称都叫格。数学家起名字的水平和我五五开。<a href="#reffn_2" title="Jump back to footnote [2] in the text."> ↩</a>
</blockquote>

