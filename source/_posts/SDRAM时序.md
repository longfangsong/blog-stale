---
title: SDRAM时序
date: 2019-07-03 00:00:00
tags: [计组]
mathjax: true
comments: true
summary: DRAM由于设计上使用了电容来存储数据，其时序远比SRAM复杂。本文讲述了S(ynchronous)DRAM的操作时序。
---
<p><del>人类怎么会设计出SDRAM这种复杂到爆炸的玩意啊TAT，上帝也是个老混蛋好好的晶体管漏什么电啊。</del></p>
    <blockquote>
<p><strong>动态随机存取存储器</strong>（<strong>Dynamic Random Access Memory</strong>，<strong>DRAM</strong>）是一种<a href="https://zh.wikipedia.org/wiki/半导体" rel="noopener" target="_blank">半导体</a><a href="https://zh.wikipedia.org/wiki/記憶體" rel="noopener" target="_blank">存储器</a>，主要的作用原理是利用<a href="https://zh.wikipedia.org/wiki/電容" rel="noopener" target="_blank">电容</a>内存储<a href="https://zh.wikipedia.org/wiki/電荷" rel="noopener" target="_blank">电荷</a>的多寡来代表一个<a href="https://zh.wikipedia.org/wiki/二进制" rel="noopener" target="_blank">二进制</a><a href="https://zh.wikipedia.org/wiki/位元" rel="noopener" target="_blank">比特</a>（bit）是1还是0。由于在现实中晶体管会有漏电电流的现象，导致电容上所存储的电荷数量并不足以正确的判别数据，而导致数据毁损。因此对于DRAM来说，周期性地充电是一个无可避免的要件。由于这种需要定时刷新的特性，因此被称为“动态”存储器。相对来说，静态存储器（<a href="https://zh.wikipedia.org/wiki/静态随机存取存储器" rel="noopener" target="_blank">SRAM</a>）只要存入数据后，纵使不刷新也不会丢失记忆。</p>
<p>—— 中文Wikipedia，DRAM页面</p>
<p><strong>同步动态随机存取內存</strong>（synchronous dynamic random-access memory，简称SDRAM）是有一个<a href="https://zh.wikipedia.org/w/index.php?title=同步接口&amp;action=edit&amp;redlink=1" rel="noopener" target="_blank">同步接口</a>的动态随机存取內存（<a href="https://zh.wikipedia.org/wiki/DRAM" rel="noopener" target="_blank">DRAM</a>）。通常DRAM是有一个异步接口的，这样它可以随时响应控制输入的变化。而SDRAM有一个同步接口，在响应控制<a href="https://zh.wikipedia.org/w/index.php?title=输入&amp;action=edit&amp;redlink=1" rel="noopener" target="_blank">输入</a>前会等待一个<a href="https://zh.wikipedia.org/wiki/时钟信号" rel="noopener" target="_blank">时钟信号</a>，这样就能和计算机的<a href="https://zh.wikipedia.org/wiki/系统总线" rel="noopener" target="_blank">系统总线</a>同步。时钟被用来驱动一个有限状态机，对进入的指令进行<a href="https://zh.wikipedia.org/wiki/流水线_(计算机" rel="noopener" target="_blank">管线</a>)(Pipeline)操作。这使得SDRAM与没有同步接口的异步DRAM(asynchronous DRAM)相比，可以有一个更复杂的操作模式。</p>
<p>—— 中文Wikipedia，SDRAM页面</p>
</blockquote>
<p><del>你这样说谁懂啊</del></p>
<p>简单来说，DRAM就是一种用电容里有没有电来表示这一位是0还是1的存储器，而SDRAM在普通DRAM的基础上加了一些外围电路，使得对DRAM内数据的操作仅在时钟信号到来时才会进行，这样便于和总线进行同步等<sup><a href="#fn_1" id="reffn_1">1</a></sup>。</p>
<h2 id="内部结构"><a class="headerlink" href="#内部结构" title="内部结构"></a>内部结构</h2><p>这里以我的开发板上的H57V2562GTR-75C（根据数据手册，这款芯片有4个bank，bank内的列地址有9位，行地址有13位，每个存储单元为16位）为例，其结构如图：</p>
<p><img alt="diagram" src="./diagram.png"/></p>
<p>我们主要需要关系的就是这个有效地址，从图中可以发现，这块内存的存储阵列是由4个Bank组成的，其中每个Bank内部大概是这样的<sup><a href="#fn_2" id="reffn_2">2</a></sup>：</p>
<p><img alt="Group" src="./Group.svg"/></p>
<p>其中每个存储单元内有16<sup><a href="#fn_3" id="reffn_3">3</a></sup>个单独的由电容构成的存储体，故每个存储单元内可以存储16个位。</p>
<p>所以一个有效地址<sup><a href="#fn_4" id="reffn_4">4</a></sup>构成如下：</p>
<div class="table-container">
<table>
<thead>
<tr>
<th>内容</th>
<th>Bank选择</th>
<th>行选择</th>
<th>列选择</th>
</tr>
</thead>
<tbody>
<tr>
<td>位数</td>
<td>2</td>
<td>13</td>
<td>9</td>
</tr>
</tbody>
</table>
</div>
<p>共24位，这样一个地址确定了内存中的16位数据。</p>
<p>这样可以容易地算出，这一内存芯片的大小总计为 $2 ^ {24} \times 16 = 268435456$ 位，合33554432​字节，即32MiB。</p>
<h2 id="引脚说明"><a class="headerlink" href="#引脚说明" title="引脚说明"></a>引脚说明</h2><p>以<strong>54_TSOPII</strong>封装方式为例，忽略（往往板子上已经帮你焊好的）供电引脚：</p>
<div class="table-container">
<table>
<thead>
<tr>
<th>引脚</th>
<th>类型</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr>
<td>CLK</td>
<td>input</td>
<td>时钟信号</td>
</tr>
<tr>
<td>CKE</td>
<td>input</td>
<td>时钟使能信号，控制SDRAM内部时钟是否运行，为0时，根据其他连线SDRAM会进入断电、挂起或自刷新状态</td>
</tr>
<tr>
<td>NCS</td>
<td>input</td>
<td>片选信号</td>
</tr>
<tr>
<td>BA0、BA1</td>
<td>input</td>
<td>Bank地址</td>
</tr>
<tr>
<td>A0-A12</td>
<td>input</td>
<td>地址线，行列分时复用，行地址使用全部13根线，列地址只使用0-8号，同时A10被用作自动预充电是否选中全部bank的flag。</td>
</tr>
<tr>
<td>NRAS，NCAS，NWE</td>
<td>input</td>
<td>指定了要做的操作</td>
</tr>
<tr>
<td>LDQM，UDQM</td>
<td>inout</td>
<td>是否要Mask掉一半的输入输出（16位中的8位）</td>
</tr>
<tr>
<td>DQ0-15</td>
<td>inout</td>
<td>数据线</td>
</tr>
</tbody>
</table>
</div>
<h2 id="SDRAM初始化时序"><a class="headerlink" href="#SDRAM初始化时序" title="SDRAM初始化时序"></a>SDRAM初始化时序</h2><p><img alt="sdram_init_timming" src="./sdram_init_timming.png"/></p>
<p>光看时序图可（肯）能（定）不好理解，我们还是像软件算法那样一步步写下来比较好，记得所有指令都在CLK上升沿时被执行：</p>
<ol>
<li><p>在芯片上电并为其输入稳定的时钟信号后，等待（即不断发送<code>NOP</code>指令，<code>{NRAS, NCAS, NWE} = 'b111</code>）至少100µs</p>
</li>
<li><p>发送<code>precharge</code>指令（即<code>{NRAS, NCAS, NWE} = 'b010</code>，同时<code>A10 = 1</code>来选中全部bank）。</p>
</li>
<li><p>等待<code>precharge</code>完成，等待时长为$t_{RP}$，在我这块片子上至少是20ns。</p>
</li>
<li><p>发送<code>auto refresh</code>指令（即<code>{NRAS, NCAS, NWE} = 'b001</code>），并等待其执行完成，等待时长为$t_{RC}$，在我的片子上至少是63ns。</p>
</li>
<li><p>重复步骤4至少一次。</p>
</li>
<li><p>发送<code>load mode register</code>指令（即<code>{NRAS, NCAS, NWE} = 'b000</code>），mode register的值由地址线（含Bank地址线）给出：</p>
<p><img alt="mode_register" src="./mode_register.png"/></p>
<p>可见</p>
<ul>
<li><code>A9</code>线上的值代表了写入的模式（突发写还是单写）</li>
<li><code>{A6, A5, A4}</code>线上的值代表了CAS延迟（CL），即读操作的列地址写入RAM控制器后，读到的值在几个时钟周期后才会出现在输出线上</li>
<li><code>A3</code>设置突发类型，连续型还是非连续型</li>
<li><code>{A2, A1, A0}</code>线上的值代表突发读写时的长度</li>
</ul>
<p>需要注意的是，“突发”并非指从输入的地址开始向后连续读写多个存储单元的值，而是：</p>
<ul>
<li><p>开始时将每个行分成长为突发长度的多块</p>
</li>
<li><p>进行读写时从输入的地址开始读写，地址在当前的块内进行“环形递增（连续突发的情况）”</p>
<p><img alt="burst" src="./burst.png"/></p>
</li>
</ul>
</li>
<li><p>等待<code>load mode register</code>完成，等待时长为$t_{MRD}$，在我这块片子上至少是2个时钟周期（即发送一个NOP）。</p>
</li>
<li><p>初始化完成，可以开始用了。</p>
</li>
</ol>
<h2 id="SDRAM写入时序"><a class="headerlink" href="#SDRAM写入时序" title="SDRAM写入时序"></a>SDRAM写入时序</h2><p><img alt="write-without_autoprecharge" src="./write-without_autoprecharge.png"/></p>
<ol>
<li>发送<code>active</code>指令（<code>{NRAS, NCAS, NWE} = 'b011</code>），同时<code>A</code>和<code>BA</code>线上送入BANK和相应行的地址，来激活对应的行。</li>
<li>等待$t_{RCD}$（发送NOP），在我的片子上是20ns。</li>
<li>发送<code>write</code>指令（<code>{NRAS, NCAS, NWE} = 'b100</code>），同时<code>A</code>和<code>BA</code>线上送入BANK和相应行地址，<code>A10 = 0</code>，<code>DQ</code>线上送入第一个16位要写入的数据。</li>
<li>【根据突发长度重复几次】指令线送入NOP，DQ线上送入第n个要写入的数据。</li>
<li>如果从<code>active</code>指令发出到现在，时间未满$t_{RAS}$，本片为$42ns$，则用NOP补足。</li>
<li>发送<code>precharge</code>指令（即<code>{NRAS, NCAS, NWE} = 'b010</code>）</li>
<li>等待<code>precharge</code>完成，等待时间为$t_{RP}$，在我的片子上是20ns。</li>
<li>写入完成</li>
</ol>
<h2 id="SDRAM读取时序"><a class="headerlink" href="#SDRAM读取时序" title="SDRAM读取时序"></a>SDRAM读取时序</h2><p><img alt="read_without_autoprecharge" src="./read_without_autoprecharge.png"/></p>
<ol>
<li>发送<code>active</code>指令（<code>{NRAS, NCAS, NWE} = 'b011</code>），同时<code>A</code>和<code>BA</code>线上送入BANK和相应行的地址，来激活对应的行。</li>
<li>等待$t_{RCD}$（发送NOP），在我的片子上是20ns。</li>
<li>发送<code>read</code>指令（<code>{NRAS, NCAS, NWE} = 'b101</code>），同时<code>A</code>和<code>BA</code>线上送入BANK和相应行地址，<code>A10 = 0</code>。</li>
<li><code>read</code>后$CL$个时钟周期后，读取的几个数据依次出现在DQ上（每个时钟上升沿出现一个）</li>
<li>如果从<code>active</code>指令发出到现在，时间未满$t_{RAS}$，本片为$42ns$，则用NOP补足。</li>
<li>发送<code>precharge</code>指令（即<code>{NRAS, NCAS, NWE} = 'b010</code>）</li>
<li>等待<code>precharge</code>完成，等待时间为$t_{RP}$，在我的片子上是20ns。</li>
<li>读取完成</li>
</ol>
<h2 id="SDRAM刷新时序"><a class="headerlink" href="#SDRAM刷新时序" title="SDRAM刷新时序"></a>SDRAM刷新时序</h2><p><img alt="auto_refresh" src="./auto_refresh.png"/></p>
<p>直接在其他命令完成后（即最后的<code>precharge</code>完全完成后）发送<code>auto refresh</code>（<code>{NRAS, NCAS, NWE} = 'b001</code>，然后等待$t_{RC}$（我的片子上至少是15ns）即可。</p>
<p>SDRAM内部电容保存数据的最长时间是64ms。一次刷新可以刷新每个bank的一行。因此两次刷新之间的时间间隔不应超过$64ms / 每bank行数$，在我的片子上这个数值是$64ms / 8192 = 7.8125\mu s$，考虑到时钟精度问题，$7.68\mu s$（1024个时钟周期）刷新一次为宜。</p>
<p>所以，整个SDRAM的操作就可以建模为一个状态机，初始化完成后，进入等待状态，当需要刷新时就进行刷新，在读写请求到来时，如果没有刷新的任务，就进入读写状态，否则进行一次突发读写，然后再判断是否需要刷新或进一步读写。</p>
<blockquote id="fn_1">
<sup>1</sup>. 然而我觉得并不方便TAT<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_2">
<sup>2</sup>. 很多资料上画的储存单元都是正方形的，这给人一种“一个储存单元对应一个电容，也对应一个位”的错误暗示，此处把单元画成长方形，希望可以让读者更容易地看出存储单元是有宽度的，对应了多个位。<a href="#reffn_2" title="Jump back to footnote [2] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_3">
<sup>3</sup>. 随芯片型号而定<a href="#reffn_3" title="Jump back to footnote [3] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_4">
<sup>4</sup>. 如果要求按字节编址，则要再增加一位，来判断是读写当前内存16位中的高8位还是低8位，即向RAM控制器发送的地址为25位。<a href="#reffn_4" title="Jump back to footnote [4] in the text."> ↩</a>
</blockquote>

