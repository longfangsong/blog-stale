---
title: NFA确定化
date: 2020-04-21 00:00:00
tags: [编译原理, 自动机]
mathjax: true
comments: true
summary: 老师这么讲谁懂啊。
---
<p>欠了两年（2018-04-12~2020-04-21）的欠账终于还是要还啊。</p>
    <h2 id="NFA"><a class="headerlink" href="#NFA" title="NFA"></a>NFA</h2><p>就一从一个状态给定一个符号（允许为空符号$\epsilon $）可能会转换到多个其他状态的自动机。</p>
<p>图中的：</p>
<ul>
<li>圈指一个状态</li>
<li>箭头和上面的字符指在某个状态时，输入这个字符后，可以转换到箭头指向的状态</li>
<li>双圈指接受状态，就是说如果全部输入串都输入完成后停在了这个状态，那么这个输入就是NFA对应的正规语言的句子。</li>
</ul>
<p>例如下图就是一个识别$(a*|b*)$的NFA。</p>
<p><img alt="nfa" src="./nfa.png"/></p>
<p>值得注意的是NFA并非“不能进行匹配”，只是进行匹配的时候，由于给定一个状态和一个输入可以转换到多个不同的状态，所以如果全部状态输入完后状态机没有停在接受状态，也不代表状态机不能匹配所给的输入，也有可能是选择的状态转换不正确，可以回朔后选择其他状态转换再试<sup><a href="#fn_0" id="reffn_0">0</a></sup>。</p>
<p>如果同一状态相同输入能转换到的不同状态的数量过多，使用NFA进行匹配就会较为没有效率。</p>
<h2 id="DFA"><a class="headerlink" href="#DFA" title="DFA"></a>DFA</h2><p>DFA是一种特殊的NFA<sup><a href="#fn_1" id="reffn_1">1</a></sup>。</p>
<p>没有$\epsilon$边，且从一个状态给定一个符号只能转换到一个状态的NFA就是DFA。</p>
<p><img alt="dfa" src="./dfa.png"/></p>
<p>DFA的匹配过程就很直接了，跟着输入一直走，能走到接受状态就行。所以DFA本身的匹配速度是很快的。</p>
<h2 id="NFA的确定化"><a class="headerlink" href="#NFA的确定化" title="NFA的确定化"></a>NFA的确定化</h2><h3 id="例子"><a class="headerlink" href="#例子" title="例子"></a>例子</h3><p>我们从一个例子<sup><a href="#fn_2" id="reffn_2">2</a></sup>开始讲解：</p>
<p><img alt="nfa-example" src="./nfa-example.png"/></p>
<p>该NFA识别的是$(a|b)*abb$。</p>
<p>我们将这个NFA化为DFA。</p>
<p>一开始从start进去，即使不作任何输入，从状态$0$开始凭借着$\epsilon$边，也可以转到状态$\{0,1,2,4,7\}$<sup><a href="#fn_3" id="reffn_3">3</a></sup>，那么因为DFA中没有$\epsilon$边，而一个start状态又是必须的，在我们生成的DFA中，这些个状态就只能化为一个状态，称之为$A$。</p>
<p><img alt="A" src="./A.png"/></p>
<p>然后看看我们从这个$A$状态（也就是NFA中的$\{0,1,2,4,7\}$这几个状态）在输入$a$时能转到哪些状态：</p>
<ul>
<li>从$2$，能转到$3$</li>
<li>从$7$，能转到$8$</li>
<li>从转到的$3$和$8$中，再进行一次$\epsilon$转换（因为这不需要任何输入），能转到$\{1,2,3,4,6,7,8\}$</li>
</ul>
<p>所以在<strong>向原NFA中$A$对应的状态输入了$a$后，可能转到的NFA状态</strong>是$\{1,2,3,4,6,7,8\}$，这种组合显然并不是$A$状态，我们把它化为一个状态$B$，并连接在$A$后面：</p>
<p><img alt="AB" src="./AB.png"/></p>
<p>同理<strong>从$A$由输入$b$能到达的状态</strong>$C=\{1,2,4,5,6,7\}$</p>
<p><img alt="ABC" src="./ABC.png"/></p>
<p>至此从$A$出发的DFA中的路径就考察完了。</p>
<p>为了方便观察，我将NFA中的状态对应到的DFA中的状态标注在图上：</p>
<p><img alt="annotated1" src="./annotated1.png"/></p>
<p>我们继续从$B$开始考察：</p>
<ul>
<li>从这个状态经$a$，可以到达的状态是$\{1,2,3,4,6,7,8\}$，这又落回了$B$，故$B$经输入$a$转到自身。</li>
<li>从这个状态经$b$，可以到达的状态是$\{1,2,4,5,6,7,9\}$，这种NFA状态的组合在之前从未出现过，故将其化为一个DFA状态$D$</li>
</ul>
<p>然后得到：</p>
<p><img alt="ABCD" src="./ABCD.png"/></p>
<p><img alt="annotated2" src="./annotated2.png"/></p>
<p>接着考察$C$：</p>
<ul>
<li>$C+a \rightarrow \{1,2,3,4,6,7,8\}$，落入了$B$</li>
<li>$C+b \rightarrow \{1,2,4,5,6,7\}$，落入了$C$</li>
</ul>
<p>所以：</p>
<p><img alt="ABCD2" src="./ABCD2.png"/></p>
<p>然后是$D$：</p>
<ul>
<li>$D+a \rightarrow \{1,2,3,4,6,7,8\}$，落入了$B$</li>
<li>$D+b \rightarrow \{1,2,4,5,6,7,10\}$，创建新状态$E$，由于$E$包含了旧NFA的接受状态$10$，故其是DFA的接受状态。</li>
</ul>
<p>所以得到：</p>
<p><img alt="ABCDE" src="./ABCDE.png"/></p>
<p><img alt="annotated3" src="./annotated3.png"/></p>
<p>然后是$E$：</p>
<ul>
<li>$E+a \rightarrow B$</li>
<li>$E+b\rightarrow C$</li>
</ul>
<p>所以可以得到：</p>
<p><img alt="final" src="./final.png"/></p>
<p>由于这个DFA里面所有的状态都被考虑过了，所以这就是最后的结果了。</p>
<p>附上最后得到的NFA状态到DFA状态的对应关系表：</p>
<div class="table-container">
<table>
<thead>
<tr>
<th>NFA</th>
<th>DFA</th>
</tr>
</thead>
<tbody>
<tr>
<td>$0,1,2,4,7$</td>
<td>$A$</td>
</tr>
<tr>
<td>$1,2,3,4,6,7,8$</td>
<td>$B$</td>
</tr>
<tr>
<td>$1,2,4,5,6,7$</td>
<td>$C$</td>
</tr>
<tr>
<td>$1,2,4,5,6,7,9$</td>
<td>$D$</td>
</tr>
<tr>
<td>$1,2,4,5,6,7,10$</td>
<td>$E$</td>
</tr>
</tbody>
</table>
</div>
<h3 id="算法"><a class="headerlink" href="#算法" title="算法"></a>算法</h3><p>总结上面的过程，我们可以得到NFA的确定化算法：</p>
<ol>
<li>将NFA的开始状态及其所有可通过$\epsilon$边的状态映射为DFA​的开始状态</li>
<li>如果DFA中的每个状态都被“考虑过”，则算法完成</li>
<li>否则，考虑DFA中的一个没有被考虑过的状态$N$：<ol>
<li>对字母表里的每个字母$ch$<ol>
<li>找出这个节点<strong>对应NFA中的所有节点</strong>经过$ch$加上任意多的$\epsilon$边能到达的所有状态的集合</li>
<li>若这个集合是DFA中已经存在的某个状态$M$对应的NFA状态集合的子集，则添加连线$N + ch = M$</li>
<li>否则向DFA中添加新状态$P$，其对应NFA状态集合就是上述集合，如果这个集合中包含接受状态，那么这个节点也是接受状态，并添加连线$N+ch=P$</li>
</ol>
</li>
</ol>
</li>
<li>最终得到的DFA即为所求</li>
</ol>
<h3 id="代码4"><a class="headerlink" href="#代码4" title="代码4 "></a>代码<sup><a href="#fn_4 " id="reffn_4 ">4 </a></sup></h3><p>代码见<a href="https://github.com/longfangsong/RegexCalculator/blob/master/src/finiteAutomata/Nondeterministic.kt" rel="noopener" target="_blank">Github仓库</a></p>
<figure class="highlight kotlin"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/><span class="line">13</span><br/><span class="line">14</span><br/><span class="line">15</span><br/><span class="line">16</span><br/><span class="line">17</span><br/><span class="line">18</span><br/><span class="line">19</span><br/><span class="line">20</span><br/><span class="line">21</span><br/><span class="line">22</span><br/><span class="line">23</span><br/><span class="line">24</span><br/><span class="line">25</span><br/><span class="line">26</span><br/><span class="line">27</span><br/><span class="line">28</span><br/><span class="line">29</span><br/><span class="line">30</span><br/><span class="line">31</span><br/><span class="line">32</span><br/><span class="line">33</span><br/></pre></td><td class="code"><pre><span class="line"><span class="function"><span class="keyword">fun</span> <span class="title">toDFA</span><span class="params">()</span></span>: Deterministic {</span><br/><span class="line">        start <span class="keyword">as</span> Nondeterministic.State</span><br/><span class="line">  			<span class="comment">// 还没有被考虑过的状态, equivalentStates: 经过𝜀边能到达的状态</span></span><br/><span class="line">  			<span class="comment">// 由于DFA状态没有完全被构造出来，故存起来比较困难</span></span><br/><span class="line">  			<span class="comment">// 所以存和这个DFA状态对应的NFA状态集</span></span><br/><span class="line">        <span class="keyword">val</span> statesToFind = mutableSetOf(start.equivalentStates())</span><br/><span class="line">  			<span class="comment">// 结果的转换关系表</span></span><br/><span class="line">        <span class="keyword">val</span> result = mutableMapOf&lt;Set&lt;Nondeterministic.State&gt;, Deterministic.State&gt;()</span><br/><span class="line">        <span class="keyword">var</span> nextChar = <span class="string">'A'</span></span><br/><span class="line">  			<span class="comment">// 构建DFA开始状态</span></span><br/><span class="line">        result[start.equivalentStates()] = Deterministic.State(</span><br/><span class="line">                nextChar++.toString(),</span><br/><span class="line">                start.equivalentStates().any { it.accept },</span><br/><span class="line">                mutableMapOf())</span><br/><span class="line">        <span class="keyword">while</span> (!statesToFind.isEmpty()) {</span><br/><span class="line">            <span class="comment">// 现在开始考虑这组NFA状态对应的DFA状态</span></span><br/><span class="line">            <span class="keyword">val</span> theStates = statesToFind.first()</span><br/><span class="line">            statesToFind.remove(theStates)</span><br/><span class="line">            <span class="keyword">for</span> (ch <span class="keyword">in</span> alphabet) {</span><br/><span class="line">                <span class="keyword">val</span> canGoTo = theStates.directConnectedTo(ch)</span><br/><span class="line">                <span class="keyword">val</span> equivalenteStates = canGoTo.equivalentStates</span><br/><span class="line">              	<span class="comment">// 不存在对应DFA状态，构建</span></span><br/><span class="line">                <span class="keyword">if</span> (equivalenteStates !<span class="keyword">in</span> result.keys) {</span><br/><span class="line">                    result[equivalenteStates] = Deterministic.State(nextChar++.toString(), equivalenteStates.any { it.accept }, mutableMapOf())</span><br/><span class="line">                    statesToFind.add(equivalenteStates)</span><br/><span class="line">                }</span><br/><span class="line">              	<span class="comment">// 添加该状态到其他状态的转换关系</span></span><br/><span class="line">                result[theStates]!!.transitions[ch] = result[equivalenteStates]!!</span><br/><span class="line">            }</span><br/><span class="line">        }</span><br/><span class="line">  			<span class="comment">// 保存为DFA并返回</span></span><br/><span class="line">        <span class="keyword">return</span> Deterministic(result.values.toSet(), result.values.minBy { it.name }!!)</span><br/><span class="line">    }</span><br/></pre></td></tr></table></figure>
<blockquote id="fn_0">
<sup>0</sup>. 就像DFS那样。<a href="#reffn_0" title="Jump back to footnote [0] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_1">
<sup>1</sup>. “确定性自动机是一种特殊的非确定性自动机”听上去太怪异了，实际上应该称NFA为“不一定确定自动机”，而将除DFA以外的NFA称为“非确定性自动机”感觉才对，我想这大概可能是翻译的锅吧。<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_2">
<sup>2</sup>. 这个例子来源于龙书<a href="#reffn_2" title="Jump back to footnote [2] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_3">
<sup>3</sup>. 注意每个状态可以看作和自己有一根$\epsilon$边相连<a href="#reffn_3" title="Jump back to footnote [3] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_4">
<sup>4</sup>. 我两年前写的代码真是惨不忍睹啊……<a href="#reffn_4" title="Jump back to footnote [4] in the text."> ↩</a>
</blockquote>

