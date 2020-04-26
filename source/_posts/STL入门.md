---
title: STL入门
date: 2017-09-27 00:00:00
tags: [新手级, C++, STL]
mathjax: true
comments: true
summary: STL快速入门
---
<blockquote>
<p>STL，虽然是一套程序库（library），却不只是一般印象中的程序库，而是一个有着划时代意义，背后拥有先进技术与深厚理论的产品。说他是产品也可以，说它是规格也可以，说是软件组件技术发展史上的一个大突破点，它也当之无愧。 ——《STL源码剖析》</p>
<p>对软件开发而言，STL是坚甲利兵，可以节省你许多时间。对编程技术而言，STL是金柜石室——所有与编程工作最有直接密切关联的一些最被广泛运用的数据结构和算法，STL都有实现，并符合最佳（或极佳）效率。 ——《STL源码剖析》</p>
</blockquote>
<p>在C++语言的编程过程中，很多问题都早已被无数程序猿解决过（尤其是数据结构和算法问题），为了让这些问题无需再被后来人无数次写出代码来解决，一群大佬就为C++编写了一套堪称完美的库文件：STL。</p>
<p>但由于设计者的思想极其高妙，STL没有那么容易被新手理解和使用，我写这篇文章尽量简单地讲明一些STL的使用方法，至于更多使用方法、设计思想甚至内部实现，请各位自行探究。</p>
<h2 id="容器"><a class="headerlink" href="#容器" title="容器"></a>容器</h2><blockquote>
<p>容器，置物之所也。 ——《STL源码剖析》</p>
</blockquote>
<p>所谓容器，就是<del>数据结构的实现</del>存放数据的地方。</p>
<p>其实我们平时使用的数组就是一种容器，不过这种容器极度的不灵活，大小是固定的，如果你不知道要放入的数据有多少个，那么你只能一次开一个很大的数组，这样非常浪费空间，而且你还要自己注意这个数组用到哪里了。如果你手动运用动态内存分配维护一个动态数组，那你正在重复造一个STL里有的轮子。</p>
<h3 id="vector"><a class="headerlink" href="#vector" title="vector"></a>vector</h3><p><code>vector</code>恐怕是使用最多的STL容器了。它就是传说中STL内置的动态数组。</p>
<p>制作一个<code>vector</code>的方法：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">// 头文件：&lt;vector&gt;</span></span><br/><span class="line"><span class="built_in">vector</span>&lt;类型名&gt; 变量名;</span><br/></pre></td></tr></table></figure>
<p>这样就制作了一个可以用来存放某一类型值的<code>vector</code>了。</p>
<p>制作<code>vector</code>的时候，不像一般的数组，需要手动指定它的大小，在需要往<code>vector</code>中添加元素的时候，只需要简单地使用<code>push_back</code>即可：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v;</span><br/><span class="line">v.push_back(<span class="number">1</span>);</span><br/><span class="line"><span class="comment">// v:[1]</span></span><br/><span class="line">v.push_back(<span class="number">2</span>);</span><br/><span class="line"><span class="comment">// v:[1,2]</span></span><br/></pre></td></tr></table></figure>
<p>而要访问<code>vector</code>中的元素，只需像普通的数组一样使用<code>[]</code>运算符即可。</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">// 接上例</span></span><br/><span class="line">v[<span class="number">0</span>] <span class="comment">//1</span></span><br/><span class="line">v[<span class="number">1</span>] <span class="comment">//2</span></span><br/><span class="line">v[<span class="number">2</span>] <span class="comment">//很遗憾，不会报错，但是这样做也是不正确的，这和访问普通的数组时越界一样危险</span></span><br/></pre></td></tr></table></figure>
<p>元素类型为<code>vector</code>的<code>vector</code>可以作为多维数组使用：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/><span class="line">13</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt;&gt; v;<span class="comment">//vector&lt;int&gt;类型的vector</span></span><br/><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v1;</span><br/><span class="line">v1.push_back(<span class="number">1</span>);</span><br/><span class="line">v1.push_back(<span class="number">2</span>);</span><br/><span class="line">v.push_back(v1);</span><br/><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v2;</span><br/><span class="line">v1.push_back(<span class="number">3</span>);</span><br/><span class="line">v1.push_back(<span class="number">4</span>);</span><br/><span class="line">v.push_back(v2);</span><br/><span class="line">v[<span class="number">0</span>][<span class="number">0</span>] <span class="comment">//1</span></span><br/><span class="line">v[<span class="number">0</span>][<span class="number">1</span>] <span class="comment">//2</span></span><br/><span class="line">v[<span class="number">1</span>][<span class="number">0</span>] <span class="comment">//3</span></span><br/><span class="line">v[<span class="number">1</span>][<span class="number">1</span>] <span class="comment">//4</span></span><br/></pre></td></tr></table></figure>
<p>在向<code>vector</code>中插入了很多数据之后，如果你想查看<code>vector</code>里数据的个数，只需要使用<code>size</code>函数：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v;</span><br/><span class="line">v.push_back(<span class="number">1</span>);</span><br/><span class="line">v.push_back(<span class="number">2</span>);</span><br/><span class="line">v.size() <span class="comment">// 2</span></span><br/></pre></td></tr></table></figure>
<p>而<code>pop_back</code>函数可以用来从<code>vector</code>尾部删除元素：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">// 接上例</span></span><br/><span class="line">v.pop_back() <span class="comment">// v:[1]</span></span><br/><span class="line">v.size() <span class="comment">// 1</span></span><br/></pre></td></tr></table></figure>
<p><code>vector</code>还有很多成员函数，这里列出部分：</p>
<div class="table-container">
<table>
<thead>
<tr>
<th>函数</th>
<th>简介</th>
</tr>
</thead>
<tbody>
<tr>
<td>at(n)</td>
<td>作用同[n]，但是会检测是否越界</td>
</tr>
<tr>
<td>front()</td>
<td>返回第一个元素的引用</td>
</tr>
<tr>
<td>back()</td>
<td>返回最后一个元素的引用</td>
</tr>
<tr>
<td>clear()</td>
<td>清空<code>vector</code></td>
</tr>
</tbody>
</table>
</div>
<p>其余有些等我后面说了<code>iterator</code>再说……（x1）</p>
<h3 id="deque"><a class="headerlink" href="#deque" title="deque"></a>deque</h3><p>头文件：<code>&lt;deque&gt;</code></p>
<p><code>deque</code>就是两端都开口的<code>vector</code>，你或许注意到了<code>vector</code>只有<code>push_back</code>而没有<code>push_front</code>    ，这是由<code>vector</code>的内部实现原理决定的，而<code>deque</code>则突破了这一个限制，增加了<code>push_front</code>和<code>pop_front</code>两个函数。还有一些函数等我后面说了<code>iterator</code>再说……（x2）</p>
<h3 id="list"><a class="headerlink" href="#list" title="list"></a>list</h3><p>头文件：<code>&lt;list&gt;</code></p>
<p><code>list</code>是一个双向链表。链表的优点就是可以快速地插入和删除任何位置的数据，但缺点是无法使用<code>[]</code>运算符求出第n个元素。</p>
<p><code>list</code>拥有上面的<code>deque</code>的除<code>[]</code>和<code>at</code>所有功能，至于如何插入和删除数据……等我后面说了<code>iterator</code>再说……（x3）</p>
<h3 id="set"><a class="headerlink" href="#set" title="set"></a>set</h3><p>头文件：<code>&lt;set&gt;</code></p>
<p><code>set</code>大致相当于数学中的集合，可以用<code>insert(x)</code>插入一个元素，但重复插入多个相同元素后只会保留一个元素。也可以用<code>erase(x)</code>来移除一个元素。</p>
<h3 id="map"><a class="headerlink" href="#map" title="map"></a>map</h3><p>头文件：<code>&lt;map&gt;</code></p>
<p><code>map</code>相当于数学中的映射，可以将一个数（称为键）映射到另外一个数（称为值）。</p>
<p>使用方法也很简单：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">map</span>&lt;<span class="keyword">int</span>,<span class="keyword">int</span>&gt; m; <span class="comment">//从int映射到int</span></span><br/><span class="line">m[<span class="number">0</span>] = <span class="number">100</span>; <span class="comment">//将0对应的值设置为100</span></span><br/><span class="line">m[<span class="number">2</span>] = <span class="number">200</span>; <span class="comment">//将2对应的值设置为200</span></span><br/><span class="line">m[<span class="number">0</span>] <span class="comment">// 100</span></span><br/><span class="line">m[<span class="number">1</span>] <span class="comment">// 0，map有默认值的</span></span><br/></pre></td></tr></table></figure>
<h3 id="其他"><a class="headerlink" href="#其他" title="其他"></a>其他</h3><p>还有一些其他STL容器，在此并不一一详细讲解。</p>
<p>稍微列张表吧，具体怎么用上网查：</p>
<div class="table-container">
<table>
<thead>
<tr>
<th>容器</th>
<th>用途</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr>
<td>stack</td>
<td>栈，只能从一端插入、读取和移除元素</td>
<td>使用<code>push</code>来放入元素，<code>pop</code>来移除元素，<code>top</code>来看当前栈顶的元素，不能用<code>[]</code>访问</td>
</tr>
<tr>
<td>queue</td>
<td>队列，只能从一端插入，从另一端移除元素，但可以从两端读取值。</td>
<td>使用<code>push()</code>来放入元素，<code>pop</code>来移除元素， <code>front()</code>和<code>back()</code>来查询头和尾部的元素</td>
</tr>
<tr>
<td>priority_queue</td>
<td>优先队列，会自动将插入的元素<del>建堆，算了为了容易理解可以看作</del>排序，然后将最大（或最小，可自定义）的元素放置在 top处。</td>
<td>使用<code>push()</code>来放入元素，<code>pop()</code>来移除top处的元素，<code>top()</code>用于查看top处的元素</td>
</tr>
<tr>
<td>multiset</td>
<td>同set，但允许有多个相同的值</td>
<td></td>
</tr>
<tr>
<td>multimap</td>
<td>同set，但允许有多个相同的键</td>
</tr>
</tbody>
</table>
</div>
<p>另外值得一题的是，在STL推出后，<code>string</code>类也被改写成类似<code>vector</code>的形式了，因此<code>string</code>也可以被视作一种STL容器。</p>
<h2 id="迭代器"><a class="headerlink" href="#迭代器" title="迭代器"></a>迭代器</h2><blockquote>
<p>itertator模式：提供一种方法，使之能够依序寻访某个聚合物（容器）所含的各个元素，而又无需暴露该聚合物的内部表述方式。 ——《设计模式》</p>
</blockquote>
<p>迭代器这个东西很好用，但是……很玄妙，比较难以理解。（语死早的）我尽量讲清楚。</p>
<p>我们结合例子来理解。</p>
<p>例如有这么一个<code>vector</code>：</p>
<p><img alt="Array" src="./Array.svg"/></p>
<p>迭代器就是一种变量。</p>
<p>迭代器和指针有些类似（实际上指针就是一种迭代器），可以指向<code>vector</code>中的一个元素。</p>
<p>例如有一个迭代器a，指向“21”这个元素：</p>
<p><img alt="Iterator" src="./Iterator.svg"/></p>
<p>此时可以利用<code>a</code>读取所指元素的值：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">*a <span class="comment">// 21</span></span><br/></pre></td></tr></table></figure>
<p>也可以让a在<code>vector</code>中往后进一格：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line">++a <span class="comment">//或者a++</span></span><br/><span class="line"><span class="comment">// *a 现在是 89</span></span><br/><span class="line">*(a+<span class="number">1</span>)<span class="comment">//检测a所指的元素的下一个元素的值，得到95</span></span><br/></pre></td></tr></table></figure>
<p><img alt="add1" src="./add1.svg"/></p>
<p>而用一些成员函数就能求出一个<code>vector</code>的第一个，倒数第一个，第一个之前的和最后一个之后的元素。</p>
<p><img alt="b_and_e" src="./b_and_e.svg"/></p>
<p>注意<code>rbegin()</code>和<code>rend()</code>两个得到的迭代器是“反向迭代器”，这是一种特别的迭代器，对它进行自增操作实际上是把迭代器往<code>vector</code>的前面移动，它的“后面”实际上就是<code>vector</code>的“前面”。</p>
<p>第一个元素之前和最后一个元素之后的迭代器是干什么用的呢？我们结合程序来讲解：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/><span class="line">13</span><br/><span class="line">14</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; a;</span><br/><span class="line"><span class="comment">// a.push_back(...);</span></span><br/><span class="line"><span class="comment">// ...</span></span><br/><span class="line"><span class="comment">//向a中插入一些元素，使其和上面图示的vector内容相同</span></span><br/><span class="line"><span class="comment">//下面这个循环用来输出</span></span><br/><span class="line"><span class="comment">//感谢C++11，auto可以让编译器推断类型，我们不再需要打类型名了</span></span><br/><span class="line"><span class="comment">//其实it的类型是vector&lt;int&gt;::iterator，实在太长了……所以说C++11出来之前我不大用STL的</span></span><br/><span class="line"><span class="keyword">for</span>(<span class="keyword">auto</span> it=a.begin(); it != a.end(); ++it) {</span><br/><span class="line">    <span class="built_in">cout</span> &lt;&lt; *it &lt;&lt; <span class="string">' '</span>;</span><br/><span class="line">}</span><br/><span class="line"><span class="comment">//从后往前输出</span></span><br/><span class="line"><span class="keyword">for</span>(<span class="keyword">auto</span> it=a.rbegin(); it != a.rend(); ++it) {</span><br/><span class="line">    <span class="built_in">cout</span> &lt;&lt; *it &lt;&lt; <span class="string">' '</span>;</span><br/><span class="line">}</span><br/></pre></td></tr></table></figure>
<p>这里<code>it != a.end()</code>这句就显示出“最后一个元素之后的迭代器”（一般称之为超尾迭代器）的方便性了。</p>
<p>（其实遍历vector有更方便的方法，请自己查range-based-for的资料）</p>
<p>那么到底为什么要有迭代器这种奇怪的东西呢？用索引访问vector不是挺好的吗？</p>
<p>没错，是挺好的，但是这样的话，有些不能用<code>[]</code>访问内容的容器（例如<code>list</code>）怎么遍历呢？</p>
<p>更进一步，如果你写了一个算法，却要针对所有不同种类的容器实现一遍，这岂不是要累死人？</p>
<p>所以伟大的STL就引入了迭代器这个东西。</p>
<p>不同种类的容器都有各自的迭代器，迭代器分为几类，每类迭代器支持不同的运算，具体分类比较复杂，牵扯到类和继承的问题，在此不讲。</p>
<p>几乎每种容器都提供了<code>begin()</code>、<code>end()</code>、<code>rbegin()</code>和<code>rend()</code>这些求迭代器的函数。</p>
<p>引入迭代器之后，STL就能无视容器内部的差异，提供一系列能在任何一种能求出相应迭代器的容器上运行的，相当NB的现成算法，让我们在写代码的时候用的超爽。</p>
<h2 id="算法"><a class="headerlink" href="#算法" title="算法"></a>算法</h2><p>大多数算法都在<code>&lt;algorithm&gt;</code>中。</p>
<ol>
<li><p><code>sort</code></p>
<p>就是简单的排序。</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">sort(起始迭代器, 超尾迭代器, 判断函数)</span><br/></pre></td></tr></table></figure>
<p>其中判断函数可以省略，默认为从小到大排。</p>
<p>例子：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v = {<span class="number">5</span>, <span class="number">7</span>, <span class="number">4</span>, <span class="number">2</span>, <span class="number">8</span>, <span class="number">6</span>, <span class="number">1</span>, <span class="number">9</span>, <span class="number">0</span>, <span class="number">3</span>};</span><br/><span class="line">sort(v.begin(), v.end());<span class="comment">//[0,1,2,3,4,5,6,7,8,9]</span></span><br/><span class="line">sort(v.begin(), v.end(),[](<span class="keyword">int</span> a, <span class="keyword">int</span> b) { <span class="comment">//从大到小，请看上期FP中的lambda章节</span></span><br/><span class="line">  <span class="keyword">return</span> b &lt; a;</span><br/><span class="line">});</span><br/><span class="line"><span class="comment">//[9,8,7,6,5,4,3,2,1,0]</span></span><br/></pre></td></tr></table></figure>
<p>注意<code>sort</code>排序不stable（什么是stable排序请自行查阅资料），如果需要stable的排序，STL中也有一个使用方法和<code>sort</code>类似的<code>stable_sort</code>。</p>
</li>
<li><p><code>merge</code></p>
<p>将两个排好序的序列合并成一个排好序的序列。</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line">merge( 序列<span class="number">1</span>起始迭代器, 序列<span class="number">1</span>超尾迭代器,</span><br/><span class="line">                序列<span class="number">2</span>起始迭代器, 序列<span class="number">2</span>超尾迭代器,</span><br/><span class="line">                结果输出起始迭代器 );</span><br/></pre></td></tr></table></figure>
<p>其中“结果输出起始迭代器”常常使用<code>back_insert_iterator</code>（需要头文件<code>&lt;iterator&gt;</code>），这是一种用来向容器尾部插入数据的神奇迭代器。</p>
<p>例子：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">std</span>::<span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v1={<span class="number">0</span>,<span class="number">1</span>,<span class="number">3</span>,<span class="number">4</span>,<span class="number">4</span>,<span class="number">5</span>,<span class="number">5</span>,<span class="number">8</span>,<span class="number">8</span>,<span class="number">9</span>}, </span><br/><span class="line">				 v2={<span class="number">0</span>,<span class="number">2</span>,<span class="number">2</span>,<span class="number">3</span>,<span class="number">6</span>,<span class="number">6</span>,<span class="number">8</span>,<span class="number">8</span>,<span class="number">8</span>,<span class="number">9</span>};</span><br/><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; dst;</span><br/><span class="line">merge(v1.begin(), v1.end(), v2.begin(), v2.end(), back_inserter(dst));</span><br/><span class="line"><span class="comment">//dst: [0 0 1 2 2 3 3 4 4 5 5 6 6 8 8 8 8 8 9 9]</span></span><br/></pre></td></tr></table></figure>
</li>
<li><p><code>find</code></p>
<p>在序列中查找元素。</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line">find(起始迭代器, 超尾迭代器, 要寻找的元素);</span><br/><span class="line"><span class="comment">// 返回指向找到元素的迭代器，如果没有找到，返回超尾迭代器</span></span><br/></pre></td></tr></table></figure>
<p>例子：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v{<span class="number">0</span>, <span class="number">1</span>, <span class="number">2</span>, <span class="number">3</span>, <span class="number">4</span>};</span><br/><span class="line">find(v.begin(), v.end(), <span class="number">3</span>); <span class="comment">//返回指向3的迭代器</span></span><br/><span class="line">find(v.begin(), v.end(), <span class="number">5</span>); <span class="comment">//返回等于v.end()的迭代器</span></span><br/></pre></td></tr></table></figure>
<p><code>map</code>、<code>set</code>等容器实现了作为成员函数的<code>find</code>，具体用法请自行探究。</p>
</li>
<li><p><code>search</code></p>
<p>在序列中查找序列。</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v{<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>,<span class="number">4</span>,<span class="number">5</span>};</span><br/><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; s{<span class="number">2</span>,<span class="number">3</span>,<span class="number">4</span>};</span><br/><span class="line">search(v.begin(),v.end(),s.begin(),s.end()); <span class="comment">// 返回指向v中2的迭代器</span></span><br/><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; s2{<span class="number">5</span>,<span class="number">6</span>,<span class="number">7</span>};</span><br/><span class="line">search(v.begin(),v.end(),s2.begin(),s2.end()); <span class="comment">// 返回等于v.end()的迭代器</span></span><br/></pre></td></tr></table></figure>
</li>
<li><p><code>next_permutation</code></p>
<p>将所给序列变为所给序列的下一个排列。在达到最后一个可能的排列时返回<code>false</code>。</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">string</span> s = <span class="string">"aba"</span>;</span><br/><span class="line">sort(s.begin(), s.end());</span><br/><span class="line"><span class="keyword">do</span> {</span><br/><span class="line">        <span class="built_in">cout</span> &lt;&lt; s &lt;&lt; <span class="string">'\n'</span>;</span><br/><span class="line">} <span class="keyword">while</span>(next_permutation(s.begin(), s.end()));</span><br/><span class="line"><span class="comment">//输出：</span></span><br/><span class="line"><span class="comment">//aab</span></span><br/><span class="line"><span class="comment">//aba</span></span><br/><span class="line"><span class="comment">//baa</span></span><br/></pre></td></tr></table></figure>
<p>与之相对的，有<code>prev_permutation</code>，将所给序列变为所给序列的上一个排列。</p>
</li>
<li><p><code>max_element</code></p>
<p>返回指向一个序列中最大的元素的迭代器。</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v{ <span class="number">3</span>, <span class="number">1</span>, <span class="number">-14</span>, <span class="number">1</span>, <span class="number">5</span>, <span class="number">9</span> };</span><br/><span class="line">max_element(v.begin(), v.end()) <span class="comment">//返回指向9的迭代器</span></span><br/></pre></td></tr></table></figure>
<p>与之相对的，有<code>min_element</code>返回指向一个序列中最小的元素的迭代器。</p>
</li>
<li><p>还有很多其他的STL算法，但是不是使用不多就是使用方法比较……奇妙，我难以用容易理解的方式说清楚。请大家自己查阅资料。</p>
<h2 id="依赖于迭代器的容器操作"><a class="headerlink" href="#依赖于迭代器的容器操作" title="依赖于迭代器的容器操作"></a>依赖于迭代器的容器操作</h2><p>欠债还钱，前面说“说了<code>iterator</code>再说……”的内容在此补上：</p>
<ol>
<li><p><code>insert</code>成员函数（<code>vector</code>、<code>deque</code>、<code>list</code>等容器都实现了这个成员函数）</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">v.insert(v中的迭代器, 要插入的值);</span><br/></pre></td></tr></table></figure>
<p>作用是向“v中的迭代器”之前（想一想，为什么是之前？）插入值。</p>
<p>例子：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v{<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>}</span><br/><span class="line">vec.insert(v.begin(), <span class="number">200</span>);<span class="comment">//v:[200,1,2,3]</span></span><br/></pre></td></tr></table></figure>
<p>也可以这样用：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">v.insert(v中的迭代器, 要插入的序列的起始迭代器, 要插入的序列的超尾迭代器);</span><br/></pre></td></tr></table></figure>
<p>来一次插入一个序列。</p>
<p>（记得第一个参数的迭代器可以使用<code>back_insert_iterator</code>等神奇的迭代器。）</p>
</li>
<li><p><code>erase</code>成员函数（<code>vector</code>、<code>deque</code>、<code>list</code>等容器都实现了这个成员函数）</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">v.erase(v中的迭代器)</span><br/></pre></td></tr></table></figure>
<p>作用是从序列中删除迭代器所指的元素。</p>
<p>例子：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; c{<span class="number">0</span>, <span class="number">1</span>, <span class="number">2</span>, <span class="number">3</span>, <span class="number">4</span>, <span class="number">5</span>, <span class="number">6</span>, <span class="number">7</span>, <span class="number">8</span>, <span class="number">9</span>};</span><br/><span class="line">c.erase(c.begin());<span class="comment">//c:[1, 2, 3, 4, 5, 6, 7, 8, 9]</span></span><br/></pre></td></tr></table></figure>
<p>​</p>
<p>注意如果用在<code>vector</code>、<code>deque</code>这些容器上，会使得删除的迭代器和它之后的迭代器全部失效，而<code>list</code>容器则不存在这种问题（这和内部实现有关）。</p>
<p>和插入同理，可以一次删除一个序列：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">v.erase(起始迭代器, 超尾迭代器);</span><br/></pre></td></tr></table></figure>
</li>
</ol>
<p>还有一些别的，我说不动了……</p>
</li>
</ol>
<p><del>这篇好长，我写了好久……累死了，STL这个话题太庞大了，还要考虑你们的接受能力……</del></p>
<p><del>由于太累，临表涕零，不知所言，如果写的有问题，请多包涵。</del></p>
<p>顺便给大家一个查C++标准库的网站（有Dash/Zeal的就用不到了）：<a href="http://www.cplusplus.com" rel="noopener" target="_blank">C++标准库查询</a></p>

