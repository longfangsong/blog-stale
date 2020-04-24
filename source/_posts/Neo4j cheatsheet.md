---
title: Neo4j cheatsheet
date: 2018-04-07 00:00:00
tags: [cheatsheet, Neo4j, NoSQL, 图数据库]
mathjax: true
comments: true
summary: Neo4j图数据库常用功能cheatsheet。
---
<p>Neo4j是在市面上用的比较多的一款NoSQL图形数据库，它能将数据用由节点和边构成的图表示出来，无论是从数据的可视化来看，还是从分析数据之间的关系上来看都是非常好的一款数据库。</p>
 hexo-inject:begin  hexo-inject:end <h2 id="安装"><a class="headerlink" href="#安装" title="安装"></a>安装</h2><p>推荐安装官方提供的Desktop版本，安装真的非常方便。</p>
<p><a href="https://neo4j.com/download/" rel="noopener" target="_blank">下载地址</a></p>
<h2 id="新建工程-amp-数据库"><a class="headerlink" href="#新建工程-amp-数据库" title="新建工程&amp;数据库"></a>新建工程&amp;数据库</h2><p>打开你下载到的App，你会看到这样的页面：</p>
<p><img alt="init" src="./Init.png"/></p>
<p>点击New创建一个新的Project，像这样：</p>
<p><img alt="ewProjec" src="./newProject.png"/></p>
<p>把鼠标移到项目名称附近会出现修改按钮，可以修改项目名称，点击New Graph创建新的图数据库。</p>
<p><img alt="newProject1" src="./newProject1.png"/></p>
<p>我们选择Local。</p>
<p><img alt="newProject2" src="./newProject2.png"/></p>
<p>输入数据库的名字和密码，点击Create，数据库就创建出来了。</p>
<h2 id="开启数据库服务器"><a class="headerlink" href="#开启数据库服务器" title="开启数据库服务器"></a>开启数据库服务器</h2><p>创建完数据库之后就会看到这样的画面：</p>
<p><img alt="start" src="./start.png"/></p>
<p>点击Start，服务器就启动了。</p>
<h2 id="打开Neo4j交互式界面"><a class="headerlink" href="#打开Neo4j交互式界面" title="打开Neo4j交互式界面"></a>打开Neo4j交互式界面</h2><p>默认配置下，访问<a href="http://localhost:7474" rel="noopener" target="_blank">http://localhost:7474</a>即可进入交互式页面。</p>
<p>可以在自己的浏览器上访问这个页面，也可以点击Manage后点击Open Browser直接在Neo4j提供的浏览器窗口中访问。</p>
<p>按照页面上的提示输入用户名密码即可与Neo4j进行交互。</p>
<h2 id="常用Neo4j语句"><a class="headerlink" href="#常用Neo4j语句" title="常用Neo4j语句"></a>常用Neo4j语句</h2><p>Neo4j并不使用常见的SQL语句来进行增删查改操作，而是使用了自己制作的一套名叫Cypher的语言。</p>
<p>打开官方例子，就能学到一些简单的Cypher查询语句。</p>
<p><img alt="CypherExampl" src="./CypherExample.png"/></p>
<p>如果想深入学习Cypher语言（和其他图数据库、Neo4j相关），推荐阅读<a href="https://neo4j.com/books/" rel="noopener" target="_blank">官方推荐的参考书目</a>，其中好几本都是可以免费获取电子版的。</p>
<h2 id="导入大量数据"><a class="headerlink" href="#导入大量数据" title="导入大量数据"></a>导入大量数据</h2><p>Neo4j有多种方式从关系型数据库中导入数据，主要有这几种：</p>
<ul>
<li>逐条<code>CREATE</code></li>
<li>使用<code>LOAD CSV</code>语句导入CSV</li>
<li>使用<code>neo4j-admin import</code>工具导入CSV</li>
</ul>
<p>当数据量大的时候，推荐使用最后一种。</p>
<h3 id="neo4j-admin-import使用说明"><a class="headerlink" href="#neo4j-admin-import使用说明" title="neo4j-admin import使用说明"></a><code>neo4j-admin import</code>使用说明</h3><p><code>neo4j-admin import</code>是官方附赠的CSV导入工具。</p>
<p>首先你需要准备好CSV文件，至于这个怎么来我就不管了，你从关系型数据库导出也好，用程序生成出来也好，只是千万要记得Neo4j导入的时候如果碰到<code>"</code>就会报错，所以必须要转义成<code>\"</code>，如果你在不支持raw string的语言里，那你很有可能要写成<code>\\\"</code>，还有要注意导出CSV的时候数据中本来的<code>,</code>号也要替换掉（我的做法是替换成中文<code>，</code>）。</p>
<p>一般的做法是将所有同类型节点做成一个csv，将关系做成另外一个csv。</p>
<p>最好在第一行加上每列的类型，像这样：</p>
<p>节点表：</p>
<p><img alt="sv_header" src="./csv_header1.png"/></p>
<p>关系表：</p>
<p><img alt="sv_header" src="./csv_header2.png"/></p>
<p><code>:START_ID</code>是指连接开始的节点的ID，<code>:END_ID</code>就是指连接结束的节点。</p>
<p>然后就是导入了，先找到这个数据库到对应的文件夹，点击manage页面下的Open Folder就能找到其位置：</p>
<p><img alt="penfolde" src="./openfolder.png"/></p>
<p>然后cd到这个目录下面，执行实际的导入命令：</p>
<figure class="highlight bash"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/></pre></td><td class="code"><pre><span class="line">./bin/neo4j-admin import \</span><br/><span class="line">--mode=csv \</span><br/><span class="line">--database=你的数据库文件名，例如example.db，记住这个工具不能导入到已经存在数据库文件中 \</span><br/><span class="line">--id-type=string \</span><br/><span class="line">--nodes:节点类型名称=<span class="string">"节点数据所在的csv"</span> \</span><br/><span class="line">--relationships:关系类型名称=<span class="string">"关系数据所在的csv"</span></span><br/></pre></td></tr></table></figure>
<p>如果没有报错就说明导入成功了。</p>
<p>要让数据库服务器在提供数据时使用我们刚刚导入的数据库文件，你需要修改：</p>
<p><img alt="setting" src="./settings.png"/></p>
<p>将<code>dbms.active_database</code>前的<code>#</code>去掉，然后将<code>graph.db</code>改成你导入好的数据库文件名称，再Apply（会重启数据库）即可。</p>

