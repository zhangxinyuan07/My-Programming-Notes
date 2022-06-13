# [快速幂](https://leetcode.cn/problems/powx-n/)

注意题目帮你约定了数据的范围, 所以不需要考虑溢出的问题

-   $-100.0 < x < 100.0$
-   $-2^{31} <= n <= 2^{31}-1$
-   $-10^4 <= x^n <= 10^4$

```C++
class Solution
{
public:
    double quickMul(double x, long long N) {
        // 下面的while循环里如果N是奇数的时候， N /= 2 就会舍去一次, 即少乘了一次multiplier
        // 用extra来保存所有漏掉的multiplier
        double extra = 1.0; 
        double multiplier = x;
        if (N == 0 || x == 1.0) return 1.0;
        while (N != 1) {
            if (N % 2 == 1) extra *= multiplier;
            
            multiplier *= multiplier;
            N /= 2;
        }
        return multiplier * extra;
    }

    double myPow(double x, int n)
    {
        long long N = n; // 不这样会溢出
        return N >= 0 ? quickMul(x, N) : 1.0 / quickMul(x, -N);
    }
};
```



# 快速乘







# 两数相除







# [斐波那契数列(矩阵快速幂方法)](https://leetcode.cn/problems/fei-bo-na-qi-shu-lie-lcof/)





# [剪绳子II](https://leetcode.cn/problems/jian-sheng-zi-ii-lcof/)

题目要求 : 答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

这道题和[剪绳子](https://leetcode.cn/problems/jian-sheng-zi-lcof/)的区别就是 : 计算幂的时候可能会超范围, 需要在求幂过程中不断取模, 那么这个幂函数就得自己写.

这个幂函数当然得用[快速幂](#[快速幂])去写, 但和上面的快速幂不一样, 要考虑取余的问题!!!

这道题很难一次写出没问题的代码!!!

```C++
// 这道题太容易出错了!!!

class Solution {
    int MOD = 1000000007;
public:
    int cuttingRope(int n) {
        if (n <= 3) {
            return n - 1;
        }
        
        int quotient = n / 3;
        int remainder = n % 3;
        if (remainder == 0) {
            return helper(3, quotient, 1);
        } else if (remainder == 1) {
            return helper(3, quotient - 1, 4); // 这里quotient-1是因为最后长度为4的时候拆分成3和1还不如不拆
        } else {
            return helper(3, quotient, 2);
        }
    }

    int helper(int a, int b, int c) {
        long ans = 1;

        ans *= quickMul(a, b);

        ans = (ans * c) % MOD;
        return (int)ans;
    }

    int quickMul(int a, int b) {
        if (b == 0 || a == 1) return 1; // 这一步要判断啊!
        int extra = 1;
        long multiplier = a; // 这里还必须用long, 要当心啊!
        while (b != 1) {
            if (b % 2 == 1) extra = (extra * multiplier) % MOD;

            // 这里可不能写成 multiplier *= multiplier % MOD, 和下面的意思不一样啊!
            multiplier = (multiplier * multiplier) % MOD; 
            
            b /= 2;
        }
        int ans = (extra * multiplier) % MOD;
        return ans;
    }
};
```





