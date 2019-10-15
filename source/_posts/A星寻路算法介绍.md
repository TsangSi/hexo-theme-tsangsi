---
title: "[游戏开发] A星寻路算法介绍"
catalog: true
toc_nav_num: true
date: 2016-1-30 16:00:00
subtitle: ""
header-img: "/img/article_header/article_header.png"
tags:
- 游戏开发
catagories:
- 游戏开发
updateDate: 2016-1-30 16:00:00
top: 1

---

你是否在做一款游戏的时候想创造一些怪兽或者游戏主角，让它们移动到特定的位置，避开墙壁和障碍物呢？

如果是的话，请看这篇教程，我们会展示如何使用A星寻路算法来实现它！

在网上已经有很多篇关于A星寻路算法的文章，但是大部分都是提供给已经了解基本原理的高级开发者的。

本篇教程将从最基本的原理讲起。我们会一步步讲解A星寻路算法，幷配有很多图解和例子。

不管你使用的是什么编程语言或者操作平台，你会发现本篇教程很有帮助，因为它在非编程语言的层面上解释了算法的原理。稍后，会有一篇教程，展示如何在Cocos2D iPhone 游戏中实现A星算法。

现在找下到达一杯咖啡因饮料和美味的零食的最短路径，开始吧！:]

# 一只探路猫
 

让我们想象一下，有一款游戏，游戏中一只猫想要找到获取骨头的路线。

“为什么会有一只猫想要骨头？！”你可能会这么想。在本游戏中， 这是一只狡猾的猫，他想捡起骨头给狗，以防止被咬死！:]

现在想像一下下图中的猫想找到到达骨头的最短路径：

This cat just wants someone to throw him a bone!

不幸的是，猫不能直接从它当前的位置走到骨头的位置，因为有面墙挡住了去路，而且它在游戏中不是一只幽灵猫！

游戏中的猫同样懒惰，它总是想找到最短路径，这样当他回家看望它的女朋友时不会太累:-)

但是我们如何编写一个算法计算出猫要选择的那条路径呢？A星算法拯救了我们！

 

# 简化搜索区域
 

寻路的第一步是简化成容易控制的搜索区域。

怎么处理要根据游戏来决定了。例如，我们可以将搜索区域划分成像素点，但是这样的划分粒度对于我们这款基于方块的游戏来说太高了（没必要）。

作为代替，我们使用方块（一个正方形）作为寻路算法的单元。其他的形状类型也是可能的（比如三角形或者六边形），但是正方形是最简单并且最适合我们需求的。

像那样去划分，我们的搜索区域可以简单的用一个地图大小的二维数组去表示。所以如果是25*25方块大小的地图，我们的搜索区域将会是一个有625 个正方形的数组。如果我们把地图划分成像素点，搜索区域就是一个有640，000个正方形的数组了（一个方块是32*32像素）！

现在让我们基于目前的区域，把区域划分成多个方块来代表搜索空间（在这个简单的例子中，7*6个方块 = 42 个方块）：

Dividing the maze into a tile-based search area

 

# Open和Closed列表
 

既然我们创建了一个简单的搜索区域，我们来讨论下A星算法的工作原理吧。

除了懒惰之外，我们的猫没有好的记忆力，所以它需要两个列表：

一个记录下所有被考虑来寻找最短路径的方块（称为open 列表）
一个记录下不会再被考虑的方块（成为closed列表）
猫首先在closed列表中添加当前位置（我们把这个开始点称为点 “A”）。然后，把所有与它当前位置相邻的可通行小方块添加到open列表中。

下图是猫在某一位置时的情景（绿色代表open列表）:
Adding adjacent tiles from the start position to the open list

现在猫需要判断在这些选项中，哪项才是最短路径，但是它要如何去选择呢？

在A星寻路算法中，通过给每一个方块一个和值，该值被称为路径增量。让我们看下它的工作原理！

# 路径增量
 

我们将会给每个方块一个G+H 和值：

G是从开始点A到当前方块的移动量。所以从开始点A到相邻小方块的移动量为1，该值会随着离开始点越来越远而增大。
H是从当前方块到目标点（我们把它称为点B，代表骨头！）的移动量估算值。这个常被称为探视，因为我们不确定移动量是多少 – 仅仅是一个估算值。
你也许会对“移动量”感兴趣。在游戏中，这个概念很简单 – 仅仅是方块的数量。

然而，在游戏中你可以对这个值做调整。例如：

