---
title: Haskell与函数式编程
date: 2018-09-05 00:00:00
tags: [函数式编程, Haskell]
mathjax: true
comments: true
summary: 函数式编程是近几年新兴的一种编程的思维方式，而Haskell作为一门纯函数式编程语言，可以极大的帮助一个程序员学会用函数式思维方式思考。
---
<p>你也许不会在工作中用一门纯函数式编程语言（那样对大脑太不友好），但你应当学习这样一门语言，因为这将会改变你的思维，即使你平常使用的是一些常见的语言，如C++/Java(8+)/Python/JS，你还是能从这些语言中找到函数式编程的影子，另外，函数式编程思想在架构的设计上也有很大的作用。</p>
    <h2 id="What’s-the-difference"><a class="headerlink" href="#What’s-the-difference" title="What’s the difference?"></a>What’s the difference?</h2><p>函数式编程语言和我们平常使用比较多的命令式编程语言有极大的不同。</p>
<p>（像Haskell这样的）纯函数式编程语言和命令式编程语言有以下几个巨大的不同：</p>
<ol>
<li>没有变量，一切量都是不变的<ul>
<li>因此也没有循环了</li>
<li>也没有传统意义上的逻辑判断</li>
<li>因此函数都是“纯函数”，因为没有变量，所以也几乎不可能有副作用<sup><a href="#fn_3" id="reffn_3">3</a></sup></li>
</ul>
</li>
<li>函数是”一等公民”，每个函数都是一个变量，有其类型，能作为其他函数的参数<sup><a href="#fn_2" id="reffn_2">2</a></sup></li>
</ol>
<p>看到这里你就意识到了，你需要把你在命令式编程语言界学到的大部分知识全部忘掉。</p>
<p>确实是这样，因为这完全是两种不同的思维体系，函数式编程和命令式编程的关系就像欧式几何和非欧几何一样。</p>
<p>你也许会以为没有变量是没有办法编程的<sup><a href="#fn_1" id="reffn_1">1</a></sup>，但这是一种常见的误解，你将在下面看到，变量不仅没那么重要，而且有时“可变”只会带来麻烦。</p>
<h2 id="Why-Haskell"><a class="headerlink" href="#Why-Haskell" title="Why Haskell?"></a>Why Haskell?</h2><p>Haskell除了是一门纯函数式编程语言外，还有如下特点</p>
<ol>
<li>惰性求值，所有的值不被用到就不会被计算</li>
<li>静态强类型，并有着我所见过最好的类型系统</li>
</ol>
<h2 id="基本操作"><a class="headerlink" href="#基本操作" title="基本操作"></a>基本操作</h2><h3 id="基本数据类型"><a class="headerlink" href="#基本数据类型" title="基本数据类型"></a>基本数据类型</h3><p>Haskell中的数据类型和C中的大致相同，整数字符浮点数应有尽有。</p>
<p>此外，Haskell中比较厉害的一个类型是列表类型，下面会说。</p>
<h3 id="调用函数"><a class="headerlink" href="#调用函数" title="调用函数"></a>调用函数</h3><p>Haskell调用函数的方式和C语言不太一样：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">-- 假设有函数f</span></span><br/><span class="line"><span class="title">f</span> x <span class="comment">-- 这就是计算f(x)了</span></span><br/><span class="line"><span class="title">g</span> x y <span class="comment">-- g(x,y)</span></span><br/></pre></td></tr></table></figure>
<p>Haskell省去了括号和逗号，这虽然让人不太适应，但也部分避免了像Lisp那样一屏幕的括号的尴尬。</p>
<h3 id="基本运算"><a class="headerlink" href="#基本运算" title="基本运算"></a>基本运算</h3><p><code>+</code>、<code>-</code>、<code>*</code>、<code>/</code>、<code>&amp;&amp;</code>、<code>||</code>、<code>==</code>都和C语言一样。</p>
<p>特别的，取反要用<code>not</code>（实际上这是个函数，在Haskell中这些运算符本质都是函数），C中的不等于符号<code>!=</code>，在这里是<code>/=</code>。</p>
<p>求余要用<code>mod</code>函数：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">mod</span> <span class="number">3</span> <span class="number">2</span></span><br/></pre></td></tr></table></figure>
<p>如果你想要像C语言中的<code>%</code>那样中缀调用<code>mod</code>的话，你可以使用<code>` </code>将函数包裹：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="number">3</span> `mod` <span class="number">2</span></span><br/></pre></td></tr></table></figure>
<h4 id="列表"><a class="headerlink" href="#列表" title="列表"></a>列表</h4><p>首先要提一句，Haskell中所谓的字符串还是字符列表的一个语法糖。</p>
<p>Haskell的列表是一个非常强大的玩意，我们可以用和Python中制作列表相似的方法做一个Haskell列表：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">[<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>,<span class="number">4</span>,<span class="number">5</span>,<span class="number">6</span>]</span><br/></pre></td></tr></table></figure>
<p>Haskell列表可以被拼接：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line">[<span class="number">1</span>,<span class="number">2</span>]++[<span class="number">3</span>,<span class="number">4</span>]++[<span class="number">5</span>,<span class="number">6</span>]</span><br/><span class="line"><span class="comment">-- [1,2,3,4,5,6]</span></span><br/></pre></td></tr></table></figure>
<p>也可以拼一个元素上去：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line"><span class="number">1</span>:[<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>]</span><br/><span class="line"><span class="comment">-- [1,1,2,3]</span></span><br/><span class="line"><span class="comment">-- 注意只能拼在前面</span></span><br/><span class="line"><span class="comment">-- [1,2,3]:1 会报错的</span></span><br/></pre></td></tr></table></figure>
<p>可以各种取元素：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/><span class="line">13</span><br/><span class="line">14</span><br/><span class="line">15</span><br/><span class="line">16</span><br/><span class="line">17</span><br/><span class="line">18</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">take</span> <span class="number">3</span> [<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>,<span class="number">4</span>,<span class="number">5</span>,<span class="number">6</span>,<span class="number">7</span>]</span><br/><span class="line"><span class="comment">-- 取前三个，[1,2,3]</span></span><br/><span class="line"><span class="title">drop</span> <span class="number">3</span> [<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>,<span class="number">4</span>,<span class="number">5</span>,<span class="number">6</span>,<span class="number">7</span>]</span><br/><span class="line"><span class="comment">-- 取前三个之外的元素，[4,5,6,7]</span></span><br/><span class="line"><span class="title">head</span> [<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>,<span class="number">4</span>,<span class="number">5</span>,<span class="number">6</span>,<span class="number">7</span>]</span><br/><span class="line"><span class="comment">-- 取第一个，1</span></span><br/><span class="line"><span class="title">tail</span> [<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>,<span class="number">4</span>,<span class="number">5</span>,<span class="number">6</span>,<span class="number">7</span>]</span><br/><span class="line"><span class="comment">-- 取除第一个之外的元素，[2,3,4,5,6,7]</span></span><br/><span class="line"><span class="title">init</span> [<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>,<span class="number">4</span>,<span class="number">5</span>,<span class="number">6</span>,<span class="number">7</span>]</span><br/><span class="line"><span class="comment">-- 取最后一个之外的元素，[1,2,3,4,5,6]</span></span><br/><span class="line"><span class="title">last</span> [<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>,<span class="number">4</span>,<span class="number">5</span>,<span class="number">6</span>,<span class="number">7</span>]</span><br/><span class="line"><span class="comment">-- 最后一个，7</span></span><br/><span class="line"><span class="number">1</span> `elem` [<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>]</span><br/><span class="line"><span class="title">elem</span> <span class="number">1</span> [<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>]</span><br/><span class="line"><span class="comment">-- 判断元素是否在列表中，两句等价，都为True</span></span><br/><span class="line">[<span class="number">1</span>,<span class="number">2</span>,<span class="number">3</span>,<span class="number">4</span>] !! <span class="number">2</span></span><br/><span class="line"><span class="comment">-- 取列表的第二个元素，3</span></span><br/><span class="line"><span class="comment">-- 用!!有点奇怪不是吗😓</span></span><br/></pre></td></tr></table></figure>
<p>另外构造列表还有很多特别的姿势：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">-- 使用区间</span></span><br/><span class="line">[<span class="number">1.</span><span class="number">.5</span>]</span><br/><span class="line"><span class="comment">-- [1,2,3,4,5]</span></span><br/><span class="line"><span class="comment">-- 一般的等差数列都能推出来</span></span><br/><span class="line">[<span class="number">1</span>,<span class="number">3.</span><span class="number">.42</span>]</span><br/><span class="line"><span class="comment">-- [1,3,5,7,9,11,13,15,17,19,21,23,25,27,29,31,33,35,37,39,41]</span></span><br/><span class="line"><span class="comment">-- 使用列表推导式</span></span><br/><span class="line">[x*<span class="number">2</span> | x &lt;- [<span class="number">1.</span><span class="number">.10</span>], x*<span class="number">2</span> &gt;= <span class="number">12</span>]</span><br/><span class="line"><span class="comment">-- 意为：对于在[1..10]之中的x，若有x*2 &gt;= 12，则将x*2放入列表</span></span><br/></pre></td></tr></table></figure>
<p>由于Haskell的惰性求值特性，你可以构造一个无穷的列表：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line">[<span class="number">1.</span>.]</span><br/><span class="line"><span class="comment">-- 从1开始，到无穷大为止</span></span><br/></pre></td></tr></table></figure>
<p>运用这个特点我们能做一些很酷的事情，比如求前10个7的倍数或末尾含7的数：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">take</span> <span class="number">10</span> [x | x &lt;- [<span class="number">1.</span>.], x `mod` <span class="number">7</span> ==<span class="number">0</span> || x `mod` <span class="number">10</span> == <span class="number">7</span>]</span><br/></pre></td></tr></table></figure>
<p>就只有一行代码，简单方便，可读性好。</p>
<p>你拿命令式语言写，估计得絮絮叨叨写一大坨了吧。</p>
<h3 id="自己写函数"><a class="headerlink" href="#自己写函数" title="自己写函数"></a>自己写函数</h3><p>Haskell的函数语法非常直白，很像数学中的函数：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">triple</span> x = <span class="number">3</span> * x</span><br/></pre></td></tr></table></figure>
<p>就定义好了一个函数<code>triple</code>，它的作用就是返回输入参数的三倍。</p>
<p>很像数学里的函数的写法：</p>
<script type="math/tex; mode=display">
triple(x) = 3*x</script><p>多个参数：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">length</span> x y = sqrt (x * x + y * y)</span><br/></pre></td></tr></table></figure>
<p>也很像数学中的函数：</p>
<script type="math/tex; mode=display">
length(x,y) = sqrt(x*x+y*y)</script><p>如果函数要分类讨论，可以使用“模式匹配”等技巧，它们在函数式编程中替代了逻辑判断：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">-- 当参数恰好匹配的时候会返回对应的值</span></span><br/><span class="line"><span class="title">sillyFunction</span> <span class="number">0</span> = <span class="number">0</span></span><br/><span class="line"><span class="title">sillyFunction</span> <span class="number">-1</span> = <span class="number">2</span></span><br/><span class="line"><span class="title">sillyFunction</span> <span class="number">2</span> = <span class="number">3</span></span><br/><span class="line"><span class="comment">-- 都没有匹配到，会进入这个默认的匹配</span></span><br/><span class="line"><span class="title">sillyFunction</span> x = x/<span class="number">2</span>+<span class="number">1</span></span><br/></pre></td></tr></table></figure>
<p>就像是：</p>
<script type="math/tex; mode=display">
sillyFunction(x) =
\begin{equation}
\begin{cases}
0 & x=0\\
-1 & x=2\\
2 & x=3 \\
x/2+1 & otherwise
\end{cases}
\end{equation}</script><p>记得匹配的顺序是从上到下，因此如果参数为x的匹配放到第一个那么就会GG。</p>
<p>（说起来有点像匹配URL啊……）</p>
<p>如果要匹配的是一个范围，那么应当使用“哨卫”语法：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">f</span> x</span><br/><span class="line"> | x &lt;= <span class="number">10</span> = x</span><br/><span class="line"> | x &lt;= <span class="number">20</span> = x/<span class="number">2</span></span><br/><span class="line"> | x &lt;= <span class="number">30</span> = x*x</span><br/><span class="line"> | otherwise = x*x*x</span><br/></pre></td></tr></table></figure>
<p>就像是：</p>
<script type="math/tex; mode=display">
f(x) =
\begin{equation}
\begin{cases}
x & x\le10\\
x/2 & 10 < x \le 20\\
x*x & 20 < x \le 30 \\
x*x*x & otherwise
\end{cases}
\end{equation}</script><p>如果在列表上进行模式匹配，可以这样写：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">listFunction</span> [] = <span class="string">"Empty!"</span></span><br/><span class="line"><span class="title">listFunction</span> (x:[]) = <span class="string">"Only One!"</span></span><br/><span class="line"><span class="title">listFunction</span> (x:y:[]) = <span class="string">"There are two!"</span></span><br/><span class="line"><span class="comment">-- 这里要提一下，show函数返回输入值的字符串表示</span></span><br/><span class="line"><span class="title">listFunction</span> (x:y:_) = <span class="string">"More than 2! First two are "</span> ++ show x ++ <span class="string">" and "</span> ++ show y</span><br/></pre></td></tr></table></figure>
<h4 id="递归"><a class="headerlink" href="#递归" title="递归"></a>递归</h4><blockquote>
<p>想要理解递归，你要先理解递归。</p>
</blockquote>
<p>有了上面那一堆东西，大部分命令式语言能实现的东西就能被实现了，但是我们还没讲到一样重要的东西：递归。</p>
<p>我们来考察“最大值”函数，它应该接受一个列表，返回列表中的最大值。</p>
<p>在命令式编程中，我们会使用一个循环来实现这一点。</p>
<p>然而我们没有循环了，该怎么办？</p>
<p>用递归啊。</p>
<p>我们可以这样想，如果一个列表里只有一个元素，那么这个元素就是最大的：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">maxInList</span> [x] = x</span><br/></pre></td></tr></table></figure>
<p>否则，就应该是这个列表第一个值和其余部分中的最大值中比较大的那个</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">maxInList</span> (x:xs) = max x (maxInList xs)</span><br/></pre></td></tr></table></figure>
<p>于是就这么愉快地写好了，这个函数只有两行，如果使用命令式语言，恐怕很难用两行完成（当然你要是硬要用C语言然后把代码都挤在一行上我也没话说）。</p>
<p>为了进一步展示函数式编程的美，我们来看看函数式的快速排序：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">quicksort</span> [] = []</span><br/><span class="line"><span class="title">quicksort</span> (x:xs) = (quicksort [y | y &lt;- xs, y &lt; x]) ++ [x] ++ (quicksort [y | y &lt;- xs, y &gt;= x])</span><br/></pre></td></tr></table></figure>
<p>va，还是只有两行，爽！</p>
<h2 id="进阶"><a class="headerlink" href="#进阶" title="进阶"></a>进阶</h2><h3 id="curry化函数与Hindley-Milner类型签名"><a class="headerlink" href="#curry化函数与Hindley-Milner类型签名" title="curry化函数与Hindley-Milner类型签名"></a>curry化函数与Hindley-Milner类型签名</h3><p>我们前面说过Haskell中的函数可以带多个参数：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">length</span> x y = sqrt (x * x + y * y)</span><br/></pre></td></tr></table></figure>
<p>但是我实际上，我要说，所有的Haskell函数都只接受一个参数，返回一个值。</p>
<p>那上面那个玩意是怎么弄出来的呢？</p>
<p>我们先看看这个函数的类型：（在GHCI中使用<code>:t length</code>）</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">length</span> :: <span class="type">Floating</span> a =&gt; a -&gt; a -&gt; a</span><br/></pre></td></tr></table></figure>
<p>WTF？这是啥神仙玩意？</p>
<p>实际上这是一个叫Hindley-Milner类型签名的东西，Haskell主要使用这种东西来标记一个函数的类型。</p>
<p>这个东西这样读：</p>
<p>Length is a fuction which takes an argument of type “a” and returns a fuction which (</p>
<p>takes an argument of type “a” and returns a value of type “a” </p>
<p>) where “a” is a type of typeclass Floating</p>
<p>用中文：</p>
<p>Length函数接受一个”a”类型的值作为参数，返回一个（接受一个”a”类型值作为参数，返回一个”a”类型值的函数），其中”a”是Floating类型类下的类型。</p>
<p>如果你还是觉得有点晕的话，我们给上面的类型打上括号：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">length</span> :: <span class="type">Floating</span> a =&gt; (a -&gt; (a -&gt; (a)))</span><br/></pre></td></tr></table></figure>
<p>可以理解为，这里一共有两个函数，一个是<code>length</code>本身，它接受一个”a”类型参数，返回一个类型为</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="type">Floating</span> a =&gt; a -&gt; a</span><br/></pre></td></tr></table></figure>
<p>的函数。</p>
<p>这个新的函数接受一个”a”类型参数，返回一个”a”类型的值。</p>
<p>所以一个Haskell函数只接受一个参数，然后要么返回一个函数，负责“吃掉”剩下的参数，要么返回一个值，就是函数运行的结果。</p>
<p>那么这样有什么好处呢？</p>
<p>一个好处是容易创建偏函数：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">length</span> x y = sqrt (x * x + y * y)</span><br/><span class="line"><span class="title">f</span> = length <span class="number">2</span></span><br/><span class="line"><span class="title">f</span> <span class="number">3</span> <span class="comment">-- 即length 2 3</span></span><br/><span class="line"><span class="title">f</span> <span class="number">4</span> <span class="comment">-- 即length 2 4</span></span><br/></pre></td></tr></table></figure>
<h3 id="typeclass"><a class="headerlink" href="#typeclass" title="typeclass"></a>typeclass</h3><p>看到<code>typeclass</code>不要想到面向对象中的<code>class</code>，相比之下，<code>typeclass</code>更像<code>interface</code>（尤其像Go的<code>interface</code>），也就是表达“一种类型的能力”（<code>interface</code>大概是这个意思）而非“一个对象的能力”（<code>class</code>大概是这个意思）。</p>
<p>说的简单一些，<code>typeclass</code>就要求一个类型能被放在某个函数的参数里做运算。</p>
<p>有以下一些常见的<code>typeclass</code>：</p>
<ul>
<li><p>Eq类型类</p>
<p>可判断相等性的类型，要求类型实现了<code>==</code>和<code>/=</code>两个函数<sup><a href="#fn_4" id="reffn_4">4</a></sup>。</p>
</li>
<li><p>Ord类型类</p>
<p>可比较大小的类型，要求类型实现了<code>compare</code>。</p>
</li>
<li><p>Show类型类</p>
<p>可以转成字符串，也就可以被显示出来的类型，实现<code>show</code>。</p>
</li>
<li><p>Read类型类</p>
<p>可以从字符转出来的类型，实现<code>read</code>。</p>
</li>
<li><p>Enum类型类</p>
<p>可以求其前驱和后继的类型，实现<code>pred</code>和<code>succ</code>。</p>
</li>
<li><p>Bounded类型类</p>
<p>有界的类型，实现<code>minBound</code>和<code>maxBound</code>。</p>
</li>
<li><p>Num类型类</p>
<p>表示数值的类型类，基本上就是<code>Int</code>、<code>Integer</code>、<code>Float</code>、<code>Double</code>。</p>
</li>
<li><p>Floating类型类</p>
<p>表示浮点数的类型类，基本上就是<code>Float</code>和<code>Double</code>。</p>
</li>
<li><p>Integeral类型类</p>
<p>表示整数的类型类，基本上就是<code>Int</code>（会溢出的整数）和<code>Integer</code>（大整数）。</p>
</li>
</ul>
<p>这些就是一些基本的类型类。</p>
<p>当然你可以自己做一些类型，并让它们实现某个类型类，但这部分绝非Haskell最令人感到舒适的部分，毕竟这是传统面向对象语言的专长（而我的思维方式不幸地已经适应了这种写法）。</p>
<p>不过在此还是留下例子：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/></pre></td><td class="code"><pre><span class="line"><span class="class"><span class="keyword">data</span> <span class="type">TrafficLight</span> = <span class="type">Red</span> | <span class="type">Yellow</span> | <span class="type">Green</span></span></span><br/><span class="line"></span><br/><span class="line"><span class="class"><span class="keyword">instance</span> <span class="type">Eq</span> <span class="type">TrafficLight</span> <span class="keyword">where</span></span></span><br/><span class="line">　　 <span class="type">Red</span> == <span class="type">Red</span> = <span class="type">True</span></span><br/><span class="line">　　 <span class="type">Green</span> == <span class="type">Green</span> = <span class="type">True</span></span><br/><span class="line">　　 <span class="type">Yellow</span> == <span class="type">Yellow</span> = <span class="type">True</span></span><br/><span class="line">　　 _ == _ = <span class="type">False</span></span><br/></pre></td></tr></table></figure>
<h2 id="呓语"><a class="headerlink" href="#呓语" title="呓语"></a>呓语</h2><h3 id="函子"><a class="headerlink" href="#函子" title="函子"></a>函子</h3><blockquote>
<p>设<em>C</em>和<em>D</em>为范畴，从<em>C</em>至<em>D</em>的<strong>函子</strong>为一映射<img alt="F" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/545fd099af8541605f7ee55f08225526be88ce57"/>:</p>
<ul>
<li>将每个对象<img alt="X \in C" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/cbbba9385d2769d1b712aace22eb12b7b027532f"/>映射至一对象<img alt="F(X) \in D" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/7133b230eb2fabfe9212374d671ba25a9587a4b1"/>上，</li>
<li>将每个态射<img alt="f:X\rightarrow Y \in C" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/2df18d6f5a51aa7cf12ba70209987ee2f6d1c10c"/>映射至一态射<img alt="F(f):F(X) \rightarrow F(Y) \in D" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/3a6564d3053f8f71b0af20bada84eb2710cb6942"/>上，使之满足下列条件：</li>
<li>对任何对象<img alt="X \in \mathcal{C}" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/ff0f1e44334d39c0ba83ae6d79d31cede4491cc8"/>，恒有<img alt="{\displaystyle F(\mathbf {id} _{X})=\mathbf {id} _{F(X)}}" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/7011a0191d55c97595b9ee935c79625f6bc46e92"/>。</li>
<li>对任何态射<img alt="f: X \to Y, \; g: Y \to Z" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/adc8502944942868160be8aa386e13ee47fc919f"/>，恒有<img alt="F(g \circ f) = F(g) \circ F(f)" src="https://wikimedia.org/api/rest_v1/media/math/render/svg/5386b2f5cd99fb03896b82b2a5efeec6ca0edb30"/>。换言之，函子会保持单位态射与态射的复合。</li>
</ul>
<p>一个由一范畴映射至其自身的函子称之为“自函子”。</p>
</blockquote>
<p>上面这坨都是啥神仙玩意。</p>
<p><del>数学家就喜欢把其实很简单事情搞得看上去超级复杂，美其名曰“严密”，以凸显其远超常人的智商，实际上我们都知道……好吧他们是真的很聪明TAT。</del></p>
<p>我用一句话说清楚：</p>
<p>函子（Functor）就是可以被map-over（即通过map向对象中的子对象应用一个函数）的对象。<sup><a href="#fn_5" id="reffn_5">5</a></sup></p>
<p>或者，一码胜千言：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line"><span class="class"><span class="keyword">class</span> <span class="type">Functor</span> f <span class="keyword">where</span></span></span><br/><span class="line">　　fmap :: (a -&gt; b) -&gt; f a -&gt; f b</span><br/></pre></td></tr></table></figure>
<p>啊，<code>Functor</code>是一个类型类，它要求实现了它的类型实现<code>fmap</code>函数，它取一个<code>(a -&gt; b)</code>和一个<code>f a</code>（即f类型里面的a类型）值作为参数，返回一个<code>f b</code>的值。</p>
<p>比如列表就是一个<code>Functor</code>：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">map</span> :: (a -&gt; b) -&gt; [a] -&gt; [b]</span><br/><span class="line"></span><br/><span class="line"><span class="class"><span class="keyword">instance</span> <span class="type">Functor</span> [] <span class="keyword">where</span></span></span><br/><span class="line">　　fmap = map</span><br/></pre></td></tr></table></figure>
<p>另外，Haskell中的<code>Set</code>和<code>Maybe</code>（可空值）也是<code>Functor</code>。</p>
<p><code>Maybe</code>是个好东西，下面就用它讲解了：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="class"><span class="keyword">instance</span> <span class="type">Functor</span> <span class="type">Maybe</span> <span class="keyword">where</span></span></span><br/><span class="line">    fmap func (<span class="type">Just</span> x) = <span class="type">Just</span> (func x)  <span class="comment">-- 有东西写作 Just xxx, map上去就是Just f(xxx)</span></span><br/><span class="line">    fmap func <span class="type">Nothing</span>  = <span class="type">Nothing</span>		<span class="comment">-- 没东西写作 Nothing, map上去还是Nothing</span></span><br/></pre></td></tr></table></figure>
<p>总的来说，<code>Functor</code>就是表现的像是容器或者上下文的一个类型，你可以通过<code>fmap</code>向容器中的元素应用一个操作这样。</p>
<h3 id="Applicative"><a class="headerlink" href="#Applicative" title="Applicative"></a>Applicative</h3><p><code>Applicative</code>是<code>Functor</code>的升级版本（也称<code>Applicative Functor</code>），这是个啥呢？</p>
<p>我们已经知道了我们可以将一个函数<code>map</code>到一个<code>Functor</code>上，但是如果我们要应用的函数<sup><a href="#fn_6" id="reffn_6">6</a></sup>也在上下文中呢？</p>
<p>例如<code>Just (+3)</code>这种？</p>
<p>在已经能完成<code>Functor</code>所有功能的基础上，<code>Applicative</code>也会帮我们解开函数的上下文，然后应用：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="class"><span class="keyword">class</span> <span class="type">Functor</span> f =&gt; <span class="type">Applicative</span> f <span class="keyword">where</span></span>  <span class="comment">-- Applicative一定是Fuctor</span></span><br/><span class="line">    pure :: a -&gt; f a					<span class="comment">-- 返回一个包裹在上下文中的值</span></span><br/><span class="line">    (&lt;*&gt;) :: f (a -&gt; b) -&gt; f a -&gt; f b   <span class="comment">-- 应用上下文中的函数</span></span><br/></pre></td></tr></table></figure>
<p>例如<code>Maybe</code>：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line"><span class="class"><span class="keyword">instance</span> <span class="type">Applicative</span> <span class="type">Maybe</span> <span class="keyword">where</span></span></span><br/><span class="line">    pure = <span class="type">Just</span></span><br/><span class="line">    <span class="type">Nothing</span> &lt;*&gt; _ = <span class="type">Nothing</span>				<span class="comment">-- 应用 Nothing 得 Nothing</span></span><br/><span class="line">    (<span class="type">Just</span> func) &lt;*&gt; something = fmap func something</span><br/></pre></td></tr></table></figure>
<h3 id="Monad-Ah-Finally"><a class="headerlink" href="#Monad-Ah-Finally" title="Monad (Ah! Finally!)"></a>Monad (Ah! Finally!)</h3><blockquote>
<p>Monad有啥难的，不过是自函子范畴上的一个幺半群罢了。</p>
</blockquote>
<p><del>这么说的都给我拖出去毙了。就你懂群论代数系统范畴论。😠</del></p>
<p><del>抱歉毙不掉，第一个说这句话的是Haskell委员会成员。</del></p>
<p><code>Monad</code>是<code>Applicative</code>的升级版本，它在<code>Applicative</code>的基础上，添加了一个“接受一个上下文中的值和一个(接受普通值返回上下文中的值的函数)，返回一个上下文中的值”的功能。</p>
<p>（简化过的）Monad是这样的：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="class"><span class="keyword">class</span> <span class="type">Applicative</span> m =&gt; <span class="type">Monad</span> m <span class="keyword">where</span></span>    </span><br/><span class="line">	return :: a -&gt; m a     </span><br/><span class="line">	(&gt;&gt;=) :: m a -&gt; (a -&gt; m b) -&gt; m b</span><br/></pre></td></tr></table></figure>
<p>同样看<code>Maybe</code>：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="class"><span class="keyword">class</span> <span class="type">Applicative</span> <span class="type">Maybe</span> =&gt; <span class="type">Monad</span> <span class="type">Maybe</span> <span class="keyword">where</span></span></span><br/><span class="line">    return :: a -&gt; <span class="type">Maybe</span> a</span><br/><span class="line">    (&gt;&gt;=) :: <span class="type">Maybe</span> a -&gt; (a -&gt; <span class="type">Maybe</span> b) -&gt; <span class="type">Maybe</span> b</span><br/></pre></td></tr></table></figure>
<p>那么引入这样一堆玩意有啥好处？</p>
<p>我们来看这个：</p>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="title">half</span> x = <span class="keyword">if</span> even x</span><br/><span class="line">    <span class="keyword">then</span> <span class="type">Just</span> (x `div` <span class="number">2</span>)</span><br/><span class="line">    <span class="keyword">else</span> <span class="type">Nothing</span></span><br/></pre></td></tr></table></figure>
<figure class="highlight haskell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/></pre></td><td class="code"><pre><span class="line"><span class="type">Prelude</span>&gt; <span class="type">Just</span> <span class="number">20</span> &gt;&gt;= half						<span class="comment">-- 把Just 20塞进half函数里</span></span><br/><span class="line"><span class="type">Just</span> <span class="number">10</span></span><br/><span class="line"><span class="type">Prelude</span>&gt; <span class="type">Just</span> <span class="number">20</span> &gt;&gt;= half &gt;&gt;= half				<span class="comment">-- 把Just 20的结果塞进half函数里</span></span><br/><span class="line"><span class="type">Just</span> <span class="number">5</span></span><br/><span class="line"><span class="type">Prelude</span>&gt; <span class="type">Just</span> <span class="number">20</span> &gt;&gt;= half &gt;&gt;= half &gt;&gt;= half</span><br/><span class="line"><span class="type">Nothing</span></span><br/></pre></td></tr></table></figure>
<p>链式调用，管道操作，酷毙了。</p>
<p><img alt="monad_chain" src="http://blog.leichunfeng.com/images/monad_chain.png"/></p>
<h2 id="从函数式编程到函数式架构"><a class="headerlink" href="#从函数式编程到函数式架构" title="从函数式编程到函数式架构"></a>从函数式编程到函数式架构</h2><p>上面这些东西都很不错，但我觉得你很难在“搬砖”的时候用到他们。</p>
<p>搬砖的时候最多用一下<code>map</code>、<code>reduce</code>、<code>filter</code>和少量递归的想法，几乎没有可能会显式地用到<code>Applicative</code>和<code>Monad</code>这些，原因很简单：他们太难了，很多人理解不能。</p>
<p>但是这些都是微观的函数式编程，我认为函数式真正NB的用途在于架构上，也就是“宏观的函数式编程”。</p>
<p>我们想一想我们在函数式编程里学到了什么。</p>
<ol>
<li>“可变的”、“副作用”是不好的</li>
<li>数据流&gt;&gt;函数&gt;&gt;函数&gt;&gt;函数 = 程序的结果</li>
</ol>
<p>这些想法在架构中也能用到。</p>
<ol>
<li><p>“可变”、“副作用”会带来管理上的复杂性，每个函数或方法必须明确“调用前要满足的条件”和“调用后会导致的副作用”，而这很可能会导致“认知超载”，故应当限制可变的东西。</p>
<p>或者说，可以将系统设计为：</p>
<p>系统状态=f(系统状态，用户行为)</p>
<p>这样系统状态的改变的唯一原因就是“用户行为”<sup><a href="#fn_8" id="reffn_8">8</a></sup>。</p>
</li>
<li><p>领域层（这是从DDD里借来的词） &gt;&gt; 渲染函数 = 表现层</p>
</li>
</ol>
<p>将他们结合起来，我们可以得到这样一个架构：</p>
<ol>
<li>有一个“领域层”</li>
<li>领域层 = applyAction(领域层,用户行为)</li>
<li>表现层 = render(领域层)</li>
</ol>
<p>那么这样一个架构像什么呢？</p>
<p>你可以说它像MVVM：</p>
<ul>
<li>“领域层” —— VM层</li>
<li>render函数——由MVVM框架负责提供。</li>
<li>用户行为——是指用户修改了VM层的数据</li>
</ul>
<p>但我认为它更像是Flux：</p>
<p><img alt="img" src="http://www.ruanyifeng.com/blogimg/asset/2016/bg2016011503.png"/></p>
<ul>
<li>“领域层”——Store</li>
<li>render函数——自己提供</li>
<li>Action——就是Action</li>
</ul>
<p>相比MVVM，Dispatcher为Flux提供了一个应用Action的统一入口，引起Store变化的原因被放在Dispatcher里面统一管理了起来，显得更加清晰。</p>
<blockquote id="fn_1">
<sup>1</sup>. 我记得lq老师的书中极言变量的重要性，我并不是说他说得不对，但我认为他只看到了Part of the story.<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_2">
<sup>2</sup>. 做到这一点的语言就可以说是支持函数式编程， JS和Python本来如此，C++的支持通过函数指针/Callable object/lambda部分实现，Java 8+通过lambda部分实现。<a href="#reffn_2" title="Jump back to footnote [2] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_3">
<sup>3</sup>. 不幸的是，“副作用”还包括IO等，因此实际上是没有办法完全消去副作用的。不过，Haskell将IO限制在程序中一些特定的部分（<code>do</code>块等）。<a href="#reffn_3" title="Jump back to footnote [3] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_4">
<sup>4</sup>. 别忘了！Haskell 中所有运算符都是函数！ <a href="#reffn_4" title="Jump back to footnote [4] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_5">
<sup>5</sup>. 注意这是Haskell和大部分程序语言中对Functor的定义，它并不完全和上面的严密数学定义等价！<a href="#reffn_5" title="Jump back to footnote [5] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_6">
<sup>6</sup>. 别忘了！函数是一等公民！它也是一个值，也能放在容器中！<a href="#reffn_6" title="Jump back to footnote [6] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_7">
<sup>7</sup>. <code>Functor</code>、<code>Applicative</code>和<code>Monad</code>的部分介绍和图片来自<a href="http://blog.leichunfeng.com/blog/2015/11/08/functor-applicative-and-monad/" rel="noopener" target="_blank">这里</a><a href="#reffn_7" title="Jump back to footnote [7] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_8">
<sup>8</sup>. 记得吗，一个类或者模块应该有且只有一个改变的原因（SRP），开发过程和程序的运行过程中的最佳实践其实也是相通的啊。<a href="#reffn_8" title="Jump back to footnote [8] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_10">
<sup>10</sup>. 当然，MVVM和Flux各个层之间的通信要靠各种不同的方法，比如Rx等。<a href="#reffn_10" title="Jump back to footnote [10] in the text."> ↩</a>
</blockquote>

