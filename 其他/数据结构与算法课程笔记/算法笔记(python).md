# 递归实例

```python
# 递归计算一个数组元素之和


# 定义递归函数
def listsum(li):
    # 基线条件：数组为空,则返回0
    if len(li) == 0:
        return 0
    # 递归条件:如果数组非空，则返回第一个元素与其它元素之和，这里其它元素之和再次调用递归函数本身
    else:
        return li[0] + listsum(li[1:])


print(listsum([2, 4, 6]))
```











#  广度优先搜索实例

用于解决无权图最短路径问题

## 问题描述

如图，你有这样一个关系网，你要从中找出关系最近的芒果销售商

![image-20210421090017555](/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/数据结构与算法笔记/算法笔记(python).assets/image-20210421090017555.png)

## 问题分析

**算法：**

<img src="/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/数据结构与算法笔记/算法笔记(python).assets/image-20210421090822772.png" alt="image-20210421090822772" style="zoom:50%;" />

## 代码实现

```python
# 广度优先搜索示例
from collections import deque  # 用于创建队列
# 队列是先入先出，栈是后入先出


# 用字典来表示一个图（图由节点和边组成）
graph = {"you": ["alice", "bob", "claire"],
         "bob": ["anuj", "peggy"],
         "alice": ["peggy"],
         "claire": ["thom", "jonny"],
         "anuj": [], "peggy": [], "thom": [], "jonny": []}

# 用最后一个字母是否为m来简单判断是否为芒果销售商


def person_is_seller(name):
    return name[-1] == 'm'


def search(name):
    search_queue = deque()  # 用deque()函数创建一个队列
    search_queue += graph[name]  # 把传入的字符串在字典中对应的值压入队列
    searched = []  # 用于记录已经被搜索过的人，防止无限循环
    while search_queue:
        person = search_queue.popleft()  # 弹出队列中的第一个人赋值给person
        if person not in searched:  # 判断
            if person_is_seller(person):
                print(person + " is a mango seller!")
                return True
            else:
                search_queue += graph[person]  # 如果不是，则将他的朋友也压入队列
                searched.append(person)  # 标记为已搜索
    return False


search("you")
```



## 总结

**运行时间：**

如果你在你的整个人际关系网中搜索芒果销售商，就意味着你将沿每条边前行(记住，边是 从一个人到另一个人的箭头或连接)，因此运行时间至少为O(边数)。

你还使用了一个队列，其中包含要检查的每个人。将一个人添加到队列需要的时间是固定的， 即为O(1)，因此对每个人都这样做需要的总时间为O(人数)。所以，广度优先搜索的运行时间为 O(人数 + 边数)，这通常写作O(V + E)，其中V为顶点(vertice)数，E为边数。



- 广度优先搜索指出是否有从A到B的路径。
- 如果有，广度优先搜索将找出最短路径。
- 面临类似于寻找最短路径的问题时，可尝试使用图来建立模型，再使用广度优先搜索来解决问题。
- 有向图中的边为箭头，箭头的方向指定了关系的方向，例如，rama→adit表示rama欠adit钱。 
- 无向图中的边不带箭头，其中的关系是双向的，例如，ross - rachel表示“ross与rachel约会，而rachel也与ross约会”。

- 队列是先进先出(FIFO)的。
- 栈是后进先出(LIFO)的。
- 你需要按加入顺序检查搜索列表中的人，否则找到的就不是最短路径，因此搜索列表必须是队列。
- 对于检查过的人，务必不要再去检查，否则可能导致无限循环。









# 迪克斯特拉算法实例

用于解决带权(非负)图最短路径问题

## 问题描述

如图，找出从起点到终点的最小开销路径

<img src="/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/数据结构与算法笔记/算法笔记(python).assets/image-20210421084755526.png" alt="image-20210421084755526"  />

## 问题分析

解决该问题需要如下三个散列表：

![image-20210421084953274](/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/数据结构与算法笔记/算法笔记(python).assets/image-20210421084953274.png)

