---
title: You Only Look Once Unified, Real-Time Object Detection翻译
date: 2018-05-05 00:00:00
tags: [机器视觉, AI, 论文翻译]
mathjax: true
comments: true
summary: 我们介绍YOLO，一种新的物体检测方法。先前的物体检测工作重新设置分类器以执行检测。与之相反的，我们将对象检测框架化为空间分离边界框和相关类别概率的回归问题。单个神经网络在一次评估中直接从完整图像检测边界框和分类概率。由于整个检测流水线是单个网络，因此可以直接针对检测性能端到端地进行优化。我们的统一架构非常快。我...
---
<h2 id="介绍"><a class="headerlink" href="#介绍" title="介绍"></a>介绍</h2><p>我们人类瞥一眼图像，立即知道图像中的物体在哪里以及它们如何相互作用。人的视觉系统是快速而准确的，使我们能够执行复杂的任务，如驾驶时几乎没有明显的想法。快速，准确的目标检测算法可以让计算机在没有专用传感器的情况下驾驶汽车，使辅助设备能够向人类用户传达实时的场景信息，并释放通用目标响应机器人系统的潜力。<br/>目前的检测系统重新利用分类器进行检测。为了检测对象，这些系统为该对象提供一个分类器，并在不同的位置评估它，并在测试图像中进行缩放。像可变形零件模型（DPM）这样的系统使用滑动窗口方法，其中分类器在整个图像上均匀间隔的位置运行。<br/>最近的方法，如R-CNN使用区域建议方法首先在图像中生成潜在的边界框，然后在这些建议的框上运行分类器。分类后，接下来的处理用于细化边界框，消除重复检测，并根据场景中的其他对象重新设置框。这些复杂的方法速度很慢，难以优化，因为每个组件都必须单独进行训练。<br/>我们将物体检测作为一个单一的回归问题重建，直接从图像的像素计算出包围盒坐标和分类概率。使用我们的系统，您只需要在图像上看一次（YOLO，You Only Look Once），就能检测出现物体的位置。<br/>YOLO很简单：参见图1. 单个卷积网络同时预测这些盒子的多边界框和类概率。 YOLO在全图像上训练并直接优化检测性能。这种统一的模型与传统的物体检测方法相比有几个优点。<br/>首先，YOLO速度非常快。由于我们将检测视为回归问题，因此我们不需要复杂的管线。我们只是在测试的时候在一幅新图像上运行我们的神经网络来预测检测结果。我们的基础网络运行速度为每秒45帧，Titan X GPU上，没有进行批处理，而快速版本运行速度超过150 fps。这意味着我们可以在不到25毫秒的延迟时间内实时处理流媒体视频。此外，YOLO实现了其他实时系统平均精度的两倍以上。有关我们的系统在网络摄像头上实时运行的演示，请参阅我们的<a href="http://pjreddie.com/yolo/" rel="noopener" target="_blank">项目网页</a>。</p>
<p>其次，在进行预测时，YOLO会在全局范围内对图像进行预测。与基于滑动窗口和区域提议的技术不同，YOLO在训练和检测期间会看到整个图像，因此它隐含地编码关于类别的相关信息以及它们的外观。Fast R-CNN是一种很好的检测方法，但是因为它无法看到更大的上下文，因此将图像中的小块错误地映射为对象。与Fast R-CNN相比，YOLO的背景识别错误数量少了一半。<br/>第三，YOLO学习物体的一般化表示。在对自然图像进行训练并对艺术作品进行测试时，YOLO大幅优于DPM和R-CNN等检测方法。由于YOLO具有高度概括性，因此在应用于新领域或意外输入时不太可能发生故障。<br/>YOLO在准确性方面仍落后于最先进的检测系统。虽然它可以快速识别图像中的物体，但它仍需努力精确定位某些物体，尤其是小物体。我们在实验中进一步检查了这些折衷。<br/>我们所有的训练和测试代码都是开源的。各种预训练模型也可以下载。<img alt="Figure1" src="./Figure1.png"/></p>
<h2 id="统一检测"><a class="headerlink" href="#统一检测" title="统一检测"></a>统一检测</h2><p>我们将对象检测的单独组件集成到单个神经网络中。我们的网络使用整个图像的特征来预测每个边界框。它还同时预测所有类别的所有边界框。这意味着我们的网络全局性地关于图像中的全部图像和所有对象。 YOLO设计可实现端到端训练和实时级速度，同时保持较高的平均精度。<br/>我们的系统将输入图像划分为S×S网格。如果对象的中心落入网格单元格中，则该网格单元格负责检测该对象。<br/>每个网格单元预测$B$个边界框并计算其置信度。这些置信度分数反映了该模型对框内是否包含对象有多少信心，以及网格中物体就是其预测出的物体的准确度。形式上我们将信度系数定义为$Pr(Object) \times IOU^{truth}_{pred}$。如果该单元格中不存在对象，则置信度分数应为零。否则，我们希望信度系数等于预测框与确定对象之间的交并比（IOU）。<br/>每个边界框由5个预测值组成：$x,y,w,h$和置信度。 $(x,y)$坐标表示相对于网格单元边界的框的中心。宽度和高度是相对于整个图像预测的。最后，置信度预测表示预测框与任何确定对象框之间的IOU。<br/>每个网格单元还预测$C$个条件类别概率$Pr(Class_i|Object)$。 这些概率取决于包含对象的网格单元。 我们只预测每个网格单元的一组类别概率，而不管网格$B$的数量是多少。<br/>在测试时，我们将条件类概率和单个盒子置信度的预测值乘起来，</p>
<script type="math/tex; mode=display">
Pr(Class_i|Object)* Pr(Object)*IOU^{truth}_{pred} = Pr(Class_i)*IOU^{truth}_{pred}</script><p>这为我们提供了每个网格的每个类特定的置信度分数。这些分数编码该类出现在盒子中的概率以及预测盒子如何适合该对象。</p>
<p>为了在PASCAL VOC上评估YOLO，我们使用$S = 7,B = 2$。PASCAL VOC有20个标记类别，因此$C = 20$。我们的最终预测是$7\times7\times30$张量。<img alt="Figure2" src="./Figure2.png"/></p>
<h3 id="网络设计"><a class="headerlink" href="#网络设计" title="网络设计"></a>网络设计</h3><p>我们将此模型作为卷积神经网络实施并在PASCAL VOC检测数据集上进行评估。网络的初始卷积层从图像中提取特征，而全连接的层预测输出概率和坐标。<br/>我们的网络架构受到GoogLeNet图像分类模型的启发。我们的网络有24个卷积层，其次是2个全连接层。我们简单地使用$1\times 1$的缩减层，然后是$3\times 3$的卷积层，与Lin等人的相似，而不是GoogLeNet使用的inception模块。完整的网络如图3所示。<br/>我们还训练了一个快速版本的YOLO，旨在推动快速对象检测。Fast YOLO使用较少卷积层（9而不是24）的神经网络，并在这些层中使用较少的滤波器。除了网络规模之外，YOLO和Fast YOLO之间的所有训练和测试参数都相同。<br/>我们网络的最终输出是7×7×30的预测值的张量。</p>
<h3 id="训练"><a class="headerlink" href="#训练" title="训练"></a>训练</h3><p>我们在ImageNet 1000-class比赛的数据集上对卷积层进行预训练。对于预训练，我们使用图3中的前20个卷积层，接着一个平均池化层和一个全连接层。我们训练这个网络大约一个星期，并且在ImageNet 2012验证集上实现单一crop的前5个精度达到88%，这可与Caffe模型动物园中的GoogLeNet模型相匹敌。我们使用Darknet框架来进行所有训练和预测。<br/>然后，我们转换模型来执行检测。Ren等人表明，添加卷积和全连接层设到预训练网络都可以提高性能。根据他们的例子，我们添加了随机初始化权重的四个卷积层和两个全连接层。检测通常需要细粒度的视觉信息，因此我们将网络的输入分辨率从224×224增加到448×448。<br/>我们的最后一层预测分类概率和包围盒坐标。我们通过图像宽度和高度对包围盒宽度和高度进行归一化，使它们落在0和1之间。我们将包围盒$x$和$y$坐标参数化为特定网格单元位置的偏移，因此它们也落在0和1之间。<br/>我们的最后一层使用一个线性激活函数，所有其他层使用以下的修正线性激活函数：</p>
<script type="math/tex; mode=display">
\phi(x) = 
\left\{
\begin{aligned}
&x, &if\ x > 0 \\
& 0.1x, &otherwise
\end{aligned}
\right.</script><p>我们优化了模型输出中的平方和误差。我们使用平方和误差，因为它很容易优化，但它并不完全符合我们最大化平均精度的目标。它对分类错误和定位错误一视同仁，这可能并不理想。而且，在每个图像中，许多网格单元不包含任何对象。这将这些单元格的“置信度”分数推向零，通常压倒包含对象的单元格的梯度。这可能导致模型不稳定，从而导致训练早期发生偏离。<br/>为了弥补这一点，我们增加了边界框坐标预测的损失，并减少了不包含对象的框的置信度预测的损失。我们使用两个参数$\lambda_{coord}$和$\lambda_{noobj}$来实现这一点。我们设定$\lambda_{coord} = 5$和$\lambda_{noobj} = .5$。<br/>平方和误差也同样对大包围盒和小包围盒中的误差进行加权。我们的错误指标应该反映出，大包围盒的小偏差比小包围盒小很多。为了部分解决这个问题，我们预测边界框宽度和高度的平方根，而不是直接预测宽度和高度。<br/>YOLO预测每个网格单元的多个边界框。在训练时，我们只需要一个边界框预测器对每个对象负责。我们将一个预测器指定为“责任人”，以根据哪个预测具有当前IOU最高的IOU与基础事实来预测对象。这导致了边界框预测器之间的特化。每个预测器可以更好地预测某些尺寸，纵横比或对象类别，从而改善整体回忆。<br/>在训练期间，我们优化以下多部分损失函数：</p>
<script type="math/tex; mode=display">
\bold{\lambda_{coord}}\sum^{s^2}_{i=0}\sum^B_{j=0} \mathbb{I}^{obj}_{i\ j}[(x_i-\hat {x_i})^2+(y_i-\hat {y_i})^2] \\
+\bold{\lambda_{coord}}\sum^{s^2}_{i=0}\sum^B_{j=0} \mathbb{I}^{obj}_{i\ j}[(\sqrt w_i-\sqrt {\hat {w_i}})^2+(\sqrt h_i-\sqrt {\hat {h_i}})^2] \\
+ \sum^{s^2}_{i=0}\sum^B_{j=0} \mathbb{I}^{obj}_{i\ j}(C_i-\hat C_i)^2 \\
+ \lambda_{noobj} \sum^{s^2}_{i=0}\sum^B_{j=0} \mathbb{I}^{noobj}_{i\ j}(C_i-\hat C_i)^2 \\
+ \sum^{s^2}_{i=0} \mathbb{I}^{obj}_i \sum _{c \in classes} (p_i(c)-\hat p_i(c))^2</script><p>其中$\mathbb{I}^{obj}_i$表示物体是否出现在网格$i$中，$\mathbb{I}^{obj}_{i\ j}$表示网格$i$中的第$j$个边界框检测器“负责”这个检测。</p>
<p>请注意，仅在对象存在于该网格单元中时损失函数才会惩罚分类错误（因此前面讨论了条件类别概率）。如果该预测器对确定对象的包围盒“负责”（即，具有该网格单元中所有预测器的最高IOU），则它也仅惩罚边界框坐标错误。<br/>我们使用PASCAL VOC 2007 和 2012训练和验证数据对网络进行了大约135个epoch的训练。在2012年进行测试时，我们还包括了VOC 2007测试数据。在整个训练过程中，我们使用64的batch size，0.9的动量和0.0005的decay。<br/>我们的学习率计划如下：对于第一个epoch，我们慢慢地将学习率从$10^{-3}$提高到$10^{-2}$。如果我们从高学习率开始，我们的模型通常会由于不稳定的梯度而发散。我们继续以$10^{-3}$进行75个epoch的训练，然后以$10^{-3}$进行30个epoch的训练，最后以$10^{-4}$进行30个epoch的训练。<br/>为避免过拟合，我们使用dropout和扩展的的数据增强。在第一个连接层之后，速率为$.5$的dropout层防止了层之间的协调。对于数据增强，我们引入随机缩放和移动，规模高达20％的原始图像大小。我们还在HSV色彩空间中随机调整图像的曝光和饱和度达1.5倍。</p>
<h3 id="推断"><a class="headerlink" href="#推断" title="推断"></a>推断</h3><p>就像在训练中一样，预测测试图像只需要跑一次网络。 对于PASCAL VOC，网络预测每个图像的98个边界框和每个框的类别概率。 YOLO在测试时速度非常快，因为它与分类器方法不同，只需要一次网络评估。<br/>网格设计强化了边界框预测中的空间多样性。 通常很清楚一个对象所在的网格单元是什么，网络仅为每个对象预测一个包围盒。 但是，多个网格的边界附近的一些大对象或物体可以很好地被多个网格定位。 非最大抑制可用于修复这些多重检测。 相对于R-CNN或DPM而言，这一操作对性能来说不是至关重要的，非最大抑制可以增加2-3％的mAP。</p>
<h3 id="YOLO的局限性"><a class="headerlink" href="#YOLO的局限性" title="YOLO的局限性"></a>YOLO的局限性</h3><p>YOLO对边界框预测强加空间约束，因为每个网格单元只能预测两个方框，并且只能有一个分类。 这种空间约束限制了我们的模型可以预测的相邻物体的数量。 我们的模型在大群出现的小物体（例如鸟群）上表现不太好。<br/>由于我们的模型学习从数据中预测边界框，因此它很难将对象推广到新的或不寻常的高宽比或配置。 我们的模型还使用相对粗糙的特征来预测边界框，因为我们的体系结构具有来自输入图像的多个下采样层。<br/>最后，当我们训练一个接近检测性能的损失函数时，我们的损失函数认为小包围盒与大包围盒的误差相同。 大包围盒中的小错误通常是良性的，但小包围盒中的小错误对IOU有更大的影响。 我们的主要错误来源是不正确的定位。</p>
<h3 id="和其他检测系统相比较"><a class="headerlink" href="#和其他检测系统相比较" title="和其他检测系统相比较"></a>和其他检测系统相比较</h3><p>目标检测是计算机视觉中的核心问题。 检测流水线通常从输入图像（Haar，SIFT，HOG，卷积特征）中提取一组鲁棒的特征开始。 然后使用分类器或定位器来标识特征空间中的对象。 这些分类器或定位器可以在整个图像上以滑动窗口方式运行，也可以在图像中某些区域的子集上运行。 我们将YOLO检测系统与几种顶级检测框架进行比较，突出了关键的相似性和差异。</p>
<p>（不太重要，暂时略）</p>
<h2 id="实验"><a class="headerlink" href="#实验" title="实验"></a>实验</h2><p>首先，我们将YOLO与PASCAL VOC 2007上的其他实时检测系统进行比较。要了解YOLO和R-CNN变体之间的差异，我们将探索由YOLO和Fast R-CNN（R-CNN的最高效的版本之一）制作的VOC 2007的错误。 根据不同的错误分析，我们展示YOLO可用于重新获得Fast R-CNN检测结果，并减少背景误报带来的错误，从而显着提升性能。 我们还展示了VOC 2012的结果，并将mAP与当前最先进的方法进行比较。 最后，我们在两个艺术数据集上显示了YOLO上的其他检测器能更好地推广到新领域。</p>
<h3 id="和其他检测系统相比较-1"><a class="headerlink" href="#和其他检测系统相比较-1" title="和其他检测系统相比较"></a>和其他检测系统相比较</h3><p>（不太重要，略）</p>
<h3 id="VOC-2007-错误分析"><a class="headerlink" href="#VOC-2007-错误分析" title="VOC 2007 错误分析"></a>VOC 2007 错误分析</h3><p>为了进一步检查YOLO和当前其他最先进的检测器之间的差异，我们详细分析了VOC 2007的结果。我们将YOLO与Fast R-CNN进行比较，因为Fast R-CNN在PASCAL数据集上是表现最好的探测器之一，它的检测结果是公开可用的。<br/>我们使用Hoiem等人的方法和工具。 对于测试时每个类别，我们查看该类别的前N个预测。 每个预测要么是正确的，要么就要根据错误的类型进行分类：</p>
<ul>
<li>正确：正确的分类和且$IOU&gt; .5$</li>
<li>定位：正确的分类，$.1 &lt;IOU &lt;.5$</li>
<li>类似：分类类似，$IOU&gt; .1$</li>
<li>其他：分类错误，$IOU&gt; .1$</li>
<li>背景：对任何对象的$IOU &lt;.1$</li>
</ul>
<p>图4显示了所有20个类别的平均错误类型的细分。</p>
<p>YOLO在正确地定位对象上有一点困难。 定位错误占所有YOLO错误的总和超过所有其他来源的总和。Fast R-CNN的定位错误更少，但背景错误更多。 其最高检测结果中有13.6％是误报，不包含任何对象。 Fast R-CNN错误地检测背景的可能性比YOLO高3倍。<img alt="Figure4" src="./Figure4.png"/></p>
<h3 id="结合R-CNN和YOLO"><a class="headerlink" href="#结合R-CNN和YOLO" title="结合R-CNN和YOLO"></a>结合R-CNN和YOLO</h3><p>YOLO出的背景错误比Fast R-CNN少得多。通过使用YOLO消除Fast R-CNN的背景检测，我们获得了显着的表现提升。对于R-CNN预测的每个边界框，我们检查YOLO是否预测了一个类似的框。如果确实如此，那么我们会根据YOLO预测的概率和两个框之间的重叠情况给出预测。<br/>最好的Fast R-CNN模型在VOC 2007测试集中达到了71.8％的mAP。当与YOLO合并时，其mAP增加了3.2％至75.0％。我们还尝试将顶级Fast R-CNN模型与其他几个版本的Fast R-CNN相结合。这些结合的平均增幅在0.3％至0.6％之间，详情见表2。</p>
<div class="table-container">
<table>
<thead>
<tr>
<th></th>
<th>mAP</th>
<th>结合</th>
<th>增益</th>
</tr>
</thead>
<tbody>
<tr>
<td>Fast R-CNN</td>
<td>71.8</td>
<td>-</td>
<td>-</td>
</tr>
<tr>
<td>Fast R-CNN（2007数据集）</td>
<td>66.9</td>
<td>72.4</td>
<td>.6</td>
</tr>
<tr>
<td>Fast R-CNN（VGG-M）</td>
<td>59.2</td>
<td>72.4</td>
<td>.6</td>
</tr>
<tr>
<td>Fast R-CNN（CaffeNet）</td>
<td>57.1</td>
<td>72.1</td>
<td>.3</td>
</tr>
<tr>
<td>YOLO</td>
<td>63.4</td>
<td>75.1</td>
<td>3.2</td>
</tr>
</tbody>
</table>
</div>
<p>来自YOLO的增益不仅仅是模型集成的副产品，因为组合不同版本的Fast R-CNN几乎没有什么增益。相反，正是因为YOLO在测试时间出现了各种各样的错误，所以它在提高Fast R-CNN的性能方面非常有效。<br/>不幸的是，这种组合不会从YOLO的速度中受益，因为我们分别运行每个模型，然后合并结果。但是，由于YOLO速度如此之快，与Fast R-CNN相比，它不会增加任何显著的计算时间。</p>
<h3 id="VOC-2012-结果"><a class="headerlink" href="#VOC-2012-结果" title="VOC 2012 结果"></a>VOC 2012 结果</h3><p>在VOC 2012测试集中，YOLO评分为57.9％。 这比现有技术水平低，更接近使用VGG-16的最初的R-CNN，见表3。与其最接近的竞争对手相比，我们的系统在小物体上表现不佳。 在<em>瓶子</em>，<em>羊</em>，<em>电视/显示器</em>等类别上，YOLO得分比R-CNN或Feature Edit低8-10％。 然而，在其他类别，如<em>猫</em>和<em>火车</em>，YOLO的表现更好。<br/>我们将Fast R-CNN和YOLO组合的模型是性能最高的检测方法之一。 Fast R-CNN与YOLO的组合提高了2.3％，在公共排行榜上提升了5个位置。<img alt="Table3" src="./Table3.png"/></p>
<h3 id="泛化：在艺术品中的人物识别"><a class="headerlink" href="#泛化：在艺术品中的人物识别" title="泛化：在艺术品中的人物识别"></a>泛化：在艺术品中的人物识别</h3><p>用于对象检测的学术数据集从同一分布中绘制训练和测试数据。在现实世界的应用中，很难预测所有可能的用例，并且测试数据可能与系统已经看到的不同。我们将YOLO与毕加索数据集和人物艺术数据集中的其他检测系统进行了比较，这两个数据集用于在艺术品检测人物。<br/>图5显示了YOLO和其他检测方法之间的比较性能。作为参考，我们用VOC 2007数据训练所有的模型，并给出了<em>人</em>的检测AP。毕加索上的模型接受VOC 2012训练，而People-Art上的则接受VOC 2010培训。<br/>R-CNN在VOC 2007上有很高的AP值。然而，当应用于艺术品时，R-CNN的表现显著下降。 R-CNN使用选择性搜索来调整自然图像的边界框提案。 R-CNN中的分类器步骤只能看到小区域，需要很好的提案。<br/>DPM在应用于艺术品时可以很好地维护其AP。之前的工作认为DPM表现良好，因为它具有强大的物体形状和布局空间模型。虽然DPM不会像R-CNN那样退化，但从一开始就只有较低的AP。<br/>YOLO在VOC 2007上表现很好，将它运用于艺术作品带来的AP下降也比其他方法少。与DPM一样，YOLO对对象的大小和形状，以及对象之间的关系和对象通常出现的位置之间的关系建模。艺术品和自然图像在像素级别上有很大不同，但它们在物体的大小和形状方面相似，因此YOLO仍然可以预测出边界框和检测结果。</p>
<h2 id="在实际环境下的实时检测"><a class="headerlink" href="#在实际环境下的实时检测" title="在实际环境下的实时检测"></a>在实际环境下的实时检测</h2><p>YOLO是一款快速，精确的物体检测器，非常适合计算机视觉应用。 我们将YOLO连接到网络摄像头，并验证它是否保持实时性能，包括从摄像头获取图像并显示检测结果的时间。<br/>由此产生的系统是互动的和参与的。 虽然YOLO单独处理图像，但当连接到网络摄像头时，它的功能类似于跟踪系统，可在对象移动并在外观上发生变化时检测对象。 系统演示和源代码可在<a href="https://pjreddie.com/yolo/" rel="noopener" target="_blank">我们的项目网站</a>上找到。</p>
<h2 id="结论"><a class="headerlink" href="#结论" title="结论"></a>结论</h2><p>我们介绍YOLO，一种用于物体检测的统一模型。 我们的模型构造简单，可以直接在完整的图像上训练。 与基于分类器的方法不同，YOLO是通过与检测性能直接对应的损失函数进行训练的，并且整个模型是一起训练的。<br/>快速YOLO是文献中最快的通用对象检测器，YOLO推动实时对象检测的最新技术。 YOLO还很好地推广到新领域，使其成为依赖于快速，强大物体检测的应用的理想选择。</p>

