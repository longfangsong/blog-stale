---
title: First集和Follow集究竟是什么，以及LL(1)文法的分析
mathjax: true
comments: true
date: 2020-05-17 16:37:01
tags: [编译原理]
summary: 课上这个First集和Follow集出现地非常之突兀，好像突然就有这样一个东西了，本文试图讲解清楚First和Follow集究竟是什么，以及为什么我们需要它。
---

## 为什么需要First集和Follow集

了解[带回朔的递归下降文法分析方法](https://longfangsong.github.io/2020/05/17/最简单的Parser写法——含回朔的递归自顶而下分析/)的同学可能会发现这种分析法由于在文法中每个出现`|`的点上，都会**尝试**这个点上所有可能的生成式，直到有一个匹配成功为止，这种方案虽然保证了对所有文法中的句子，最终都能通过这种方式构建出其语法树，但由于可能存在的大量回朔，效率就会收到影响。

那么我们就要想了，有没有什么方法可以选择出这个点上**唯一**的生成式，来消除所有回朔的可能性，这样效率就能大大提升了。

事实证明，对于一部分文法来说，这么做是可能的。

## 可以选出唯一生成式的实例

例如下面一个（消除了左递归的，所有的$|$都拆开了的）文法：
$$
E \rightarrow T E' \\\\
E' \rightarrow + T E' \\\\
E' \rightarrow \epsilon \\\\
T \rightarrow F T' \\\\
T' \rightarrow * FT' \\\\
T' \rightarrow \epsilon \\\\
F \rightarrow (E) \\\\
F \rightarrow id
$$
可以看出，在调用某个（直接或间接）带有`|`的parse函数，并看到了下一个输入为某个符号时，选用的生成式是：

| parse\输入符号 | $id$               | $+$                      | $*$                  | $($                | $)$                      | 输入结束（接受输入）     |
| -------------- | ------------------ | ------------------------ | -------------------- | ------------------ | ------------------------ | ------------------------ |
| $E$            | $E\rightarrow TE'$ |                          |                      | $E\rightarrow TE'$ |                          |                          |
| $E'$           |                    | $E'\rightarrow +TE'$     |                      |                    | $E'\rightarrow \epsilon$ | $E'\rightarrow \epsilon$ |
| $T$            | $T\rightarrow FT'$ |                          |                      | $T\rightarrow FT'$ |                          |                          |
| $T'$           |                    | $T'\rightarrow \epsilon$ | $T'\rightarrow *FT'$ |                    | $T'\rightarrow \epsilon$ | $T'\rightarrow \epsilon$ |
| $F$            | $F\rightarrow id$  |                          |                      | $F\rightarrow (E)$ |                          |                          |

这里生成式的选择（即每个格子中的产生式）正是唯一的。

那么如何求这张表呢？

容易想到的是，对某个产生式：
$$
A \rightarrow BCD...X
$$
检查其能生成的每个句子中的第一个符号，并将该产生式填入$A$对应行中，这些符号的对应的列中即可。

而求出“能生成的每个句子中的第一个符号”的方法是：按顺序检查$B,C,D...X$中的每一个，如果这个符号是$\epsilon$，那么略过这个符号继续往下看就好了（如果后面啥也没有了，那就把$\epsilon$看成是终结符），如果是终结符，那这“第一个符号”就是它了，如果是非终结符，那么递归地往下看这个符号就好了。

取前面例子中的$E \rightarrow T E'$为例：

- 检查$T$，是非终结符，递归地向下看，有：

  $T \rightarrow F T'$

  - 检查$F$，是非终结符，递归地向下看，有：

    $F \rightarrow (E)$

    - 检查'$($'，是终结符，故下一步看到'$($'时，可以选择$E \rightarrow T E'$，填入表$E$行'$)$'列中。

    $F \rightarrow id$

    - 检查$id$，是终结符，故下一步看到$id$时，可以选择$E \rightarrow T E'$，填入表$E$行$id$列中。

  - $F$不会为$\epsilon$，故到此结束

- $T$不会为$\epsilon$，故到此结束

考虑过每个非终结符对应的产生式后，对应的这一行除了最后一列就填好了，非终结符全部考虑过后，这整个表除了最后一列就都填好了。

最后一列的事情暂时按下不表，我们此时已经自己推出了这个First集了。

## First集

到这里，你就已经自己发明出First集的定义与求法了：

### 定义

龙书的定义里面有一堆数学符号，其实说到底和我上面的定义完全一致。

> Define $FIRST(α)$, where $α$ is any string of grammar symbols, to be the set of terminals that begin strings derived from $α$. If $\alpha \stackrel{*}{\Rightarrow} \epsilon$, then $\epsilon$ is also in $FIRST(α)$.  ——  “Compilers: Principles, Techniques, and Tools, 2/e” 
>

定义$FIRST(α)$，其中$\alpha$是一个语法符号构成的任意串，为一组可以被$\alpha$推导出的终结符串的起始字符的集合。如果$\alpha \stackrel{*}{\Rightarrow} \epsilon$，那$\epsilon$也在$FIRST(α)$中。

这不就是我们上面说的“能生成的每个句子中的第一个符号”按照推导式左侧的非终结符全部并起来的结果吗。

### 求法

同理，数学符号……

