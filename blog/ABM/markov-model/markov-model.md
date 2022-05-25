---
title: "Markov model"
date: "2015-03-23"
categories: 
  - "abm"
---

马尔可夫模型是个很有意思的模型，记住两个示例。 学生上课注意力：Alert:Bored 国家体制：Free:Partly Free:Not Free

          Alert(t)  Bored(t)
Alert(t+1)  0.8      0.25       P          P
                            X         = 
Bored(t+1)  0.2      0.75      (1-P)     （1-P）

So 0.8xP + 0.25x(1-P) = P,
we got P=5/9, finally 5/9 of the students will be alert.
