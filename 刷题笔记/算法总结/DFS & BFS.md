# 深度优先遍历 (DFS) 概述





# 广度优先遍历 (BFS) 概述







# DFS & BFS 问题分类总结

- 岛屿问题
- 二叉树
- 拓扑排序问题





# 岛屿问题

## 岛屿问题总结

[岛屿类问题的通用解法、DFS 遍历框架](https://leetcode-cn.com/problems/number-of-islands/solution/dao-yu-lei-wen-ti-de-tong-yong-jie-fa-dfs-bian-li-/)

## 题目详解

### [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

#### DFS

```C++
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int nr = grid.size();
        if (!nr) return 0;
        int nc = grid[0].size();

        int num_islands = 0;
        for (int r = 0; r < nr; ++r) {
            for (int c = 0; c < nc; ++c) {
                if (grid[r][c] == '1') {
                    ++num_islands;
                    dfs(grid, r, c);
                }
            }
        }

        return num_islands;
    }

private:
    // 传入参数r和c代表当前遍历到的位置的坐标,
    // 每遍历到一个值为'1'的节点, 就进入该函数
    // 递归地去遍历该节点上下左右所有相邻的节点, 并把此次递归遍历过的所有节点值设为'0'
    // 每找到一块陆地, 就把它相邻的陆地全部找到, 所有相邻的陆地就组成一块岛屿
    void dfs(vector<vector<char>>& grid, int r, int c) {
    // 1. 递归结束条件
        // 对于二叉树来说, 遇到空节点就结束, 其实本质是 : 遍历到不能遍历的地方就不遍历了(听起来是废话, 但道理很重要)
        // 那么对于这种网格来说, 结束条件为 :
        // - 如果当前位置超出网格范围, 就return
        // - 如果当前位置遍历过, 就return. (同时避免陷入死循环)

        // 像二叉树一样, 对每个节点递归遍历其左子树和右子树
        // 这种网格的结构, 相当于每个节点有上下左右四个子树要遍历

        // 但有个问题, 刚才刚遍历过的那个节点, 现在再去递归, 递归里面又会来递归现在这个节点, 就会陷入死循环
        // 怎样避免这个问题?
        // 当然是, 把每个遍历过的节点都做个标记, 然后在递归的结束条件里加上判断, 如果遇到遍历过的节点, 就直接return

        if (outOfrange(grid, r, c)) return; // 如果超出网格范围, return
        if (grid[r][c] == '0') return; // 如果遍历过或者本来就不该遍历, return

        


        int nr = grid.size(); // 获取二维网格的行数
        int nc = grid[0].size(); // 列数

        grid[r][c] = '0'; // 先把当前的置0, 这一步可至关重要

        dfs(grid, r - 1, c); // dfs上
        dfs(grid, r + 1, c); // dfs下
        dfs(grid, r, c - 1); // dfs左
        dfs(grid, r, c + 1); // dfs右
    }

    // 用于判断是否超出网格范围
    bool outOfrange(vector<vector<char>>& grid, int r, int c) {
        int nr = grid.size(); // 获取二维网格的行数
        int nc = grid[0].size(); // 列数
        if (r < 0 || r >= nr || c < 0 || c >= nc) return false;
        else return true;
    }
};
```



#### BFS

```C++
```







### [463. 岛屿的周长](https://leetcode-cn.com/problems/island-perimeter/) 





### [695. 岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/)





### [827. 最大人工岛](https://leetcode-cn.com/problems/making-a-large-island/)







# 拓扑排序问题

## 拓扑排序问题总结







## 题目详解

### [207. 课程表](https://leetcode-cn.com/problems/course-schedule/)





### [210. 课程表II](https://leetcode-cn.com/problems/course-schedule-ii/)
