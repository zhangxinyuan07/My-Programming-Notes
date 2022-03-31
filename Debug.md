# Debug记录

## error: reference to non-static member function must be called

```C++
class Solution {
    bool cmp(string& s1, string& s2) { // 错误, 应声明为static
        return s1.size() < s2.size();
    }  
public:
    int longestStrChain(vector<string>& words) {
        sort(words.begin(), words.end(), cmp);
        ...
    }
};
```

如果谓词 `cmp` 不声明为静态成员函数则会引发此异常

问题的原因其实就是函数参数不匹配的问题。因为我们普通的成员函数都有一个隐含的this指针，表面上看我们的谓词函数 `cmp` 只有两个参数，但实际上它有三个参数，而我们调用sort（）排序函数的时候只需要用到两个参数进行比较，所以就出现了形参与实参不匹配的情况（函数有三个形参，但是只输入了两个实参）。

所以，解决办法就是把谓词函数 `cmp` 定义为static成员函数。
