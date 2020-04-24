---
title: MapReduce框架简介
date: 2020-03-18 00:00:00
tags: [分布式系统]
mathjax: true
comments: true
summary: MapReduce框架是分布式系统领域经典中的经典，本文大致介绍了这一框架的设计。
---
<p>上了MIT 6.824之后发现这个MapReduce和Haskell里的<code>map</code>+<code>reduce</code>(fold)还是有点差别的，另外在运算过程上还有一些细节。</p>
 hexo-inject:begin  hexo-inject:end <h2 id="整个过程的输入与输出"><a class="headerlink" href="#整个过程的输入与输出" title="整个过程的输入与输出"></a>整个过程的输入与输出</h2><p>MapReduce中，整个过程的输入与输出都是一组键值对。</p>
<h2 id="map过程"><a class="headerlink" href="#map过程" title="map过程"></a>map过程</h2><p>由用户定义的map函数会应用到输入键值对中的每一个上。</p>
<p>map函数应当输出若干新的键值对。</p>
<p>即：</p>
<script type="math/tex; mode=display">
map\ (key, value) \rightarrow [(key',value')]</script><h2 id="reduce过程"><a class="headerlink" href="#reduce过程" title="reduce过程"></a>reduce过程</h2><p>由用户定义的reduce函数会应用到所有map函数的输出根据$key$ aggregate之后的结果上。</p>
<p>reduce函数应当输出一个值的集合，一般来讲这个值的集合应该比输入的值列表要小，在大多数情况下，这个集合应该只有零到一个元素。</p>
<p>即：</p>
<script type="math/tex; mode=display">
reduce (key, [value]) \rightarrow [value']</script><h2 id="例子：url-count"><a class="headerlink" href="#例子：url-count" title="例子：url count"></a>例子：url count</h2><p>计算每个<code>url</code>在一堆网页中被引用的次数。</p>
<p><code>map</code>: 接受<code>(网页内容, &lt;null&gt;)</code>作为参数，返回<code>[(url, count)]</code>。</p>
<p><code>reduce</code>: 接受<code>(url, [counts])</code>作为参数，返回<code>{sum(counts)}</code>。</p>
<hr/>
<p>如果只是用MapReduce的话，了解到这里已经足够了。</p>
<p>下面讲的东西牵扯到了Google MapReduce的实现细节。</p>
<h2 id="运算细节"><a class="headerlink" href="#运算细节" title="运算细节"></a>运算细节</h2><p><img alt="img" src="https://pic4.zhimg.com/80/v2-add6b28c0c1632fe764271b8ad7b14fb_1440w.jpg"/></p>
<p>整个MapReduce 计算的流程如下：</p>
<ol>
<li><p>作为输入的文件会被分为多个 Split，记为$M$个map任务，每个数据片段的大小一般从 16MB 到 64MB，可以手动指定</p>
</li>
<li><p>用户程序会创建/复用已存在的$Master$和$Worker$节点（往往不同的机器上）</p>
</li>
<li><p>$Master$节点为空闲的worker分配map和reduce任务</p>
</li>
<li><p>收到 Map 任务的 Worker（常称为Mapper）读入分配到的 Split</p>
<p>将读入的内容解析为输入键值对并调用由用户定义的 Map 函数。</p>
<p>由 Map 函数产生的中间结果键值被分成$R$个部分（$R$和划分规则由用户指定，常用划分规则是$hash(key)\ mod\ R$），对会被暂时存放在缓冲区内，并周期性地写入本地磁盘。</p>
</li>
<li><p>任务完成时，Mapper 将中间结果完全写入磁盘，并将其在本地磁盘上的存放位置报告给 Master，Master会将这个信息报告给Reducer</p>
</li>
<li><p>Reducer根据Master发来的位置信息从map所在节点的硬盘上远程读取这些数据，当读到了所有中间数据之后，会根据key进行排序，然后将同一个key对应的value aggregate起来</p>
</li>
<li><p>用key和value aggregate的结果调用Reduce函数，将结果写入输出文件，Reduce的结果文件存储在谷歌的分布式文件系统GFS上</p>
</li>
</ol>
<h3 id="容错"><a class="headerlink" href="#容错" title="容错"></a>容错</h3><p>MapReduce 可以容忍worker节点出现问题，在 MapReduce 集群中，Master 会周期性地 ping 每一个 Worker 。如果某个 Worker 在一段时间内没有响应，Master 就会认为这个 Worker 已经不可用。分配给这个Worker的任务会被重新分配。</p>
<p>注意做map任务的worker如果挂掉，说明其本地磁盘上的中间结果文件也不可用了，此时要通知所有等待该Worker map结果的Reducer，要改为从重新分配到的节点上取结果。<sup><a href="#fn_1" id="reffn_1">1</a></sup></p>
<p>MapReduce 容忍master节点出现问题的能力相对较弱，论文中的实现不允许master挂掉，如果master挂了，就认为整个 MapReduce 运算过程失败了。</p>
<p>如果挂的是reducer，由于结果存在GFS上，其可用性由GFS保证，故无需特别的容错处理。</p>

