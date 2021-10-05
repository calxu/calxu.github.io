---
title: '运筹学-cvxpy包解凸优化问题'
date: '2021-10-04'
categories:
  - 运筹学
tags:
  - 运筹学
---



供应链里仓内作业优化问题抽象出来经常是一种凸优化问题，这里记录下用cvxpy包解凸优化问题的demo，方便自己后面复习用。

解决问题：当 `$2x-y^2>=0$`，求x+y的最小值。

```
from cvxpy import *

x = Variable()
y = Variable()

constraints = [2 * x - y ** 2 >= 0]

obj = Minimize( x + y )

prob = Problem(obj, constraints)

prob.solve()


# print(prob.status)
print(prob.value)
print(x.value, y.value)
```

输出结果：

```
-0.49999999933064904
0.4999589681940953 -0.9999589675247443
```

注：这里是python求解精度的问题，四舍五入即可。



结论：这里给出得是最简单的demo，复杂问题可以解化为这些最小模块的简单问题来解决。
