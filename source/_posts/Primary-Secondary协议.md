---
title: Primary-Secondary协议
date: 2020-04-23 00:00:00
tags: [分布式系统]
mathjax: true
comments: true
summary: Primary-Secondary协议是一种强中心化的副本控制协议。GFS就是这种类型的副本控制类型的用户。
---
<blockquote>
<!-- hexo-inject:begin --><!-- hexo-inject:end --><p>在 primary-secondary 类型的协议中，副本被分为两大类，其中有且仅有一个副本作为 primary 副本， 除 primary 以外的副本都作为 secondary 副本。维护 primary 副本的节点作为中心节点，中心节点负责维护数据的更新、并发控制、协调副本的一致性。</p>
</blockquote>
<p>就是像这样的，有个Master节点负责协调谁是Primary，谁是Secondary。</p>
<p><img alt="PrimarySecondary" src="./PrimarySecondary.png"/></p>
<h2 id="写"><a class="headerlink" href="#写" title="写"></a>写</h2><p>写入时：</p>
<ul>
<li>先通过某种方式（一般是询问Master）获取数据Primary副本的位置</li>
<li>向Primary写入数据</li>
<li>Primary向自己对应的Secondary写数据</li>
<li>Primary根据Secondary的返回情况，返回是否写入成功</li>
</ul>
<p><img alt="PrimarySecondary-write" src="./PrimarySecondary-write.png"/></p>
<p>其中Primary负责决定多个请求同时到来时写入数据的顺序。</p>
<h2 id="读"><a class="headerlink" href="#读" title="读"></a>读</h2><p>读取时，可以：</p>
<ul>
<li>从任意副本读，只保证最终一致性</li>
<li>只从Primary读，保证强一致性，但增大Primary的压力<ul>
<li>为了解决这个问题，可以把不同数据的Primary分布到不同节点上，如果数据访问是均匀的，那么压力也是平均分配到多个节点上的。</li>
</ul>
</li>
<li>写入时，如果某个Secondary没有返回写入成功，则将这个Secondary标记为不可用，这降低了系统的可用性（A）。<ul>
<li>实践中，常常将可用的Secondary是否可用存储在某个中心服务器上，如Master或Primary上，读取时需要先请求哪个Secondary可用。</li>
</ul>
</li>
</ul>
<h2 id="Primary的确定与切换"><a class="headerlink" href="#Primary的确定与切换" title="Primary的确定与切换"></a>Primary的确定与切换</h2><p>一开始的Primary比较容易确定：Master钦定一个就好了，用哈希用轮换都可以。</p>
<p>关键问题在于Primary挂掉<sup><a href="#fn_1" id="reffn_1">1</a></sup>的时候如何切换到新的Primary。</p>
<p>如何确定Primary挂掉，请见下面的Lease机制等章节。</p>
<p>如何在Primary挂掉时选择新的Primary，请见下面的Quorum 机制等章节。</p>
<h2 id="Secondary的故障恢复"><a class="headerlink" href="#Secondary的故障恢复" title="Secondary的故障恢复"></a>Secondary的故障恢复</h2><p>最简单粗暴的方法是直接当作新的节点，全部从Primary拷贝一份。</p>
<p>另外可以通过日志技术，回放Primary的操作日志中最后一次checkpoint之后的部分就可以达到相同的状态。</p>
<p>对于脏数据，即在本Secondary上已经写入成功，但Primary决定向用户返回写入失败，此时留在本机上的数据是无效的，这就是脏数据。解决这一问题的一个方法是让Primary告知Secondary是否写入成功，一定时间内如果没有告知或被告知写入失败，就丢弃这份数据，当然也有部分产品不对脏数据进行处理。</p>

