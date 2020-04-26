---
title: IEEE 754详解
date: 2018-09-11 00:00:00
tags: [编码理论]
mathjax: true
comments: true
summary: IEEE二进制浮点数算术标准（IEEE 754）是20世纪80年代以来最广泛使用的浮点数运算标准，为许多CPU与浮点运算器所采用。现代各种计算机语言也多将IEEE 754浮点数作为储存小数时的默认方案。
---
<h2 id="浮点数的表示"><a class="headerlink" href="#浮点数的表示" title="浮点数的表示"></a>浮点数的表示</h2><p>IEEE 754中的浮点数表示如下：</p>
    <script type="math/tex; mode=display">
浮点数实际值 = 符号位 \times 分数值 \times (10)_2^{指数}</script><p>实际存储在内存中的是</p>
<ul>
<li>符号位</li>
<li>指数</li>
<li>分数值</li>
</ul>
<p>在内存中的浮点数大概长这样<sup><a href="#fn_1" id="reffn_1">1</a></sup>：</p>
<p><img alt="memo-representation" src="./memo-representation.svg"/></p>
<p>IEEE 754规定了四种表示浮点数值的方式：</p>
<div class="table-container">
<table>
<thead>
<tr>
<th>浮点数类型</th>
<th>符号位长度</th>
<th>指数位长度</th>
<th>分数位长度</th>
<th>总长度</th>
</tr>
</thead>
<tbody>
<tr>
<td>单精确度</td>
<td>1</td>
<td>8</td>
<td>23</td>
<td>32</td>
</tr>
<tr>
<td>双精确度</td>
<td>1</td>
<td>11</td>
<td>52</td>
<td>64</td>
</tr>
<tr>
<td>延伸单精确度</td>
<td>1</td>
<td>-</td>
<td>-</td>
<td>43+</td>
</tr>
<tr>
<td>延伸双精确度</td>
<td>1</td>
<td>-</td>
<td>-</td>
<td>79+</td>
</tr>
</tbody>
</table>
</div>
<p>本文为了节约篇幅，下面的讲解皆以单精度数为例，将其拓展到其他精度，只需扩展对应的位的长度即可。</p>
<h3 id="符号位"><a class="headerlink" href="#符号位" title="符号位"></a>符号位</h3><p>代表浮点数本身的符号，老规矩，0正1负。</p>
<h3 id="指数（exponent）"><a class="headerlink" href="#指数（exponent）" title="指数（exponent）"></a>指数（exponent）</h3><p>指数是一种特别的有符号数，它在计算机中的储存方式是这样的：</p>
<script type="math/tex; mode=display">
储存值 = 实际值+(10)_2^{指数位长度-1} - 1</script><p>对于单精度浮点数，上述式子中的“指数位长度”可以用8代入：</p>
<script type="math/tex; mode=display">
储存值=实际值+(0111\ 1111)_2 \\
or \\
储存值=实际值+127_{10}</script><p>这样，如果实际的指数数值是2，储存值就是$129_{10}$或${1000\ 0001}_2$。</p>
<p>如果是-3，储存值就是$124_{10}$或$0111\ 1100_2$。</p>
<p>特别的，储存值为${1111\ 1111}_2$或者说$255_{10}$时代表特殊情况，此时：</p>
<ul>
<li>分数值为0，符号位为0 —— 代表$+\infty$</li>
<li>分数值为0，符号位为1 —— 代表$-\infty$</li>
<li>分数值非0                —— 代表这个浮点数“不是数字”，多用在计算出现错误的情况下</li>
</ul>
<p>另外，在储存值为$0$时，这是一个“非规格浮点数”，其实际值并非$127$而仍然是$126$，将在下一个话题中讲述。</p>
<p>为何这样储存，是因为这样在浮点数做比较的时候较为方便，直接按照字典序比较即可<sup><a href="#fn_3" id="reffn_3">3</a></sup>。</p>
<h3 id="分数值-尾数（fraction）"><a class="headerlink" href="#分数值-尾数（fraction）" title="分数值/尾数（fraction）"></a>分数值/尾数（fraction）</h3><p>分数值的储存要分类讨论：</p>
<ul>
<li><p>规格浮点数</p>
<p>在指数储存值非 $0$ 也非 ${1111\ 1111}_2$ 时，这个浮点数是一个规格化浮点数。</p>
<p>此时分数值表示的是一个$[1,2)$之间的数。</p>
<p>储存方式如下：</p>
<script type="math/tex; mode=display">
储存值 = 补全位数的小数部分(实际值-1)</script><p>也就是说只保存小数点后的部分，1是隐含的，比如说表示$1.1_2$：</p>
<script type="math/tex; mode=display">
1.1 - 1 = 0.1 \\
  补全23位: \\
  100\ 0000\ 0000\ 0000\ 0000\ 0000</script><p>再比如表示$1.11_2$。</p>
