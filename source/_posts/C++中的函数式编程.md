---
title: C++中的函数式编程
date: 2017-09-24 00:00:00
tags: [C++, 函数式编程, STL]
mathjax: true
comments: true
summary: 如何使用C++进行函数式编程
---
<h1 id="C-中的函数式编程"><a class="headerlink" href="#C-中的函数式编程" title="C++中的函数式编程"></a>C++中的函数式编程</h1><p>自从学了Haskell，了解到函数式编程的强大力量之后，我所有的程序中都带上了FP的味道。在很多情况下，FP可以使代码变得跟短，更“自文档化”（虽然不当的使用也会反其道而行之）。</p>
    <p>虽然C++对函数式编程并没有“First Class”的支持，但是正如可以用C语言写出面向对象风格的程序一样，用C++也可以写出有函数式风格的程序，尤其是在C++11、14、17等先进标准相继推出的现代，使用C++进行函数式编程更加的容易。</p>
<p>正如《代码大全》所说：</p>
<blockquote>
<p>“深入一种语言去编程” 的程序员首先决定他要表达的思想是什么，然后决定如何使用特定语言提供的工具来表达这些思想。</p>
</blockquote>
<p>（本文主要建立在C++11标准上讲述，因为这是我校的OJ支持的最高C++版本。）</p>
<h2 id="Lambda-expression"><a class="headerlink" href="#Lambda-expression" title="Lambda expression"></a>Lambda expression</h2><p>C++11引入了Lambda表达式的概念，使我们可以构造一个匿名函数（闭包）来在需要使用函数却不适合写一个“真正的”函数或callable object时使用。</p>
<p>多数时候使用的Lambda expression大概长这样：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">[](参数列表) { 函数体 }</span><br/></pre></td></tr></table></figure>
<p>其中前面那对<code>[]</code>是用来进行capture（捕获）的，<del>啥是捕获本人嘴笨越讲越不清楚，干脆不讲了，反正后面用不到</del>请各位自己查资料了解。</p>
<p>我们可以将lambda expression 赋给一个变量，然后像一个普通函数那样使用它：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line"><span class="keyword">auto</span> f = [](<span class="keyword">int</span> a,<span class="keyword">int</span> b){<span class="keyword">return</span> a &gt; b;};</span><br/><span class="line">f(<span class="number">2</span>,<span class="number">3</span>) <span class="comment">//false</span></span><br/></pre></td></tr></table></figure>
<p>感谢C++11，我们无需知道lambda expression真正的类型是什么（实际上，官方文档说闭包的类型是写不出来的）。</p>
<p>一般情况下lambda表达式的返回值的类型会自动推断得到，所以无需手动标明返回类型，不过如果你觉得有必要标明的话，可以：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">[](参数) -&gt; 返回类型 { 函数体 }</span><br/></pre></td></tr></table></figure>
<p>有了闭包，C++的“函数”总算是和函数式编程中的函数拥有类似的地位了。</p>
<p>如果需要知道在没有C++11的情况下如何使用<code>function</code>等技术来实现一些和lambda基本等效（但要写多得多的代码）的“函数”，请自行查阅资料。</p>
<h2 id="map、reduce、filter"><a class="headerlink" href="#map、reduce、filter" title="map、reduce、filter"></a>map、reduce、filter</h2><p>map-reduce-filter这一组函数在函数式编程中简直令人爱不释手，很大程度上就是它们在函数式编程中杀死了循环。</p>
<p>我曾经一度以为C++中没有实现这三个函数（况且map都用来做了容器的名字），直到后来我才知道它们其实一直都在，只是改名换姓藏在不同的头文件里，才让我找不到它们。</p>
<h3 id="map——for-each、transform"><a class="headerlink" href="#map——for-each、transform" title="map——for_each、transform"></a>map——for_each、transform</h3><p>map的作用是对一个序列中的每一个元素执行一个操作。</p>
<ol>
<li><p><code>for_each</code></p>
<p>在C++中map函数对应的函数主要是for_each（在<code>&lt;algorithm&gt;</code>中）。</p>
<p>使用方法是这样：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">for_each(初始迭代器, 结尾迭代器, 应用的操作);</span><br/></pre></td></tr></table></figure>
<p>例如：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; nums{<span class="number">3</span>,<span class="number">2</span>,<span class="number">4</span>,<span class="number">6</span>,<span class="number">9</span>,<span class="number">7</span>};</span><br/><span class="line">for_each(nums.begin(), nums.end(), [](<span class="keyword">int</span> &amp;n){ n++; });</span><br/><span class="line"><span class="comment">// nums现在是[4,3,5,7,10,8]</span></span><br/></pre></td></tr></table></figure>
<p>​</p>
</li>
<li><p><code>transform</code></p>
<p>如果需要对一个你不希望或不能改变其中内容的序列进行map操作，并将结果复制到一个新序列中，那你就应该使用transform。</p>
<p>这种不改变原序列的方式更加接近纯函数式编程中的map，但是相比for_each来说transform使用不太方便（其实也就多了一个参数）。</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">transform(初始迭代器, 结尾迭代器, 要保存结果位置的初始迭代器, 应用的操作);</span><br/></pre></td></tr></table></figure>
<p>其中第三个参数一般会填一个<code>back_insert_iterator</code>。</p>
<p>和上面类似的例子：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">// 注意back_inserter在&lt;iterator&gt;里</span></span><br/><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; nums{<span class="number">3</span>,<span class="number">2</span>,<span class="number">4</span>,<span class="number">6</span>,<span class="number">9</span>,<span class="number">7</span>},output;</span><br/><span class="line">transform(nums.begin(), nums.end(), back_inserter(output) ,[](<span class="keyword">int</span> &amp;a){<span class="keyword">return</span> a+<span class="number">1</span>;});</span><br/><span class="line"><span class="comment">// nums没有变化，output是[4,3,5,7,10,8]</span></span><br/></pre></td></tr></table></figure>
<p>当然如果你要是想用transform达到<code>for_each</code>的效果，“要保存结果位置的初始迭代器”也填“初始迭代器”就好了。</p>
</li>
</ol>
<p>其实如果我能实现的话，我会把C++中的map函数实现成返回一个<code>initializer_list</code>的右值引用，这样就真的和函数式的map一模一样了。</p>
<h3 id="reduce——accumulate、reduce-C-17"><a class="headerlink" href="#reduce——accumulate、reduce-C-17" title="reduce——accumulate、reduce(C++17)"></a>reduce——accumulate、reduce(C++17)</h3><p>reduce的作用是对某个序列的第一个元素和初始值进行一个操作，再将得到的结果和第二个元素进行同样的操作，再将得到的结果和第三个元素进行同样的操作……直到整个序列都被如此操作过为止。</p>
<p>⬆️有点难理解，看个例子吧：</p>
<p>简单的求和问题，1+2+3+4+…+100，不用公式。</p>
<p>reduce是这样做的：</p>
<p>先对初始值（0）和序列中的第一个值（1）进行操作（加法），得到结果1，</p>
<p>再对结果值（1）和序列中的第二个值（2）进行操作（加法），得到结果3，</p>
<p>再对结果值（3）和序列中的第三个值（4）进行操作（加法），得到结果7，</p>
<p>…</p>
<p>再对结果值（4950）和序列中的最后一个值（100）进行操作（加法），得到结果5050。</p>
<p>没有更多元素了，故最终结果就是5050。</p>
<ol>
<li><p><code>accumulate</code></p>
<p>在C++17标准之前，reduce函数在C++中对应的函数是accumulate。</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">accumulate(初始迭代器, 结尾迭代器, 初始值, 要执行的操作);</span><br/></pre></td></tr></table></figure>
<p>如果不提供要执行的操作（即省略不写），那么默认是加法操作。</p>
<p>例子：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v{<span class="number">1</span>, <span class="number">2</span>, <span class="number">3</span>, <span class="number">4</span>, <span class="number">5</span>, <span class="number">6</span>, <span class="number">7</span>, <span class="number">8</span>, <span class="number">9</span>, <span class="number">10</span>};</span><br/><span class="line">accumulate(v.begin(), v.end(), <span class="number">0</span>);<span class="comment">//55</span></span><br/><span class="line">accumulate(v.begin(), v.end(), <span class="number">1</span>, [](<span class="keyword">int</span> a,<span class="keyword">int</span> b){<span class="keyword">return</span> a*b;});<span class="comment">//3628800</span></span><br/><span class="line">accumulate(v.begin(), v.end(), <span class="number">1</span>, <span class="built_in">std</span>::multiplies&lt;<span class="keyword">int</span>&gt;());</span><br/><span class="line"><span class="comment">// 和上句等效，需要&lt;functional&gt;，更多C++内置函数模版请自己查文档</span></span><br/><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">double</span>&gt; v1{<span class="number">1.1</span>, <span class="number">2.2</span>, <span class="number">3.3</span>, <span class="number">4</span>, <span class="number">5</span>, <span class="number">6</span>, <span class="number">7</span>, <span class="number">8</span>, <span class="number">9</span>, <span class="number">10</span>};</span><br/><span class="line">accumulate(v1.begin(), v1.end(), <span class="number">0</span>); <span class="comment">//还是55</span></span><br/><span class="line"><span class="comment">//这里是个坑，accumulate每次操作的结果是根据初始值的类型决定的</span></span><br/><span class="line">accumulate(v1.begin(), v1.end(), <span class="number">0.</span>); <span class="comment">//55.6</span></span><br/></pre></td></tr></table></figure>
<p>此外，使用<code>rbegin</code>和<code>rend</code>函数返回的反向迭代器可以实现从后往前reduce的操作（类似Haskell中的<code>rfold</code>）。</p>
</li>
<li><p><code>reduce</code></p>
<p>和<code>accumulate</code>只有微小的区别，由于是C++17的新特性（很多情况下都不能用，尤其是OJ），请读者自行探究。</p>
</li>
</ol>
<h3 id="filter——copy-if"><a class="headerlink" href="#filter——copy-if" title="filter——copy_if"></a>filter——copy_if</h3><p>filter的作用是从某个序列中筛选出符合条件的元素。</p>
<ol>
<li><p><code>copy_if</code></p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">copy_if(初始迭代器, 结尾迭代器, 要保存结果位置的初始迭代器, 判断函数);</span><br/></pre></td></tr></table></figure>
<p>用法和<code>transform</code>有点像</p>
<p>例子：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; v{<span class="number">-1</span>, <span class="number">2</span>, <span class="number">3</span>, <span class="number">-4</span>, <span class="number">5</span>, <span class="number">6</span>, <span class="number">-7</span>, <span class="number">8</span>, <span class="number">9</span>, <span class="number">10</span>};</span><br/><span class="line"><span class="built_in">vector</span>&lt;<span class="keyword">int</span>&gt; result;</span><br/><span class="line">copy_if(v.begin(),v.end(),back_inserter(result),[](<span class="keyword">int</span> a){<span class="keyword">return</span> a&gt;<span class="number">0</span>;});</span><br/><span class="line"><span class="comment">// result:[2,3,5,6,8,9,10]</span></span><br/></pre></td></tr></table></figure>
<p>其他还有一些<code>remove_if</code>之类的函数也能达到类似的效果（虽然并不像标准的filter），请读者自行研究。</p>
</li>
</ol>
<p>C++中比较常用的函数式特性大概就是这些，不过记住函数式编程有利也有弊，请根据情况选择是否要使用。</p>

