---
title: 最简单的Parser写法——含回朔的递归自顶而下分析
mathjax: true
comments: true
date: 2020-05-17 16:30:31
tags: [编译原理]
summary: 本文介绍了一种白痴都能懂的Parser构造方法：含回朔的递归自顶而下分析，如果你更喜欢函数式那套玩意的话，你可能会称之为“语法分析器组合子”。
---

如果你听了老师上课讲的那一大坨根本没人听得懂的parse方法之后认为自己这辈子都造不出编译器了，那么本文介绍含回朔的递归自顶而下分析法正是你所需要的，这种方式非常容易想到，也非常容易编写，除了文法分析之外，对于词法分析，这个方法也可以一手包办，而且足以应付绝大多数编程语言的构造。

我们先结合一个例子来看一下这玩意怎么使的：

## 主要思想

含回朔的递归自顶而下分析法的主要思想是：将每个非终结符（推导式左边）的分析写成一个函数，将右侧的内容组合写在函数体里面，如果这个函数从左向右匹配某段代码字符串成功，就返回存放了对应信息的结构体和剩下的字符串，否则返回Error。

## 实践

我们要分析的文法是：
$$
Expression \rightarrow Ident | Constant | InBrackets \mid Expression * Expression | Expression + Expression \\InBrackets \rightarrow '(' Expression')' \\Ident \rightarrow [a-zA-Z][a-zA-Z0-9]* \\Constant \rightarrow [0-9]*
$$



上面的例子可以写成四个函数[^1]：

```
fn expression(code: string) -> (Expresion, string) | Error;
fn in_brackets(code: string) -> (InBrackets, string) | Error;
fn ident(code: string) -> (Ident, string) | Error;
fn constant(code: string) -> (Constant, string) | Error;
```

那么函数体怎么写呢？

### `alt`、`many0`和`tag`

我们首先针对`|`和`*`，定义两个函数：

```
// 接受一组parser函数和字符串作为参数，返回其中最早成功匹配的parser的匹配结果，相当于|
fn alt<R>(parsers: Vec<Fn(string) -> (R, string) | Error>, input: string) -> (R, string) | Error {
	for parser in parsers {
		if parser(input) != Error {
			return parser(input)
		}
	}
	return Error
}

// 接受一个parser函数，返回其重复匹配0次以上的结果，相当于*
fn many0(parser: Fn(string) -> (R, string) | Error, input: string) -> (Vec<R>, string) | Error {
	result = new Vec();
	while parser(input) != error {
		r, input = parser(input)
		result.push(r)
	}
	return (result, input)
}
```

在实现一个`tag`函数来识别某些指定的字面量[^2]：

```
fn tag(tagname: string, code: string) -> (string, string) | Error {
	if code.starts_with(tagname) {
		return tagname, code[len(tagname):]
	} else {
		return Error
	}
}
```

然后就可以实现上面的几个函数了：

```
fn mul(code: string) -> (Expression, string) | Error {
	// 识别左操作数, ? 代表如果它前面的函数返回Error，则当前函数也返回Error
	lhs, rest = expression(code)?
	// 识别*号
	op, rest = tag("*", rest)?
	// 识别右操作数
	rhs, rest = expression(rest)?
	return Expression {lhs, "*", rhs}
}

// add 函数同理，*变为+

fn expression(code: string) -> (Expresion, string) | Error {
	// 注意这里的顺序，优先级越低操作符对应的函数越要在前面
	// 不理解的话考虑一下1+2*3会怎么被parse就知道了
	return alt([
		add,
		mul,
		ident,
		constant,
		in_brackets,
	], code)
}
fn in_brackets(code: string) -> (InBrackets, string) | Error {
	bracket, rest = tag("(",code)?
	expr, rest = expression(rest)?
	bracket, rest = tag(")",code)?
	return InBrackets(expr), rest
}
fn ident(code: string) -> (Ident, string) | Error {
	return alt(tag("a", code), tag("b", code),........) 
}
fn digit(code: string) -> (string, string) | Error {
	return alt(tag("0", code), tag("1", code),........) 
}
fn constant(code: string) -> (Constant, string) | Error {
	return many0(digit, code)
}
```

### 左递归

实际写出来之后会发现这里有一个问题：

```
fn expression(code: string) -> (Expresion, string) | Error {
	return alt([
		add,
		mul,
		ident,
		constant,
		in_brackets,
	], code)
}
```

其中的`mul`，间接调用了`expression(code)`，而这时又会反过去调用`mul(code)`，且其中的`code`参数一直没有变化，`add`也有这个问题，这样在程序中就会造成无限递归。

这也是为什么课上说在做自顶而下文法分析的时候要先消除左递归的原因。

怎么消除也很简单，我们要让`mul`和`add`至少从`code`中“消耗”一个优先级比其自身高的expression，即：

```
fn higher_than_mul(code: string) -> (Expression, string) | Error {
	return alt([
		ident,
		constant,
		in_brackets
	], code)
}

fn mul(code: string) -> (Expression, string) | Error {
	// 识别左操作数, 消耗掉一个优先级高于*结果的语法元素
	lhs, rest = higher_than_mul(code)?
	// 识别*号
	op, rest = tag("*", rest)?
	// 识别右操作数
	rhs, rest = expression(rest)?
	return Expression {lhs, "*", rhs}
}

// add 同理，你应该自己能想到的
```

实际上就相当于将Expression部分的文法改成了：
$$
Expression \rightarrow Add | Mul | Ident | Constant | InBrackets \\
Add \rightarrow HigherThanAdd + Expression \\
Mul \rightarrow HigherThanMul * Expression \\
HigherThanMul \rightarrow Ident | Constant|InBrackets \\
HigherThanAdd \rightarrow Mul | HighterThanMul
$$
和书上讲的消除左递归的方案是不是很像？

这样一个简单表达式的parser就完成了。

## 优势与不足

### 优势

这种parser的优势有：

- 容易理解与实现
- 写出的代码是完全可读可调试的

### 不足

这种parser的不足有：

- 效率问题，回朔可能会带来一定的性能问题，因此可能不适用于一些极其要求性能的场合[^3]

注意我没有提到任何关于“可以分析的文法类的大小”的问题，我没有查到带回朔的递归下降分析器能分析的文法类，但如果我没有想错的话它能分析$LL(\infin)$，总之在实践中，这一分析器可以应用于几乎所有可能会碰到的文法，当然，除了C++之外，C++不是$CFG$……。


[^1]: 本文中的代码均为伪代码，返回类型也不太严谨（如果你一定要深究的话，那么可以把它们看作是协变的），实际实现可以参考[Geal/nom](https://github.com/Geal/nom)及[我对其的简化实现](https://github.com/longfangsong/tiny-nom)。

[^2]: 你可以认为tag是用来识别“终结符”的

[^3]: 一般的编译器中，文法分析器的性能其实是无需首先考虑的（后端每个步骤的耗时常常是整个前端的耗时的十倍甚至百倍之多），但在部分场合比如数据库中SQL的解释中，较慢的文法分析器可能会带来性能瓶颈。