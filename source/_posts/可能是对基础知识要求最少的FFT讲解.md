---
title: 可能是对基础知识要求最少的FFT讲解
date: 2017-10-20 00:00:00
tags: [算法, 数学]
mathjax: true
comments: true
summary: 快速傅立叶变换算法的详细讲解，及其在高精度乘法上的运用
---
<p>​    在不久，或是很久之前，我们做了一道高精度乘法的题目，这倒霉的题非常的不适合我们这些萌新。虽说压位可以过，但对时间系数上的优化并不王道，而分治算法代码的复杂度并不亚于FFT，时间复杂度上的优化也不多，故仍然不王道。</p>
    <p>​    然而看到FFT，快速傅立叶变换，这样一个名字总是令人觉得它十分的高大上而敬而远之。我以前也是由于这个原因拒绝学习这个方法，直到某一天，我突然不知为何（也许和我突然决定学习编程那一天一样，是耳畔响起了上帝的低语）特别想了解一下这个玄而又玄的算法。</p>
<p>​    翻开《算导》一看才知道，如果只是想要大概了解这个方法做了什么一个事情，大概是个什么原理，而非对其进行严格的数学证明，其实不需要什么高深的数学知识。</p>
<p>​    故作此篇，以用一种比较容易理解（只要求高中数学知识）的方式来解释FFT，兼以备忘。</p>
<h2 id="多项式的点值表示"><a class="headerlink" href="#多项式的点值表示" title="多项式的点值表示"></a>多项式的点值表示</h2><p>​    我们先来看看这样一个问题，现在有一些如下形式的多项式函数：</p>
<script type="math/tex; mode=display">
f(x) = a_0+a_1x+a_2x^2+...+a_{n-1}x^{n-1}</script><p>​    如果要把这样一个多项式函数保存在计算机中，可以采取什么样的方法呢？</p>
<p>​    最容易想到的是储存所有的系数：</p>
<script type="math/tex; mode=display">
\{a_0,a_1,a_2,...,a_{n-1}\}</script><p>​    但是也有另外一种方法可以储存，让我们借助几个例子来理解：</p>
<p>​    假如我们令：$\ n=2,a_0 = 1,a_1 = 2 ​$</p>
<p>​    那么就有：</p>
<script type="math/tex; mode=display">
f(x) = 1+2x</script><p>​    这显然是一条直线：<img alt="2x+1" src="./2x+1.png"/></p>
<p>而我们当然知道，这条直线可以用两点来确定，例如选取：$\ \{(0,1),(1,3)\} $</p>
<p>这两点，就能唯一确定这个多项式了。</p>
<p>如果你还记得初中学习的二次函数的内容的话，你就会想起一个二次函数能被三个点唯一确定。<img alt="0+2x-x^2" src="./0+2x-x^2.png"/></p>
<p>例如这幅图就告诉我们，可以用：$\ \{(0,0),(1,1),(2,0)\} $</p>
<p>这三个点唯一确定一个二次函数：$\ f(x)=0+2x-x^2 $</p>
<p>推而广之，<strong>对于更高次的函数，我们也可以用它图像上的几个点来唯一确定它</strong>，即：</p>
<p><strong>对于任意一个多项式函数：</strong></p>
<script type="math/tex; mode=display">
f(x) = a_0+a_1x+a_2x^2+...+a_{n-1}x^{n-1}</script><p><strong>我们都可以将其表示为：</strong></p>
<script type="math/tex; mode=display">
\{(x_0,f(x_0)),(x_1,f(x_1)),...,(x_{n-1},f(x_{n-1})\}</script><p>这就是多项式的点值表示。</p>
<p><strong>注意此处的$x_0～x_{n-1} $可以在$f(x)$的定义域内任意选择，所以一个多项式的点值表示并不唯一。</strong></p>
<p>那么这样表示有什么好处呢？</p>
<p>我们来看一个问题：如何将两个多项式相乘：</p>
<script type="math/tex; mode=display">
f_1(x) =3+2x+x^2\\
f_2(x)=5+3x+2x^2\\
F(x) = f_1(x) * f_2(x) = ?</script><p>如果我们用多项式的系数表示来计算：</p>
<script type="math/tex; mode=display">
A_1=\{3,2,1\} \\
A_2 = \{5,3,2\} \\ 
\begin{split}
A &= \{5*3,3*3+2*5,3*2+5*1+2*3,2*2+1*3,1*2\}\\
& =\{15,19,17,7,2\}
\end{split}
\\
F(x)=15+19x+17x^2+7x^3+2x^4</script><p>可见需要9次乘法，所以这是一个$O(n^2)$算法。</p>
<p>而在点值表示下（注意$P_1 $和$P_2 $的取的点的个数要根据确定结果所需点的个数来定）：</p>
<script type="math/tex; mode=display">
P_1=\{(0,3),(1,6),(2,11),(3,18),(4,27)\} \\
P_2=\{(0,5),(1,10),(2,19),(3,32),(4,49)\} \\
P = \{(0,15),(1,60),(2,209),(3,576),(4,1323)\}</script><p>可见只用到了5次乘法（计算方法是$n_1 + n_2 - 1$，$n_1$、$n_2$分别是$P_1$、$P_2$的次数），这是一个$O(n)$算法。</p>
<p>同理除法也可以做类似的优化。</p>
<p>加法和减法在点值表示下也能用类似的方法完成，不过此时相对于系数表示并无优势。</p>
<p><strong>在进行多项式的乘除法的时候，多项式的点值表示比起系数表示效率高得多。</strong></p>
<p>然而有一个问题，多项式的点值表示进行运算虽然方便，但得到的结果仍然是点值表示的多项式，点值表示并不能像系数表示那样方便地转化为我们人平常容易阅读的形式（你总不会在初中数学卷子上让你求二次函数的地方写上一个“过点$\ (0,0),(1,1),(2,0) $的函数”吧？），而且输入一个多项式时一般会输入系数表示而非点值表示。所以我们就要找到一种方法，可以方便快速地完成点值表示和系数表示之间的互相转化，and it is what FFT(DFT&amp;IDFT) does！</p>
<h2 id="离散傅立叶变换（DFT）"><a class="headerlink" href="#离散傅立叶变换（DFT）" title="离散傅立叶变换（DFT）"></a>离散傅立叶变换（DFT）</h2><p>我们先来完成将一个多项式的系数表示转化为点值表示这件事，这件事的名称叫离散傅立叶变换（DFT）。</p>
<p>乍一看这是件很简单的事，只需要任意选取$n$个$x$将其带入式子直接求出$y$即可，然而这就需要把$n$个$x$对式子里的每一项都进行一次乘法，这就需要$O(n^2)$的时间复杂度。</p>
<p>然而，通过仔细地选取这些$x$的值，我们可以将这个时间复杂度降到$O(nlgn)$，怎么做？</p>
<p>现在准备好你的脑子<del>（我自己都不够用，所以不发脑子，请你们自己带好</del>），我们需要一点点数学知识了，不过别担心，这些数学知识绝对没有难到你不能理解。</p>
<h2 id="Something-About-Math"><a class="headerlink" href="#Something-About-Math" title="Something About Math"></a>Something About Math</h2><h3 id="世间最美的公式"><a class="headerlink" href="#世间最美的公式" title="世间最美的公式"></a>世间最美的公式</h3><script type="math/tex; mode=display">
e^{i\pi}+1=0</script><p>这就是大名鼎鼎的欧拉公式，它把人类数学史上的数个重大发现：有理数（$1$和$0$，这两个恰好是最重要的有理数）、无理数（$e$和$\pi$）、复数（$i$）用最基本的加法和等号联系在了一起，高斯曾经说过：</p>
<blockquote>
<p>一个人第一次看到这个公式而不感到它的美丽，他不可能成为数学家。</p>
</blockquote>
<p>（也许我不能成为数学家了……，第一次看到这个公式连$i$是啥都不知道）</p>
<p>由于说好了不说难以理解的数学知识，在此不对这个公式进行证明。<del>其实是我不会证（好吧，泰勒展开可证）。</del></p>
<h3 id="n-次单位复根"><a class="headerlink" href="#n-次单位复根" title="$n$次单位复根"></a>$n$次单位复根</h3><p>$n$次单位复根是指满足方程$z^n = 1$的所有复数。</p>
<p>根据上面的欧拉公式，显然有：</p>
<script type="math/tex; mode=display">
z=(-1)^{2\cdot\frac k n}=(e^{i\pi})^{2\cdot \frac k n}=e^{\frac{2i\pi} nk},k=0,1,2,...,n-1</script><p>我们将$e^{\frac{2i\pi} nk}$这一个$n$次单位复根记为$\omega^k_n$。</p>
<p>将$\omega^k_n$扩展到所有整数上，有：</p>
<script type="math/tex; mode=display">
\omega^k_n=\omega^{k+m\cdot n}_n,m\in Z</script><p>例如，方程$z^3=1$，根据我们高中学习过的立方虚根，可以得到：</p>
<script type="math/tex; mode=display">
z_1=1=e^{\frac{2i\pi} 3*0}\\
z_2 = -\frac1 2 +\frac {\sqrt{3}} 2 i=(-1)^{\frac 2 3}=e^{\frac{2i\pi} 3*1}\\
z_3 = -\frac1 2 -\frac {\sqrt{3}} 2 i=(-1)^{\frac 4 3}=e^{\frac{2i\pi} 3*2}</script><p>即：</p>
<script type="math/tex; mode=display">
\omega^0_3=1 \\
\omega^1_3= -\frac1 2 +\frac {\sqrt{3}} 2 i=e^{\frac{2i\pi} 3*1} \\
\omega^2_3=-\frac1 2 -\frac {\sqrt{3}} 2 i=e^{\frac{2i\pi} 3*2}</script><p>再例如，$z^4=1$：</p>
<script type="math/tex; mode=display">
\omega^0_4=1 = e^{\frac{2i\pi} 4*0}\\
\omega^1_4= i=e^{\frac{2i\pi} 4*1} \\
\omega^2_4=-1=e^{\frac{2i\pi} 4*2} \\
\omega^3_4=-i=e^{\frac{2i\pi} 4*3}</script><p>参考上面的例子，我们可以发现，对于偶次单位复根，有性质：</p>
<script type="math/tex; mode=display">
\omega^k_n=-\omega^{k+\frac n 2}_n\\ and\\
(\omega^k_n)^2=(\omega^{k+\frac n 2}_n)^2\\
k=0,1,2,…,\frac n 2-1</script><p><strong>即，对于每一个偶次单位复根，总有另一个单位复根和它互为相反数。</strong></p>
<h2 id="快速傅立叶变换（FFT）"><a class="headerlink" href="#快速傅立叶变换（FFT）" title="快速傅立叶变换（FFT）"></a>快速傅立叶变换（FFT）</h2><p>快速傅立叶变换使用了分治的思想，他将一个多项式按照奇偶项分成了两个部分（假设这个多项式恰好能被分为两份，如果不能，可以补充一项系数为$0$的项）：</p>
<script type="math/tex; mode=display">
f(x) = a_0+a_1x+a_2x^2+...+a_{n-1}x^{n-1}\\
f_0(x) = a_0+a_2x+a_4x^2 + ...+a_{n-2}x^{\frac n 2 -1}\\
f_1(x) = a_1+a_3x+a_5x^2 + ...+a_{n-1}x^{\frac n 2 -1}\\
f(x) = f_0(x^2)+xf_1(x^2)\\
or \\
f(\sqrt x) = f_0(x)+\sqrt xf_1(x)</script><p>然后问题就变为了求$f_0(x)$和$f_1(x)$两个子多项式的点值表示，然后用这两个子多项式的点值表示推算$f(x)$的点值表示。</p>
<p>值得注意的是，利用公式：</p>
<script type="math/tex; mode=display">
f(x) = f_0(x^2)+xf_1(x^2) \\
or \\
f(\sqrt x) = f_0(x)+\sqrt xf_1(x)</script><p>只能得到$\frac n 2$个点值（因为$f_0(x)$和$f_1(x)$都是$\frac n 2$次多项式），而我们一共需要$n$个点值来确定一个多项式，但是如果我们使用了上面讲的偶次单位复根的性质，就可以用下面的公式：</p>
<script type="math/tex; mode=display">
\begin{equation} 
f(\omega^{k+\frac n 2}_n)=f_0((\omega^{k+\frac n 2}_n)^2)+\omega^{k+\frac n 2}_nf_1((\omega^{k+\frac n 2})^2)\\
=f_0((\omega^k_n)^2)-\omega^k_nf_1((\omega^k_n)^2)\end{equation}</script><p>来计算出另外那$\frac n 2$个点值。</p>
<p>可见用这种方法我们将一个问题分拆成了两个规模减半的子问题，根据经验，这样做的时间复杂度是$O(nlgn)$（实际上是可以证明的，但在此出于文章简单性考虑不作证明）。</p>
<p>上面的公式未免有些令人头大<del>（该死的希腊字母）</del>，我们还是通过例子来讲解。</p>
<p>比如，我们要将$f(x)=3x^3+2x^2+6x+1$化成点值表示，那么我们可以先分解出两个多项式：</p>
<script type="math/tex; mode=display">
f_0(x)=1+2x \\
f_1(x)=6+3x</script><p>接着我们需要分别将$f_0(x)$和$f_1(x)$化为点值表示，根据转化规则，两者的点值表示分别是：</p>
<script type="math/tex; mode=display">
P_0 =\{(1,3),(-1,-1)\}\\
P_1 =\{(1,9),(-1,3)\}</script><p>由此已经可以得到：</p>
<script type="math/tex; mode=display">
\begin{equation} 
P=\{(\sqrt 1,3+\sqrt 1*9),(\sqrt{-1},-1+\sqrt{-1}*3)\}\\=\{(1,3+1*9),(i,-1+i*3)\}\\
=\{(1,12),(i,-1+3i)\}
\end{equation}</script><p>即：</p>
<script type="math/tex; mode=display">
f(\omega^0_4) = f(1) =f(\sqrt1)=f_0(1)+\sqrt1f_1(q)= 12\\
f(\omega^1_4)=f(i)=f(\sqrt{-1})=f_0(-1)+\sqrt{-1}f_1(-1)=-1+3i</script><p>而我们可以借由公式计算出：</p>
<script type="math/tex; mode=display">
f(\omega^2_4) =f(-1)= f(\omega^{0+\frac 4 2}_4)=f_0((\omega^0_4)^2)-\omega^0_4f_1((\omega^0_4)^2)=f_0(1)-\sqrt1f_1(1)=-6\\
f(\omega^3_4) =f(-i)= f(\omega^{1+\frac 4 2}_4)=f_0((\omega^1_4)^2)-\omega^1_4f_1((\omega^1_4)^2)=f_0({-1})-\sqrt{-1}f_1({-1})=-1-3i</script><p>所以最终可以得到$f(x)$的完整点值表示：</p>
<script type="math/tex; mode=display">
\begin{equation} 
P = \{(\omega^0_4,f(\omega^0_4)),(\omega^1_4,f(\omega^1_4)),(\omega^2_4,f(\omega^2_4)),(\omega^3_4,f(\omega^3_4))\}\\
=\{(1,12),(i,-1+3i),(-1,-6),(-i,-1-3i)\}
\end{equation}</script><p>可以看出使用单位复根确实成功地将计算规模减小了一半！</p>
<h2 id="逆DFT（IDFT）"><a class="headerlink" href="#逆DFT（IDFT）" title="逆DFT（IDFT）"></a>逆DFT（IDFT）</h2><p>现在我们已经会快速地把系数表示的多项式转化为点值表示了，那么怎么把点值表示的多项式再倒回来变成系数表示呢？</p>
<p><del>我觉得这远比正向的把系数表示转化为点值表示难，也许是我没学过线性代数的原因。</del></p>
<p>其实这就相当于解这个线性方程组中的 $a_k,k=0,1,2…n-1$：</p>
<script type="math/tex; mode=display">
\begin {equation}
\left\{
\begin {aligned}
a_0+a_1(\omega^0_n)+a_2(\omega^0_n)^2+...+a_{n-1}(\omega^0_n)^{n-1} = & f(\omega^0_n)\\
a_0+a_1(\omega^1_n)+a_2(\omega^1_n)^2+...+a_{n-1}(\omega^1_n)^{n-1}  = & f(\omega^1_n)\\
...\\
a_0+a_1(\omega^{n-1}_n)+a_2(\omega^{n-1}_n)^2+...+a_{n-1}(\omega^{n-1}_n)^{n-1}  = & f(\omega^{n-1}_n)
  \end {aligned}
  \right.
  \end {equation}</script><p>用线性代数中的矩阵可以写成（别担心，用高中的线性代数知识完全可以理解的）：</p>
<script type="math/tex; mode=display">
\left [
  \begin {matrix}
  (\omega^0_n)^0 & (\omega^0_n)^1 & \cdots & (\omega^0_n)^{n-1}\\
  (\omega^1_n)^0 & (\omega^1_n)^1 & \cdots & (\omega^1_n)^{n-1}\\
  \vdots & \vdots & \ddots &\vdots \\
  (\omega^{n-1}_n)^0 & (\omega^{n-1}_n)^1 & \cdots & (\omega^{n-1}_n)^{n-1}
  \end {matrix}
\right]
\left [
  \begin {matrix}
  a_0 \\ 
  a_1 \\
  \vdots \\
  a_{n-1}\\
  \end {matrix}
\right]
=
\left [
  \begin {matrix}
      f(\omega^0_n)\\
      f(\omega^1_n)\\
      \vdots\\
      f(\omega^{n-1}_n)
  \end {matrix}
\right]</script><p>我们考虑这样一个式子：</p>
<script type="math/tex; mode=display">
\begin {equation}
\left\{
\begin {aligned}
& a_0+a_1(\omega^{-0}_n)+a_2(\omega^{-0}_n)^2+...+a_{n-1}(\omega^{-0}_n)^{n-1}\\
& a_0+a_1(\omega^{-1}_n)+a_2(\omega^{-1}_n)^2+...+a_{n-1}(\omega^{-1}_n)^{n-1}\\
& ...\\
& a_0+a_1(\omega^{-(n-1)}_n)+a_2(\omega^{-(n-1)}_n)^2+...+a_{n-1}(\omega^{n-1}_n)^{n-1}
  \end {aligned}
  \right.
  \end {equation}</script><p>或者以矩阵的形式表示：</p>
<script type="math/tex; mode=display">
\left [
  \begin {matrix}
  (\omega^{-0}_n)^0 & (\omega^{-0}_n)^1 & \cdots & (\omega^{-0}_n)^{n-1}\\
  (\omega^{-1}_n)^0 & (\omega^{-1}_n)^1 & \cdots & (\omega^{-1}_n)^{n-1}\\
  \vdots & \vdots & \ddots &\vdots \\
  (\omega^{-(n-1)}_n)^0 & (\omega^{-(n-1)}_n)^1 & \cdots & (\omega^{-(n-1)}_n)^{n-1}
  \end {matrix}
\right]
\left [
  \begin {matrix}
  a_0 \\ 
  a_1 \\
  \vdots \\
  a_{n-1}\\
  \end {matrix}
\right]
=
\left [
?
\right]</script><p>由于某些超过我们能力的数学因素（这里利用到了单位复根的性质），这些式子的结果如下：</p>
<script type="math/tex; mode=display">
\left [
  \begin {matrix}
  (\omega^{-0}_n)^0 & (\omega^{-0}_n)^1 & \cdots & (\omega^{-0}_n)^{n-1}\\
  (\omega^{-1}_n)^0 & (\omega^{-1}_n)^1 & \cdots & (\omega^{-1}_n)^{n-1}\\
  \vdots & \vdots & \ddots &\vdots \\
  (\omega^{-(n-1)}_n)^0 & (\omega^{-(n-1)}_n)^1 & \cdots & (\omega^{-(n-1)}_n)^{n-1}
  \end {matrix}
\right]
\left [
  \begin {matrix}
  a_0 \\ 
  a_1 \\
  \vdots \\
  a_{n-1}\\
  \end {matrix}
\right]
=
\left [
  \begin {matrix}
  n \\ 
  n \\
  \vdots \\
  n\\
  \end {matrix}
\right]
\\ or\\
\frac 1 n
\left [
  \begin {matrix}
  (\omega^{-0}_n)^0 & (\omega^{-0}_n)^1 & \cdots & (\omega^{-0}_n)^{n-1}\\
  (\omega^{-1}_n)^0 & (\omega^{-1}_n)^1 & \cdots & (\omega^{-1}_n)^{n-1}\\
  \vdots & \vdots & \ddots &\vdots \\
  (\omega^{-(n-1)}_n)^0 & (\omega^{-(n-1)}_n)^1 & \cdots & (\omega^{-(n-1)}_n)^{n-1}
  \end {matrix}
\right]
\left [
  \begin {matrix}
  a_0 \\ 
  a_1 \\
  \vdots \\
  a_{n-1}\\
  \end {matrix}
\right]
=
\left [
  \begin {matrix}
  1 \\ 
  1 \\
  \vdots \\
  1\\
  \end {matrix}
\right]</script><p>根据线性代数运算中的一些运算法则（请自己查阅有关逆矩阵的资料，或者干脆把这当作是黑箱），结合上面几个式子，可以得到：</p>
<script type="math/tex; mode=display">
\left [
  \begin {matrix}
  a_0 \\ 
  a_1 \\
  \vdots \\
  a_{n-1}\\
  \end {matrix}
\right]
=\frac 1 n
\left [
  \begin {matrix}
  (\omega^{-0}_n)^0 & (\omega^{-0}_n)^1 & \cdots & (\omega^{-0}_n)^{n-1}\\
  (\omega^{-1}_n)^0 & (\omega^{-1}_n)^1 & \cdots & (\omega^{-1}_n)^{n-1}\\
  \vdots & \vdots & \ddots &\vdots \\
  (\omega^{-(n-1)}_n)^0 & (\omega^{-(n-1)}_n)^1 & \cdots & (\omega^{-(n-1)}_n)^{n-1}
  \end {matrix}
\right]
\left [
  \begin {matrix}
      f(\omega^0_n)\\
      f(\omega^1_n)\\
      \vdots\\
      f(\omega^{n-1}_n)
  \end {matrix}
\right]</script><p>即：</p>
<script type="math/tex; mode=display">
\begin {equation}
\left\{
\begin {aligned}
& a_0=\frac 1 n[f(\omega^0_n)\cdot(\omega^{-0}_n)^0+f(\omega^1_n)\cdot(\omega^{-0}_n)^1+\cdots+f(\omega^{n-1}_n)\cdot(\omega^{-0}_n)^{n-1}]\\
& a_1=\frac 1 n[f(\omega^0_n)\cdot(\omega^{-1}_n)^0+f(\omega^1_n)\cdot(\omega^{-1}_n)^1+\cdots+f(\omega^{n-1}_n)\cdot(\omega^{-1}_n)^{n-1}]\\
& \cdots\\
& a_{n-1}=\frac 1 n[f(\omega^0_n)\cdot(\omega^{-(n-1)}_n)^0+f(\omega^1_n)\cdot(\omega^{-(n-1)}_n)^1+\cdots+f(\omega^{n-1}_n)\cdot(\omega^{-(n-1)}_n)^{n-1}]
  \end {aligned}
  \right.
  \end {equation}</script><p>现在我们将$a_k,k=0,1,…,n-1$看作是一个关于变量$\omega^k_n$的线性函数，将$f(\omega^k_n),k=0,1,…,n-1$看作是函数 各项之前的系数，你就会发现，我们要求$a_k$，其实就是求一个系数表示的函数：</p>
<script type="math/tex; mode=display">
A=\{f(\omega^0_n),f(\omega^1_n),...,f(\omega^{n-1}_n)\}</script><p>的点值表示！</p>
<p>即用上面的$A$计算下面的$P$：</p>
<script type="math/tex; mode=display">
P=\{(\omega^{-0}_n,na_0),(\omega^{-1}_n,na_1),...,(\omega^{-(n-1)}_n,na_{n-1})\}</script><p>然后提取出其中的$a_k$即可。</p>
<p>这不就是我们刚刚学的DFT吗，我们同样也可以用FFT在$O(nlgn)$的时间内完成！</p>
<p><del>（用FFT转过去，还用FFT转回来，神奇）</del></p>
<h2 id="用FFT来解决大整数相乘"><a class="headerlink" href="#用FFT来解决大整数相乘" title="用FFT来解决大整数相乘"></a>用FFT来解决大整数相乘</h2><p>一个$n$位的大整数，就是把一个多项式函数$f(x)$中的$x$带入$10$的计算结果。</p>
<p>这样的话，大整数相乘就是这样一个思路：</p>
<ol>
<li>将每个大整数的各位作为一个多项式系数放入一个数组</li>
<li>对数组代表的多项式进行DFT，得到这些多项式的点值表示的数组</li>
<li>将这些点值表示的数组逐项相乘，获得结果多项式的点值表示的数组</li>
<li>将结果用IDFT转回系数表示</li>
<li>将结果多项式的系数作为大整数的各位输出</li>
</ol>
<p>有了上面介绍的FFT，这些操作就能在$O(nlgn)$的时间复杂度内完成！</p>
<h2 id="更多"><a class="headerlink" href="#更多" title="更多"></a>更多</h2><p>听说还有一种神奇的算法叫快速数论变换（FNT），它用“原根”替代“$n$次单位复根”，避免了使用浮点数表示的复数来做FFT带来的精度问题，不过我的数学太差，算不出来<del>（不，我和CSL大佬不一样，我数学是真的差）</del>。要是有能把我讲懂的就请多多指教了！</p>
<p><del>第一次写算法文章，累到吐血。</del></p>