- graph用于记录有向图
- costs用于记录分别到达所有子节点的最小开销 
- parents用于记录实现最小开销所需经的父节点
- 随着算法的进行，将不断更新costs和parents



**算法**：

<img src="/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/数据结构与算法笔记/算法笔记(python).assets/image-20210421085542559.png" alt="image-20210421085542559"  />

## 代码实现

```python
# 迪克斯特拉算法实例

# 1. 需要三个散列表：graph、costs和parents
# 记录节点所有邻居及其开销（记录整张图）
graph = {
    'start': {'a': 6, 'b': 2},
    'a': {'fin': 1}, 'b': {'a': 3, 'fin': 5},
    'fin': {}
}

# 记录去往所有子节点的最小开销
infinity = float("inf")  # 表示无穷大
costs = {'a': 6, 'b': 2, 'fin': infinity}

# 记录要实现最小开销到达邻居所经的父节点
parents = {'a': 'start', 'b': 'start', 'fin': None}

# 记录处理过的节点
processed = []


# 2. 算法实现
def find_lowest_cost_node(costs):
    lowest_cost = infinity
    lowest_cost_node = None
    for node in costs:  # 遍历所有节点
        cost = costs[node]
        if cost < lowest_cost and node not in processed:  # 如果当前节点的开销更低且未处理过，
            lowest_cost = cost  # 就将其视为开销最低的节点
            lowest_cost_node = node
    return lowest_cost_node


node = find_lowest_cost_node(costs)  # 在未处理的节点中找出开销最小的节点
while node is not None:  # 只要还有未处理的节点就继续循环
    cost = costs[node]
    neighbors = graph[node]
    for n in neighbors.keys():  # 遍历当前节点的所有邻居（子节点）
        new_cost = cost + neighbors[n]  # 记录去往当前节点的邻居的开销
        if costs[n] > new_cost:  # 如果经过当前节点前往该邻居更近，
            costs[n] = new_cost  # 就更新该邻居的开销
            parents[n] = node  # 同时将该邻居的父节点设置为当前节点
    processed.append(node)  # 将当前节点标记为已处理过
    node = find_lowest_cost_node(costs)  # 找出下一个要处理的节点，并循环

print(costs)
print(parents)
```



## 总结

- 广度优先搜索用于在非加权图中查找最短路径
- 狄克斯特拉算法用于在加权图中查找最短路径
- 仅当权重为正时狄克斯特拉算法才管用
- 如果图中包含负权边，请使用贝尔曼-福德算法
- 狄克斯特拉算法背后的**关键理念**:找出图中最便宜的节点，并确保没有到该节点的更便宜的路径
- 在无向图中，每条边都是一个环，狄克斯特拉算法只适用于有向无环图(directed acyclic graph，DAG)

![Dijkstra Animation.gif](/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/数据结构与算法笔记/算法笔记(python).assets/220px-Dijkstra_Animation.gif)

<img src="/Users/zhangxinyuan/Library/Application Support/typora-user-images/image-20210421092936200.png" alt="image-20210421092936200" style="zoom:50%;" />









# 贪婪算法实例

贪婪算法:每步都采取最优的做法。用专业术语说，就是你每步都选择**局部最优解**，最终得到的就是全局最优解。

贪婪算法的**优点**—— 并非在任何情况下都行之有效，但它**易于实现，简单易行**



贪婪算法不一定总能得到最优解，但**在有些情况下，完美是优秀的敌人**。有时候，你只需找到一 个能够大致解决问题的算法，此时贪婪算法正好可派上用场，因为它们实现起来很容易，得到的结果又与正确结果相当接近。

## 问题描述

假设你办了个广播节目，要让全美50个州的听众都收听得到。为此，你需要决定在哪些广播台播出。在每个广播台播出都需要支付费用，因此你力图在尽可能少的广播台播出。现有广播台名单如下：

<img src="/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/数据结构与算法笔记/算法笔记(python).assets/image-20210422195842978.png" alt="image-20210422195842978" style="zoom:50%;" />

