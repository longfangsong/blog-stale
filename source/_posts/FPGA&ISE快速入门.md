---
title: FPGA&ISE快速入门
date: 2018-10-01 00:00:00
tags: [FPGA]
mathjax: true
comments: true
summary: 入了一块FPGA，Xilinx的Spartan-6系列，花了半天才用ISE烧进了程序，这里记录一下踩的坑。
---
<p>（我还可以再底一点的，从沙子开始……）</p>
    <p>（……好吧，我觉得不行）</p>
<p>国庆入了一块Xilinx的Spartan-6系列FPGA，试图把程序烧写进去。</p>
<p>非常惨的事情是，这家公司有问题(x)<sup><a href="#fn_1" id="reffn_1">1</a></sup>，做的软件都没有Mac版，甚至Linux版本都限制Open Suse和Red hat发行版，而Windows却全线（甚至Server版本都）覆盖……</p>
<p>开始觉得Ubuntu凑合能跑，结果还是我Naive了，界面能跑但是功能都是烂的，无奈虚拟机装了个Win 7，稍微卡了点……。</p>
<p>所以提醒想专门搞嵌入式（含）以下的同学，一台Windows还是必要的，否则你会发现你的工具链爆炸难配（STM32还有Mac和Linux的命令行方案，FPGA我还真没找着，知道的神仙请务必告诉我）。</p>
<p>当然你要告诉我你机器好任性跑在虚拟机里一点不卡那我只能说有钱是可以为所欲为的……</p>
<h2 id="安装"><a class="headerlink" href="#安装" title="安装"></a>安装</h2><p>到<a href="https://china.xilinx.com/support/download/index.html/content/xilinx/zh/downloadNav/design-tools.html" rel="noopener" target="_blank">这里</a>来下载ISE，注意你可能会注意到还有个东西叫<a href="https://china.xilinx.com/products/design-tools/vivado.html" rel="noopener" target="_blank">Vivado</a>，那个是给比我等屌丝有钱的，买得起Virtex-7、Kintex-7、Artix-7、和 Zynq®-7000 这些相对高级<sup><a href="#fn_2" id="reffn_2">2</a></sup>的FPGA的人用的……我费了半天下下来结果没用，一口老血……</p>
<p>作者写此文时最新版为14.7版本，我直接下载了<a href="https://china.xilinx.com/member/forms/download/xef.html?filename=Xilinx_ISE_DS_Win_14.7_1015_1.tar" rel="noopener" target="_blank">基于 Windows 7/XP/Server 的完整安装程式</a>。</p>
<p>下载网速不太稳定，貌似不是墙的问题，所以架梯子也没用……</p>
<p>下载完成解压，点击xsetup.exe来安装。</p>
<p>一路下一步，直到这里。</p>
<p>这里让你选择版本，WebPACK<sup><a href="#fn_3" id="reffn_3">3</a></sup>还是Design Suite，一般小打小闹WebPACK就够了，没必要上Design Suite。</p>
<p><img alt="Version" src="./Version.png"/></p>
<p>话说这玩意简直是吃硬盘的，我把电脑上其他的虚拟机都删了，凑出空间给这个Win分了40G才勉强装下……</p>
<p>然后其他都是默认，一路继续（包括装驱动也是继续），最后装完了会问你要Licence，这个嘛……懂的。</p>
<p>然后开就是了。</p>
<p><img alt="DS" src="./DS.png"/></p>
<p>关掉Tip of day的废话(x)，然后new一个Project。</p>
<p>Name什么的自己填，Top-level source当然选HDL（图形化编程邪道去死啦（x）<sup><a href="#fn_4" id="reffn_4">4</a></sup>）。</p>
<p>下一步这里比较重要的只有Family和Device两个，以我的板子为例，Family选<code>Spartan6</code>，Device选<code>XC6SLX9</code>，其他东西默认就好了。</p>
<p>然后创建入口module：</p>
<p><img alt="NewFile" src="./NewFile.png"/></p>
<p><img alt="NewFile2" src="./NewFile2.png"/><br/><img alt="module" src="./module.png"/></p>
<p>然后会生成Verilog代码，往里填东西就是了。</p>
<p>比如简单的这样一个与逻辑：</p>
<figure class="highlight verilog"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/><span class="line">4</span><br/><span class="line">5</span><br/><span class="line">6</span><br/><span class="line">7</span><br/><span class="line">8</span><br/></pre></td><td class="code"><pre><span class="line"><span class="keyword">module</span> main(</span><br/><span class="line">    <span class="keyword">input</span> a,</span><br/><span class="line">    <span class="keyword">input</span> b,</span><br/><span class="line">    <span class="keyword">output</span> out</span><br/><span class="line">    );</span><br/><span class="line">	<span class="keyword">assign</span> out = a &amp; b;</span><br/><span class="line"></span><br/><span class="line"><span class="keyword">endmodule</span></span><br/></pre></td></tr></table></figure>
<p>接着就是分配引脚，你可以使用GUI分配：</p>
<p><img alt="PlanAhead" src="./PlanAhead.png"/></p>
<p>一直下一步，ISE会为你打开PlanAhead，在屏幕下方这里就能为这些代码里写的端口分配实际的硬件引脚了。</p>
<p><img alt="PINS" src="./PINS.png"/></p>
<p>分配完之后会为你生成.ucf文件。</p>
<p><img alt="ucf" src="./ucf.png"/></p>
<p>当然你也可以不借助GUI自己写这个文件，语法非常简单直白，甚至不需要我解释：</p>
<figure class="highlight plain"><table><tr><td class="gutter"><pre><span class="line">1</span><br/><span class="line">2</span><br/><span class="line">3</span><br/></pre></td><td class="code"><pre><span class="line">NET "a" LOC = A2;</span><br/><span class="line">NET "b" LOC = A3;</span><br/><span class="line">NET "out" LOC = A4;</span><br/></pre></td></tr></table></figure>
<p>写完添加到工程里面就行。</p>
<p>要注意的是你的硬件生产厂家给你的硬件接口编号往往和ISE软件里的不一样……请务必要想办法拿到映射表之类的东西。</p>
<p>接着编译，随便点一个绿色小箭头就行了。</p>
<p>然后就是烧录了，点击这里</p>
<p><img alt="download" src="./download.png"/></p>
<p>会为你打开ISE iMPACT，点击：</p>
<p><img alt="scan" src="./scan.png"/></p>
<p>然后是：</p>
<p><img alt="initChain" src="./initChain.png"/></p>
<p>如果你看见了这个：</p>
<p><img alt="attach" src="./attach.png"/></p>
<p>选No。</p>
<p>其他的东西默认就行。</p>
<p>然后在出来的设备上右键，Program就下载好了。</p>
<p>这样就能用了，不过你的代码并没有被存到PROM或者Flash里面，因此一reset或者掉电就丢失了，你要做一个MCS文件，这部分在</p>
<p><img alt="prom" src="./prom.png"/></p>
<p>里面，双击进入：</p>
<p><img alt="Pram" src="./Pram.png"/></p>
<p>像上图这样选择各项参数，注意第二步的时候要点一下红圈里的按键。</p>
<p>完了之后点OK，会让你选择你要放入Prom中的.bit文件。</p>
<p>接下来会问你要不要继续添加，当然No。</p>
<p>然后OK，右键，Generate File，mcs文件就做好了。</p>
<p>回到设备页面，右键你的设备。</p>
<p>我们这次选择</p>
<p><img alt="addFlash" src="./addFlash.png"/></p>
<p>然后选择我们做出来的mcs，接着选PROM的型号</p>
<p><img alt="PromType" src="./PromType.png"/></p>
<p>然后原来FPGA的头上就会出现Flash。</p>
<p><img alt="flash" src="./flash.png"/></p>
<p>先在这个Flash上面右键，Erase清掉原来的东西。</p>
<p>然后右键，Program把你的程序下进去，</p>
<p>就好了，下次上电就会自动跑你的程序了。</p>
<blockquote id="fn_1">
<sup>1</sup>. 隔壁Quartus也这样，所以我相信这是有理由的，但隔壁的Quartus可以做一些骚操作然后在Ubuntu下面跑起来。<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_2">
<sup>2</sup>. Spartan-6系列是45nm制程，穷人之选 TAT，Virtex-7、Kintex-7、Artix-7、和 Zynq®-7000是28nm，另外还有UltraSCALE和UltraSCALE+，分别是20和16nm的。<a href="#reffn_2" title="Jump back to footnote [2] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_3">
<sup>3</sup>. 不是你想的那个webpack😓<a href="#reffn_3" title="Jump back to footnote [3] in the text."> ↩</a>
</blockquote>
<blockquote id="fn_4">
<sup>4</sup>. 讲道理做小电路图形化还是挺方便的，那个Schematic就是，具体咋玩可以自己探究。<a href="#reffn_4" title="Jump back to footnote [4] in the text."> ↩</a>
</blockquote>

