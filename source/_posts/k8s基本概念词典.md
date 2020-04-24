---
title: k8s基本概念词典
date: 2019-12-24 00:00:00
tags: [k8s]
mathjax: true
comments: true
summary: k8s里的概念茫茫多，其实我熟的也就是node、deployment、pod和service这几个最常用的……搞个速查表不用每次都翻文档好了。
---
<h2 id="API-Server"><a class="headerlink" href="#API-Server" title="API Server"></a>API Server</h2><p>我理解就是可以由此调用k8s的restful API来观察和管理各个集群里的各个对象。</p>
 hexo-inject:begin  hexo-inject:end <h2 id="Pod"><a class="headerlink" href="#Pod" title="Pod"></a>Pod</h2><p>一般来说，pod就是一个在跑的容器。</p>
<p>当然也有一些情况是一个Pod内部署多个相关的容器的，比如多个容器需要共享一个存储 Volume 或者一个 IP 地址</p>
<h2 id="Service"><a class="headerlink" href="#Service" title="Service"></a>Service</h2><p>简单来说就是给一组Pod在集群内一个稳定（不随着Pod消亡/创建/修改而变化）的IP，并在这组Pod间进行负载均衡。</p>
<h2 id="Controller"><a class="headerlink" href="#Controller" title="Controller"></a>Controller</h2><p>Controller负责监视集群状态，然后推动集群往“用户想要的状态”变化，每种资源都对应一种controller。</p>
<h2 id="kube-controller-manager"><a class="headerlink" href="#kube-controller-manager" title="kube-controller-manager"></a>kube-controller-manager</h2><p>所有Controller的总称。</p>
<h2 id="kubelet"><a class="headerlink" href="#kubelet" title="kubelet"></a>kubelet</h2><p>一个在每个node上都运行的worker，定期获取node要部署的pod的期望状态，并调用对应的容器平台接口达到这个状态。</p>
<h2 id="kube-proxy"><a class="headerlink" href="#kube-proxy" title="kube-proxy"></a>kube-proxy</h2><p>Service到Pod的转发与负载均衡的实现。（在现在的k8s实现下，在Service和对应endpoint更新时，这玩意在会动iptables来实现目的）</p>
<h2 id="Job"><a class="headerlink" href="#Job" title="Job"></a>Job</h2><p>简单来说就是用来创建一个Pod，跑一次，然后结束。</p>
<h2 id="Cron-Job"><a class="headerlink" href="#Cron-Job" title="Cron Job"></a>Cron Job</h2><p>创建在给定时间点运行一次的pod，或是周期性创建并运行Pod。</p>
<h2 id="Replication-Controller"><a class="headerlink" href="#Replication-Controller" title="Replication Controller"></a>Replication Controller</h2><p>一种用来保证有一定量的Pod副本个数在同时运行的Controller，注意这个Controller只能负责Pod的<strong>个数<sup><a href="#fn_1" id="reffn_1">1</a></sup></strong>。</p>
<p>现在已经不推荐使用，建议使用Deployment。</p>
<h2 id="ReplicaSet"><a class="headerlink" href="#ReplicaSet" title="ReplicaSet"></a>ReplicaSet</h2><p>改进版的Replication Controller，改进了选择器支持（我是没看出有多大变化）。</p>
<h2 id="Deployment"><a class="headerlink" href="#Deployment" title="Deployment"></a>Deployment</h2><p>用于创建和管理Pod和对应ReplicaSet，经由Deployment，我们既可以更新pod族对应的镜像，也可以对其副本数进行伸缩。</p>
<h2 id="StatefulSet"><a class="headerlink" href="#StatefulSet" title="StatefulSet"></a>StatefulSet</h2><p>StatefulSet是“有状态的Deployment”。</p>
<p>其应用场景包括：</p>
<ul>
<li>Pod重新调度后还要访问到相同的持久化数据（需要使用PVC实现）</li>
<li>Pod重新调度后其PodName和HostName不变（需要一个Headless Service实现）</li>
<li>Pod的有序性（前面的pod准备好了，后面的pod才能开始运行，后面的pod销毁了，才能销毁前面的pod）</li>
</ul>
<p>从我自己的角度来看，就是将Deployment的“随机ID”改为了“自增ID”，并利用这个ID做一些将Pod和PVC绑定等等的事情。</p>
<h2 id="DaemonSet"><a class="headerlink" href="#DaemonSet" title="DaemonSet"></a>DaemonSet</h2><p>DaemonSet 确保全部（或者某些）节点上运行一个 Pod 的副本。</p>
<p>就是每个符合条件的节点都给他绑一个Pod。</p>
<h2 id="Volume"><a class="headerlink" href="#Volume" title="Volume"></a>Volume</h2><p>持久卷，不随着Pod移除而被移除的存储卷。</p>
<h2 id="PersistentVolume"><a class="headerlink" href="#PersistentVolume" title="PersistentVolume"></a>PersistentVolume</h2><p>由集群管理员事先创建好的存储卷。</p>
<h2 id="PersistentVolumeClaim"><a class="headerlink" href="#PersistentVolumeClaim" title="PersistentVolumeClaim"></a>PersistentVolumeClaim</h2><p>用户对<code>PersistentVolume</code>的使用请求，用户只需告知k8s自己需要什么样的存储卷，k8s会自动从创建好的PV中挑选合适的，给用户使用。</p>
<h2 id="Storage-Class"><a class="headerlink" href="#Storage-Class" title="Storage Class"></a>Storage Class</h2><blockquote>
<p><code>StorageClass</code> 为管理员提供了描述存储 <code>"类"</code> 的方法。 不同的<code>类型</code>可能会映射到不同的服务质量等级或备份策略，或是由群集管理员制定的任意策略。</p>
</blockquote>
<p>就是某种符合某些条件的卷归为的一类。</p>
<h2 id="Ingress"><a class="headerlink" href="#Ingress" title="Ingress"></a>Ingress</h2><p>用于管理对集群中服务的外部访问。</p>
<h2 id="ConfigMap"><a class="headerlink" href="#ConfigMap" title="ConfigMap"></a>ConfigMap</h2><p>不加密版本的Secret。</p>
<h2 id="Operator"><a class="headerlink" href="#Operator" title="Operator"></a>Operator</h2><p>k8s的用户扩展。</p>
<p>将k8s的资源和Controller的概念扩展到特定的应用程序上，使得对这些应用程序的“资源”的管理也能像对k8s的原生资源管理那样进行。</p>
<h2 id="服务网格"><a class="headerlink" href="#服务网格" title="服务网格"></a>服务网格</h2><p>服务间通信的基础设施层。</p>
<blockquote>
<p>服务网格（Service Mesh）是致力于解决服务间通讯的基础设施层。它负责在现代云原生应用程序的复杂服务拓扑来可靠地传递请求。实际上，Service Mesh 通常是通过一组轻量级网络代理（Sidecar proxy），与应用程序代码部署在一起来实现，而无需感知应用程序本身。—— <a href="https://twitter.com/wm" rel="noopener" target="_blank">Willian Morgan</a></p>
</blockquote>
<p><img alt="Service Mesh 架构图" src="https://jimmysong.io/blog/what-is-a-service-mesh/service-mesh-arch.png"/></p>
<p>Service Mesh 作为 sidecar 运行，对应用程序来说是透明的，所有应用程序间的流量都会通过它，所以对应用程序流量的控制都可以在 serivce mesh 中实现。</p>
<h2 id="Istio"><a class="headerlink" href="#Istio" title="Istio"></a>Istio</h2><p>一个服务网格的实现。</p>
<h3 id="Envoy"><a class="headerlink" href="#Envoy" title="Envoy"></a>Envoy</h3><blockquote>
<p>Envoy 是用 C++ 开发的高性能代理，用于协调服务网格中所有服务的入站和出站流量。</p>
</blockquote>
<p>就一proxy。</p>
<h3 id="虚拟服务"><a class="headerlink" href="#虚拟服务" title="虚拟服务"></a>虚拟服务</h3><blockquote>
<p>使用虚拟服务，您可以为一个或多个主机名指定流量行为。在虚拟服务中使用路由规则，告诉 Envoy 如何发送虚拟服务的流量到适当的目标。路由目标地址可以是同一服务的不同版本，也可以是完全不同的服务。</p>
</blockquote>
<p>就是一个“更容易配置”的，从URL到一个服务集合的proxy。</p>
<h3 id="目标规则"><a class="headerlink" href="#目标规则" title="目标规则"></a>目标规则</h3><p>目标规则将应用于流量的“真实”目标地址。</p>
<p>就是从服务集合到单个服务的proxy。</p>
<blockquote id="fn_1">
<sup>1</sup>. 讲道理根据我在上海大学开源社区的运维经验来看，更新镜像的场景比改变副本数的场景多多了……究其原因还是社区又穷服务又没人用，什么动态缩扩容什么的功能根本没用 TAT。<a href="#reffn_1" title="Jump back to footnote [1] in the text."> ↩</a>
</blockquote>

