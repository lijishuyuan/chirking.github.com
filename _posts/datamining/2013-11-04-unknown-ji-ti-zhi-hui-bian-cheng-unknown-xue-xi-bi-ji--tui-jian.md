---
layout: post
title: "《集体智慧编程》学习笔记：推荐算法"
description: ""
category: "datamining"
tags: datamining 学习笔记
---
{% include JB/setup %}

# 协同过滤

在推荐算法里，所谓协同过滤，就是通过你对音乐、图书等物品的喜好，找到跟你有相似喜好的人，然后再根据这些人的喜好，找到你有可能喜好的物品，向你推荐。  
其实这跟我们买东西，会去问问看朋友的推荐，是同样的道理。只不过，问朋友时，我们知道，他的喜好可能跟我很相似，而这里，则只能根据大家喜好的历史数据，先去找到相似的人群。  
还有一种办法，就是先根据大家的喜好，确定物品本身的相似程度。然后再直接推荐跟我喜好的物品最相似的物品。这样还有一个好处，就是处理起来比较快，不需要去遍历喜好的人，只需要遍历物品。


# 相似性

既然要找到相似的人，那就必须要有办法判断相似性。相似性有几种判断方法，比如是否都读过同一本书，给某部电影打了几分。  
根据不同的场景和数据，应该选择不一样的方式，比如电影就应该看打的分数，因为很多热门电影大家都看过，只不过大家评价可能有很大差别，看过同样一些电影的人品味可能相差非常大。而音乐就不一样，大家在听音乐之前，就已经有了选择，通常听过的歌越相似，音乐品味也越相似。  
  
既然要比较相似性，就必须对相似性有个量化过程。相似性的量化过程通常转换成数学里的距离公式。典型的距离公式有：  

欧几里得距离，这个比较好理解，可以理解为多维空间里2个点的真实距离。缺点是，在打分数据里面，比如常用的1-5分，每个人对这个分值的理解不一样，比如有些人认为3分是一般，5分是非常好，而有些人则会打1分和3分。实际上2个人的意思是一样的，但是距离算出来却很远，也就是相似性很差。  
皮尔逊方法，这个公式就稍微比较复杂了。基本思想就是，找到一条线，尽可能的靠近所有的点。通过观察这条线，就能判断相似性了。避免了上面说的“夸大分值”的问题。  
曼哈顿距离，这个最好算，就是每个维度的差值的绝对值加起来。欧几里得距离因为对差值做了平方，实际上就是扩大了某些相距比较大的维度的影响。比如对某部电影A打了1分，B打了5分，即使其他电影他们都很一致，距离也会变得很远。欧几里得距离更倾向于扩大坏的影响，而曼哈顿距离对某些维度非常不一致的这种情况并不敏感。  
Jaccard系数又称Tanimoto系数，就是共同点的比例，比如两个人都听过的歌的数量除以两个人一共听过的歌的数量。前面的都用于打分的场景，这个适合有没有都做过的场景。  
其他还有明考斯基距离，切比雪夫距离，公式都很复杂，有空慢慢研究。  

<!--more-->


# 物品量化

既然要推荐，那就要找到被推荐的人最有可能喜欢的物品。这就涉及到一个量化的过程。相当于给物品打分，然后选出打分最高的几个给予推荐。  
那么量化基于的原则是什么呢？从前面讲的协同过滤知道，所谓推荐，其实就是让跟我喜好相似的人来推荐。这里就有2个数值，一个是跟我相似的程度，另一个是这个跟我相似的人对这件物品推荐的程度。

这里可以使用加权平均算法。推荐程度为值，相似程度为权。每个值分别乘以对应的权，然后相加除以所有权的和。基本上就近似与我最有可能对这件物品的推荐程度了。  

为了方便，只需要计算跟我最相似的一部分人就可以了。

# 基于物品过滤

每次推荐时，去找到跟我相似的人，这个过程通常比较耗时。所以，有一种办法是，根据人们的喜欢，算出不同物品间的相似度。  
然后以我对物品的喜欢程度为值，这些物品跟其他物品的相似度为权，进行加权平均。这样来对物品来进行量化，进而进行推荐。 

因为当物品被足够的多人评价或标记喜好之后，物品之间的相似度会趋于稳定。这样我们可以事先算好。这对算法的性能而言非常重要。   


# 判断推荐效果

对效果的判断，无非是线上和线下2种。  
线上直接通过AB Test，通过点击率，购买率等很容易就能对吧出2种算法的优劣。  
线下，可以拿一些用户，把他的购买、评价等记录去掉一部分，然后对他们进行推荐，看推荐的物品有多少刚好是他们购买过或者评价比较高的。


# 关键点

推荐的关键点还是对数据的理解，不同的场景可以使用的数据，应该使用的数据都不一样。比如豆瓣网核心就是评价、打分，而淘宝的核心则是购买。    
还有些数据非常复杂，比如淘宝商品数目非常多，你很难找到跟你同样买过很多同一件商品的人。所以我们不得不通过类目去计算，而类目又有层级的概念。并且通过类目计算会损失很多细节，比如品牌偏好，价格偏好等等，如何在相似度量化里体现出来，需要去思考。  

另一个关键点是对算法的选择，每一种算法都有它的性格。有时候我们拿捏不准，就只能分别去尝试，看到底哪种最适合了，这里经验也会起到很大的作用。





