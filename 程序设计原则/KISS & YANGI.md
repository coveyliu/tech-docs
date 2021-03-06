
<!-- TOC -->

- [1. KISS 原则](#1-kiss-%E5%8E%9F%E5%88%99)
    - [1.1. 什么样的代码是简单的？](#11-%E4%BB%80%E4%B9%88%E6%A0%B7%E7%9A%84%E4%BB%A3%E7%A0%81%E6%98%AF%E7%AE%80%E5%8D%95%E7%9A%84)
    - [1.2. 如何符合 KISS 原则](#12-%E5%A6%82%E4%BD%95%E7%AC%A6%E5%90%88-kiss-%E5%8E%9F%E5%88%99)
- [2. YAGNI](#2-yagni)
- [3. 总结](#3-%E6%80%BB%E7%BB%93)

<!-- /TOC -->

# 1. KISS 原则
<a id="markdown-kiss-%E5%8E%9F%E5%88%99" name="kiss-%E5%8E%9F%E5%88%99"></a>



- 什么是 KISS 原则
- 什么样的代码是简单的代码
- 如何写出简单的代码



KISS 原则的英文描述有好几个版本，比如下面这几个。
Keep It Simple and Stupid.
Keep It Short and Simple.
Keep It Simple and Straightforward.

表达的意思都是一致的：保持简单



这个原则并没有告诉我们如何保持简单，也没有告诉我们什么样的代码是简单的



## 1.1. 什么样的代码是简单的？
<a id="markdown-%E4%BB%80%E4%B9%88%E6%A0%B7%E7%9A%84%E4%BB%A3%E7%A0%81%E6%98%AF%E7%AE%80%E5%8D%95%E7%9A%84%EF%BC%9F" name="%E4%BB%80%E4%B9%88%E6%A0%B7%E7%9A%84%E4%BB%A3%E7%A0%81%E6%98%AF%E7%AE%80%E5%8D%95%E7%9A%84%EF%BC%9F"></a>



代码行数少的代码是简单的吗？代码逻辑复杂的代码就不是简单的吗？



代码简单与否与每个人的理解程度有关





## 1.2. 如何符合 KISS 原则
<a id="markdown-%E5%A6%82%E4%BD%95%E7%AC%A6%E5%90%88-kiss-%E5%8E%9F%E5%88%99" name="%E5%A6%82%E4%BD%95%E7%AC%A6%E5%90%88-kiss-%E5%8E%9F%E5%88%99"></a>



引用争哥总结的几点吧：

> 1. 不要使用同事可能不懂的技术来实现代码。比如前面例子中的正则表达式，还有一些编
>    程语言中过于高级的语法等。
> 2. 不要重复造轮子，要善于使用已经有的工具类库。经验证明，自己去实现这些类库，出
>    bug 的概率会更高，维护的成本也比较高。
> 3. 不要过度优化。不要过度使用一些奇技淫巧（比如，位运算代替算术运算、复杂的条件
>    语句代替 if-else、使用一些过于底层的函数等）来优化代码，牺牲代码的可读性。



另外引用一句争哥对简单代码的理解

> 我们在做开发的时候，一定不要过度设计，不要觉得简单的东西就没有技术含量。实际上，越是能用简单的方法解决复杂的问题，越能体现一个人的能力。



个人觉得，代码设计还是要的，毕竟想提升能力嘛





# 2. YAGNI
<a id="markdown-yagni" name="yagni"></a>



- 什么是 `YAGNI`
- 这个原则的要求是什么？
- 核心思想是什么？
- 如何满足 `YAGNI`?



YAGNI 原则的英文全称是：You Ain’t Gonna Need It。直译就是：你不会需要它。这个原则要求在代码开发中：不要去设计当前用不到的功能；不要去编写当前用不到的代码。这条原则的核心思想就是：不要做过度设计。



如何满足？

- 没用的某个框架之前，不需要在代码中使用，也不需要在 `maven` 或 `gradle`项目中引入相应的类库

  （想到这个，我真想对 ab 的上两个程序员说：TMD，学点基础的东西吧）







# 3. 总结
<a id="markdown-%E6%80%BB%E7%BB%93" name="%E6%80%BB%E7%BB%93"></a>



- KISS 原则讲的是“如何做”的问题（尽量保持简单）
-  YAGNI 原则说的是“要不要做”的问题（当前不需要的就不要做）
- 满足 KISS 的几个建议
  - 不要使用同事可能不懂的技术来实现代码；
  - 不要重复造轮子，要善于使用已经有的工具类库；
  - 不要过度优化。