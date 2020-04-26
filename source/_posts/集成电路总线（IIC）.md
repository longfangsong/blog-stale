---
title: 集成电路总线（IIC）
date: 2018-03-20 00:00:00
tags: [单片机, 接口技术]
mathjax: true
comments: true
summary: I²C（Inter-Integrated Circuit），集成电路总线，是一种串行通信总线，常用于单片机之间和单片机与EEPROM、DAC、ADC、RTC等设备的通信。
---
<p>I²C和SPI相比通信协议没那么简单，但是用的线比较少，因此封装尺寸和耗电量可以减少。</p>
    <p>另外，注意要读“I平方C”或者“IIC”，不要读“I二C”，这就和读“C井”一样不专业（正确读法是“C sharp”）。</p>
<h2 id="IIC的接线"><a class="headerlink" href="#IIC的接线" title="IIC的接线"></a>IIC的接线</h2><p>IIC只使用了两根线（全部都采用开漏输出，上拉电阻），串行数据线（SDA）和串行时钟线（SCL）。</p>
<ul>
<li>SDA：用于传输数据，连接从设备的SDA</li>
<li>SCL：时钟信号，用于达成关于通信速度的共识，连接从设备的SCL</li>
</ul>
<p>由于只有两根线，所以通过类似SPI那样通过片选线来选择要与之通信的芯片的方式就不可行了，幸运的是每个（其实应该是每批）IIC设备出厂时都附带了一个IIC地址，主设备在交换数据开始前会向总线上广播自己准备与之通信的IIC设备的地址，然后相关的IIC设备会启动，无关的IIC设备不会启用，通过这种方式，一组SDA-SCL的IIC总线上可以直接使用多个不同IIC地址的设备，当然缺点就是如果不另外进行配置<sup><a href="#fn_1" id="reffn_1">1</a></sup>，一般不能安装多个同一批号的设备。</p>
<h2 id="IIC的操作"><a class="headerlink" href="#IIC的操作" title="IIC的操作"></a>IIC的操作</h2><p>IIC的操作其实是很毒的：</p>
<ul>
<li><p>首先主设备要发一个开始传输信号，这个信号长这样</p>
<p><img alt="image-20180320160940" src="./start-bit.png"/></p>
<p>简单来说就是在SCL的高电平期间，SDA发送一个下降沿。</p>
<p>在得到这个信号后，所有IIC从设备都会被唤醒，然后等待接收接下来的信号。</p>
</li>
<li><p>接下来发送的是7位的IIC地址，发送方法如下：</p>
<p><img alt="image-20180320160940" src="./send-data.png"/></p>
<p>简单来说，从设备会在SCL为高电平时读取SDA线上的数据位，而完全不理睬SCL为低电平的时候SDA上的电平变化等。</p>
</li>
<li><p>接下来发送一个代表主设备要进行读还是写操作的位。0代表写，1代表读。</p>
</li>
<li><p>接着主设备会将SDA设置为输入（通过上拉电阻拉高），等待从设备将SDA拉低，来表示从设备成功被选中（这一动作被称为“（从设备）发送ACK”）。</p>
</li>
<li><p>接下来根据发送的读/写操作位来进行读/写操作，</p>
<ul>
<li>若主设备处于写模式，则会发送一个字节，然后等待从设备发送ACK，接着发送下一个字节，如此循环，直到主设备发送结束传输信号或者通信失败（ACK主设备迟迟等不到从设备的ACK，则认为发送失败）。</li>
<li>若主设备处于读模式，则由从设备发送一个字节，主设备发送ACK，接着从设备发送下一个字节，如此循环，直到主设备发送结束传输信号。</li>
</ul>
</li>
<li><p>主设备发送停止信号，停止信号长这样：</p>
<p><img alt="image-20180320160940" src="./end-bit.png"/></p>
<p>简单来说就是在SCL的高电平期间，SDA发送一个上升沿。</p>
</li>
</ul>
<blockquote id="fn_1">
<sup>1</sup>. 另外配置的案例：比如常用的EEPROM芯片的IIC地址中，前四个地址位都是1010，但后三个地址位可以用硬件连接决定，所以可以最多连接多达8个EEPROM芯片。<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_图片来源">
<sup>图片来源</sup>. <a href="https://www.cnblogs.com/bixiaopengblog/p/7469536.html" rel="noopener" target="_blank">https://www.cnblogs.com/bixiaopengblog/p/7469536.html</a><a href="#reffn_图片来源" title="Jump back to footnote [图片来源] in the text."> ↩</a>
</blockquote>