<script type="math/tex; mode=display">
1.11 - 1 = 0.11 \\
  补全23位: \\
  110\ 0000\ 0000\ 0000\ 0000\ 0000</script><p>以此类推。</p>
</li>
<li><p>非规格浮点数</p>
<p>在指数储存值为 $0$ 时，这个浮点数是一个非规格化浮点数。</p>
<p>此时分数值表示的是一个$[0,1)$之间的数。</p>
<p>储存方式：</p>
<script type="math/tex; mode=display">
储存值 = 补全位数的小数部分(实际值)</script><p>如：</p>
<script type="math/tex; mode=display">
(0.1)_2 \\
  补全23位: \\
  100\ 0000\ 0000\ 0000\ 0000\ 0000</script><p>  实际上非规格浮点数做的事情就是填补距离 $0$ 最近的规格浮点数<sup><a href="#fn_2" id="reffn_2">2</a></sup>：</p>
<script type="math/tex; mode=display">
0\ 0000\ 0001\ 0000\ 0000\ 0000\ 0000\ 0000\ 000_{浮点} \\
  \approx \\
  (1.17549\times 10 ^{-38})_{10}</script><p>与 $0$ 之间的空间。</p>
<p>需要非规格浮点数的原因是为防止“突然式下溢出”。</p>
</li>
</ul>
<h4 id="突然式下溢出-amp-渐进式下溢出"><a class="headerlink" href="#突然式下溢出-amp-渐进式下溢出" title="突然式下溢出 &amp; 渐进式下溢出"></a>突然式下溢出 &amp; 渐进式下溢出</h4><p>如果不采用非规格浮点数，那么能表示的最接近$0$的数和次接近$0$的数之间的差值为$(10)_2^{-23}（分数值差异）\times (10)_2^{-127}（指数）= (10)_2^{-150}$<sup><a href="#fn_4" id="reffn_4">4</a></sup></p>
<p>然而最接近$0$的数和$0$之间的差异仅有$(10)_2^{-127}$，如果你脑子里有数轴的话，你可以想象出这样一番场景：数轴从外往里浮点数能表示的数越来越密集（想一想，为什么），到了$0$点附近……</p>
<p>却突然空了。</p>
<p>感觉就很不舒服。</p>
<p>而且，如果你将明明不等的最接近$0$的数和次接近$0$的数相减……</p>
<p>答案却是0。</p>
<p>这就糟透了。</p>
<p>然后Intel的一群天才就强烈推荐IEEE采用<strong>渐进式下溢出</strong>。</p>
<p>在使用了<strong>渐进式下溢出</strong>的情况下，最接近$0$的数就变成了非规格的：</p>
<script type="math/tex; mode=display">
0\ 0000\ 0000\ 0000\ 0000\ 0000\ 0000\ 0000\ 001_{浮点} \\
  \approx \\
  (1.4013\times 10 ^{-45})_{10}</script><p>它和次接近$0$的数与和$0$的差值都是其本身，使用这种方法从0~最小规格化浮点数之间的空间被非规格化浮点数均匀地填充了。</p>
