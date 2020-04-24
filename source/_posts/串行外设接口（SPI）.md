---
title: 串行外设接口（SPI）
date: 2018-03-20 00:00:00
tags: [单片机, 接口技术]
mathjax: true
comments: true
summary: 串行外设接口，即通常所说的SPI，是单片机中常用的一种和外部设备通信用的同步串行通信接口规范，常用于和SD卡，LCD屏幕等设备的通信中。
---
<p>SPI这个接口其实在我玩Arduino的时候就在用了，不过当时就是个调库侠，啥规定原理都不知道只管<code>#include &lt;SPI.h&gt;</code>就好了，现在玩STM32就不一样了，不知道SPI规范到底是个啥就凉凉。</p>
 hexo-inject:begin  hexo-inject:end <h2 id="SPI的接线"><a class="headerlink" href="#SPI的接线" title="SPI的接线"></a>SPI的接线</h2><p>SPI的接线比IIC要多一点，规定在主设备和从设备上各要有四个接线：</p>
<ul>
<li>SCLK：时钟信号，主设备的SCLK（可以是一个数字口）要接到从设备的SCLK上，以达成关于通信速度的共识</li>
<li>MOSI：主设备输出，从设备输入，主设备的MOSI（可以是一个数字口）要接到从设备的MISO<sup><a href="#fn_1" id="reffn_1">1</a></sup>上</li>
<li>MISO：主设备输入，从设备输出，主设备的MISO（可以是一个数字口）要接到从设备的MOSI<sup><a href="#fn_1" id="reffn_1">1</a></sup>上</li>
<li>SS（其实多称CS）：选择哪一个从设备接收和发送信息用的接线，由主设备的一个数字口连接到从设备，一般来说给低电平代表这个从设备“被选中”来和主设备通信。</li>
</ul>
<p>由于SS的存在，主设备的SCLK、MOSI和MISO可以复用，如图，SCLK，MOSI和MISO分别只占用一个引脚，这些引脚上穿输的数据由哪一个从设备接收则由相应的SS决定。</p>
<p><img alt="multiSlave" src="./multiSlave.png"/></p>
<h2 id="SPI的操作"><a class="headerlink" href="#SPI的操作" title="SPI的操作"></a>SPI的操作</h2><p>假设一个主设备已经连接了一个或多个从设备，那么可以用下面的方法来和一个从设备通信。</p>
<ol>
<li><p>在SCLK上给出一个时钟信号来配置时钟，通常是时钟频率是几个MHz</p>
</li>
<li><p>把对应的SS置为低电平</p>
</li>
<li><p>（如果从设备需要一段时间来准备好要发送给主设备的数据，主设备要等待那么长的一段时间）</p>
</li>
<li><p>在每个时钟周期内</p>
<ul>
<li>主设备会发送一个位给从设备（通过给MOSI线高电平来发送1，给低电平来发送0），这一步常常在时钟信号的下降沿发生</li>
<li>同时从设备会发送一个位给主设备（同理是使用MISO线来接收这个得到的值），这一步常常在时钟信号的上升沿发生</li>
</ul>
<p>这两部分构成了一个全双工数据传输，即使你并不想主设备给从设备发送任何信息而只是想获得从设备所发送的信息，或反之，只想向从设备中写入信息，这个全双工数据传输仍然是全双工的。</p>
<blockquote>
<p>传输通常会使用到给定字长的两个移位寄存器，一个在主设备中，一个在从设备中; 它们以虚拟环形拓扑连接。 数据通常先移出最大的位。 在时钟边沿，主机和从机均移出一位，然后在传输线上输出给对方。 在下一个时钟沿，每个接收器都从传输线接受该位，并设置为移位寄存器的新的最低有效位。 在完成这样一个移出-移入的周期后，主机和从机就交换寄存器中的一位。 ——维基百科</p>
</blockquote>
</li>
</ol>
<h2 id="SPI代码示例"><a class="headerlink" href="#SPI代码示例" title="SPI代码示例"></a>SPI代码示例</h2><p>代码来自维基百科。</p>
<figure class="highlight c"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/><span class="line">9</span><br/><span class="line">10</span><br/><span class="line">11</span><br/><span class="line">12</span><br/><span class="line">13</span><br/><span class="line">14</span><br/><span class="line">15</span><br/><span class="line">16</span><br/><span class="line">17</span><br/><span class="line">18</span><br/><span class="line">19</span><br/><span class="line">20</span><br/><span class="line">21</span><br/><span class="line">22</span><br/><span class="line">23</span><br/><span class="line">24</span><br/><span class="line">25</span><br/><span class="line">26</span><br/><span class="line">27</span><br/><span class="line">28</span><br/><span class="line">29</span><br/><span class="line">30</span><br/></pre></td><td class="code"><pre><span class="line"><span class="comment">/*</span></span><br/><span class="line"><span class="comment"> * 通过SPI协议在主设备和从设备之间交换一个字节的数据</span></span><br/><span class="line"><span class="comment"> *</span></span><br/><span class="line"><span class="comment"> * Polarity and phase are assumed to be both 0, i.e.:</span></span><br/><span class="line"><span class="comment"> *   - 输入数据在SCLK的上升沿捕获。</span></span><br/><span class="line"><span class="comment"> *   - 输出数据在SCLK的下降沿传播。</span></span><br/><span class="line"><span class="comment"> *</span></span><br/><span class="line"><span class="comment"> * 返回接收到的一字节的数据</span></span><br/><span class="line"><span class="comment"> */</span></span><br/><span class="line"></span><br/><span class="line"><span class="keyword">uint8_t</span> SPI_transfer_byte(<span class="keyword">uint8_t</span> byte_out) {</span><br/><span class="line">	<span class="keyword">uint8_t</span> byte_in = <span class="number">0</span>;</span><br/><span class="line">	<span class="keyword">uint8_t</span> bit;</span><br/><span class="line">	<span class="keyword">for</span> (bit = <span class="number">0x80</span>; bit; bit &gt;&gt;= <span class="number">1</span>) {</span><br/><span class="line">		<span class="comment">/* Shift-out a bit to the MOSI line */</span></span><br/><span class="line">		write_MOSI((byte_out &amp; bit) ? HIGH : LOW);</span><br/><span class="line">		<span class="comment">/* Delay for at least the peer's setup time */</span></span><br/><span class="line">		delay(SPI_SCLK_LOW_TIME);</span><br/><span class="line">		<span class="comment">/* Pull the clock line high */</span></span><br/><span class="line">		write_SCLK(HIGH);</span><br/><span class="line">		<span class="comment">/* Shift-in a bit from the MISO line */</span></span><br/><span class="line">		<span class="keyword">if</span> (read_MISO() == HIGH)</span><br/><span class="line">			byte_in |= bit;</span><br/><span class="line">			<span class="comment">/* Delay for at least the peer's hold time */</span></span><br/><span class="line">			delay(SPI_SCLK_HIGH_TIME);</span><br/><span class="line">			<span class="comment">/* Pull the clock line low */</span></span><br/><span class="line">			write_SCLK(LOW);</span><br/><span class="line">		}</span><br/><span class="line">		<span class="keyword">return</span> byte_in;</span><br/><span class="line">}</span><br/></pre></td></tr></table></figure>
<p>总的来说SPI是相当方便的一个通信协议，相比IIC我更愿意使用SPI一些。</p>
<blockquote id="fn_1">
<sup>1</sup>. 板子上标记一般是MOSI接MISO，MISO接MOSI，这和上面的图片不太一样，注意甄别，其实不行反过来试试看就行。<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>

