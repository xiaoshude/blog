# 控制复杂度的思考模型

事物及其关系的复杂无边界，但人脑却有。所以，把复杂的东西，通过一定的策略，控制在能处理的范围，很有意义。

因为我是个智力普通的人，触及【上界】更加频繁，也就很早开始关注和思考这个问题。
把这 10 几年的想法梳理下来。原因有二：

- 我渴望规律和模式，
- 其二，使命感。
这两个原因，近乎构成了所有的动机。

## 等价思考

等价和相等，一旦把这两词放在一起，便瞬间理解了等价的精髓：在关注的地方，两者相等，其他地方不相等，或者相不相等无所谓。

第一次意识到等价的魔力在高一物理，场景是，一个小车行驶在波浪形路面上，一会上坡，一会下坡，加速度、速度、重力方向有关系。这个关系可以通过课本上的定理和公式运算出来，但需要时间。
你知道的，就是那种选择题，基本 2分钟内得知道答案，不然只能放弃。
以我的计算速度，绝对做不出来，哪怕对运算的过程无比熟悉 —— 无奈【路】太长。

那怎么办？
有没有办法，缩短思考的路径，得到一样的答案？

后来，找到了，几乎在 3s 内就能得到答案，而且和耗时 5分钟，运算的结果一样。
其实，在算答案时，我压根没想任何公式，而再想一个虚拟的场景，场景里有一只手，从这个手里，我看到了加速度的方向。

我很快『算』出了答案，并不是脑子灵，而是走了一条直路 —— 直面目的的路，在这条路上，乌龟都能比兔子先到达终点。

一旦发现这个规律，就会发现身边到处都是，而且为之前的视而不见感到惊讶。 —— 这同样是一条规律。

## 以终为始

先明确要达成的目的，然后一步步向下拆解，直到【基础】。而不是反过来，如此，思考的每一步都会有方向感。

这个策略，真正开始有意识的应用，还是工作之后。

当我研究一个库或复杂功能的实现时，

1. 先弄清核心函数的出入参和副作用，通常函数签名和文档可以得到。 —— 明确目的
2. 达到这个目的，如果自己实现，怎么做。自己设计数据结构和流程，然后实现，而不是直接看作者的实现。
3. 看作者的实现，并和自己的对比。

这样，会更快的看明白源代码。
对于复杂的功能，如果上来直接看作者实现，会把我搞晕，迷路在森林中，那种踉跄寻找方向的感觉，十分糟糕。
而提前手里拿一个地图，哪怕是铅笔画在牛皮纸上那种，也完全不一样。

当接到一个复杂的需求时，以终为始的策略，让我设计技术方案不会 overwhelming（只能用这个词了。。。）。

1. 明确需要的数据，和数据的使用方式。
2. 对数据分类，抽象数据模型
3. 对外 API
4. API 核心逻辑
5. 表结构设计

以目的 —— 最终要提供的数据和操作为出发点，让整个思考过程容易且无遗漏。

## 模式

当要解决一个复杂问题时，我需要固定的模式，应用上面两个策略（也许之后还有更多），而不是无意识和随机的选择。

这个模式，是大脑的程序，最后可能固化为神经元之间的突触链接。

1. 以终为始，明确目的。
2. 面向目的，设计等价模型，减少信息量，同时和已有模型建立关联。

描述模式时，我通常想到是决策树，沿着它，能做出目前最优的选择序列，并不断用实践投喂它，然后依赖反馈进化。

## 总结

回顾最初的两个目的。

1. 写到这里，我逐步理清了复杂问题的思考模式。
2. 给同样的【普通人】一些(可能的)帮助。
