---
title: （控制台）输入输出和EOF
date: 2017-09-20 00:00:00
tags: [新手级, C, C++]
mathjax: true
comments: true
summary: 控制台C/C++控制台IO入门
---
<h1 id="（控制台）输入输出和EOF"><a class="headerlink" href="#（控制台）输入输出和EOF" title="（控制台）输入输出和EOF"></a>（控制台）输入输出和EOF</h1><p>最近很多人问我有关输入输出的问题，在此写一篇文章统一回答。</p>
    <p><strong><em>想知道如何接受数量不定的输入数字，或者得到这些输入数字的个数的同学，请直接跳到最后一部分。</em></strong></p>
<h2 id="C语言的控制台IO系统"><a class="headerlink" href="#C语言的控制台IO系统" title="C语言的控制台IO系统"></a>C语言的控制台IO系统</h2><p>首先是C语言带来的<code>&lt;stdio.h&gt;</code>（<code>&lt;cstdio&gt;</code>）中的printf和scanf这一组函数。</p>
<p><code>printf</code>在官方文档中长这样：</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="function"><span class="keyword">int</span> <span class="title">printf</span><span class="params">( <span class="keyword">const</span> <span class="keyword">char</span> *format, ... )</span></span>;</span><br/></pre></td></tr></table></figure>
<p>（看不懂不要紧，我马上解释）</p>
<p>使用printf的时候，在括号里先写一个字符串，字符串中可以包含几个格式控制符（下面会说），然后再写各个要输出的变量。</p>
<p>eg.</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line"><span class="keyword">int</span> a = <span class="number">42</span>;</span><br/><span class="line"><span class="built_in">printf</span>(<span class="string">"Hello %d"</span>,a); <span class="comment">//输出Hello 42</span></span><br/></pre></td></tr></table></figure>
<p>和<code>printf</code>相对的是<code>scanf</code>，这个函数用来接受输入的数：</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="function"><span class="keyword">int</span> <span class="title">scanf</span><span class="params">( <span class="keyword">const</span> <span class="keyword">char</span>          *format, ... )</span></span>;</span><br/></pre></td></tr></table></figure>
<p>eg.</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="keyword">int</span> a;</span><br/><span class="line"><span class="built_in">scanf</span>(<span class="string">"%d"</span>,&amp;a);</span><br/><span class="line"><span class="built_in">printf</span>(<span class="string">"%d"</span>,a+<span class="number">1</span>); <span class="comment">//输出输入的值+1</span></span><br/></pre></td></tr></table></figure>
<p>同理，在括号里先写一个字符串，字符串中可以包含几个格式控制符，然后再写各个要输出的变量<strong>的地址</strong>（不理解什么是地址的话，就当成是<code>&amp;</code>符号+变量名好了）。</p>
<h3 id="格式控制符"><a class="headerlink" href="#格式控制符" title="格式控制符"></a><strong>格式</strong>控制符</h3><p>格式控制符，简单来说就是帮变量在字符串中占一个位置，在实际输出的时候，格式控制符会被后面变量的值替换掉。</p>
<p>不同类型的变量要使用不同的格式控制符来占位。</p>
<p>各种类型的变量要使用的格式控制符见下表（实际上这个表被简化过了，全表请见官方文档）。</p>
<div class="table-container">
<table>
<thead>
<tr>
<th>类型</th>
<th>格式控制符</th>
</tr>
</thead>
<tbody>
<tr>
<td>int</td>
<td>%d或%i</td>
</tr>
<tr>
<td>unsigned</td>
<td>%u（十进制输入输出）</td>
</tr>
<tr>
<td>unsigned      [注1]</td>
<td>%x（十六进制输入输出，字母小写）</td>
</tr>
<tr>
<td>unsigned      [注1]</td>
<td>%X（十六进制输入输出，字母大写）</td>
</tr>
<tr>
<td>unsigned      [注1]</td>
<td>%o（八进制输入输出）</td>
</tr>
<tr>
<td>char</td>
<td>%c</td>
</tr>
<tr>
<td>double          [注2]</td>
<td>%lf  (输入输出时采用人的习惯写法，默认保留6位小数)</td>
</tr>
<tr>
<td>double          [注2]</td>
<td>%le (输入输出时采用科学记数法）</td>
</tr>
<tr>
<td>double          [注2]</td>
<td>%lg (输入输出时采用科学记数法或人的习惯写法中较合适的，同时在输出时会自动吞掉末尾的0)</td>
</tr>
<tr>
<td>char *（字符串）</td>
<td>%s</td>
</tr>
<tr>
<td>long long</td>
<td>%ll</td>
</tr>
<tr>
<td>unsigned long long</td>
<td>%llu</td>
</tr>
</tbody>
</table>
</div>
<p>注1: 在输入时可以直接在int类型上使用，输出时，如果在int上使用，会进行类型转换。</p>
<p>注2: 根据官方文档，无论是否在输出浮点数时是否使用l前缀，要求的数据类型都是double（float会进行转换），而在输入时会对double和float进行区分。在大部分情况下，硬件资源足够支持我们在所有需要使用浮点数的时候使用double，故本表不列出float。</p>
<p>格式控制符除了“占位”的作用之外，还具有在输出时进行格式控制的作用（不然干嘛叫这名字）。</p>
<p>格式控制符怎么进行格式控制，我们借助例子讲解：</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/><span class="line">13</span><br/><span class="line">14</span><br/><span class="line">15</span><br/><span class="line">16</span><br/><span class="line">17</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">printf</span>(<span class="string">"%10d"</span>,<span class="number">42</span>);</span><br/><span class="line"><span class="comment">//        42</span></span><br/><span class="line"><span class="comment">// ^ 8个空格，即输出占满10位，注意如果数字本身超过了10位，那么数字会按照原样输出</span></span><br/><span class="line"><span class="built_in">printf</span>(<span class="string">"%-10d*"</span>,<span class="number">42</span>);</span><br/><span class="line"><span class="comment">//42        *</span></span><br/><span class="line"><span class="comment">//^左对齐占满10位（*没啥用，就是告诉你前面那个42确实占了10位）</span></span><br/><span class="line"><span class="built_in">printf</span>(<span class="string">"%010d"</span>,<span class="number">42</span>);</span><br/><span class="line"><span class="comment">//0000000042</span></span><br/><span class="line"><span class="comment">//输出占满10位，不足部分用0填充</span></span><br/><span class="line"><span class="comment">//%0-10d或者%-010d都不会像期望地那样工作，你可以自己试一试</span></span><br/><span class="line"><span class="comment">//实际上，如果这种情况下它像期望的那样工作了，反而会有奇怪的误解</span></span><br/><span class="line"><span class="built_in">printf</span>(<span class="string">"%10lf"</span>,<span class="number">42.42</span>);</span><br/><span class="line"><span class="comment">// 42.420000</span></span><br/><span class="line"><span class="comment">//同理占满10位为止</span></span><br/><span class="line"><span class="built_in">printf</span>(<span class="string">"%10.2lf"</span>,<span class="number">42.42</span>);</span><br/><span class="line"><span class="comment">//     42.42</span></span><br/><span class="line"><span class="comment">//整个数字占10位，小数部分占两位</span></span><br/></pre></td></tr></table></figure>
<p>基本上就是这样，更多玩法可以看官方文档。</p>
<h2 id="C-语言的控制台IO系统"><a class="headerlink" href="#C-语言的控制台IO系统" title="C++语言的控制台IO系统"></a>C++语言的控制台IO系统</h2><p>C++语言的控制台IO系统主要由<code>&lt;iostream&gt;</code>提供。</p>
<p>相比C语言的IO系统，C++语言的IO更加安全（无需担心打错格式控制符、漏掉输入时的&amp;等等），但是代价就是格式控制较为麻烦。</p>
<p>C++控制台输出主要靠cout这个对象。</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="keyword">int</span> a=<span class="number">42</span>;</span><br/><span class="line"><span class="built_in">cout</span>&lt;&lt;<span class="string">"Hello "</span>&lt;&lt;a;</span><br/><span class="line"><span class="comment">//输出Hello 42</span></span><br/></pre></td></tr></table></figure>
<p>不过，这里有一个坑：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/></pre></td><td class="code"><pre><span class="line"><span class="meta">#<span class="meta-keyword">include</span> <span class="meta-string">&lt;iostream&gt;</span></span></span><br/><span class="line"><span class="keyword">using</span> <span class="keyword">namespace</span> <span class="built_in">std</span>;</span><br/><span class="line"><span class="function"><span class="keyword">int</span> <span class="title">main</span><span class="params">()</span> </span>{</span><br/><span class="line">    <span class="keyword">int</span> a=<span class="number">42</span>;</span><br/><span class="line">    <span class="built_in">cout</span> &lt;&lt; <span class="string">"Hello "</span>&lt;&lt;a;</span><br/><span class="line">    <span class="keyword">while</span>(<span class="number">1</span>)</span><br/><span class="line">            ;   </span><br/><span class="line">  	<span class="keyword">return</span> <span class="number">0</span>;</span><br/><span class="line">}</span><br/></pre></td></tr></table></figure>
<p>你们觉得这个程序在进入死循环前会把Hello 42输出吗？</p>
<p>答案是不会。</p>
<p>这就怪了，</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">cout</span> &lt;&lt; <span class="string">"Hello "</span>&lt;&lt; a;</span><br/></pre></td></tr></table></figure>
<p>明明在循环前面，为什么会不输出呢？</p>
<p>这和cout实现的内部机理有关，cout（其实所有ostream及其子类的对象都是这样）在接到输出的时候并不会直接将其输出到屏幕上，而是将其放入一块我们称之为“缓冲区”的内存中，只有当我们手动要求它输出或者缓冲区装不下了，cout才会将输出写到屏幕上。</p>
<p>要手动要求cout把缓冲区的内容输出到屏幕，你可以向cout中插入一个flush或者一个endl（如果你使用endl，你同时还会得到一个换行）</p>
<p>eg.</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/></pre></td><td class="code"><pre><span class="line"><span class="meta">#<span class="meta-keyword">include</span> <span class="meta-string">&lt;iostream&gt;</span></span></span><br/><span class="line"><span class="keyword">using</span> <span class="keyword">namespace</span> <span class="built_in">std</span>;</span><br/><span class="line"><span class="function"><span class="keyword">int</span> <span class="title">main</span><span class="params">()</span> </span>{</span><br/><span class="line">    <span class="keyword">int</span> a=<span class="number">42</span>;</span><br/><span class="line">    <span class="built_in">cout</span> &lt;&lt; <span class="string">"Hello "</span>&lt;&lt; a &lt;&lt; flush;</span><br/><span class="line">    <span class="keyword">while</span>(<span class="number">1</span>)</span><br/><span class="line">            ;   </span><br/><span class="line">  	<span class="keyword">return</span> <span class="number">0</span>;</span><br/><span class="line">}</span><br/></pre></td></tr></table></figure>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/></pre></td><td class="code"><pre><span class="line"><span class="meta">#<span class="meta-keyword">include</span> <span class="meta-string">&lt;iostream&gt;</span></span></span><br/><span class="line"><span class="keyword">using</span> <span class="keyword">namespace</span> <span class="built_in">std</span>;</span><br/><span class="line"><span class="function"><span class="keyword">int</span> <span class="title">main</span><span class="params">()</span> </span>{</span><br/><span class="line">    <span class="keyword">int</span> a=<span class="number">42</span>;</span><br/><span class="line">    <span class="built_in">cout</span> &lt;&lt; <span class="string">"Hello "</span>&lt;&lt; a &lt;&lt; <span class="built_in">endl</span>;</span><br/><span class="line">    <span class="keyword">while</span>(<span class="number">1</span>)</span><br/><span class="line">            ;   </span><br/><span class="line">    <span class="keyword">return</span> <span class="number">0</span>;</span><br/><span class="line">}</span><br/></pre></td></tr></table></figure>
<p>这样在死循环开始前输出就会出现了。</p>
<p>C++控制台输入主要靠cin这个对象。</p>
<p>eg.</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line"><span class="keyword">int</span> a;</span><br/><span class="line"><span class="built_in">cin</span> &gt;&gt; a;</span><br/><span class="line"><span class="built_in">cout</span> &lt;&lt; a+<span class="number">1</span> &lt;&lt; <span class="built_in">endl</span>; <span class="comment">//输出输入的值+1</span></span><br/></pre></td></tr></table></figure>
<p>很简单，没啥可说的。</p>
<h3 id="cout的格式控制"><a class="headerlink" href="#cout的格式控制" title="cout的格式控制"></a>cout的格式控制</h3><p>cout的格式控制比printf烦了很多……</p>
<p>要控制cout的格式，只要往里插入一些奇怪的东西就行了：</p>
<div class="table-container">
<table>
<thead>
<tr>
<th>用途</th>
<th></th>
</tr>
</thead>
<tbody>
<tr>
<td>setw(n)</td>
<td><strong>下一个</strong>插入的输出项占满n位</td>
</tr>
<tr>
<td>setfill(ch)</td>
<td>用字符ch填满<strong>下面所有输出</strong>中占位用的空白</td>
</tr>
<tr>
<td>（需要包含<code>&lt;iomanip&gt;</code>） setprecision(n)</td>
<td>在未插入过<code>fixed</code>的情况下，<strong>下面所有输出</strong>保留n位有效数字。在插入过<code>fixed</code>的情况下，<strong>下面所有输出</strong>保留n位小数。</td>
</tr>
<tr>
<td>left</td>
<td><strong>下面所有</strong>的输出左对齐</td>
</tr>
<tr>
<td>right</td>
<td><strong>下面所有</strong>的输出右对齐</td>
</tr>
<tr>
<td>fixed</td>
<td><strong>下面输出的所有</strong>小数使用类似C中%lf的格式输出</td>
</tr>
<tr>
<td>scientific</td>
<td><strong>下面输出的所有</strong>小数使用类似C中%le的格式输出</td>
</tr>
</tbody>
</table>
</div>
<p>其实还有更多，但常用的就这些，其他请自行查阅文档。</p>
<hr/>
<h2 id="EOF"><a class="headerlink" href="#EOF" title="EOF"></a>EOF</h2><blockquote>
<p><strong>文件结尾</strong>（英语：End of File，缩写为EOF），是操作系统无法从数据源读取更多数据的情形。</p>
</blockquote>
<p>我们从文件里读取不定数量的数据就靠它了！</p>
<p>一般在OJ程序判题的时候，是使用重定向符将文件里的内容作为控制台输入输入给程序的，而这时文件的结束符EOF就能用来判断是否数据输入已经结束了。</p>
<p>如果要自己在控制台输入EOF来模拟OJ的输入，可以在输入完要输入的数之后：</p>
<ul>
<li>如果你使用的是*nix操作系统，先换行，再输入<code>Ctrl+D</code></li>
<li>如果你使用Windows，输入<code>Ctrl+Z</code></li>
</ul>
<p>而在文件中判断是否已经读到EOF的方法也很简单：</p>
<p>C语言中：</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">// 假设前面有了一个 int i</span></span><br/><span class="line"><span class="built_in">scanf</span>(<span class="string">"%d"</span>,&amp;i) == EOF </span><br/><span class="line"><span class="comment">//EOF是一个常量，在大部分环境下等于-1</span></span><br/><span class="line"><span class="comment">//所以当这个表达式为ture的时候，就代表读取到了文件末尾</span></span><br/></pre></td></tr></table></figure>
<p>C++中：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line"><span class="built_in">cin</span>.eof()</span><br/><span class="line"><span class="comment">//表达式为ture的时候，就代表读取到了文件末尾</span></span><br/></pre></td></tr></table></figure>
<p>C++中，如果你确定你的输入流不会被其他错误输入弄坏（一般来说OJ就是这种情况），可以简单地写成：</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line">!<span class="built_in">cin</span></span><br/><span class="line"><span class="comment">//cin转换成bool值默认为true，出现错误或读到eof，时会变成false，表达式为ture的时候，就代表读取到了文件末尾</span></span><br/></pre></td></tr></table></figure>
<p>那么，要读取到遇到EOF为止，可以这样做：</p>
<p>C：</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">//例如每次读取一个int</span></span><br/><span class="line"><span class="keyword">int</span> a;</span><br/><span class="line"><span class="keyword">while</span>(<span class="built_in">scanf</span>(<span class="string">"%d"</span>,&amp;a) != EOF) {</span><br/><span class="line">    <span class="comment">//使用a做一些事</span></span><br/><span class="line">}</span><br/><span class="line"><span class="comment">//读取到EOF后会到达这里</span></span><br/></pre></td></tr></table></figure>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">//每次读取一个int和一个double</span></span><br/><span class="line"><span class="keyword">int</span> a;</span><br/><span class="line"><span class="keyword">double</span> b;</span><br/><span class="line"><span class="keyword">while</span>(<span class="built_in">scanf</span>(<span class="string">"%d %lf"</span>,&amp;a, &amp;b) != EOF) {</span><br/><span class="line">    <span class="comment">//使用a和b做一些事</span></span><br/><span class="line">}</span><br/><span class="line"><span class="comment">//读取到EOF后会到达这里</span></span><br/></pre></td></tr></table></figure>
<p>C++:</p>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">//例如每次读取一个int</span></span><br/><span class="line"><span class="keyword">int</span> a;</span><br/><span class="line"><span class="keyword">while</span>(!<span class="built_in">cin</span>.eof()) {</span><br/><span class="line">  	<span class="built_in">cin</span> &gt;&gt; a;</span><br/><span class="line">    <span class="comment">//使用a做一些事</span></span><br/><span class="line">}</span><br/><span class="line"><span class="comment">//读取到EOF后会到达这里</span></span><br/></pre></td></tr></table></figure>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">// 另一种方法，只在确定不会出现其他非法输入时有用</span></span><br/><span class="line"><span class="keyword">int</span> a;</span><br/><span class="line"><span class="keyword">while</span>(<span class="built_in">cin</span> &gt;&gt; a) {</span><br/><span class="line">    <span class="comment">//使用a做一些事</span></span><br/><span class="line">}</span><br/><span class="line"><span class="comment">//读取到EOF后会到达这里</span></span><br/></pre></td></tr></table></figure>
<figure class="highlight c++"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">//每次读取一个int和一个double</span></span><br/><span class="line"><span class="keyword">int</span> a;</span><br/><span class="line"><span class="keyword">double</span> b;</span><br/><span class="line"><span class="keyword">while</span>(<span class="built_in">cin</span> &gt;&gt; a &gt;&gt; b) {</span><br/><span class="line">    <span class="comment">//使用a和b做一些事</span></span><br/><span class="line">}</span><br/><span class="line"><span class="comment">//读取到EOF后会到达这里</span></span><br/></pre></td></tr></table></figure>
<p>如果要统计输入数字的个数，只需增加一个计数器：</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/></pre></td><td class="code"><pre><span class="line"><span class="keyword">int</span> a;</span><br/><span class="line"><span class="keyword">unsigned</span> count = <span class="number">0</span>; <span class="comment">//别忘了初始化！！！</span></span><br/><span class="line"><span class="keyword">while</span>(<span class="built_in">scanf</span>(<span class="string">"%d"</span>,&amp;a) != EOF) {</span><br/><span class="line">    ++ count;</span><br/><span class="line">    <span class="comment">//（如果你按常人的习惯从第1个开始数）这是第count个输入数据</span></span><br/><span class="line">    <span class="comment">//（如果你按C系语言的习惯从第0个开始数）这是第count-1个输入数据</span></span><br/><span class="line">    <span class="comment">//使用a做一些事</span></span><br/><span class="line">}</span><br/><span class="line"><span class="comment">// count现在是数据总量</span></span><br/></pre></td></tr></table></figure>
<p>就是这样了。</p>
<p>下次我或许会深入一些，探讨一下C++和C输入输出的同步问题。</p>

