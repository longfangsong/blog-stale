---
title: SLR(1)分析
mathjax: true
comments: true
date: 2020-06-29 12:45:45
tags: [编译原理]
summary: SLR(1)分析通过对FOLLOW集的分析，可以处理一些LR(0)中的归约-归约冲突。
---

先前我们讲[LR(0)分析](https://longfangsong.github.io/2020/06/28/LR-0-%E5%88%86%E6%9E%90/)的时候，曾经讲过LR面对下面这两种冲突无能为力：

- 归约-归约冲突
  $$
  A \rightarrow b \cdot \\\\
  C \rightarrow d \cdot
  $$

- 移进-归约冲突
  $$
  A \rightarrow a\cdot b \\\\
  B \rightarrow c \cdot
  $$

此时仅仅靠这两个项是无法判断要移进还是要归约，归约成哪一个非终结符的，但是通过附加一些其他信息就可以进行确定了。

我们参考[LL(1)文法](https://longfangsong.github.io/2020/05/17/First集和Follow集究竟是什么，以及LL(1)文法的分析)中的解决方案，看一下后面一个输入符号是什么，然后：

- 对于形如
  $$
  A \rightarrow a\cdot b \\\\
  B \rightarrow c \cdot
  $$
  的移进-归约冲突，如果后面一个输入符号是 $b$，那么就移进，否则归约为 $B$。

- 对于形如
  $$
  A \rightarrow b \cdot \\\\
  C \rightarrow d \cdot
  $$
  的归约-归约冲突，如果下一个输入符号是$e$，那么根据是否存在包含或可以推导出 $Ae$ 或者 $Ce$ 的产生式（用形式化的语言来说，就是 $e$ 是否在 $A$ 或 $C$ 的 [FOLLOW 集](https://longfangsong.github.io/2020/05/17/First%E9%9B%86%E5%92%8CFollow%E9%9B%86%E7%A9%B6%E7%AB%9F%E6%98%AF%E4%BB%80%E4%B9%88%EF%BC%8C%E4%BB%A5%E5%8F%8ALL(1)%E6%96%87%E6%B3%95%E7%9A%84%E5%88%86%E6%9E%90/#Follow%E9%9B%86)中），来选择归约到 $A$ 还是 $C$。

推广到更一般的情形，对于一堆同一个项集中的项：
$$
A_1\rightarrow 一坨东西\cdot a_1 \cdot 一坨东西 \\\\
A_2\rightarrow 一坨东西\cdot a_2 \cdot 一坨东西 \\\\
\cdots\\\\
A_n\rightarrow 一坨东西\cdot a_n \cdot 一坨东西 \\\\

B_1\rightarrow 一坨东西\cdot \\\\
B_2\rightarrow 一坨东西\cdot \\\\
\cdots \\\
B_k\rightarrow 一坨东西\cdot \\\\
$$
（注意SLR(1)中下面两项不能分出先后）

- 若下一个输入符号在 $\{a_1,a_2,\cdots,a_n\}$ 中，则移进。
- 若下一个输入符号在 $FOLLOW(B_i)$ 中，则归约到 $B_i$。