<h2 id="附表"><a class="headerlink" href="#附表" title="附表"></a>附表</h2><p>32位浮点数情况：</p>
<div class="table-container">
<table>
<thead>
<tr>
<th>类别</th>
<th>符号位</th>
<th>实际指数</th>
<th>有偏移指数</th>
<th>指数</th>
<th>尾数</th>
<th>十进制数值</th>
</tr>
</thead>
<tbody>
<tr>
<td>零</td>
<td>0</td>
<td>-127</td>
<td>0</td>
<td>0000 0000</td>
<td>000 0000 0000 0000 0000 0000</td>
<td>0.0</td>
</tr>
<tr>
<td>负零</td>
<td>1</td>
<td>-127</td>
<td>0</td>
<td>0000 0000</td>
<td>000 0000 0000 0000 0000 0000</td>
<td>−0.0</td>
</tr>
<tr>
<td>1</td>
<td>0</td>
<td>0</td>
<td>127</td>
<td>0111 1111</td>
<td>000 0000 0000 0000 0000 0000</td>
<td>1.0</td>
</tr>
<tr>
<td>-1</td>
<td>1</td>
<td>0</td>
<td>127</td>
<td>0111 1111</td>
<td>000 0000 0000 0000 0000 0000</td>
<td>−1.0</td>
</tr>
<tr>
<td>最小的非规约数</td>
<td>*</td>
<td>-126</td>
<td>0</td>
<td>0000 0000</td>
<td>000 0000 0000 0000 0000 0001</td>
<td>$±2^{−23} \times  2^{−126} = ±2^{−149} \approx ±1.4 \times 10^{-45}$</td>
</tr>
<tr>
<td>中间大小的非规约数</td>
<td>*</td>
<td>-126</td>
<td>0</td>
<td>0000 0000</td>
<td>100 0000 0000 0000 0000 0000</td>
<td>$±2^{−1} \times 2 ^ {−126} = ±2^{−127} \approx ±5.88\times10^{-39}$</td>
</tr>
<tr>
<td>最大的非规约数</td>
<td>*</td>
<td>-126</td>
<td>0</td>
<td>0000 0000</td>
<td>111 1111 1111 1111 1111 1111</td>
<td>$±(1−2^{−23}) \times 2^{−126} \approx ±1.18\times 10^{-38}$</td>
</tr>
<tr>
<td>最小的规约数</td>
<td>*</td>
<td>-126</td>
<td>1</td>
<td>0000 0001</td>
<td>000 0000 0000 0000 0000 0000</td>
<td>$±2^{−126} \approx ±1.18×10^{-38}$</td>
</tr>
<tr>
<td>最大的规约数</td>
<td>*</td>
<td>127</td>
<td>254</td>
<td>1111 1110</td>
<td>111 1111 1111 1111 1111 1111</td>
<td>$±(2−2^{−23}) \times 2^{127} \approx ±3.4\times10^{38}$</td>
</tr>
<tr>
<td>正无穷</td>
<td>0</td>
<td>128</td>
<td>255</td>
<td>1111 1111</td>
<td>000 0000 0000 0000 0000 0000</td>
<td>+∞</td>
</tr>
<tr>
<td>负无穷</td>
<td>1</td>
<td>128</td>
<td>255</td>
<td>1111 1111</td>
<td>000 0000 0000 0000 0000 0000</td>
<td>−∞</td>
</tr>
<tr>
<td>NaN</td>
<td>*</td>
<td>128</td>
<td>255</td>
<td>1111 1111</td>
<td>non zero</td>
<td>NaN</td>
</tr>
</tbody>
</table>
</div>
<blockquote id="fn_1">
<sup>1</sup>. 在小端序计算机（X86等）中，最右边的是最低字节的最低位，以<code>float</code>为例，从右往左数，[0-22]位是分数位，[23-30]是指数位，[31]是符号位。<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_2">
<sup>2</sup>. 为讲解方便，在讨论“最接近0的浮点数”时一律取正数作为研究对象。<a href="#reffn_2" title="Jump back to footnote [2] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_3">
<sup>3</sup>. 实际上将符号位也反转过来，比较会更加容易，可以整个数不加修改地作字典序比较，人类感觉上也更加自然（0总感觉比1小），不过在电路上一个非门也费不了什么事<a href="#reffn_3" title="Jump back to footnote [3] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_4">
<sup>4</sup>. 维基和百度百科中此处写的是$2^{-126}$和$2^{-149}$，显然他们没有考虑到如果去掉非规格浮点数的设定后，可以有更多规格浮点数<a href="#reffn_4" title="Jump back to footnote [4] in the text."> ↩</a>
</blockquote>