每个广播台都覆盖特定的区域，不同广播台的覆盖区域可能重叠。

<img src="/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/数据结构与算法笔记/算法笔记(python).assets/image-20210422195928383.png" alt="image-20210422195928383" style="zoom:50%;" />

## 问题分析

若用精确算法，遍历所有可能的广播台组合的集合，则运行时间为O(2^n)。



此时**需要贪婪算法以得到近似解：**

1. 选出这样一个广播台，即它覆盖了最多的未覆盖州。即便这个广播台覆盖了一些已覆盖 的州，也没有关系

2. 重复第一步，直到覆盖了所有的州



判断近似算法优劣的标准如下：

1. 速度
2. 近似解与最优解的接近程度



在这个例子中，贪婪算法运行时间为O(n^2)



## 代码实现

```python
# 贪婪算法实例


states_needed = {'mt', 'wa', 'or', 'id', 'nv', 'ut', 'ca', 'az'}  # 需要覆盖的州,该集合会不断删除
stations = {'kone': {'id', 'nv', 'ut'}, 'ktwo': {'mt', 'id', 'wa'},
            'kthree': {'nv', 'or', 'ca'}, 'kfour': {'nv', 'ut'},
            'kfive': {'az', 'ca'}
           }  # 每个广播台分别覆盖的州

final_stations = set()  # 用于存储最终选择的广播台

while states_needed:
    best_station = None  # 覆盖了最多未覆盖州的广播台
    states_covered = set()  # 该广播台覆盖的未覆盖州
    for station, states_for_station in stations.items():  # 遍历字典找到当前覆盖最多未覆盖州的广播台
        covered = states_needed & states_for_station  # 取剩下的需要覆盖的州与当前广播可以覆盖的州的交集
        if len(covered) > len(states_covered):
            best_station = station  # for循环结束后，states_covered就是局部最优解
            states_covered = covered
    final_stations.add(best_station)  # 局部最优解放入最后结果
    states_needed -= states_covered  # 去掉已覆盖的州

print(final_stations)
```



## 总结

集合覆盖问题和旅行商问题的共同点：要计算所有的解，并从中挑选出最优解。

这两个问题都属于**NP完全问题**，NP完全问题的简单定义是：以难解著称的问题。



NP完全问题无处不在，若能判断NP完全问题，就不必寻找最完美的解决方案，使用近似算法即可。

但要判断是不是NP完全问题很难，没有确定的标准 ，但有一些规律：

- 元素较少时算法的运行速度非常快，但随着元素数量的增加，速度会变得非常慢。
- 涉及“所有组合”的问题通常是NP完全问题。
- 不能将问题分成小问题，必须考虑各种可能的情况。这可能是NP完全问题。
- 如果问题涉及序列(如旅行商问题中的城市序列)且难以解决，它可能就是NP完全问题。
- 如果问题涉及集合(如广播台集合)且难以解决，它可能就是NP完全问题。
- 如果问题可转换为集合覆盖问题或旅行商问题，那它肯定是NP完全问题。





贪婪算法寻找局部最优解，企图以这种方式获得全局最优解。

面临NP完全问题时，最佳的做法是使用近似算法。

贪婪算法易于实现、运行速度快，是不错的近似算法。



# 动态规划

- 使用动态规划时，要么考虑拿走整件商品，要么不拿，没法判断该不该拿走商品的一部分（这种情况用贪婪算法即可轻松解决）
- 如果互相依赖，即选了一个其他的权重会变，这种情况无法使用动态规划建模。**动态规划仅当每个子问题都是离散的，即不依赖于其他子问题时才管用。**
- 每种动态规划都涉及网格，单元格中的值通常就是要优化的值，每个单元格都是一个子问题，考虑如何将问题分成子问题有助于找出网格的坐标轴。
- 有些算法并非精确的解决步骤，而只是帮你理清思路的框架。动态规划就是这样一种算法，没有通用的解决问题的公式。