如果你允许对角线移动，你可以针对对角线移动把移动量调得大一点。
如果你有不同的地形，你可以将相应的移动量调整得大一点 – 例如针对一块沼泽，水，或者猫女海报:-)
这就是大概的意思 – 现在让我们详细分析下如何计算出G和H值。

# 关于G值
 

G是从开始点A到达当前方块的移动量（在本游戏中是指方块的数目）。

为了计算出G的值，我们需要从它的前继（上一个方块）获取，然后加1。所以，每个方块的G值代表了从点A到该方块所形成路径的总移动量。

例如，下图展示了两条到达不同骨头的路径，每个方块都标有它的G值：
An illustration of the G variable in the A* Pathfinding Algorithm

# 关于H值
H值是从当前方块到终点的移动量估算值（在本游戏中是指方块的数目）。

移动量估算值离真实值越接近，最终的路径会更加精确。如果估算值停止作用，很可能生成出来的路径不会是最短的（但是它可能是接近的）。这个题目相对复杂，所以我们不会再本教程中讲解，但是我在教程的末尾提供了一个网络链接，对它做了很好的解释。

为了让它更简单，我们将使用“曼哈顿距离方法”（也叫“曼哈顿长”或者“城市街区距离”），它只是计算出距离点B，剩下的水平和垂直的方块数量，略去了障碍物或者不同陆地类型的数量。

例如，下图展示了使用“城市街区距离”，从不同的开始点到终点，去估算H的值（黑色字）：
An illustration of the H variable in the A* pathfinding algorithm with the Manhattan algorithm

# A星算法
 

既然你知道如何计算每个方块的和值（我们将它称为F，等于G+H),  我们来看下A星算法的原理。

猫会重复以下步骤来找到最短路径：

将方块添加到open列表中，该列表有最小的和值。且将这个方块称为S吧。
将S从open列表移除，然后添加S到closed列表中。
对于与S相邻的每一块可通行的方块T：
如果T在closed列表中：不管它。
如果T不在open列表中：添加它然后计算出它的和值。
如果T已经在open列表中：当我们使用当前生成的路径到达那里时，检查F 和值是否更小。如果是，更新它的和值和它的前继。
如果你对它的工作原理还有点疑惑，不用担心 – 我们会用例子一步步介绍它的原理！:]

猫的路径
让我们看下我们的懒猫到达骨头的行程例子。

在下图中，我根据以下内容，列出了公式F = G + H 中的每项值：

F（方块的和值）：左上角
G（从A点到方块的移动量）：左下角
H（从方块到B点的估算移动量): 右下角
同时，箭头指示了到达相应方块的移动方向。

最后，在每一步中，红色方块表示closed列表，绿色方块表示open列表。

好的，我们开始吧！

## 第一步

第一步，猫会确定相对于开始位置（点A）的相邻方块，计算出他们的F和值，然后把他们添加到open列表中：
A* Example Part 1

你会看到每个方块都列出了H值（有两个是6，一个是4）。我建议根据“城市街区距离”去计算方块的相关值，确保你理解了它的原理。

同时注意F值（在左上角）是G（左下角）值和H（右下脚）值的和。
## 第二步

在第二步中，猫选择了F和值最小的方块，把它添加到closed列表中，然后检索它的相邻方块的相关数值。
A* Example Part 2

现在你将看到拥有最小增量的是F值为4的方块。猫尝试添加所有相邻的方块到open列表中（然后计算他们的和值），除了猫自身的方块不能添加以外（因为它已经被添加到了closed列表中）或者它是墙壁方块（因为它不能通行）。

注意被添加到open列表的两个新方块，他们的G值都增加了1，因为他们现在离开始点有2个方块远了。你也许需要再计算下“城市街区距离”以确保你理解了每个新方块的H值。
## 第三步

再次，我们选择了有最小F和值（5）的方块，继续重复之前的步骤：
A* Example Part 3

现在，只有一个可能的方块被添加到open列表中了，因为已经有一个相邻的方块在close列表中，其他两个是墙壁方块。

## 第四步

现在我们遇到了一个有趣的情况。正如你之前看到的，有4个方块的F和值都为7 – 我们要怎么做呢？！

有几种解决方法可以使用，但是最简单（快速）的方法是一直跟着最近被添加到open列表中的方块。现在继续沿着最近被添加的方块前进。
A* Example Part 4

这次有两个可通过的相邻方块了，我们还是像之前那样计算他们的和值。
## 第五步

