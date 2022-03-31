# 数组

## 删除数组元素的两种方法

### 双指针

普通：

```C++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
		int slow = 0, fast = 0;
        while (fast < nums.size()) {
			while (nums[fast] == val) {
                fast++;
            }
            nums[slow++] = nums[fast++];
        }
        return slow;
    }    
};
```

改进：

因为此题数组元素位置可以改变，可以把一个指针放在左端，另一个放在右端，left遇到要删除的就直接把right复制过来，然后right位置减一，复制过来如果还是要删除的就接着复制，直到不等于val，然后left++

```C++
```







### erase方法

这种方法实现起来比较简单，但erase时间复杂度为O(n)，每删除一个元素都要把后面的全部向前覆盖，而双指针是一边删除一边覆盖，只需覆盖一轮。

```C++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        vector<int>::iterator it = nums.begin();
        while (it != nums.end()) {
            if (*it == val) {
                it = nums.erase(it);
            } else {
                it++;
            }
        }
        return nums.size();
    }
};
```



## 二分查找



## 滑动窗口

## 双指针

### 一前一后

#### 删除数组元素

后面的指向当前要填入的位置，前面的去找可以填的数字

#### 删除链表倒数第N个元素

前面的指针先走N步，后面的再指向链表头，然后同时前进

### 一快一慢

#### 寻找环形链表入口

兔子每次走两步，小乌龟每次走一步。兔子套圈追上小乌龟以后歇着不动了，此时老乌龟从起点出发来接小乌龟，两只乌龟同时前进，最后老乌龟在环入口处接到了小乌龟。

### 一左一右

### 一 A 一 B

#### 相交链表

一个指向链表A，一个指向链表B。A指针遍历完链表A就移到链表B开头，B指针遍历完链表B就移到链表A开头。相当于把两个长度不等的链表拼接起来，A + B = B + A，同时移动，最后相交处必对齐。

