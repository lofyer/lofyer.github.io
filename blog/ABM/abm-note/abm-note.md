---
title: "Agent Based Modeling学习笔记合集"
date: "2015-11-14"
categories: 
  - "abm"
---

# Agent Based Modeling学习笔记合集

## 最小增长模型

这个模型可以简单解释GDP增长、公司状况等等增长模型，比较实用。

基本条件： 100个工人操作1个生产豆子的机器，生产出的豆子可以用来消费、购买机器（一个豆子）、储蓄，每个机器的产出为 100\*机器数量^0.5 个豆子，机器第二年的报废率为10%，每年总产出的20%用于投资，其他用于消费。

计算：

总产出 = 100 \* 机器数量^0.5

储蓄 = 储蓄率s \* 总产出

总产出 = 消费 + 投资

下一年机器数量 = 当年机器数量 + 投资机器数量 - 报废率d \* 当年机器数量

可知，报废的机器数量总有与投资机器数量相等的时候。

此时我们应有储蓄保证每年消费最大，即将储蓄用于报废机器的增补投资。

s \* 100 \* 机器数量^0.5 = d \* 机器数量

此时机器数量维持在 10000s/d

消费 = 10000 \* s \* (1-s)/d

为使消费最大，s在此场景中取0.5

## 基于ABM的云平台控制器

这篇文章可以写一篇论文。

注意，这里的controller不是指OpenStack的controller，而是整个平台过程控制的controller，也就是Automatic System。

好吧，这个问题不大，但也不小。好在我会建模会模拟，嘿哈。（这里不用EBM建模的原因是这个系统有些复杂，公式不足以直观发现问题，但为了提升一下本文的友好度，所以我会加几个公式^\_^）