接着我们选择了最小和值（7）的方块，继续重复之前的步骤：
A* Example Part 5

我们越来越接近终点了！

## 第六步

你现在训练有素了！我打赌你能够猜出下一步是下面这样子了：
A* Example Part 6

我们差不多到终点了，但是这次你看到有两条到达骨头的最短路径提供给我们选择：
Two shortest paths to the bone

在我们的例子中，有两条最短路径：

1-2-3-4-5-6
1-2-3-4-5-7
It doesn’t really matter which of these we choose, it comes down to the actual implementation in code.

选择哪一条其实没关系，现在到了真正用代码实现的时候了。

## 第七步

让我们从其中一块方块，再重复一遍步骤吧：
A* Example Part 7

啊哈，骨头在open列表中了！
## 第八步

现在目标方块在open列表中了，算法会把它添加到closed列表中：
A* Example Part 8

然后，算法要做的所有事情就是返回，计算出最终的路径！
A* Example Part 9

#一只有远见的猫
在上面的例子中，我们看到当猫在寻找最短路径时，它经常选择更好的方块（那个在它的未来最短路径上的方块）- 好像它是一只有远见的猫！

但是如果猫是盲目的，并且总是选择第一个添加到它的列表上的方块，会发生什么事情？

下图展示了所有在寻找过程中会被使用到的方块。你会看到猫在尝试更多的方块，但是它仍然找到了最短路径（不是之前的那条，而是另一条等价的）：
What would happen if the cat wasn't so smart...

图中的红色方块不代表最短路径，它们只是代表在某个时候被选择为“S”的方块。

我建议你看着上面的图，并且尝试过一遍步骤。这次无论你看到哪个相邻的方块，都选择“最坏”的方式去走。你会发现最后还是找到了最短路径！

所以你可以看到跟随一个“错误的”方块是没有问题的，你仍然会在多次重复尝试后找到最短路径。

所以在我们的实现中，我们会按照以下的算法添加方块到open列表中：

相邻的方块会返回这些顺序: 上面/左边/下面/右边。
当所有的方块都有相同的和值后，方块会被添加到open列表中（所以第一个被添加的方块是第一个被猫挑选的）。
下面是从原路返回的示意图：
The cat finding the shortest path, even after some wrong turns

最短的路径是从终点开始，一步步返回到起点构成的（例子：在终点我们可以看到箭头指向右边，所以该方块的前继在它的左边）。

总的来说，我们可以用下面的伪代码，合成猫的寻找过程。这是Objective-C写的，但是你可以用任何的语言去实现它：

	[openList add:originalSquare]; // start by adding the original position to the open list
	do {
		currentSquare = [openList squareWithLowestFScore]; // Get the square with the lowest F score
	 
		[closedList add:currentSquare]; // add the current square to the closed list
		[openList remove:currentSquare]; // remove it to the open list
	 
		if ([closedList contains:destinationSquare]) { // if we added the destination to the closed list, we've found a path
			// PATH FOUND
			break; // break the loop
		}
	 
		adjacentSquares = [currentSquare walkableAdjacentSquares]; // Retrieve all its walkable adjacent squares
	 
		foreach (aSquare in adjacentSquares) {
	 
			if ([closedList contains:aSquare]) { // if this adjacent square is already in the closed list ignore it
				continue; // Go to the next adjacent square
			}
	 
			if (![openList contains:aSquare]) { // if its not in the open list
	 
				// compute its score, set the parent
				[openList add:aSquare]; // and add it to the open list
	 
			} else { // if its already in the open list
	 
				// test if using the current G score make the aSquare F score lower, if yes update the parent because it means its a better path
	 
			}
		}
	 
	} while(![openList isEmpty]); // Continue until there is no more available square in the open list (which means there is no path)
 

[转自莫水千流](https://www.cnblogs.com/zhoug2020/p/3468167.html "莫水千流")
# Have fun ^_^ 
---
<!-- Place this tag in your head or just before your close body tag. -->
<script async defer src="https://buttons.github.io/buttons.js"></script>
<!-- Place this tag where you want the button to render. -->

Please <a class="github-button" href="https://github.com/tsangsi/hexo-theme-tsangsi" data-icon="octicon-star" aria-label="Star tsangsi/hexo-theme-tsangsi on GitHub">Star</a> this Project if you like it! <a class="github-button" href="https://github.com/tsangsi" aria-label="Follow @tsangsi on GitHub">Follow</a> would also be appreciated!
Peace!
