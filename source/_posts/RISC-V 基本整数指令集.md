---
title: RISC-V 基本整数指令集
date: 2019-06-13 00:00:00
tags: [体系结构, 指令集]
mathjax: true
comments: true
summary: 在连续被x86虐了n多场考试之后，我决定搞懂这个简单易懂的RISC-V，至少做一个支持所有基本整数指令的CPU出来，下面是对这个基本整数指令集的分析。
---
<p>RISC-V指令集中最基本的部分是RV32I，每个RISC-V CPU都要实现这一指令集（当然嵌入式设备可以实现更为精减的RV32E）。</p>
 hexo-inject:begin  hexo-inject:end <p>RV32I中共有47条指令，其中<code>SCALL</code>和<code>FENCE</code>等可以在阉割了部分功能的条件下通过某些方式用其他指令替代，故真正”最少需要实现”的指令条数是38条。</p>
<h2 id="寻址方式"><a class="headerlink" href="#寻址方式" title="寻址方式"></a>寻址方式</h2><p>RISC-V在运算指令中只支持寄存器寻址和立即数，所有和内存的交互都通过<code>load</code>和<code>store</code>族指令进行。比x86一堆怪异而且根本记不住的寻址方式不知道高到哪里去了。</p>
<h2 id="寄存器"><a class="headerlink" href="#寄存器" title="寄存器"></a>寄存器</h2><p>RV32I要求要有：</p>
<ul>
<li><p>1个32位零寄存器<code>x0/zero</code>，这个寄存器直接与硬件的<code>0</code>相连。</p>
</li>
<li><p>31个32位通用寄存器<code>x1-x31</code>，和x86的表面通用是不同的，这些寄存器是真正的”通用”寄存器，往里塞啥都可以，用起来也都没有差别，虽然如果遵循标准的调用约定的话，他们还是有各自的用途和名字。<br/><img alt="屏幕快照 2019-06-14 上午1.06.49" src="./屏幕快照 2019-06-14 上午1.06.49.png"/></p>
</li>
<li><p>1个程序计数器<code>PC</code>，RISC-V的PC指向的是<strong>当前</strong>执行的指令的地址。</p>
</li>
</ul>
<h2 id="指令格式"><a class="headerlink" href="#指令格式" title="指令格式"></a>指令格式</h2><p><img alt="屏幕快照 2019-06-14 上午1.22.43" src="./屏幕快照 2019-06-14 上午1.22.43.png"/></p>
<p>如图，RV32I主要有</p>
<ul>
<li><code>R</code>（代表<code>register</code>）</li>
<li><code>I</code>（代表<code>imm</code>）</li>
<li><code>S</code>（代表<code>store</code>）</li>
<li><code>U</code>（代表<code>upper</code>）</li>
</ul>
<p>四种指令格式，而根据指令中立即数各个位的布局，又有另外两种指令格式</p>
<ul>
<li><code>B</code>（代表<code>branch</code>，<code>S</code>的变体，虽然和<code>store</code>完全没关系）</li>
<li><code>J</code>（代表<code>jump</code>，<code>U</code>的变体，同样和<code>upper</code>没啥关系）</li>
</ul>
<p>其中各个指令格式形成的立即数格式如下：</p>
<p><img alt="屏幕快照 2019-06-14 上午2.36.53" src="./屏幕快照 2019-06-14 上午2.36.53.png"/></p>
<h2 id="运算指令"><a class="headerlink" href="#运算指令" title="运算指令"></a>运算指令</h2><p>共有19条运算指令，实现完了这些指令集就完成了一半了。</p>
<p>所有运算使用指令<code>func3</code>字段来标记运算的类型，有时会用<code>funct7</code>的左起第6位来进一步指定操作类型（如加/减，移位是算术移位还是逻辑移位）。</p>
<div class="table-container">
<table>
<thead>
<tr>
<th><code>func3</code></th>
<th>助记符</th>
<th>作用</th>
</tr>
</thead>
<tbody>
<tr>
<td>000</td>
<td>ADD/SUB</td>
<td>加减法</td>
</tr>
<tr>
<td>001</td>
<td>SL</td>
<td>左移</td>
</tr>
<tr>
<td>010</td>
<td>SLT</td>
<td>若带符号比较结果是<code>&lt;</code>，则设置目标寄存器为1</td>
</tr>
<tr>
<td>011</td>
<td>SLTU</td>
<td>若无符号比较结果是<code>&lt;</code>，则设置目标寄存器为1</td>
</tr>
<tr>
<td>100</td>
<td>XOR</td>
<td>异或</td>
</tr>
<tr>
<td>101</td>
<td>SR</td>
<td>右移</td>
</tr>
<tr>
<td>110</td>
<td>OR</td>
<td>或</td>
</tr>
<tr>
<td>111</td>
<td>AND</td>
<td>与</td>
</tr>
</tbody>
</table>
</div>
<h3 id="寄存器-立即数运算"><a class="headerlink" href="#寄存器-立即数运算" title="寄存器-立即数运算"></a>寄存器-立即数运算</h3><p>这类运算用一个<code>I</code>型指令表示，其<code>opcode</code>为<code>0010011</code>。</p>
<p><img alt="屏幕快照 2019-06-14 上午2.35.03" src="./屏幕快照 2019-06-14 上午2.35.03.png"/></p>
<p><img alt="屏幕快照 2019-06-14 上午2.40.37" src="./屏幕快照 2019-06-14 上午2.40.37.png"/></p>
<p>移位指令中<code>L</code>代表逻辑移位，<code>A</code>代表算术移位，<code>shamt</code>代表要移动的位数。</p>
<h3 id="寄存器-寄存器运算"><a class="headerlink" href="#寄存器-寄存器运算" title="寄存器-寄存器运算"></a>寄存器-寄存器运算</h3><p>这类运算用一个<code>R</code>型指令表示，其<code>opcode</code>为<code>0110011</code>。</p>
<p><img alt="屏幕快照 2019-06-14 上午11.52.33" src="./屏幕快照 2019-06-14 上午11.52.33.png"/></p>
<p>注意在移位指令中<code>rs2</code>只有低5位有效。</p>
<h2 id="LUI"><a class="headerlink" href="#LUI" title="LUI"></a><code>LUI</code></h2><p>这是一个<code>U</code>型指令，<code>opcode</code>为<code>0110111</code>，这个指令主要用于将32位数的第31-第12位送入寄存器。与<code>ADDI</code>连用能达到将32位立即数送入寄存器的目的，在后面的跳转指令中也用于构造长距离跳转。</p>
<p><img alt="屏幕快照 2019-06-14 下午12.27.24" src="./屏幕快照 2019-06-14 下午12.27.24.png"/></p>
<h2 id="控制转移（跳转）指令"><a class="headerlink" href="#控制转移（跳转）指令" title="控制转移（跳转）指令"></a>控制转移（跳转）指令</h2><p>RISC-V中的跳转都是相对跳转。</p>
<h3 id="无条件跳转"><a class="headerlink" href="#无条件跳转" title="无条件跳转"></a>无条件跳转</h3><h4 id="JAL"><a class="headerlink" href="#JAL" title="JAL"></a><code>JAL</code></h4><p>这是一个<code>J</code>型指令，<code>opcode</code>为<code>1101111</code>，这个指令将当前<code>pc+4</code>的值存入指定寄存器（可用来进行函数调用），并进行跳转，跳转范围为（向前或向后）<code>1MiB</code>内。</p>
<p><img alt="屏幕快照 2019-06-14 下午1.14.20" src="./屏幕快照 2019-06-14 下午1.14.20.png"/></p>
<p>注意此处的<code>offset</code>没有第0位，这是由于每个指令都是32位的，代码区不可能出现<code>offset</code>第0位为1的情况。</p>
<h3 id="JALR"><a class="headerlink" href="#JALR" title="JALR"></a><code>JALR</code></h3><p>这是一个<code>I</code>型指令，<code>opcode</code>为<code>1100111</code>，和<code>JAL</code>唯一不同之处在于<code>JALR</code>的目标地址是由<code>(imm + rs1) &amp; 0xffff_fffe</code><sup><a href="#fn_1" id="reffn_1">1</a></sup>的结果。</p>
<p><img alt="屏幕快照 2019-06-14 下午1.28.47" src="./屏幕快照 2019-06-14 下午1.28.47.png"/></p>
<p>其中<code>rs1</code>的值常常是由<code>LUI</code>加载的。</p>
<h3 id="有条件跳转"><a class="headerlink" href="#有条件跳转" title="有条件跳转"></a>有条件跳转</h3><p>所有有条件跳转指令都是<code>B</code>型指令，<code>opcode</code>为<code>1100011</code>，跳转范围是向前或向后<code>4KiB</code>。</p>
<p><img alt="屏幕快照 2019-06-14 下午1.39.17" src="./屏幕快照 2019-06-14 下午1.39.17.png"/></p>
<div class="table-container">
<table>
<thead>
<tr>
<th><code>funct3</code></th>
<th>助记符</th>
<th>作用</th>
</tr>
</thead>
<tbody>
<tr>
<td>000</td>
<td>BEQ</td>
<td><code>rs1 == rs2</code>，则跳转</td>
</tr>
<tr>
<td>001</td>
<td>BNE</td>
<td><code>rs1 != rs2</code>，则跳转</td>
</tr>
<tr>
<td>100</td>
<td>BLT</td>
<td>带符号比较，<code>rs1 &lt; rs2</code>，则跳转</td>
</tr>
<tr>
<td>101</td>
<td>BGE</td>
<td>带符号比较，<code>rs1 &gt; rs2</code>，则跳转</td>
</tr>
<tr>
<td>110</td>
<td>BLTU</td>
<td>无符号比较，<code>rs1 &lt; rs2</code>，则跳转</td>
</tr>
<tr>
<td>111</td>
<td>BGEU</td>
<td>无符号比较，<code>rs1 &gt; rs2</code>，则跳转</td>
</tr>
</tbody>
</table>
</div>
<h2 id="AUIPC"><a class="headerlink" href="#AUIPC" title="AUIPC"></a><code>AUIPC</code></h2><p>这是一个<code>U</code>型指令，<code>opcode</code>为<code>0010111</code>，作用是：<code>rd = pc + imm</code>。</p>
<p><img alt="屏幕快照 2019-06-14 下午3.49.45的副本" src="./屏幕快照 2019-06-14 下午3.49.45的副本.png"/></p>
<p>这个指令和<code>JALR</code>结合，可以进行32位的相对跳转，可以用来构建PIC<sup><a href="#fn_2" id="reffn_2">2</a></sup>。</p>
<h2 id="内存存取指令"><a class="headerlink" href="#内存存取指令" title="内存存取指令"></a>内存存取指令</h2><h3 id="LOAD"><a class="headerlink" href="#LOAD" title="LOAD"></a><code>LOAD</code></h3><p>这是一组<code>I</code>型指令，<code>opcode</code>为<code>0100011</code>，用途是将内存中<code>rs1+imm</code>位置的数据加载到<code>rd</code>中。</p>
<p><img alt="屏幕快照 2019-06-14 下午4.11.45" src="./屏幕快照 2019-06-14 下午4.11.45.png"/></p>
<p>其中<code>funct3</code>指定了要加载的数据的长度：</p>
<div class="table-container">
<table>
<thead>
<tr>
<th><code>funct3</code></th>
<th>助记符</th>
<th>数据长度</th>
</tr>
</thead>
<tbody>
<tr>
<td>000</td>
<td>LB</td>
<td>8位，放入寄存器时符号扩展到32位</td>
</tr>
<tr>
<td>001</td>
<td>LH</td>
<td>16位，放入寄存器时符号扩展到32位</td>
</tr>
<tr>
<td>010</td>
<td>LW</td>
<td>32位</td>
</tr>
<tr>
<td>100</td>
<td>LBU</td>
<td>8位，放入寄存器时0扩展到32位</td>
</tr>
<tr>
<td>101</td>
<td>LHU</td>
<td>16位，放入寄存器时0扩展到32位</td>
</tr>
</tbody>
</table>
</div>
<h3 id="STORE"><a class="headerlink" href="#STORE" title="STORE"></a><code>STORE</code></h3><p>这是一组<code>S</code>型指令，<code>opcode</code>为<code>0000011</code>，用途是将<code>rs2</code>中的数据放入内存中的<code>rs1+imm</code>位置。</p>
<p><img alt="屏幕快照 2019-06-14 下午4.31.54" src="./屏幕快照 2019-06-14 下午4.31.54.png"/></p>
<p>其中<code>funct3</code>指定了要存入的数据的长度：</p>
<div class="table-container">
<table>
<thead>
<tr>
<th><code>funct3</code></th>
<th>助记符</th>
<th>数据长度（不足32位时均取低位）</th>
</tr>
</thead>
<tbody>
<tr>
<td>000</td>
<td>SB</td>
<td>8位</td>
</tr>
<tr>
<td>001</td>
<td>SH</td>
<td>16位</td>
</tr>
<tr>
<td>010</td>
<td>SW</td>
<td>32位</td>
</tr>
</tbody>
</table>
</div>
<h2 id="控制和状态寄存器相关指令"><a class="headerlink" href="#控制和状态寄存器相关指令" title="控制和状态寄存器相关指令"></a>控制和状态寄存器相关指令</h2><p>（暂时不需要，略）</p>
<h2 id="环境（系统）调用和断点指令"><a class="headerlink" href="#环境（系统）调用和断点指令" title="环境（系统）调用和断点指令"></a>环境（系统）调用和断点指令</h2><p>（暂时不需要，略）</p>
<blockquote id="fn_1">
<sup>1</sup>. <code>&amp; 0xffff_fffe</code>即将最后一位置0<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_2">
<sup>2</sup>. Position Independent Code，位置无关代码<a href="#reffn_2" title="Jump back to footnote [2] in the text."> ↩</a>
</blockquote>

