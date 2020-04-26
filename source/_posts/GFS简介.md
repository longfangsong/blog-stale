---
title: GFS简介
date: 2020-03-18 00:00:00
tags: [分布式系统, 文件系统]
mathjax: true
comments: true
summary: GFS （Google File System）是一个分布式文件系统
---
<p>GFS是一个一个适用于大规模分布式数据处理相关应用的，可扩展的分布式文件系统。</p>
    <h2 id="假设"><a class="headerlink" href="#假设" title="假设"></a>假设</h2><ul>
<li>系统建立在大量廉价普通计算机上</li>
<li>节点失效是正常的</li>
<li>存储的文件是很大（数个GB）的</li>
<li>大部分文件的写方式都为在文件尾追加，极少有随机写的要求</li>
<li>大的流式读取+小的随机读取</li>
</ul>
<h2 id="架构"><a class="headerlink" href="#架构" title="架构"></a>架构</h2><p><img alt="1089769-20180501090649479-435901512" src="./1089769-20180501090649479-435901512.png"/></p>
<ul>
<li>单个master</li>
<li>多个chunkserver（块服务器）</li>
<li>客户使用clinet访问</li>
</ul>
<p>在GFS下，每一个文件都拆成固定大小的chunk(块)，chunk一般大小为64M，且由一个id标记。每一个块都由master根据块创建的时间产生一个全局唯一的以后不会改变的64位的chunk handle标志。chunkservers在本地磁盘存储这些块。为了保证可靠性，chunk一式三份，放在三个chunkservers里。</p>
<h3 id="master"><a class="headerlink" href="#master" title="master"></a>master</h3><p>master负责存储所有的文件系统的元数据，如文件到chunk的映射关系、chunk所在的节点等信息。</p>
<p>master也同样控制系统级别的活动，比如chunk的分配，chunkserver之间的chunk镜像管理。</p>
<p>master和这些chunkserver之间会有定期通讯（心跳包），并获取chunckserver的状态。 </p>
<p>为了避免master成为性能瓶颈，客户端永远不会通过master来做文件的数据读写。客户端只是问master它应当访问那一个chunkserver来访问数据。</p>
<h3 id="副本控制"><a class="headerlink" href="#副本控制" title="副本控制"></a>副本控制</h3><p>GFS使用primary-secondary方式来控制副本数据的一致性。</p>
<p>即每次修改或者append操作时，都会在这些chunk所在（或者要添加到）的节点中选择一个primary节点（当然primary节点已经存在时就不需要选了），这个节点负责确定修改请求被执行的顺序（即，对于连续到达系统的多个对某个chunk的写请求，顺序以其到达这个节点的顺序为准），详见下面的写操作流程一节。</p>
<h2 id="读操作流程"><a class="headerlink" href="#读操作流程" title="读操作流程"></a>读操作流程</h2><p>读取的逻辑比较简单，直接从master拿到有对应chunk的节点，然后找到其中离自己最近的节点直接读就是了。</p>
<h2 id="写操作流程"><a class="headerlink" href="#写操作流程" title="写操作流程"></a>写操作流程</h2><p>GFS的写操作体现了其数据流和控制流分离的思想，这可以有效地改进性能。</p>
<p><img alt="1089769-20180501092046288-1923656632" src="./1089769-20180501092046288-1923656632.png"/></p>
<ol>
<li>客户端要对某chunk执行操作，它询问master哪个chunkserver是对应的primary以及各副本的位置信息。如果没有任何节点是primary，master选择一个节点认为其是primary（此时不会去通知这个节点）<sup><a href="#fn_1" id="reffn_1">1</a></sup>。</li>
</ol>
<p>　　2. master将primary、副本位置信息回复到客户端。客户端缓存这些数据以便未来重用，这样它仅需要在当前primary无法访问或者卸任时去再次联系master。<br/>　　3. 客户端推送数据到所有的选中的节点。只是推送，不会将数据落盘，只是在各chunkserver上将数据准备好，推送的顺序也与控制流无关。每个节点所在的chunkserver将数据存储在一个内部的缓冲区中，直到数据被使用或者过期。<br/>　　4. 一旦所有节点都确认收到了数据，客户端正式发送一个写请求到primary。写请求无真实数据，只有一个身份标识，对应第三步中发给各个节点的数据包。在primary中会持续的收到来自各个客户端的各种变更请求，本次写请求只是其中一个而已。在持续接收请求的过程中，首要副本会为每个请求分配唯一的递增序号，它也会严格按照此顺序来在本地状态中实施变更。<br/>　　5. primary将写请求推送到所有其他选中节点（请求中已带有分配的序号），每个节点都会严格按顺序依次实施变更。<br/>　　6. 次级节点回复给primary，确认他们已完成操作。<br/>　　7. primary回复客户端。在任何节点遭遇的任何错误，都被汇报给客户端。在错误发生时，此写操作可能已经在primary和某些次级节点中实施成功。（如果它在primary上就失败了，就不会分配序号也不会往后推进。）客户端则认为此次请求失败，请求所修改的区域变成了不一致状态。对于失败的变更，客户端会重试，它首先会重试步骤3到步骤7，实在不行就重试整个流程。</p>
<h2 id="故障恢复流程"><a class="headerlink" href="#故障恢复流程" title="故障恢复流程"></a>故障恢复流程</h2><p>如果一个节点长时间没有心跳包传输到master，那么认为这个节点挂掉了。</p>
<p>这种情况下，master会将这个节点上所有的chunk从其另外两个副本中读出，并创建一个新的副本，放在另一个节点上。</p>
<p><img alt="1089769-20180501091746970-1852412000" src="./1089769-20180501091746970-1852412000.png"/></p>
<p>如图中机器1挂了，那么机器2、3、4的内容都会变为<code>{o, p, q}</code>。</p>
<blockquote id="fn_1">
<sup>1</sup>. 这和基于raft和paxos的方案不同，这更像是“钦定”而后者更像选举<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>

