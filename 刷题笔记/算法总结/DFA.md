# 状态机





# 题目总结

## [8. 字符串转换整数](https://leetcode-cn.com/problems/string-to-integer-atoi/)

![fig1](DFA.assets/fig1.png)

```C++
class Automaton {
    string state = "start";
    unordered_map<string, vector<string>> table = {
        {"start", {"start", "signed", "in_number", "end"}},
        {"signed", {"end", "end", "in_number", "end"}},
        {"in_number", {"end", "end", "in_number", "end"}},
        {"end", {"end", "end", "end", "end"}}
    };

    int get_col(char c) {
        if (isspace(c)) return 0;
        if (c == '+' or c == '-') return 1;
        if (isdigit(c)) return 2;
        return 3;
    }

public:
    int sign = 1;
    long long ans = 0;

    void get(char c) {
        state = table[state][get_col(c)];
        if (state == "in_number") {
            ans = ans * 10 + c - '0';
            ans = sign == 1 ? min(ans, (long long)INT_MAX) : min(ans, -(long long)INT_MIN);
        } else if (state == "signed") {
            sign = c == '+' ? 1 : -1;
        }
    }
};


class Solution {
public:
    int myAtoi(string s) {
        Automaton automation;
        for (char c : s) {
            automation.get(c);
        }
        return automation.sign * automation.ans;
    }
};
```





## [65. 有效数字](https://leetcode-cn.com/problems/valid-number/)





## [393. UTF-8编码验证](https://leetcode-cn.com/problems/utf-8-validation/)