> 1. If X is a terminal, then $FIRST(X) = \{X\}$.
>
> 2. If X is a nonterminal and $X → Y_1Y_2 ... Y_k$ is a production for some $k \ge 1$, then place 'a' in $FIRST(X)$ if for some $i$, 'a' is in $FIRST(Y_i)$, and $\epsilon$ is in all of $FIRST(Y_1), ..., FIRST(Y_{i – 1})$; that is, $Y_1Y_2...Y_{i-1} \stackrel{*}{\Rightarrow} \epsilon$. 
>
>    If $\epsilon$ is in $FIRST(Y_j)$ for all $j = 1, 2, ..., k$, then add $\epsilon$ to $FIRST(X)$. 
>
> 3. If $X \rightarrow \epsilon$ is a production, then add $\epsilon$ to $FIRST(X)$.
>
> —— “Compilers: Principles, Techniques, and Tools, 2/e” 

1. 若X是终结符，则$FIRST(X) = \{X\}$

2. 若X是非终结符：

   若$\epsilon$ 在所有 $FIRST(Y_1), ..., FIRST(Y_{i – 1})$ 中，在'a' 在 $FIRST(Y_i)$中。则'a'也在$FIRST(X)$中。

   若对所有$j = 1, 2, ..., k$，$\epsilon$都在$FIRST(Y_j)$中，则$\epsilon$也在$FIRST(X)$中。

3. 若有生成式$X\rightarrow \epsilon$，则$\epsilon$也在$FIRST(X)$中。

和上面我们自己推出的求法相比也非常相似，不过是按照推导式左侧的非终结符全部并起来的结果。

而这种求法，显然是可以用动态规划法加速的。

## 最后一列

那这个最后一列怎么处理呢？

也很简单，如果从起始符号开始，在经过数次推导之后，在由某个非终结符作为最右边的非终结符时推导可以使整个文法的分析结束，这个非终结符对应推出$\epsilon$的推导式就能填在最后一列对应的行中。[^2]

那么怎么找出这些“从起始符号开始，在经过数次推导[^1]之后，作为最右边的非终结符时推导可以使整个文法的分析结束”的非终结符号呢？

首先我们可以看开始符号最右边的符号，这个符号一定符合我们的要求，在这里这个符号是$E '$。

如果对某个符合条件的符号$Z$和另外某个（单个）符号$X$，有产生式$Z\rightarrow YX$或$Z\rightarrow YXW$，其中$W \stackrel{*}{\Rightarrow} \epsilon$，则$X$也符合要求。


从我们的例子上来看，将上面的$Z$可以带入$E'$和$E$，有：

- $E\rightarrow TE'$，而存在$E' \rightarrow \epsilon$，故$T$也符合要求，但不能推出$\epsilon$
- $T \rightarrow* FT'$，故$T' $也符合要求，将$T' \rightarrow \epsilon$填入适当位置
- $T \rightarrow* FT'$，而存在$T' \rightarrow \epsilon$，故$F$也符合要求，但$F$不能推出$\epsilon$

## Follow集

### 定义

Follow集的定义在我们上面的求法的基础上进行了扩展：

> “Define $FOLLOW(A)$, for nonterminal A, to be the set of terminals $a$ that can appear immediately to the right of A in some sentential form; that is, the set of terminals a such that there exists a derivation of the form $S \stackrel{*}{\Rightarrow} \alpha Aa\beta$, for some $\alpha$ and $\beta$”
>
> ——  “Compilers: Principles, Techniques, and Tools, 2/e” 

Follow集就是在文法的所有句子中，可能出现在“由非终结符A生成的部分”之后的终结符的集合。而上述例子所求的其实就是这里的“终结符”为“输入结束标记”的特例。

### 求法

> 1. Place $\\$$ in $FOLLOW(S)$, where S is the start symbol, and $ is the input right endmarker.
> 2. If there is a production $A → αBβ$, then everything in $FIRST(β)$ except $\epsilon$ is in $FOLLOW(B)$.
> 3. If there is a production $A → αB$, or a production $A → αBβ$, where $FIRST(β)$ contains $\epsilon$, then everything in $FOLLOW(A)$is in $FOLLOW(B)$.
>    —— “Compilers: Principles, Techniques, and Tools, 2/e”

1. 输入结束标记（龙书中称为$\\$$）在$FOLLOW(S)$中
2. 若有$A → αBβ$，则 $FIRST(β)$ 中所有不为 $\epsilon$ 的元素都在 $FOLLOW(B)$ 中
3. 若有$A → αB$，或 $A → αBβ$，其中 $FIRST(β)$ 包含 $\epsilon$ 则 $FOLLOW(A)$ 中的所有元素也在 $FOLLOW(B)$ 中。

和我们上面的例子唯一差别在于第2条，不过也很好想，$FIRST(β)$就是$\beta$能推出的第一个非终结符的所有可能取值的集合，而$B$与$β$是紧挨着的，$B$的下一个元素就是$\beta$的第一个元素（$\epsilon$除外）。

## 总结

有了上面讲的填这类表的方法之后，对于填完后每个格子至多只有一个推导式的文法，我们就可以写出完全不含有回朔的自顶向下文法分析器（递归或者非递归均可），这类文法也就是$LL(1)$文法。当然如果将表头中的一个符号变为多个符号的组合，这种方法也可以推广到$LL(N)$文法上。

同时我们也终于明白了$FIRST$集和$FOLLOW$集的求法，这两个集合在之后的移进-规约分析中也有用。

[^1]: 注意这里“数次”含0次，所以起始符号自然符合要求

[^2]: 这里可能要自己感觉一下，如果有人有更好的讲法，请在评论区留言

