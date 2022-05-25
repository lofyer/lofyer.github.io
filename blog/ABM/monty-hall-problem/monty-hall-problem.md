---
title: "Monty Hall Problem"
date: "2016-02-21"
categories: 
  - "abm"
---

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
