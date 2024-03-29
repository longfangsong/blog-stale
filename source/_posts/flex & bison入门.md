---
title: flex & bison入门
date: 2019-05-22 00:00:00
tags: [编译原理]
mathjax: true
comments: true
summary: flex & bison是lex & yacc的继任者，可以相对简单地生成词法分析和语法分析程序。
---
<h2 id="词法分析"><a class="headerlink" href="#词法分析" title="词法分析"></a>词法分析</h2><p>词法分析的作用就是从源代码（一堆字符）中切分出一个个单词（常常称为token），并给这些单词标记”词性”。</p>
    <p>我们使用flex生成词法分析程序。</p>
<h3 id="flex"><a class="headerlink" href="#flex" title="flex"></a>flex</h3><p>flex是一个用于生成词法分析器代码的程序：</p>
<p><img alt="flex" src="./flex.svg"/></p>
<h4 id="安装"><a class="headerlink" href="#安装" title="安装"></a>安装</h4><p>大部分的包管理器（brew、apt等）应该能方便地装上flex。</p>
<p>Window用户请使用WSL或Cygwin。</p>
<h4 id="flex程序"><a class="headerlink" href="#flex程序" title="flex程序"></a>flex程序</h4><p>flex程序由三个部分由<code>%%</code>隔开的部分组成，前两个部分是必须的（但内容可以为空），最后一个部分和前面的<code>%%</code>可以省略：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/></pre></td><td class="code"><pre><span class="line">%{</span><br/><span class="line">包含头文件</span><br/><span class="line">变量、函数的声明</span><br/><span class="line">%}</span><br/><span class="line"></span><br/><span class="line">%%</span><br/><span class="line"></span><br/><span class="line">词法规则</span><br/><span class="line"></span><br/><span class="line">%%</span><br/><span class="line"></span><br/><span class="line">用户函数的定义</span><br/></pre></td></tr></table></figure>
<p>其中第一部分<code>%{ %}</code>中的内容和第三部分基本上会被原封不动地拷贝到生成的词法分析器源文件中，关键在于这个第二部分。</p>
<p>一般来说，这个词法规则是由几行形式如下的文本组成：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">正则表达式 {行为;}</span><br/></pre></td></tr></table></figure>
<p>如：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line">([-+])?[0-9]+                  {printf("an int!");}</span><br/><span class="line">([-+])?[0-9]+\.[0-9]*          {printf("a float!");}</span><br/></pre></td></tr></table></figure>
<p>上面两个就是识别常见 整数 和 浮点数 字面量的词法规则，词法分析器在”看”到这两种词法字面量时就会进行相应的动作：输出<code>"an int!"</code>或<code>"a float!"</code>。</p>
<p>在动作中可以使用<code>yytext</code>，这是一个保存了匹配到的字符串值的<code>char*</code>。</p>
<p>注意我这里只说了我写词法规则的方式（也是最简单的方式），实际上这个词法规则里面可以放很多骚东西，比如说可以用<code>BEGIN</code>开始一个”自定义状态”，还有左/右上下文相关、多文件输入等等，如果有兴趣可以自行参考相关资料。</p>
<h4 id="一个小例子"><a class="headerlink" href="#一个小例子" title="一个小例子"></a>一个小例子</h4><figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/><span class="line">13</span><br/><span class="line">14</span><br/><span class="line">15</span><br/><span class="line">16</span><br/><span class="line">17</span><br/><span class="line">18</span><br/></pre></td><td class="code"><pre><span class="line">%{</span><br/><span class="line">#include &lt;stdio.h&gt;</span><br/><span class="line">%}</span><br/><span class="line">%%</span><br/><span class="line">([-+])?[0-9]+               {printf("integer %s", yytext);}</span><br/><span class="line">([-+])?[0-9]+\.[0-9]*       {printf("double %s", yytext);}</span><br/><span class="line">[a-zA-Z][0-9a-zA-Z_]*       {printf("identifier %s", yytext);}</span><br/><span class="line">.                           {printf(" ");}</span><br/><span class="line">%%</span><br/><span class="line">int main() {</span><br/><span class="line">    yylex();</span><br/><span class="line">    return 0;</span><br/><span class="line">}</span><br/><span class="line"></span><br/><span class="line">/* 这个函数是必须的，用于多文件，默认返回1，代表没有更多文件需要处理 */</span><br/><span class="line">int yywrap() {</span><br/><span class="line">    return 1;</span><br/><span class="line">}</span><br/></pre></td></tr></table></figure>
<p>将这段代码文件保存为<code>main.l</code>，然后编译运行：</p>
<figure class="highlight bash"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">lex ./main.l &amp;&amp; gcc ./lex.yy.c &amp;&amp; ./a.out</span><br/></pre></td></tr></table></figure>
<p>向程序中输入一个字符串，会产生对应的输出：</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line">a b c 1.5 2</span><br/><span class="line">identifier a identifier b identifier c double 1.5 integer 2</span><br/></pre></td></tr></table></figure>
<h4 id="flex的原理"><a class="headerlink" href="#flex的原理" title="flex的原理"></a>flex的原理</h4><p>大体上就是用我们输入的正则表达式构造DFA，然后用这个DFA在输入中匹配，并在匹配到时进行对应的行为。</p>
<p>对DFA构造有兴趣的人可以看看<a href="https://longfangsong.github.io/2018/04/12/正则表达式-正规文法/">我的另一篇文章</a>。</p>
<h2 id="语法分析"><a class="headerlink" href="#语法分析" title="语法分析"></a>语法分析</h2><p>语法分析的作用是将词法分析中得到的token组织成特定的结构，方便后续工作。</p>
<p>我们使用bison来进行语法分析工作。</p>
<h3 id="bison"><a class="headerlink" href="#bison" title="bison"></a>bison</h3><p>bison是一个用于生成语法分析器代码的程序：</p>
<p><img alt="bison" src="./bison.svg"/></p>
<h4 id="bison程序"><a class="headerlink" href="#bison程序" title="bison程序"></a>bison程序</h4><p>bison程序的格式和flex非常相似，也是那三个部分，只是其中：</p>
<ul>
<li>第一部分有些其他的东西</li>
<li>第二部分变成了语法规则，故写法有变</li>
</ul>
<h5 id="第一部分"><a class="headerlink" href="#第一部分" title="第一部分"></a>第一部分</h5><p>第一部分多了这些东西：</p>
<h6 id="token"><a class="headerlink" href="#token" title="%token"></a><code>%token</code></h6><p><code>%token</code>用于声明一种token词性。</p>
<p>如</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">%token IDENTIFY INT_LITERAL</span><br/></pre></td></tr></table></figure>
<p>就告知bison会有<code>IDENTIFY</code>和<code>INT_LITERAL</code>两种token词性。</p>
<h6 id="union"><a class="headerlink" href="#union" title="%union"></a><code>%union</code></h6><p><code>%union</code>用于定义语法单元的值的类型。</p>
<p>这个要等到后面说了语法单元的值才能说，暂且按下不表。</p>
<h6 id="left和-right"><a class="headerlink" href="#left和-right" title="%left和%right"></a><code>%left</code>和<code>%right</code></h6><p>显然是用来指定运算符结合性的：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line">%left '&gt;' '&lt;' LESSEQ GREATEREQ EQUAL NONEQUAL</span><br/><span class="line">%left '+' '-'</span><br/><span class="line">%left '*' '/'</span><br/></pre></td></tr></table></figure>
<p>同时这里也定义了优先级：越下面的运算符优先级越高，故’*’、’/‘优先级高于’+’、’-‘</p>
<h5 id="第二部分"><a class="headerlink" href="#第二部分" title="第二部分"></a>第二部分</h5><p>语法规则的写法如下：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/></pre></td><td class="code"><pre><span class="line">规约到的语句名称:</span><br/><span class="line">	规则 {行为;}</span><br/><span class="line">	| 规则 {行为;}</span><br/><span class="line">	| 规则 {行为;}</span><br/><span class="line">	;</span><br/></pre></td></tr></table></figure>
<p>这个还是得靠例子来理解：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/></pre></td><td class="code"><pre><span class="line">expression:</span><br/><span class="line">	INT_LITERAL										{$$ = $1;}</span><br/><span class="line">	| expression '+' expression		{$$ = $1 + $2;}</span><br/><span class="line">	;</span><br/></pre></td></tr></table></figure>
<p>这个语法规则的意思是：</p>
<p>一个<code>expression</code>语句</p>
<ul>
<li>或者是一个<code>INT_LITERAL</code></li>
<li>或者是两个<code>expression</code>用<code>+</code>号连起来运算的结果</li>
</ul>
<p>后面的行为部分中的$$1<code>、</code>$$$等是什么意思呢？</p>
<p>首先要知道bison中的语法单元都是有值的，而其中$$n<code>就代表规则中第</code>n<code>个语法单元的值，</code>$$$则代表这个语句的值。</p>
<p>那么这个值是什么类型呢？</p>
<p>那就要说到上面这个<code>%union</code>、<code>%token</code>等等的知识了。</p>
<p>这个<code>%union</code>就像是一个C语言的<code>union</code>，这个<code>union</code>几乎就是bison中语法单元的值的类型，例如：</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/></pre></td><td class="code"><pre><span class="line">%<span class="keyword">union</span> {</span><br/><span class="line">    <span class="keyword">char</span> char_value;</span><br/><span class="line">    <span class="keyword">char</span> *string_value;</span><br/><span class="line">    <span class="keyword">int</span> int_value;</span><br/><span class="line">    <span class="keyword">double</span> double_value;</span><br/><span class="line">    <span class="keyword">size_t</span> id_value;</span><br/><span class="line">};</span><br/></pre></td></tr></table></figure>
<p>然后在声明token时可以指定其对应的<code>%union</code>的field，例如：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line">%token &lt;string_value&gt; IDENTIFY STRING_LITERAL</span><br/><span class="line">%token &lt;int_value&gt; INT_LITERAL</span><br/><span class="line">%token &lt;double_value&gt; DOUBLE_LITERAL</span><br/></pre></td></tr></table></figure>
<p>并且可以用<code>%type</code>指定各种语句对应的<code>%union</code>的field，如上面的expression：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">%type &lt;int_value&gt; expression</span><br/></pre></td></tr></table></figure>
<p>然后只要记得在给$$$$赋值的时候赋的是合适的类型的值即可。</p>
<h5 id="bison的原理"><a class="headerlink" href="#bison的原理" title="bison的原理"></a>bison的原理</h5><p>bison会生成基于LALR(1)的parsing table的文法分析器代码。</p>
<p>文法分析相关知识我将会在另外的文章中讲述。</p>
<h2 id="Combine-it-all-together"><a class="headerlink" href="#Combine-it-all-together" title="Combine it all together"></a>Combine it all together</h2><p>在flex和bison一起工作时，工作流程大致是这样的：</p>
<p><img alt="together" src="./together.svg"/></p>
<p>其中词法分析器使用<code>yylval</code>变量来将token的值返回给语法分析器，而用<code>return</code>直接返回token的词性。</p>
<p>同时bison生成语法分析器的同时也可以生成相应<code>.h</code>文件，供词法分析器使用。</p>
<p>说了那么多还是实操一下来的明白，我们做一个完整的例子，先编写：</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/><span class="line">13</span><br/><span class="line">14</span><br/><span class="line">15</span><br/><span class="line">16</span><br/><span class="line">17</span><br/><span class="line">18</span><br/><span class="line">19</span><br/><span class="line">20</span><br/><span class="line">21</span><br/><span class="line">22</span><br/><span class="line">23</span><br/><span class="line">24</span><br/><span class="line">25</span><br/><span class="line">26</span><br/><span class="line">27</span><br/><span class="line">28</span><br/><span class="line">29</span><br/><span class="line">30</span><br/><span class="line">31</span><br/><span class="line">32</span><br/><span class="line">33</span><br/><span class="line">34</span><br/><span class="line">35</span><br/><span class="line">36</span><br/></pre></td><td class="code"><pre><span class="line">%{</span><br/><span class="line"><span class="function"><span class="keyword">int</span> <span class="title">yylex</span><span class="params">()</span></span>;</span><br/><span class="line"><span class="function"><span class="keyword">int</span> <span class="title">yyerror</span><span class="params">(<span class="keyword">char</span> *errMsg)</span></span>;</span><br/><span class="line">%}</span><br/><span class="line">%<span class="keyword">union</span> {</span><br/><span class="line">    <span class="keyword">int</span> int_value;</span><br/><span class="line">    <span class="keyword">double</span> double_value;</span><br/><span class="line">};</span><br/><span class="line"></span><br/><span class="line">%token &lt;int_value&gt; INT_LITERAL</span><br/><span class="line">%token &lt;double_value&gt; DOUBLE_LITERAL</span><br/><span class="line"></span><br/><span class="line">%left <span class="string">'+'</span> <span class="string">'-'</span></span><br/><span class="line">%left <span class="string">'*'</span> <span class="string">'/'</span></span><br/><span class="line"></span><br/><span class="line">%type &lt;double_value&gt; expression </span><br/><span class="line"></span><br/><span class="line">%%</span><br/><span class="line"></span><br/><span class="line">expression:</span><br/><span class="line">	INT_LITERAL					        {<span class="built_in">printf</span>(<span class="string">"cast %d to double\n"</span>, $<span class="number">1</span>); $$ = (<span class="keyword">double</span>)($<span class="number">1</span>);}</span><br/><span class="line">  | DOUBLE_LITERAL            {$$ = $<span class="number">1</span>;}</span><br/><span class="line">	| expression <span class="string">'+'</span> expression	{<span class="built_in">printf</span>(<span class="string">"add %g and %g, result=%g\n"</span>, $<span class="number">1</span>, $<span class="number">3</span>, $<span class="number">1</span>+$<span class="number">3</span>); $$ = $<span class="number">1</span> + $<span class="number">3</span>;}</span><br/><span class="line">	;</span><br/><span class="line"></span><br/><span class="line">%%</span><br/><span class="line"></span><br/><span class="line"><span class="function"><span class="keyword">int</span> <span class="title">yyerror</span><span class="params">(<span class="keyword">char</span> *errMsg)</span> </span>{</span><br/><span class="line">    <span class="built_in">printf</span>(<span class="string">"%s"</span>, errMsg);</span><br/><span class="line">    <span class="keyword">return</span> <span class="number">0</span>;</span><br/><span class="line">}</span><br/><span class="line"></span><br/><span class="line"><span class="function"><span class="keyword">int</span> <span class="title">main</span><span class="params">(<span class="keyword">int</span> argc, <span class="keyword">char</span> *argv[])</span> </span>{</span><br/><span class="line">    yyparse();</span><br/><span class="line">    <span class="keyword">return</span> <span class="number">0</span>;</span><br/><span class="line">}</span><br/></pre></td></tr></table></figure>
<p>然后用bison编译：</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">bison -d ./main.y</span><br/></pre></td></tr></table></figure>
<p>其中<code>-d</code>表示要生成相应的头文件，即<code>main.tab.h</code>。</p>
<p>然后编写相应的词法分析器：</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/><span class="line">13</span><br/><span class="line">14</span><br/><span class="line">15</span><br/></pre></td><td class="code"><pre><span class="line">%{</span><br/><span class="line"><span class="meta">#<span class="meta-keyword">include</span> <span class="meta-string">"main.tab.h"</span></span></span><br/><span class="line">%}</span><br/><span class="line"></span><br/><span class="line">%%</span><br/><span class="line"></span><br/><span class="line">([-])?[<span class="number">0</span><span class="number">-9</span>]+                {yylval.int_value=atoi(yytext); <span class="keyword">return</span> INT_LITERAL;}</span><br/><span class="line">([-])?[<span class="number">0</span><span class="number">-9</span>]+\.[<span class="number">0</span><span class="number">-9</span>]*        {yylval.double_value=atof(yytext); <span class="keyword">return</span> DOUBLE_LITERAL;}</span><br/><span class="line">.                           {<span class="keyword">return</span> *yytext;}</span><br/><span class="line"></span><br/><span class="line">%%</span><br/><span class="line"></span><br/><span class="line"><span class="function"><span class="keyword">int</span> <span class="title">yywrap</span><span class="params">()</span> </span>{</span><br/><span class="line">    <span class="keyword">return</span> <span class="number">1</span>;</span><br/><span class="line">}</span><br/></pre></td></tr></table></figure>
<p>注意我们include了语法分析器头文件<code>main.tab.h</code>，这使得我们可以使用<code>INT_LITERAL</code>和<code>DOUBLE_LITERAL</code>等宏。</p>
<p>然后用bison编译：</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">bison main.y</span><br/></pre></td></tr></table></figure>
<p>然后编译输出的<code>.c</code>文件：</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">gcc ./*.c  # 懒狗就是不愿意打文件名</span><br/></pre></td></tr></table></figure>
<p>跑跑看：</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/></pre></td><td class="code"><pre><span class="line"><span class="meta">&gt;</span><span class="bash">&gt;&gt; ./a.out     </span></span><br/><span class="line">1+2+3+2.5</span><br/><span class="line">cast 1 to double</span><br/><span class="line">cast 2 to double</span><br/><span class="line">add 1 and 2, result=3</span><br/><span class="line">cast 3 to double</span><br/><span class="line">add 3 and 3, result=6</span><br/><span class="line">add 6 and 2.5, result=8.5</span><br/></pre></td></tr></table></figure>
<p>完美漂亮。</p>
<p>现在你的flex和bison水平已经足够编写一个C编译器了，当然你还需要其他知识，尤其是编译器的后端部分。</p>
<h2 id="参考资料"><a class="headerlink" href="#参考资料" title="参考资料"></a>参考资料</h2><ul>
<li>《flex &amp; bison》</li>
<li><a href="http://dinosaur.compilertools.net" rel="noopener" target="_blank">http://dinosaur.compilertools.net</a></li>
</ul>

