---
title: nasm 汇编
date: 2019-04-17 00:00:00
tags: [汇编]
mathjax: true
comments: true
summary: 这课上教的汇编是传说中微软独家的MASM，作为曾经的微软黑（“曾经”修饰“微软”，不是“微软黑”）,我不搞一点幺蛾子出来是不可能的事情，所以就开始研究这些个开源汇编编译器，看来看去，gas语法太怪，还是nasm和与其兼容的yasm比较合理。
---
<p>本文所述的是64位汇编。</p>
    <h2 id="环境搭建"><a class="headerlink" href="#环境搭建" title="环境搭建"></a>环境搭建</h2><p>安装nasm:</p>
<h3 id="macOS"><a class="headerlink" href="#macOS" title="macOS"></a>macOS</h3><p>我假设你已经装好了brew和xcode-dev-tools。</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">brew install nasm</span><br/></pre></td></tr></table></figure>
<p>就结束了。</p>
<h3 id="linux，以ubuntu为例"><a class="headerlink" href="#linux，以ubuntu为例" title="linux，以ubuntu为例"></a>linux，以ubuntu为例</h3><figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">apt install nasm</span><br/></pre></td></tr></table></figure>
<p>就好了，如果你需要调试，请自行了解安装gdb。</p>
<h2 id="代码"><a class="headerlink" href="#代码" title="代码"></a>代码</h2><p>nasm和masm语法大部分地方是相似的，但部分语法上还是略有不同的。</p>
<p>另外，64位汇编和实模式汇编虽然大部分指令相同，但寻址（取消分段，改为分页）等方面有很大的差异。</p>
<p>这些语法上的差异可以上网自行查询<sup><a href="#fn_1" id="reffn_1">1</a></sup>，在此略过不提，在此仅提供例程，例程功能是打印ASCII码表：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/><span class="line">13</span><br/><span class="line">14</span><br/><span class="line">15</span><br/><span class="line">16</span><br/><span class="line">17</span><br/><span class="line">18</span><br/><span class="line">19</span><br/><span class="line">20</span><br/><span class="line">21</span><br/><span class="line">22</span><br/><span class="line">23</span><br/><span class="line">24</span><br/><span class="line">25</span><br/><span class="line">26</span><br/><span class="line">27</span><br/><span class="line">28</span><br/><span class="line">29</span><br/><span class="line">30</span><br/><span class="line">31</span><br/><span class="line">32</span><br/><span class="line">33</span><br/><span class="line">34</span><br/><span class="line">35</span><br/><span class="line">36</span><br/><span class="line">37</span><br/><span class="line">38</span><br/><span class="line">39</span><br/><span class="line">40</span><br/><span class="line">41</span><br/><span class="line">42</span><br/><span class="line">43</span><br/></pre></td><td class="code"><pre><span class="line">global _start</span><br/><span class="line"></span><br/><span class="line">section .data</span><br/><span class="line">  char        :  db    97</span><br/><span class="line">  crlf		  	:  db    10</span><br/><span class="line">section .bss</span><br/><span class="line"></span><br/><span class="line">section .text</span><br/><span class="line"></span><br/><span class="line">_start:</span><br/><span class="line">	mov bl, 0</span><br/><span class="line">	loop_start:</span><br/><span class="line">  		mov byte [rel char], bl</span><br/><span class="line">  		mov rax, 0x2000004		; (macOS) call write，on linux, change 0x2000004 to 0x1</span><br/><span class="line">  		mov rdi, 1						; fd    = 1, aka stdout</span><br/><span class="line">  		mov rsi, char					; cbuf 	= char</span><br/><span class="line">  		mov rdx, 1						; nbyte = 1</span><br/><span class="line">  		syscall</span><br/><span class="line"></span><br/><span class="line">			add bl, 1</span><br/><span class="line"></span><br/><span class="line">			mov ah, 0</span><br/><span class="line">			mov al, bl</span><br/><span class="line"></span><br/><span class="line">			mov cx, 0xa</span><br/><span class="line">			div byte cl</span><br/><span class="line"></span><br/><span class="line">			cmp ah, 0x9</span><br/><span class="line">			jne skip</span><br/><span class="line"></span><br/><span class="line">			mov rax, 0x2000004		; (macOS) call write，on linux, change 0x2000004 to 0x1</span><br/><span class="line">  		mov rdi, 1						; fd 	  = 1, aka stdout</span><br/><span class="line">  		mov rsi, crlf					; cbuf 	= crlf</span><br/><span class="line">  		mov rdx, 1						; nbyte = 2</span><br/><span class="line">			syscall</span><br/><span class="line"></span><br/><span class="line">	skip:</span><br/><span class="line">			cmp bl, 128</span><br/><span class="line">			jne loop_start</span><br/><span class="line"></span><br/><span class="line">  mov rax, 0x2000001		  	; (macOS) call exit，on linux, change 0x2000001 to 60</span><br/><span class="line">  mov rdi, 0								; rval = 0</span><br/><span class="line">  syscall</span><br/></pre></td></tr></table></figure>
<p>另附<a href="https://github.com/apple/darwin-xnu/blob/master/bsd/kern/syscalls.master" rel="noopener" target="_blank">macOS</a><sup><a href="#fn_2" id="reffn_2">2</a></sup>和<a href="https://filippo.io/linux-syscall-table/" rel="noopener" target="_blank">linux</a> syscall列表。</p>
<h2 id="编译运行"><a class="headerlink" href="#编译运行" title="编译运行"></a>编译运行</h2><h3 id="macOS："><a class="headerlink" href="#macOS：" title="macOS："></a>macOS：</h3><p>macOS上需要出的是macho64格式：</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line">nasm -f macho64 文件名.asm</span><br/><span class="line">ld -e _start -lSystem -no_pie 文件名.o -o 输出文件名</span><br/></pre></td></tr></table></figure>
<h3 id="linux："><a class="headerlink" href="#linux：" title="linux："></a>linux：</h3><p>linux一般使用elf64格式：</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/></pre></td><td class="code"><pre><span class="line">nasm 文件名.asm -f elf64</span><br/><span class="line">ld -e _start 文件名.o -o 输出文件名</span><br/></pre></td></tr></table></figure>
<h2 id="调试"><a class="headerlink" href="#调试" title="调试"></a>调试</h2><p>以<code>lldb</code>为例，<code>gdb</code>等应该与此相似：</p>
<p>假设输出文件为<code>a.out</code>：</p>
<figure class="highlight shell"><table><tr><td class="gutter"><pre><span class="line">1</span><br/></pre></td><td class="code"><pre><span class="line">lldb a.out</span><br/></pre></td></tr></table></figure>
<p>用<code>b start</code>在开始执行你写的代码暂停（实际上是在”start”处戳一个断点）。</p>
<p>然后用<code>n</code>来单步执行，或者在<code>b 地址</code>在相应地址处戳断点。</p>
<blockquote id="fn_1">
<sup>1</sup>. 真相是我也不太懂……<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_2">
<sup>2</sup>. macOS调用时要在表中的数字基础上加上<code>0x2000000</code>，别问我为啥，这是魔法<a href="#reffn_2" title="Jump back to footnote [2] in the text."> ↩</a>
</blockquote>