如果你不熟悉ABM建模可以参考我之前的文档，[DataNote](http://datanote.readthedocs.io/zh/latest/posts/ch03.html)。

那么首先我们要确认模型的目的，即是控制整个虚拟化（容器）在一定条件下的行为，这些条件有存储、网络、计算等资源，直观的条件简单来说就是用户需求（杂项比较多）。然后确定代理人（agent, turtles）、关系连接（link）、控制规则（rules）、代理人行为（actions）等。

在公交车上想出了一个模型，factory-server-client模型，可以的，然后我再收集点主流平台的规则就可以模拟了，非常容易唬人。

**最近改为了网格-细菌-食物模型。**

先来个粗犷的图吧。

[![](/blog/images/微信截图_20170802002232.png)](https://blog.lofyer.org/wp-content/uploads/微信截图_20170802002232.png)

图中，细菌代表虚拟机，食物代表负载，网格代表物理机，食物会一直生成，其速度可控，也就是可以控制负载的变化速度。比如，当负载变化加快时，即食物增长，虚拟机的平均负载会增高，甚至会增加新的虚拟机（也就是细菌繁殖），当然，物理机总数保持不变。

那么这个模型中我们可以得到什么信息，从而达到最高的性价比呢，即虚拟机的变化量最小？

1\. 选择合适的繁殖参数（什么时候创建新虚拟机）；

2\. 可以尝试将细菌固定在一个或者一些网格中，即虚拟机的亲和性，可以观察影响；

3\. 课以观察物理机宕机状态下的虚拟机承载与变化能力；

基于这个模型，我们可以搞个controller独立运行，会通过平台API获取资源的即时信息，然后再去做优化动作。

## ABM建模导读

本文算是一个读书笔记吧，就是最近刚买的那几本书，相较于之前Scott的几本，这几本学术意味比较浓。中间发现这些作者确实有比较有意思的观点和坚实的理论基础，在此记录以用来随时回忆。

# 1\. Introduction to Agent Based Modeling

[![](/blog/images/QQ截图20170418232613-264x300.png)](https://blog.lofyer.org/wp-content/uploads/QQ截图20170418232613.png)

## c0. & c1. 简介

反正ABM应用范围与可观察性相比EBM（基于公式，也就是我们常说的数学建模）更好，但EBM更容易系统集成。

## c2. 入门

蚂蚁模型（也就是寻食模型）、网格自动机（复杂科学，在Wolfram之前就有人总结了相当一部分，另外它与有限状态机都属于自动机的一个特例）、英雄与懦夫模型（如果再加上人可以受他人感染的属性那就可以解释为什么某些人领袖特征还有人买账）、简单经济模型（只要人人都奉献，那么穷的越来越穷，富的越来越富，真不是正态分布，开源届或许也是如此^\_^）

## c3. 继续入门

四个特性：简单规则组成复杂现象，个体随机性导致一致行为，复杂模式可以“自组织”（飞鸟群、大雁群），不同模型反应世界的不同方面；森林大火模型（即渗透模型，可以用来测试系统的鲁棒性，能适用于很多领域，比如IT、经济、社会等）；有限分形聚合模型（化学与自然物理模型）；谢林模型（求同存异，人以群分啊）；EL Farol模型（学习并预测去酒馆）

## c4. 进入正题

开篇就遇到一个我十分认同并曾经也说过（哈哈）的话，来自物理学家费曼先生。 [![](/blog/images/img_2458.jpg)](https://blog.lofyer.org/wp-content/uploads/img_2458.jpg) 我不能理解我造不出来的东西。 OK进入正题，本章是将如何创建模型，有基于现象（模拟）和探索型（比如网格自动机）两种基本模型，但没有明显边界。然后接下来为了让我们快速上手ABM，作者一步步地使用NetLogo构建出了草-羊-狼模型，从而让你理解ABM建模的一般思路。建模运行以后，为了发现或验证规律，我们需要多次运行这个模型，为此NetLogo提供了行为空间（Behavior Space）方便我们多次运行并收集数据。本章末尾，作者提出了ABM与面向对象编程（OOP）异同点的思考，刚接触这个ABM的时候我就发现它俩很像，但以我短浅的经验来看，两者区别不太大，因为它俩的历史出处有非常惊人的相似或相同，要说有的话，ABM是一种思考方法，即一切从代理出发（agent），不必关心代理之间的复杂影响关系（说实话作者这个描述我是反对的，但作为一种思想而言细想又是对的，复杂的模型，复杂的关系，提纲挈领后也非常简单），而OOP。。OK我编不下去了。

## c5. 哈哈，实践篇

这章终于讲了个正经模型，红绿灯模型。这不是重点，重点是为了节省汽油，作者引入了最佳速度，并且为控制这个最佳速度，使用了简单的比例控制，然后又说了一下机器学习的作用。敢情我大学几年都在学习机器学习啊。。本章结尾也较为系统地介绍了NetLogo的特性，比如3D、GIS等，其中这个GIS我在建模世界历史时应该会用到。总之本章非常精彩，至少我很满意。

后面6789章就不写出来了，速读了一下，暂时用不到，但很快就用到。

## Compell's law

[![IMG_1824](/blog/images/IMG_1824.png)](http://blog.lofyer.org/compell-effect/img_1824/)

## Markov model

马尔可夫模型是个很有意思的模型，记住两个示例。 学生上课注意力：Alert:Bored 国家体制：Free:Partly Free:Not Free

          Alert(t)  Bored(t)
Alert(t+1)  0.8      0.25       P          P
                            X         = 
Bored(t+1)  0.2      0.75      (1-P)     （1-P）

So 0.8xP + 0.25x(1-P) = P,
we got P=5/9, finally 5/9 of the students will be alert.

## 模型思考汇总

我们生活在一个繁杂的世界里，形形色色的人、企业和政府交织在一起，产生了各种 新奇、意想不到的现象。我们看到政治冲突、市场崩盘和不断更迭的社会趋势。该如何去理解呢？运用模型。证据表明，具备模型思维的人要优于没有这种思维的人。此外，运用大量模型进行思考的人要优于只运用一种模型思考的人。为什么模型会使我们成为更好的思考者呢？模型有助于我们更好地组织信息，理清互联网上乱成一团麻或者一锅粥（选择你认为合适的比喻说法）的数据。模型有助于我们提高准确预测的能力，还有助于我们做出更好的决策并采取更有效的策略，甚至可以提高我们的机构和流程设计能力。

第1单元：简介：为什么要运用模型？

在这些课程视频中，我会解释为什么我们需要学习模型。这些原因可分为四大类：

成为睿智的世界公民 成为逻辑清晰的思考者 了解并使用数据 更好地制定决策、战略和设计

本单元有两个阅读材料。可以在第一个视频结束后，或在观看完所有视频后阅读这些材料。

[《模型思考者》序言、导论和第1章](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R1Page.pdf)

[《为什么要运用模型？》 乔舒亚·爱普斯坦 著](http://vserver1.cscs.lsa.umich.edu/~spage/ONLINECOURSE/R1Epstein.pdf)

第2单元：分类和同群效应

我们现在直接跳转到一些模型。我们对解释同一个现象（即，人们更喜欢与那些和自己外形、思想和行为相似的人生活和交流）的两种模型进行对比。入门课程视频后，我们将介绍解释这些现象的谢林（Schelling）和格兰诺维特（Granovetter）的著名模型。然后我们会介绍一个有趣的有关起立鼓掌的模型，这个模型是由我和我的朋友约翰·米勒（John Miller）共同编写的。

在第2单元中，我将会使用NetLogo软件展示一个计算版本的谢林的隔离模型。Netlogo是一款由乌里·威伦斯基（Uri Wilensky）或美国西北大学编写的免费软件。在本课程中我会多次用到NetLogo软件。可以通过以下地址下载该软件：

[NetLogo](http://ccl.northwestern.edu/netlogo/)

通过单击“文件”（file）选项，然后进入“模型库”（Models Library）可以找到我使用的谢林模型。在“模型库”目录中，单击“社会科学”（Social Science）文件夹旁边的箭头并向下滚动，然后单击名为“隔离”（Segregation）的模型。

本单元的阅读材料包括谢林模型的一些简要说明，以及格兰诺维特、米勒和佩吉的 学术论文。通读这些论文不是必须的，但是我强烈建议你们这么做，以了解社会科学家是如何搭建和解释这些模型的。

[谢林模型说明](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R2Schelling.pdf)

[格兰诺维特模型](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R2Granovetter.pdf)

[米勒和佩吉模型](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R2StandingOvation.MillerPage.pdf)

第 3 单元：聚合

在本单元中，我们将了解聚合，即事件累计的秘密。我们先从数字合计方式开始， 重点介绍中心极限定理。然后了解合计规则。并讲解生命游戏和一维细胞自动机模型。 这两种模型都展示了如何将简单的规则组合在一起来产生有趣的现象。最后，我们会讲解聚合偏好。在这里，我们看到个人偏好可以是合理的，但聚合偏好无需如此。

网上有很多有关中心极限定理、二项分布、六西格玛、生命游戏等内容的资料。我提供了一些链接以便于你入门。细胞自动机和多元化偏好的阅读材料分别来自我所著的《复杂自适应社会系统》和《区别》这两本书中的简短摘录。

[中心极限定理](http://stattrek.com/lesson3/samplingdistributions.aspx)

[二项分布](http://stattrek.com/lesson2/binomial.aspx)

[六西格玛](http://en.wikipedia.org/wiki/Six_Sigma)

[细胞自动机1](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R3CAs.pdf)

[细胞自动机2](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R3CA2.pdf)

[多元化偏好](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R3Preferences.pdf)

第4单元：决策模型

在本单元中，我们将研究人们如何制定决策的一些模型。我们先从多准则决策开始。然后介绍决策空间模型和决策树。最后讨论信息的价值。

多准则决策阅读材料是我讲解密歇根州民权提案的指导材料。它为如何使用这个技巧提供了案例研究。关于空间投票和决策模型，网上有很多很棒的PPT演示和论文。决策树资料源自亚利桑那州立大学克雷格·柯克伍德（Craig Kirkwood）的著作。

[多准则决策案例研究](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R4MCRI.pdf)

[空间模型](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R4Spatial.pdf)

[决策理论](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R4Decision.pdf)

第5单元：人类模型：电子思维

在本单元中，我们将介绍社会科学家塑造人类模型的不同方法。我们会介绍并对比三种不同的模型。理性行为者方法、行为模型以及规则模型。这些课程视频为许多后续模型提供了相应的背景知识。这些课程视频没有具体的阅读材料，但是我在课程中提及的行为经济学的一些相关书籍可用作参考。此外，如果你觉得竞次游戏很有趣，那么可以在搜索引擎中键入“罗丝玛丽·纳格尔竞次理论”（"Rosemary Nagel Race to the Bottom"）便可以得到多个相关链接。你还可以采用同样的方法找到“零智能交易者”（Zero Intelligence Traders）的相关介绍。

通过此处提供的链接可以获得行为经济学的初级读物，这里面有更多的参考资料。

[《行为经济学初级读物》](http://www.econlib.org/library/Enc/BehavioralEconomics.html)

第6单元：线性模型

在本单元中，主要介绍了线性模型。我们先从分类模型开始，该模型中的数据分为多类。我们使用这个简单的框架介绍一些度量值，如平均值、方差、决定系数。然后进入线性模型主题，讲解线性模型的作用、介绍如何读取回归输出（宝贵技能！） 以及如何用线性模型匹配非线性数据。这些课程视频旨在介绍如何使用线性模型，由此可能激励你参加这些主题相关课程。本单元最后会重点介绍我称为大系数思维和新现实思维之间的区别。本单元的阅读材料包括我编写的2篇短论文，但是你可以在网上找到有关线性模型、决定系数、回归和循证思维的更多资料。

[分类模型](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R5CategoricalModels.pdf)

[线性模型](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R5Linearmodels.pdf)

第7单元：临界点

在本单元中，我们会介绍临界点。我们重点介绍两种模型。一种是银行使用的物理学上的渗流模型，另一种是疾病传播模型。由于疾病模型比较复杂，因此我分为两部分进行 讲解。第一部分重点介绍扩散。第二部分介绍恢复。本单元的阅读材料是我编写的有 关模型的书籍中的两篇摘录。一篇关于扩散。另一篇关于临界点。此外，我还提供了一个链接，由此可以找到一篇关于临界点的技术论文。这是我和PJ·兰伯森（PJ Lamberson）共同编著的，并将在《政治学季刊》（Quarterly Journal of Political Science）上发表。由此你可以大概了解技术型社会学论文的一些基本情况。无需全篇阅读，但我强烈建议你仔细阅读简介部分。其中包含了非常有用的参考文献列表。

[临界点](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R7Tips.pdf)

[扩散和SIS](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R7Diffusion.pdf)

[兰伯森和佩吉：临界点（只阅读导论部分）](http://www.santafe.edu/research/working-papers/abstract/aecbfc4c6f63132ad19706066c864f27/)

第8单元：经济增长

在本单元中，我们将介绍几种增长模型。首先介绍指数增长的一个简单模型，然后是经济学模型，并重点阐述索洛的基本增长模型。我将模型进行了简化，去除了劳动力部分。 这些模型有助于我们区分两种增长方式：资本积累增长和创新增长。

[增长模型](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R8growth.pdf)

第9单元：多样性和创新

在本单元中，我们介绍了解决问题的几种模型，以展示多样性对创新的重要性。我们将了解到不同的观点（问题表征）和启发法如何使问题解决者团队的表现胜过个人。此外， 我们还会引入一些新概念，如“崎岖的风景”和“局部最优”。在最后的课程视频中， 我们将见识重组的威力及其如何促进经济增长。本章节的阅读材料包括普林斯顿大学出版社出版的由我编著的书籍《区别》的摘录。

[多样性和问题解决](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R9ProblemSolving.pdf)

第10单元：马尔可夫过程

在本单元中，主要介绍了马尔可夫过程（Markov Processes）。马尔可夫过程指的是一组固定状态之间的动态过程。例如，我们将讨论有些国家在民主制度和专制制度之间过渡的过程。作为马尔可夫过程，必须要能从任意一种状态转换为另一种状态，且状态之间转换的可能性必须随着时间的推移保持不变。如果这些假设成立，那么这种过程将存在唯一的均衡。换句话说，历史将变得无关紧要。这种结果称为马尔可夫收敛定理。除了马尔可夫过程之外，我们还将了解如何将这个基本框架用于其他用途，如确定文本的著作权以及药物协议的有效性等。

[马尔可夫过程](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R10Markov.pdf)

第11单元：李雅普诺夫函数

模型可以帮助我们确定系统产生结果的性质：系统是否会产生平衡、循环、随机性或复杂性？ 在本节课程视频中，我们将介绍李雅普诺夫函数。这种技术有助于我们识别哪些系统可以达到平衡。此外，我们还可以限制达到平衡的速度。在本课程视频中，我们将介绍李雅普诺夫函数（Lyapunov Functions）的形式定义，并了解如何在多种情境中应用它们。我们还会研究其不适用的范围，甚至研究使人们不能判断系统是否达到平衡的问题。

[李雅普诺夫函数](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R11Lyapunov.pdf)

第12单元：协调和文化

在本课程视频中，我们将介绍几个有关文化的模型。首先从文化是什么以及文化对社会科学家比较重要的原因开始。在分析部分，我们先从一个非常简单的，称为纯协调博弈的游戏开始。在该博弈中，局中人只有选择相同的行动才算赢。不管他们选择哪个行动，只要他们选择相同的行动便会赢。例如，你选择在公路的左侧开车还是右侧开车并不重要，重要的是你和其他人是否在同一侧开车。随后我们会介绍多人协调博弈的情景并研究文化的出现。在最后的模型中，我们将介绍模型中的一致性和协调性，这些文化同样会出现在现实世界数据中。本单元的阅读材料包括我对协调博弈的说明，以及贝德纳（Bednar）等人的学术论文。在该论文中，你将了解到我们如何使用马尔可夫过程研究模型。本课程还提供了阿克塞尔罗德（Axelrod）的Net Logo模型的链接。

[协调博弈](http://vserver1.cscs.lsa.umich.edu/~spage/ONLINECOURSE/R12CoordinationCulture.pdf)

[贝德纳等人 2010年](http://rss.sagepub.com/content/22/4/407.full.pdf+html)

[Netlogo中的阿克斯德罗文化模型](http://ccl.northwestern.edu/netlogo/models/community/Dissemination%20of%20Culture)

第13单元：路径依赖

在本课程视频中，我们介绍了路径依赖。我们将用到一些简单的瓮模型。其中最著名的是波利亚过程（Polya Process）。这些模型尽管简单，却可以帮助我们剖析路径依赖背后的逻辑。我们还会将路径依赖与递增收益、临界点联系在一起。本单元的阅读材料是我在《政治科学季刊》上发表的论文。

[路径依赖](http://www.cscs.umich.edu/~spage/pathdepend.pdf)

第14单元：网络

在本单元中，我们会介绍网络。我们会讨论网络的形成方式、结构（尤其是网络的一些通用度量）及其功能。我们通常会说网络功能自然生成，这意味着由于网络的自身结构会出现意想不到的功能。本单元的阅读材料是史蒂夫·斯托加茨（Steven Strogatz）的一篇小论文。

[斯托加茨](http://www.nature.com/nature/journal/v410/n6825/full/410268a0.html)

第15单元：随机性和随机游走

在本单元，我们会讨论随机性及其各种来源。接下来会讨论成绩与技能和运气的依赖关系，其中将运气建模为随机性。然后了解基本随机游走模型，该模型应用于有效市场假说，此假说认为市场价格涵盖了所有相关信息，余下的就是随机性。最后，我们将讨论可用于创建竞争模型的有限存储器随机游走模型。本单元的阅读材料是迈克尔·莫布森（Michael Mauboussin）的一篇有关区分技能和运气的论文。

[莫布森：技能与运气](http://www.cscs.umich.edu/~spage/ONLINECOURSE/R15SkillandLuck.pdf)

第16单元：上校赛局

在本单元中，我们将介绍上校赛局。这最早用于战争中研究多线作战。如今被广泛应用于运动、法律、恐怖主义等。我们先讨论上校赛局的基础，进行更深入的分析，然后将上校赛局与上一单元技能运气模型进行对比。本单元的阅读材料是我的一本名为《差异》的书籍的节选以及我跟卡内基梅隆大学的拉塞尔·戈尔曼（Russell Golman）合作编写的一篇论文。你只需要阅读戈尔曼论文的前四页即可。

[《差异》中的上校赛局](http://vserver1.cscs.lsa.umich.edu/~spage/ONLINECOURSE/R16BlottoDiff.pdf)

[戈尔曼的论文：上校赛局](http://www.cscs.umich.edu/~spage/ONLINECOURSE/Blotto.pdf)

第17单元：囚徒困境和集体行动

在本单元中，我们将会介绍囚徒困境、集体行动和公共池塘资源问题。我们会先讨论囚徒困境，并演示个体激励如何催生不良社会后果。然后我们会介绍产生合作的七种方式。瓦克（Nowak）和西格蒙德（Sigmund）的下列论文涵盖了其中的五种。最后，我们将讨论集体行动和公共池塘资源问题，以及如何通过深入仔细的思考来解决这些问题。诺贝尔奖获得者埃莉诺·奥斯特罗姆（Elinor Ostrom）就此著有一篇精彩的文章。

[《斯坦福哲学百科全书》中的囚徒困境](http://www.cscs.umich.edu/~spage/ONLINECOURSE/PrisonersDilemmaStanford.pdf)

[《合作的五种方式》瓦克和西格蒙德 著](http://www.cscs.umich.edu/~spage/ONLINECOURSE/NowakSigmund07.pdf)

[《超越万灵之方》奥斯特罗姆 著](http://www.cscs.umich.edu/~spage/ONLINECOURSE/Ostrom-Panacea.pdf)

第18单元：机制设计：拍卖

在本单元中，我们将会介绍机制设计。我们先研究一些基本问题：如何克服隐藏行动和隐藏信息的问题。然后转到更广泛应用的问题：如何设计拍卖。最后，我们将讨论如何运用机制对公共项目作出决策。本单元的阅读材料包括埃里克·马金斯（Eric Maskin）的一篇文章（他因在机制设计方面的贡献而获得诺贝尔奖）和V.S.撒布汉曼尼（V.S. Subrahmanian）的一些有关拍卖的幻灯片。马金斯的论文读到最后会有些困难。无需深入理解全部内容。掌握大意即可。

[马金斯：《机制设计》](http://www.cscs.umich.edu/~spage/ONLINECOURSE/maskinmechanismdesign.pdf)

[V.S.撒布汉曼尼的拍卖幻灯片](http://www.cscs.umich.edu/~spage/ONLINECOURSE/Auctionslides.pdf)

第19单元：学习：模仿者动态

在本单元中，我们将介绍模仿者动态以及费希尔基本定理。模仿者动态已被用于解释学习和演变。费希尔定理演示了适应速度是如何随变动幅度增加的。最后，我们将介绍如何用六西格玛和偏差缩减来解释费希尔定理和我们得出的结果。本单元的阅读材料很短。费希尔定理的第二篇材料更侧重于技术性。文章均摘自《多样性和复杂性》

[《模仿者方程式》](http://www.cscs.umich.edu/~spage/ONLINECOURSE/replicator.pdf)

[费希尔基本定理](http://vserver1.cscs.lsa.umich.edu/~spage/ONLINECOURSE/fisher.pdf)

第20单元：众多模型思考者：多样性和预测

在最后一个单元中，我们将介绍进行预测时创建智慧人群所用到的能力和多样性的价值。我们会先介绍分类模型和线性模型，以及如何将其用于预测。然后我们会介绍多样性预测定理，该定理提供了有关集体预测工作方式的基本知识。最后，我们会讨论使用多种模型的价值。本单元的阅读材料为多样性预测定理的简短说明。

[多样性预测定理](http://www.cscs.umich.edu/~spage/ONLINECOURSE/prediction.pdf)

先修知识 学生需要能够熟练使用基本代数。不需要微积分方面的知识，但是如果能从概念上理解导数是如何表示某点斜率的话，会对课程的学习非常有帮助。

参考资料 [《差异：多元化的力量如何建立更好的团体、企业、学校和社会（新版）》](http://chggtrx.com/click.track?CID=267582&AFID=301076&ADID=1088031&SID=modelthinking&isbn_ean=9780691138541) 斯科特·E·佩吉 著

The Difference: How the Power of Diversity Creates Better Groups, Firms, Schools, and Societies (New Edition), Scott Page

[《复杂适应系统：社会生活的计算模型简介（普林斯顿复杂性研究）》](http://chggtrx.com/click.track?CID=267582&AFID=301076&ADID=1088031&SID=modelthinking&isbn_ean=9780691127026) 约翰·米勒和斯科特·佩吉 著

Complex Adaptive Systems: An Introduction to Computational Models of Social Life (Princeton Studies in Complexity), John Miller and Scott Page

[《社会科学模型简介》](http://chggtrx.com/click.track?CID=267582&AFID=301076&ADID=1088031&SID=modelthinking&isbn_ean=9780819183811) 珍妮·拉弗和詹姆斯·马奇 著

An Introduction to Models in the Social Sciences, Jean Lave and James March

## 模型分类

Scale Model

Simple Model

Equation Model

Agents-based Model |\_ Micro-simulation(Individual based) |\_ System Dynamics(System view)

## Monty Hall Problem

这是一个令直觉犯错误的问题，我们试图将其用贝叶斯理论解释。

A,B,C三个门，其中只有一个门后有奖品——奥迪S8一辆。我们在第一次选择时，主持人会打开你选中之外的门的其中一扇空门，然后问你要不要更改你的选择。

直觉可能告诉我们：“你选哪个得奖的概率都有1/3，换了也一样，咱不换了。”

嗯，这样不对。

这样想：“假如坚持第一次的选择，那我得奖的概率就是三分之一；如果我没选中，然后我换了，那我一定得奖。我第一次选中的概率是三分之一，没选中的概 率就是三分之二，所以这样就相当于我要是换那个剩着的门那我获奖的概率就是三分之二了。”

对的，把你的选择分成两组，比如D、E，其中D是你选择的门，E是你没选择的那俩门。D后有奖品的概率是1/3，E后有奖品的概率是2/3。**当主持人开空门的时候，E组有奖品概率不变，但只剩下一个选择了，岂不很好，然后这两组门后有奖的概率不变，仍然是1/3和2/3。**

每次选择都是独立事件，我们用基础概率表示一下： 第一次选择：1/3中，2/3不中； 第二次选择：1/2中，1/2不中； 坚持选择：(1/3)\*(1/2)＝1/6中； 更改选择：(2/3)\*(1/2)＝2/6中； 然后坚持和更改的分别中的概率为1/(1+2)和2/(1+2)，就是1/3和2/3。

所以，还是换吧。

用代码看看：

#!/usr/bin/env python
import random

match\_first = 0.00
match\_second = 0.00

for i in range(1,1000):
    print "Game round %i" % i 
    door = \['a', 'b', 'c'\]
    # We choose a random door with prize.
    prize = door\[random.randint(0,2)\]
    # Now player's turn
    player\_choice = door\[random.randint(0,2)\]
    # If player choose the right one at first
    if player\_choice == prize:
        # If player sticks to his/her choice
        match\_first += 1
        # If he change hist mind and we can be sure that player is lost
        print "Stick to the first: %i\\nChange to the second: %i\\nWin on the second thought: %.2f" %(match\_first, match\_second, match\_second/i)
        continue
    # If player choose the wrong one at first
    else:
        # We open a blank door and player changes his mind, then player wins
        match\_second += 1
        print "Stick to the first: %i\\nChange to the second: %i\\nWin on the second thought: %.2f" %(match\_first, match\_second, match\_second/i)
        continue

输出结果：

Game round 999
Stick to the first: 347
Change to the second: 652
Win on the second thought: 0.65

TBD

## 开源交易平台

tradelink [http://code.google.com/p/tradelink/](http://code.google.com/p/tradelink/) Write automated trading systems, connect with 17+ broker APIs 

AIOTrade [http://sourceforge.net/projects/humaitrader](http://sourceforge.net/projects/humaitrader) AIOTrade (formerly Humai Trader Platform) is a free, open source stock technical analysis platform built on pure java. manticore-trader [http://www.manticore-projects.com/](http://www.manticore-projects.com/) manticore-trader is a free and open java software for day trading warants on stocks, currencies and comodities. It includes modules for charting, position and risk management, automatic ordering and system trading. Instruments and quotes of the main financial markets are provided daily 

G-BOT [http://www.datatime.eu/public/gbot/](http://www.datatime.eu/public/gbot/) G-BOT is a public academic project, headed by Prof. Tom Gastaldi (first University of Rome "La Sapienza"). The project is about the study of trading algorithms and fully automated strategies for systematic profitability. Marketcetera [http://trac.marketcetera.org/](http://trac.marketcetera.org/) [http://www.marketcetera.com/](http://www.marketcetera.com/) Marketcetera focuses on building the key trading functions that are common to all organizations, thus freeing our clients to concentrate on proprietary trading algorithms and other specialized software that provide a competitive advantage. 

Merchant of Venice [http://sourceforge.net/projects/mov](http://sourceforge.net/projects/mov) [http://mov.sourceforge.net/](http://mov.sourceforge.net/) MOV is a stock market trading programme that supports portfolio management, charting, technical analysis, paper trading and genetic programming. Venice runs in a graphical user interface with online help and has full documentation. 

EclipseTrader [http://sourceforge.net/projects/eclipsetrader/](http://sourceforge.net/projects/eclipsetrader/) [http://eclipsetrader.sourceforge.net/](http://eclipsetrader.sourceforge.net/) Eclipse Rich Client Platform (RCP) application featuring shares pricing watch, intraday and history charts with technical analysis indicators, level II/market depth view, news watching, and integrated trading. 

JBookTrader [http://code.google.com/p/jbooktrader/](http://code.google.com/p/jbooktrader/) All aspects of trading, such as obtaining market prices, analyzing price patterns, making trading decisions, placing orders, monitoring order executions, and controlling the risk are automated according to the user preferences. JBookTrader is a "sister" project to JSystemTrader. 

Matrex [http://sourceforge.net/projects/matrex/](http://sourceforge.net/projects/matrex/) http://matrex.sourceforge.net/ The perfect desktop tool for mathematical, statistical models and complex calculations. Adapters to matlab, scilab, octave, R. 

OpenGamma [http://www.opengamma.com/](http://www.opengamma.com/) OpenGamma provides technology for financial institutions to improve analytics calculation and delivery to front-office and risk users. Open Java Trading System (Last Update 2010-08-14) http://sourceforge.net/projects/ojts/ http://ojts.sourceforge.net/ The Open Java Trading System (OJTS) is meant to be a common infrastructure to develop (stock) trading systems. There are four parts: gathering of raw data over the internet, recognition of trading signals, a visualization module and trading with banks. 

Joone [http://sourceforge.net/projects/joone/](http://sourceforge.net/projects/joone/) Joone is a neural net framework written in Java(tm). It's composed by a core engine, a GUI editor and a distributed training environment and can be extended by writing new modules to implement new algorithms or architectures starting from base component Data Visualizer (Last Update 2009-07-17) [http://sourceforge.net/projects/dataviews](http://sourceforge.net/projects/dataviews) [http://dataviews.sourceforge.net/](http://dataviews.sourceforge.net/) 

Modular environment for graphical visualization of stock market type data SFL Java Trading System Enviroment (Last Update 2009-07-17) http://sourceforge.net/projects/sfljtse http://www.sflweb.org/index.php?blog=sfljtse Java application built on KISS principle ( Keep It Simple,Stupid ) and its aim is to provide a fast and platform indipendent infrastructure to develop and execute trading systems. 

ActiveQuant (Last Update 2009) [http://www.activequant.org/](http://www.activequant.org/) Somewhat heavy stuff, for proficient programmers only JSystemTrader (Last Update 2009) Developed to work with Interactive Broker’s API, fully automated trading system (ATS) that can trade various types of market securities during the trading day without user monitoring. 

Market Analysis System (Last Update 2009-07-17) [http://sourceforge.net/projects/eiffel-mas](http://sourceforge.net/projects/eiffel-mas) http://eiffel-mas.sourceforge.net/ System for analysis of financial markets using technical analysis. Includes facilities for stock charting and futures charting, as well as automated generation of trading signals based on user-selected criteria. Operates on both daily and intraday data. 

Oropuro trading system (Last Update 2009) [http://sourceforge.net/projects/oropuro](http://sourceforge.net/projects/oropuro) [http://www.oropuro.org](http://www.oropuro.org) Complete technical analysis & trading system, full set of features: retrieve, analyze EOD stocks data; manage multiple portfolios; technical analysis & graphical rendering; neural networks for generation of trading signals; support trader community, 

AlgoTrader [http://code.google.com/p/algo-trader/](http://code.google.com/p/algo-trader/) AlgoTrader is an automated trading system (ATS) that can trade any type of security on any market available through InteractiveBrokers or FIX. All aspects of trading like getting market data, analyzing prices, taking trade decisions, placing orders & tracking executions can be automated. Encog Encog Java and DotNet Neural Network Framework | Heaton Research Encog Ninjatrader Getting Started - Encog Machine Learning Framework Encog is an advanced neural network and machine learning framework. Encog contains classes to create a wide variety of networks, as well as support classes to normalize and process data for these neural networks. Encog trains using multithreaded resilient propagation. Encog can also make use of a GPU to further speed processing time. A GUI based workbench is also provided to help model and train neural networks.

## The first attempt to build an automated trading and signal system

Here's the sketch. [![tm](/blog/images/tm.png)](http://blog.lofyer.org/the-first-attempt-to-build-an-automated-trading-system/tm/)

Source code. [https://github.com/lofyer/trade-model-01](https://github.com/lofyer/trade-model-01)

目前新闻收集已经OK，搜索也已OK，趋势关键字尚未完成。

交易算法学习中，外汇历史收集中，学习策略未完成。

已升级为[quantum-trade-model](https://github.com/lofyer/quantum-trade-model)

对应文章为[数据笔记](http://datanote.readthedocs.io/zh/latest/)

**媒体发布-筛选服务** 官网

公众号

**策略研究-会员服务**

趋势总结（新闻关键字，暗网关键字，地图，pdf报告）

世界指标（政治稳定度，经济稳定度，社会指标）

交易模型（算法实现，托管交易，代码托管PaaS）

**技术博客**

平台文章

Timeline: 2016-7 模型学习、算法学习、平台构建、渠道收集 2016-8 算法学习、平台构建、趋势输出

[示例站点](https://forex.fusionworks.cn) 

<iframe src="https://forex.fusionworks.cn" width="100%" height="600"></iframe>

## 信号交易系统

[![](/blog/images/forexquantum.png)](https://blog.lofyer.org/wp-content/uploads/forexquantum.png)

tbd

## 国家、行业、人物、现象与总结的历史知识降维方法

知识太多，如何归纳。

有行业历史、人物历史、世界（国家）历史，统统降维划到世界历史中，降级元素视作代理，尝试使用ABM建模。

降至平面图形中，形似“蝌蚪头（蝌蚪头（蝌蚪头拖着长长的尾巴）拖着长长的尾巴）拖着长长的尾巴”，蝌蚪头是领域，尾巴是历史，蝌蚪头可以是另一个蝌蚪头的拷贝，蝌蚪头的扩展可以向大可以向小。当遇到这些元素的交集再升维至球形，往中心聚拢。

笔者以国家为最外层蝌蚪头，假如发现外星文化，同样可以再加蝌蚪头将其吃掉。