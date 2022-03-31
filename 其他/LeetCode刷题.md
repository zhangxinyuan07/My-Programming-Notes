# 两数之和

题目：给定一个整数数组 $nums$ 和一个目标值 $target$，请你在该数组中找出和为目标值的那两个整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例:
给定 `nums = [2, 7, 11, 15], target = 9`
因为 `nums[0] + nums[1] = 2 + 7 = 9`
所以返回 `[0, 1]`



## 解法

### **暴力法**

用双重for循环穷举所有可能性直到找出答案：

伪代码：

<img src="E:/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/刷题计划/LeetCode刷题笔记.assets/image-20210621145305717.png" alt="image-20210621145305717"  />

暴力法时间复杂度为 $O(n^2)$，需要改进

### **哈希表法**

建立一个哈希表映射该数组，只需遍历两次数组，用target值减去数组当前数值，然后去哈希表里找是否存在这个值，若存在则返回索引

时间复杂度为$O(n)$​

上述方法还可以再改进，只需遍历一次数组即可：  





## 代码实现

### **暴力法**

#### C++：

```C++
#include <iostream>
using namespace std;
#include <vector>

//这一部分是关键
class Solution{
public:
    vector<int> twoSum(vector<int>& nums, int target)//返回值是一个vector容器的成员函数
    {
        int i,j;
        for(i=0;i<nums.size();i++)
        {
            for(j=i+1;j<nums.size();j++)
            {
                if(nums[i]+nums[j]==target)
                {
                    return {i,j};
                }
            }
        }
        return {i,j};
    };
};

//下面不重要，是用来测试的
void func(int i){cout << i << endl;}//用于打印最后结果

int main(){
    vector<int> vec1={6,8,10,3};
    int target=9;
    Solution s;//实例化求解两数和问题的对象

    vector<int> vec2= s.twoSum(vec1,target);//用vec2来接收结果

    for_each(vec2.begin(),vec2.end(),func);

    return 0;
}

```

#### Python：

```python
# Time Complexity: O(N^2)
# Space Complexity: O(1)

class Solution:
    def twosum(self, nums: list[int], target: int) -> list[int]:
        result = []
        for i in range(0, len(nums)):
            for j in range(i+1, len(nums)):
                if nums[i] + nums[j] == target:
                    result.append(i)
                    result.append(j)
                    return result
```







### **哈希表法**

#### C++:

```C++
#include <iostream>
using namespace std;
#include <vector>
#include <map>

class Solution{
public:
    vector<int> twoSum(vector<int>& nums, int target)//返回值是一个vector容器的成员函数
    {
        map<int,int> mp; //初始化哈希表
        vector<int> vec(2,-1); //初始化一个长度为2，值都为-1的vector容器用于接收结果
        for (int i = 0; i < nums.size(); ++i)
        {
            int diff = target - nums[i];
            if (mp.count(diff))
            {
                vec[0] = mp[diff];
                vec[1] = i;
            }
            mp.insert(make_pair(nums[i],i));
        }
        return vec;
    };
};

//以下为测试代码
void func(int i){cout << i << endl;}//用于打印最后结果

void test01()
{
    vector<int> vec1={6,5,10,4};
    int target=9;
    Solution s;//实例化求解两数和问题的对象

    vector<int> vec2= s.twoSum(vec1,target);//用vec2来接收结果

    for_each(vec2.begin(),vec2.end(),func);
}

int main(){

    test01();
    return 0;
}
```

#### Python：

```python
# Time Complexity: O(N)
# Space COmplexity: O(N)

class Solution:
    @staticmethod
    def twoSum(nums: list[int], target: int) -> list[int]:
        result = []
        mapping = {}
        for i in range(0, len(nums)):
            diff = target - nums[i]
            if diff in mapping:
                result.append(mapping[diff])
                result.append(i)
                return result
            mapping[nums[i]] = i


nums = [11, 15, 2, 7]
target = 9
print(Solution.twoSum(nums, target))
```



# 两数之和II

此题也可用上一题的解法：

```C++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        map<int,int> mp; //初始化哈希表
        vector<int> vec(2,-1); //初始化一个长度为2，值都为-1的vector容器用于接收结果
        for (int i = 0; i < numbers.size(); ++i)
        {
            int diff = target - numbers[i];
            if (mp.count(diff))
            {
                if (mp[diff] < i) {
                    vec[0] = mp[diff] + 1;
                    vec[1] = i + 1;
                }
                else {
                    vec[1] = mp[diff] + 1;
                    vec[0] = i + 1;
                }
            }
            mp.insert(make_pair(numbers[i],i));
        }
        return vec;
    }
};
```







但没有利用到**数组有序**的特点，利用这一特点可以得到更优解法：

## 二分查找

```C++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        for (int i = 0; i < numbers.size(); ++i) {
            int low = i + 1, high = numbers.size() - 1;
            while (low <= high) {
                int mid = (high - low) / 2 + low;
                int sum = numbers[i] + numbers[mid];
                if ( sum == target) return {i + 1, mid + 1};
                else if (sum < target) low = mid + 1;
                else high = mid - 1;
            }
        }
        return {-1, -1};
    }
};
```

![image-20210926204555589](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210926204555589.png)

## 双指针

```C++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int left = 0, right = numbers.size() - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) return {left + 1, right + 1};
            else if (sum < target) left++;
            else right--;
        }
        return {-1, -1};
    }
};
```

![image-20210926210357719](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210926210357719.png)









# 两数相加

给你两个 非空 的链表，表示两个非负的整数。它们每位数字都是按照 逆序 的方式存储的，并且每个节点只能存储 一位 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

示例1：

![img](E:/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/刷题计划/LeetCode刷题笔记.assets/addtwonumber1.jpg)





```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

示例 2：

```
输入：l1 = [0], l2 = [0]
输出：[0]
```

示例 3：

```
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1]
```


提示：

每个链表中的节点数在范围 [1, 100] 内
0 <= Node.val <= 9
题目数据保证列表表示的数字不含前导零



## 解法

### 迭代法

要有三个单链表，分别存放加数，被加数，和

要有一个变量carry存放进位，一个变量sum存放同一位上两数加上进位之和的个位

sum = (n1 + n2 + carry) % 10

carry = (n1 + n2 + carry) / 10

遍历两个链表 l1 和 l2，每次加完l1 = l1->next  , l2 = l2 ->next

sum依次存放在结果链表中

上面是总体的思路



有一些具体细节需要考虑：

1. 两数位数不同，一个先到了结尾：
    假设l1先到结尾，则后面n1 = 0，且l1指针不需要往后移了

2. 两数最高位相加完毕以后仍有进位：

    应使sum = carry，再向结果链表中加一位



伪代码：

![func(E:/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/刷题计划/LeetCode刷题笔记.assets/func(l,12) - ListNode-5453124.png) - ListNode](/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/刷题计划/LeetCode刷题笔记.assets/func(l,12) - ListNode-5453124.png)



**复杂度分析**

时间复杂度：O(\max(m,n))，其中 m 和 n 分别为两个链表的长度。我们要遍历两个链表的全部位置，而处理每个位置只需要 O(1) 的时间。

空间复杂度：O(1)。注意返回值不计入空间复杂度。



### 递归法

![func(E:/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/刷题计划/LeetCode刷题笔记.assets/func(l1,12) - ListNode.png) - ListNode](/Users/zhangxinyuan/Nutstore Files/坚果云/坚果云文稿/Markdown文稿/刷题计划/LeetCode刷题笔记.assets/func(l1,12) - ListNode.png)







**评价：**思路不难，但具体代码实现涉及到链表的知识，可以很好地应用链表的基础知识









## 代码实现

### 迭代法

#### C++：

```c++
#include <iostream>
using namespace std;
#include <vector>
#include <map>

//定义单链表
struct ListNode{
    //单链表节点的数据域和指针域
    int val;
    ListNode *next;
    //三个构造函数
    ListNode() : val(0), next(nullptr){}
    ListNode(int x) : val(x), next(nullptr){} //用于在最尾部添加一个节点，其next指向nullptr
    ListNode(int x, ListNode *next) : val(x), next(next){} //用于在中间欻入一个节点，next指向后一个节点
};

class Solution{
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) //接收要相加的两个链表，返回值是一个链表的头节点地址
    {
        //用于存放结果的链表
        //小技巧：对于链表问题，返回结果为头结点时，通常需要先初始化一个预先指针 pre，该指针的下一个节点指向真正的头结点head。使用预先指针的目的在于链表初始化时无可用节点值，而且链表构造过程需要指针移动，进而会导致头指针丢失，无法返回结果。

        ListNode *pre = nullptr, *head = nullptr;
        //用于存放进位
        int carry = 0;
        while (l1 || l2)//只要两个链表有一个不为空
        {
            //用三目运算符定义两个加数，如果有一个链表已经到末尾，则将对应的加数置为0
            int n1 = l1 ? l1->val : 0;
            int n2 = l2 ? l2->val : 0;
            //每一对应位相加的和
            int sum = n1 + n2 + carry;

            //接下来把 sum/10 和 sum%10 分别存入 carry 和 结果链表中
            if(!pre)//如果现在链表是空的
            {
                pre = head = new ListNode(sum % 10);

            }
            else //如果链表已经有节点
            {
                head -> next = new ListNode(sum % 10);
                head = head -> next;
            }
            carry = sum / 10;

            if (l1) {
                l1 = l1->next;
            }
            if (l2) {
                l2 = l2->next;
            }
        }

        if (carry){
            head ->next = new ListNode(carry);
        }

        return  pre;
    }
};

////用于测试单链表的创建
//void test01()
//{
//    //这是单链表程序内手动创建
////    ListNode *head = new ListNode(1); //用构造函数新建一个只有一个节点的单链表,节点值为1,表头为head
////    head = new ListNode(2, head); //头插法创建第二个节点，值为2
////
////    //打印输出
////    ListNode *ptr = head; //新建一个指针，指向要打印的链表表头
////    while(ptr != nullptr)
////    {
////        cout << ptr->val << endl;
////        ptr = ptr ->next;
////    }
//
//    // 也可以写一个循环，获取用户输入来创建一个单链表
//    ListNode *numberlist = nullptr;
//    int number;
//
//    int flag;
//    cin >> flag;
//    while(cin >> number, number != flag)
//    {
//        numberlist = new ListNode(number, numberlist);
//    }
//
//        //打印输出
//    ListNode *ptr = numberlist; //新建一个指针，指向要打印的链表表头
//    while(ptr != nullptr)
//    {
//        cout << ptr->val << endl;
//        ptr = ptr ->next;
//    }
//
//}

//用于测试两数之和
void test02()
{
    ListNode *l1 = new ListNode(1);
    l1 = new ListNode(2, l1);
    l1 = new ListNode(3, l1);

    ListNode *l2 = new ListNode(8);
    l2 = new ListNode(8, l2);
    l2 = new ListNode(9, l2);

    Solution s;

    ListNode *ptr = s.addTwoNumbers(l1, l2);
    while(ptr != nullptr)
    {
        cout << ptr->val << endl;
        ptr = ptr->next;
    }
}

int main(){

    //test01();
    test02();
    return 0;
}
```



另一个改进后的有趣的版本：

```C++
class Solution{
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) //接收要相加的两个链表，返回值是一个链表的头节点地址
    {
        //用于存放结果的链表
        ListNode *pre = nullptr, *head = nullptr; //pre才是真正的头指针，head在创建链表时移动，这样头指针写就不会丢失
        //用于存放进位
        int carry = 0;

        pre = head = new ListNode();//初始化结果链表，得到了一个只有一个头节点值为0的链表，有趣的地方

        while (l1 || l2)//只要两个链表有一个不为空
        {
            //用三目运算符定义两个加数，如果有一个链表已经到末尾，则将对应的加数置为0
            int n1 = l1 ? l1->val : 0;
            int n2 = l2 ? l2->val : 0;
            //每一对应位相加的和
            int sum = n1 + n2 + carry;

            //接下来把 sum/10 和 sum%10 分别存入 carry 和 结果链表中
            head -> next = new ListNode(sum % 10);
            carry = sum / 10;

            head = head -> next;
            if (l1) {
                l1 = l1->next;
            }
            if (l2) {
                l2 = l2->next;
            }
        }

        if (carry){
            head ->next = new ListNode(carry);
        }

        return  pre->next; //有趣的地方，因为前面初始化链表时新建了一个值为0的头节点，所以这里要返回头节点下一位的首元节点
    }
};
```





#### Python:

```python
# 链表类定义
class ListNode:
    def __init__(self, val=None, next=None):
        self.val = val
        self.next = next


class Solution:
    # N is the size of l1, M is the size of l2
    # Time Complexity: O(max(M,N))
    # Space Complexity: O(max(M,N)) if dummy is counted else O(1)
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        carry = 0
        pre = head = ListNode()
        while l1 is not None or l2 is not None:
            n1 = l1.val if l1 else 0
            n2 = l2.val if l2 else 0
            sum = n1 + n2 + carry
            head.next = ListNode(sum % 10)
            carry = sum // 10
            head = head.next
            if l1 is not None: #这里如果写成 if not l1: 程序就会运行不出结果
                l1 = l1.next
            if l2 is not None:
                l2 = l2.next
        if carry:
            head.next = ListNode(carry)

        return pre.next


if __name__ == '__main__':
    # 链表功能测试
    # 创建链表
    l1 = ListNode(1)
    l1 = ListNode(2, l1)
    l1 = ListNode(3, l1)

    l2 = ListNode(7)
    l2 = ListNode(8, l2)
    l2 = ListNode(9, l2)
    # 遍历输出
    # ptr = l1
    # while ptr:
    #     print(ptr.value)
    #     ptr = ptr.next

    s = Solution()
    ptr = s.addTwoNumbers(l1, l2)
    while ptr:
        print(ptr.val)
        ptr = ptr.next
```



### 递归法

#### C++：

```C++
class Solution{
public:
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2)
    {
        int sum = l1->val + l2->val;
        int carry = sum / 10;
        ListNode *head = nullptr, *pre = nullptr;
        pre = head = new ListNode(sum % 10);
        if (l1->next || l2->next || carry)
        {
            l1 = l1->next ? l1->next : new ListNode(0); //千万别忘了new，有new才是一个指针，才能赋给l1
            l2 = l2->next ? l2->next : new ListNode(0);
            l1->val += carry;
            head->next = addTwoNumbers(l1,l2);
        }
        return pre;
    }
};
```







#### python：

```python
class Solution:
    # Recursion
    # N is the size of l1, M is the size of l2
    # Time Complexity: O(max(M,N))
    # Space Complexity: O(max(M,N)) if dummy is counted else O(1)
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        sum = l1.val + l2.val
        carry = sum // 10
        res = ListNode(sum % 10)
        if l1.next or l2.next or carry:
            l1 = l1.next if l1.next else ListNode(0)
            l2 = l2.next if l2.next else ListNode(0)
            l1.val += carry
            res.next = self.addTwoNumbers(l1,l2);
        return res;
```











# 合并两个有序链表

## 迭代法

时间复杂度：O(n + m)，其中 n 和 m 分别为两个链表的长度。因为每次循环迭代中，l1 和 l2 只有一个元素会被放进合并链表中， 因此 while 循环的次数不会超过两个链表的长度之和。所有其他操作的时间复杂度都是常数级别的，因此总的时间复杂度为 O(n+m)。

空间复杂度：O(1)。我们只需要常数的空间存放若干变量

### C++：

```C++
#include <iostream>
using namespace std;
#include <string>

//链表定义
struct ListNode {
	int val;
	ListNode* next;

	ListNode() : val(0), next(nullptr) {}
	ListNode(int x) : val(x), next(nullptr){}
	ListNode(int x, ListNode *next) : val(x), next(next){}
};



class Solution {
public:
	ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
		//初始化结果链表，pre指向真正的头结点，head是用作移动的指针
		ListNode* pre = nullptr, * head = nullptr;
		pre = head = new ListNode(-1);

		while (l1 && l2)
		{
			if (l1->val < l2->val)
			{
				head->next = l1;    //等价于 head->next = new ListNode(l1->val);
				l1 = l1->next;
			}
			else
			{
				head->next = l2;     //等价于 head->next = new ListNode(l2->val);
				l2 = l2->next;
			}
			head = head->next;
		}
		
		//if (!l1)
		//{
		//	head->next = l2;   //这里和上面都不要用head = l2，否则会出错
		//}

		//if (!l2)
		//{
		//	head->next = l1;   //这里和上面都不要用head = l2，否则会出错
		//}

		//上面这部分被注释掉的代码还可以更简洁
		// 合并后 l1 和 l2 最多只有一个还未被合并完，我们直接将链表末尾指向未合并完的链表即可
		head->next = l1 == nullptr ? l2 : l1;

		return pre->next;
	}
};


void test01()
{
	ListNode* l1 = new ListNode(4);
	l1 = new ListNode(2, l1);
	l1 = new ListNode(1, l1);

	ListNode* l2 = new ListNode(4);
	l2 = new ListNode(3, l2);
	l2 = new ListNode(1, l2);

	Solution s;

	ListNode* ptr = s.mergeTwoLists(l1, l2);
	while (ptr != nullptr)
	{
		cout << ptr->val << " ";
		ptr = ptr->next;
	}
}


//void test02()
//{
//	ListNode* l = new ListNode(4);
//	l = new ListNode(2, l);
//	l = new ListNode(1, l);
//
//	ListNode* head = l;
//	ListNode* ptr1 = head;
//
//	while (ptr1 != nullptr)
//	{
//		cout << ptr1->val << " ";
//		ptr1 = ptr1->next;
//	}
//}


int main() 
{
	
	test01();
	//test02();

	system("pause");
	return 0;
}
```



### Python:

```python
class ListNode:
    def __init__(self, val = None, next = None):
        self.val = val
        self.next = next

class Solution:
    def mergeTwoLists(self, l1:ListNode, l2:ListNode) -> ListNode:
        pre = head = ListNode(-1)
        while l1 and l2 :
            if l1.val < l2.val:
                head.next = l1
                l1 = l1.next
            else:
                head.next = l2
                l2 = l2.next
            head = head.next

        head.next = l2 if l1 == None else l1
        #更简单的表达为：
        #head = l1 or l2

        return pre.next


if __name__ == '__main__':
    # 链表功能测试
    # 创建链表
    l1 = ListNode(4)
    l1 = ListNode(2, l1)
    l1 = ListNode(1, l1)

    l2 = ListNode(4)
    l2 = ListNode(3, l2)
    l2 = ListNode(1, l2)
    # 遍历输出
    # ptr = l1
    # while ptr:
    #     print(ptr.value)
    #     ptr = ptr.next

    s = Solution()
    ptr = s.mergeTwoLists(l1, l2)
    while ptr:
        print(ptr.val)
        ptr = ptr.next
```





## 递归法

### C++

```C++
class Solution {
public:
	ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
		if (!l1)
		{
			return l2;
		}
		else if (!l2)
		{
			return l1;
		}
		else if (l1->val < l2->val)
		{
			l1->next =  mergeTwoLists(l1->next, l2);
			return l1;
		}
		else
		{
			l2->next = mergeTwoLists(l1, l2->next);
			return l2;
		}
	}
};
```





### Python

```python
class Solution:
    def mergeTwoLists(self, l1:ListNode, l2:ListNode) -> ListNode:
        if l1 == None:
            return l2
        elif l2 == None:
            return l1
        #更简洁的表达为：
        #if l1 == None or l2 == None:
        #   return l1 or l2
        
        elif l1.val < l2.val:
            l1.next = self.mergeTwoLists(l1.next, l2)
            return l1
        else:
            l2.next = self.mergeTwoLists(l1, l2.next)
            return l2
```





# 删除链表倒数第N个节点

## 计算链表长度法

- 时间复杂度：O(L)，其中 *L* 是链表的长度。
- 空间复杂度：O(1)。

### C++：

```C++
#include <iostream>
using namespace std;
#include <string>

//链表定义
struct ListNode {
	int val;
	ListNode* next;

	ListNode() : val(0), next(nullptr) {}
	ListNode(int x) : val(x), next(nullptr){}
	ListNode(int x, ListNode *next) : val(x), next(next){}
};



class Solution {
public:
	//在对链表进行操作时，一种常用的技巧是添加一个哑节点（dummy node），它的next 指针指向链表的头节点。这样一来，我们就不需要对头节点进行特殊的判断了。
	//例如，在本题中，如果我们要删除节点 y，我们需要知道节点 y 的前驱节点 x，并将 x 的指针指向 y 的后继节点。
	//但由于头节点不存在前驱节点，因此我们需要在删除头节点时进行特殊判断。但如果我们添加了哑节点，那么头节点的前驱节点就是哑节点本身，此时我们就只需要考虑通用的情况即可

	ListNode* removeNthFromEnd(ListNode* head, int n) {
		//往传入的链表前面加一个哑结点
		ListNode* dummy = new ListNode(-1, head);

		//ptr用来指向真正的头结点，防止链表丢失。pre用于对链表进行操作
		ListNode* ptr = nullptr, * pre = nullptr;
		pre = dummy;

		//获取链表长度
		int length = 0;
		while (head)
		{
			length += 1;
			head = head->next;
		}

		//把指针移到要删除的节点前面
		for (int i = 0; i < length - n ; i++)
		{
			pre = pre->next;
		}

		//指向要删除的节点的后一个节点并释放内存
		ListNode* p = pre->next;
		pre->next = pre->next->next;
		delete p;

		//返回结果
		ptr = dummy->next;
		delete dummy;

		return ptr;
	}
};


void test01()
{
	ListNode* l1 = nullptr, * head = nullptr;
	l1 = head = new ListNode(1);
	for (int i = 2; i < 6; i++)
	{
		head->next = new ListNode(i);
		head = head->next;
	}

	Solution s;

	ListNode* ptr = s.removeNthFromEnd(l1, 2);
	while (ptr != nullptr)
	{
		cout << ptr->val << " ";
		ptr = ptr->next;
	}
}


//void test02()
//{
//	ListNode* l = nullptr,* head = nullptr;
//	l = head = new ListNode(1);
//	for (int i = 2; i < 6; i++)
//	{
//		head->next = new ListNode(i);
//		head = head->next;
//	}
//
//	ListNode* ptr1 = l;
//
//	while (ptr1 != nullptr)
//	{
//		cout << ptr1->val << " ";
//		ptr1 = ptr1->next;
//	}
//}


int main() 
{
	
	test01();
	//test02();

	system("pause");
	return 0;
}
```





### Python

```python
class ListNode:
    def __init__(self, val = None, next = None):
        self.val = val
        self.next = next

class Solution:
    def removeNthFromEnd(self, head:ListNode, n:int) -> ListNode:
        dummy = ListNode(-1, head)
        pre = dummy
        length = 0
        while head:
            length += 1
            head = head.next

        for i in range(0,length - n):
            pre = pre.next

        p = pre.next
        pre.next = pre.next.next
        del p

        ptr = dummy.next
        del dummy

        return ptr



if __name__ == '__main__':
    l1 = head = ListNode(1)
    for i in range(2,6):
        head.next = ListNode(i)
        head = head.next

    s = Solution()
    ptr = s.removeNthFromEnd(l1, 3)
    while ptr:
        print(ptr.val)
        ptr = ptr.next
```



## 栈

### C++

- 时间复杂度：*O*(L)，其中 L 是链表的长度。
- 空间复杂度：O*(*L)，其中 L 是链表的长度。主要为栈的开销。

```C++
class Solution {
public:
	ListNode* removeNthFromEnd(ListNode* head, int n){
		ListNode* dummy = new ListNode(0, head);
		stack<ListNode*> stk;
		//将整个链表入栈
		ListNode* cur = dummy;
		while (cur) {
			stk.push(cur);
			cur = cur->next;
		}

		//栈先进后出，因此从栈顶弹出n个元素
		for (int i = 0; i < n; ++i) {
			stk.pop();
		}

		//此时栈顶元素即为链表中要删除的元素的前一个，把这个节点的指针指向下下个节点即可
		ListNode* prev = stk.top();
		prev->next = prev->next->next;
		ListNode* ans = dummy->next;
		delete dummy;
		return ans;
	}
};
```



### Python

```python
class Solution:
    def removeNthFromEnd(self, head:ListNode, n:int) -> ListNode:
        dummy = ListNode(0, head)
        stack = list()
        cur = dummy
        while cur:
            stack.append(cur)
            cur = cur.next
            
        for i in range(n):
            stack.pop()
                
        prev = stack[-1]
        prev.next = prev.next.next
        return dummy.next
```



## 双指针

**思路与算法:**

我们也可以在不预处理出链表的长度，以及使用常数空间的前提下解决本题。

由于我们需要找到倒数第 n个节点，因此我们可以使用两个指针 first 和 second 同时对链表进行遍历，并且 first 比second 超前 n 个节点。当first 遍历到链表的末尾时，second 就恰好处于倒数第 n 个节点。

具体地，初始时 first 和 second 均指向头节点。我们首先使用 first 对链表进行遍历，遍历的次数为 n。此时，first 和 second 之间间隔了 n−1 个节点，即 first 比 second 超前了 n 个节点。

在这之后，我们同时使用 first 和 second 对链表进行遍历。当 first 遍历到链表的末尾（即 first 为空指针）时，second 恰好指向倒数第 n 个节点。

根据方法一和方法二，如果我们能够得到的是倒数第 n 个节点的前驱节点而不是倒数第 n 个节点的话，删除操作会更加方便。因此我们可以考虑在初始时将 second 指向哑节点，其余的操作步骤不变。这样一来，当 first 遍历到链表的末尾时，second 的下一个节点就是我们需要删除的节点。



**复杂度分析**

- 时间复杂度：O(L)，其中 L是链表的长度。
- 空间复杂度：O(1)。

![p3](https://assets.leetcode-cn.com/solution-static/19/p3.png)

### C++

```C++
class Solution {
public:
	ListNode* removeNthFromEnd(ListNode* head, int n){
        //哑结点
		ListNode* dummy = new ListNode(0, head);
		ListNode* first = head;
		ListNode* second = dummy;
        
        //first向后移n个节点，使first和second之间相差n个节点
		for (int i = 0; i < n; ++i) {
			first = first->next;
		}
        
        //first和second一起向后移，first移到链表末尾时，second指向节点的下一个节点即为要删除的节点
		while (first) {
			first = first->next;
			second = second->next;
		}
        
		second->next = second->next->next;
		ListNode* ans = dummy->next;
		delete dummy;
		return ans;
	}
};
```

### Python

```python
class Solution:
    def removeNthFromEnd(self, head: ListNode, n: int) -> ListNode:
        dummy = ListNode(-1, head)
        first = head
        second = dummy
        for i in range(n):
            first = first.next
        
        while first:
            first = first.next
            second = second.next
            
        second.next = second.next.next
        return dummy.next
    
```



# 无重复的最长子串

**思路和算法**

我们先用一个例子考虑如何在较优的时间复杂度内通过本题。

我们不妨以示例一中的字符串 $abcabcbb $为例，找出从每一个字符开始的，不包含重复字符的最长子串，那么其中最长的那个字符串即为答案。对于示例一中的字符串，我们列举出这些结果，其中括号中表示选中的字符以及最长的字符串：

以 ${(a)bcabcbb}$ 开始的最长字符串为 $ {(abc)abcbb}$；
以 ${a(b)cabcbb}$ 开始的最长字符串为 ${a(bca)bcbb}$；
以 ${ab(c)abcbb}$ 开始的最长字符串为 ${ab(cab)cbb}$；
以 ${abc(a)bcbb}$ 开始的最长字符串为 ${abc(abc)bb}$；
以 ${abca(b)cbb}$ 开始的最长字符串为 ${abca(bc)bb}$；
以 ${abcab(c)bb}$ 开始的最长字符串为 ${abcab(cb)b}$；
以 ${abcabc(b)b}$ 开始的最长字符串为 ${abcabc(b)b}$；
以 ${abcabcb(b)}$ 开始的最长字符串为 ${abcabcb(b)}$。
发现了什么？如果我们依次递增地枚举子串的起始位置，那么子串的结束位置也是递增的！这里的原因在于，假设我们选择字符串中的第 $k$ 个字符作为起始位置，并且得到了不包含重复字符的最长子串的结束位置为 $rk$ 。那么当我们选择第 $k+1$ 个字符作为起始位置时，首先从 $k+1$ 到 $rk$ 的字符显然是不重复的，并且由于少了原本的第 $k$ 个字符，我们可以尝试继续增大$rk$ ，直到右侧出现了重复字符为止。

这样一来，我们就可以使用「滑动窗口」来解决这个问题了：

我们使用两个指针表示字符串中的某个子串（或窗口）的左右边界，其中左指针代表着上文中「枚举子串的起始位置」，而右指针即为上文中的 $rk$ ；

在每一步的操作中，我们会将左指针向右移动一格，表示 我们开始枚举下一个字符作为起始位置，然后我们可以不断地向右移动右指针，但需要保证这两个指针对应的子串中没有重复的字符。在移动结束后，这个子串就对应着 以左指针开始的，不包含重复字符的最长子串。我们记录下这个子串的长度；

在枚举结束后，我们找到的最长的子串的长度即为答案。

判断重复字符

在上面的流程中，我们还需要使用一种数据结构来判断 是否有重复的字符，常用的数据结构为哈希集合（即 C++ 中的 std::unordered_set，Java 中的 HashSet，Python 中的 set, JavaScript 中的 Set）。在左指针向右移动的时候，我们从哈希集合中移除一个字符，在右指针向右移动的时候，我们往哈希集合中添加一个字符。

至此，我们就完美解决了本题。

**复杂度分析**

时间复杂度：$O(N)$，其中$ N$是字符串的长度。左指针和右指针分别会遍历整个字符串一次。

空间复杂度：$O(|\Sigma|)$，其中 $\Sigma$ 表示字符集（即字符串中可以出现的字符），$|\Sigma|$表示字符集的大小。在本题中没有明确说明字符集，因此可以默认为所有 ASCII 码在 $[0, 128)$ 内的字符，即 $|\Sigma| = 128$。我们需要用到哈希集合来存储出现过的字符，而字符最多有 $|\Sigma|$个，因此空间复杂度为$ O(|\Sigma|)$。



## C++

```C++
#include <iostream>
#include <string>
#include <unordered_set>
using namespace std;


class Solution {
public:
	int lengthOfLongestSubstring(string s) {
		//occ为哈希集合，用于记录每个字符是否出现过
		//ans用于存最大子串长度
		unordered_set<char> occ;
		int n = s.size();

		//右指针，初始值为-1，相当于现在在字符串左边界的左侧
		int rk = -1, ans = 0;

		//枚举左指针位置
		for (int i = 0; i < n; ++i)
		{
			//每次while循环结束回来就把左指针向右移一格，并从哈希集合中删除一个字符
			if (i != 0)
			{
				occ.erase(s[i - 1]);
			}

			//对于每一个左指针，右指针不断右移进行循环，把不在哈希集合中的元素插入
			//值得一提的是，不需要每移动一次左指针就把右指针放到左指针前面开始循环
			//因为下面这些rk+1保证了每移一次左指针，从左指针到rk位置无重复
			//rk初始设置为-1，以及下面的rk+1，大有学问
			while (rk + 1 < n && !occ.count(s[rk + 1])) 
			{
				occ.insert(s[rk + 1]);
				++rk;
			}

			//i到rk个字符即为本次左指针所在位置为起点找到的最大无重复子串
			ans = max(ans, rk + 1 - i);
		}
		return ans;
	}
};


void test01() {
	string str = "abcabcbb";
	
	Solution s;
	int ans = s.lengthOfLongestSubstring(str);
	cout << ans << endl;
}



int main()
{

	test01();

	system("pause");
	return 0;
}
```



## Python

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        occ = set()
        n = len(s)
        rk, ans = -1, 0
        for i in range(n):
            if i != 0:
                occ.remove(s[i - 1])
            while rk + 1 < n and s[rk + 1] not in occ:
                occ.add(s[rk + 1])
                rk += 1
            ans = max(ans, rk + 1 - i)
        return ans
```







# ****寻找两个正序数组的中位数*



## 合并数组法

```C++
class Solution {
public:
	double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
		vector<int> target;
		int n = nums1.size() + nums2.size();
		target.resize(n);
		double ans = 0;

		merge(nums1.begin(), nums1.end(), nums2.begin(), nums2.end(), target.begin());
		
		if (n == 0)
		{
			ans = 0;
		}
		else if (n % 2 == 0)
		{
			ans = ((double)target[n / 2] + (double)target[n / 2 - 1]) / 2;
		}
		else
		{
			ans = target[n / 2];
		}
		
		return ans;
	}
};
```

还有一种暴力法，直接合并两个数组，再排序

时间复杂度为快速排序的时间复杂度$O(m+n)log(m+n)$

空间复杂度为$O(m+n)$



**这里用的是归并排序**，用双指针遍历合并，得到合并后的有序数组

时间复杂度为 $O(m+n)$， $m$和$n$分别为两数组的长度

空间复杂度为$O(m+n)$， 存储合并后的数组需要 $m+n $大小的空间

事实上也不用合并完就能得到结果，只要找到中位数的位置即可。由于两个数组的长度已知，因此中位数对应的两个数组的下标之和也是已知的。维护两个指针，初始时分别指向两个数组的下标 0 的位置，每次将指向较小值的指针后移一位（如果一个指针已经到达数组末尾，则只需要移动另一个数组的指针），直到到达中位数的位置

第二种思路虽然可以将空间复杂度降到$ O(1)$，但是时间复杂度仍是 $O(m+n)$。

如何把时间复杂度降低到 $O(\log(m+n))$ 呢？如果对时间复杂度的要求有 $log$，通常都需要用到二分查找，这道题也可以通过二分查找实现。



## 二分查找法

根据中位数的定义，当 m+n 是奇数时，中位数是两个有序数组中的第 (m+n)/2 个元素，当 m+n 是偶数时，中位数是两个有序数组中的第 (m+n)/2 个元素和第 (m+n)/2+1 个元素的平均值。因此，这道题可以转化成寻找两个有序数组中的第 k 小的数，其中 kk 为(m+n)/2 或 (m+n)/2+1。

假设两个有序数组分别是A 和 B。要找到第 k 个元素，我们可以比较 A[k/2−1] 和 B[k/2−1]，其中 / 表示整数除法。由于A[k/2−1] 和 B[k/2−1] 的前面分别有 A[0..k/2−2] 和 B[0..k/2−2]，即k/2−1 个元素，对于 A[k/2−1] 和 B[k/2−1] 中的较小值，最多只会有 (k/2−1)+(k/2−1)≤k−2 个元素比它小，那么它就不能是第 k 小的数了。

因此我们可以归纳出三种情况：

如果 A[k/2−1]<B[k/2−1]，则比 A[k/2−1] 小的数最多只有 A 的前k/2−1 个数和 B 的前k/2−1 个数，即比 A[k/2−1] 小的数最多只有 k−2 个，因此 A[k/2−1] 不可能是第 k 个数，A[0] 到 A[k/2−1] 也都不可能是第 k 个数，可以全部排除。

如果 A[k/2−1]>B[k/2−1]，则可以排除 B[0] 到 B[k/2−1]。

如果 A[k/2−1]=B[k/2−1]，则可以归入第一种情况处理。



可以看到，比较 A[k/2−1] 和B[k/2−1] 之后，可以排除 k/2 个不可能是第 k 小的数，查找范围缩小了一半。同时，我们将在排除后的新数组上继续进行二分查找，并且根据我们排除数的个数，减少 k 的值，这是因为我们排除的数都不大于第 k 小的数。

有以下三种情况需要特殊处理：

如果 A[k/2−1] 或者 B[k/2−1] 越界，那么我们可以选取对应数组中的最后一个元素。在这种情况下，我们必须根据排除数的个数减少 k 的值，而不能直接将 k 减去 k/2。

如果一个数组为空，说明该数组中的所有元素都被排除，我们可以直接返回另一个数组中第 k 小的元素。

如果 k=1，我们只要返回两个数组首元素的最小值即可。

用一个例子说明上述算法。假设两个有序数组如下：

```
A: 1 3 4 9
B: 1 2 3 4 5 6 7 8 9
```


两个有序数组的长度分别是 4 和 9，长度之和是 13，中位数是两个有序数组中的第 7 个元素，因此需要找到第 k=7 个元素。

比较两个有序数组中下标为 k/2−1=2 的数，即 A[2] 和 B[2]，如下面所示：

```
A: 1 3 4 9
       ↑
B: 1 2 3 4 5 6 7 8 9
       ↑
```


由于 A[2]>B[2]，因此排除 B[0] 到 B[2]，即数组 B 的下标偏移（offset）变为 3，同时更新 k 的值：k=k−k/2=4。

下一步寻找，比较两个有序数组中下标为 k/2−1=1 的数，即A[1] 和B[4]，如下面所示，其中方括号部分表示已经被排除的数。

```
A: 1 3 4 9
     ↑
B: [1 2 3] 4 5 6 7 8 9
             ↑
```


由于 A[1]<B[4]，因此排除 A[0] 到 A[1]，即数组 A 的下标偏移变为 2，同时更新 k 的值：k=k−k/2=2。

下一步寻找，比较两个有序数组中下标为 k/2−1=0 的数，即比较A[2] 和 B[3]，如下面所示，其中方括号部分表示已经被排除的数。

```
A: [1 3] 4 9
         ↑
B: [1 2 3] 4 5 6 7 8 9
           ↑
```


由于 A[2]=B[3]，根据之前的规则，排除 A 中的元素，因此排除 A[2]，即数组 A 的下标偏移变为 3，同时更新 k 的值： k=k−k/2=1。

由于 k 的值变成 1，因此比较两个有序数组中的未排除下标范围内的第一个数，其中较小的数即为第 k 个数，由于 A[3]>B[3]，因此第 k 个数是 B[3]=4。

```
A: [1 3 4] 9
           ↑
B: [1 2 3] 4 5 6 7 8 9
           ↑
```



### C++

```C++
#include <algorithm>
#include <vector>
#include <iostream>
using namespace std;

class Solution {
public:
	int getKthElement(const vector<int>& nums1, const vector<int>& nums2, int k)
	{
		/* 主要思路：要找到第 k (k>1) 小的元素，那么就取 pivot1 = nums1[k/2-1] 和 pivot2 = nums2[k/2-1] 进行比较
		* 这里的 "/" 表示整除
		* nums1 中小于等于 pivot1 的元素有 nums1[0 .. k/2-2] 共计 k/2-1 个
		* nums2 中小于等于 pivot2 的元素有 nums2[0 .. k/2-2] 共计 k/2-1 个
		* 取 pivot = min(pivot1, pivot2)，两个数组中小于等于 pivot 的元素共计不会超过 (k/2-1) + (k/2-1) <= k-2 个
		* 这样 pivot 本身最大也只能是第 k-1 小的元素
		* 如果 pivot = pivot1，那么 nums1[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums1 数组
		* 如果 pivot = pivot2，那么 nums2[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums2 数组
		* 由于我们 "删除" 了一些元素（这些元素都比第 k 小的元素要小），因此需要修改 k 的值，减去删除的数的个数
		*/

		int m = nums1.size();
		int n = nums2.size();
		int index1 = 0, index2 = 0;

		while (true)
		{
			//边界情况
			if (index1 == m) 
			{
				return nums2[index2 + k - 1];
			}
			if (index2 == n)
			{
				return nums1[index1 + k - 1];
			}
			if (k == 1)
			{
				return min(nums1[index1], nums2[index2]);
			}


			//正常情况
			int newIndex1 = min(index1 + k / 2 - 1, m - 1);
			int newIndex2 = min(index2 + k / 2 - 1, n - 1);
			int pivot1 = nums1[newIndex1];
			int pivot2 = nums2[newIndex2];
			if (pivot1 <= pivot2)
			{
				k -= newIndex1 - index1 + 1;
				index1 = newIndex1 + 1;
			}
			else
			{
				k -= newIndex2 - index2 + 1;
				index2 = newIndex2 + 1;
			}
		}
	}

	double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) 
	{
		int totalLength = nums1.size() + nums2.size();
		if (totalLength % 2 == 1)
		{
			return getKthElement(nums1, nums2, (totalLength + 1) / 2);
		}
		else
		{
			return (getKthElement(nums1, nums2, totalLength / 2) + getKthElement(nums1, nums2, totalLength / 2 + 1)) / 2.0;
		}
	}
};

void test01()
{
	vector<int> v1, v2;
	v2.push_back(1);
	v2.push_back(2);
	v1.push_back(1);
	v1.push_back(3);

	Solution s;
	cout << s.findMedianSortedArrays(v1,v2) << endl;
}

//void printVector(vector<int>& v) 
//{
//	vector<int>::iterator it = v.begin();
//	while ( it != v.end()) 
//	{
//		cout << *it << " ";
//		it++;
//	}
//	cout << endl;
//}
//
//void test02()
//{
//	vector<int> v1; //无参构造
//	for (int i = 0; i < 10; i++)
//	{
//		v1.push_back(i);
//	}
//	printVector(v1);
//}


int main() {

	test01();
	//test02();

	system("pause");

	return 0;
}
```



### Python

```Python
```





## 划分数组法

### C++

```C++
```





### Python

```Python
```





# 移除链表元素



## 迭代法

**复杂度分析**

- 时间复杂度：$O(n)$，其中 $n$ 是链表的长度。需要遍历链表一次。
- 空间复杂度：$O(1)$。

### C++

```C++
class Solution
{
public:
    ListNode* removeElements(ListNode* head, int val)
    {
        struct ListNode* dummyHead = new ListNode(0,head);
        struct ListNode* temp = dummyHead;
        while(temp->next != NULL)
        {
            if(temp->next->val == val)
            {
                temp->next = temp->next->next;
            }
            else
            {
                temp = temp->next;
            }
		}
        return dummyHead->next;
	}
};
```



### Python

```python
class ListNode:
    def __init__(self, val=None, next=None):
        self.val = val
        self.next = next


class Solution:
    def removeElements(self, head: ListNode, val: int) -> ListNode:
        dummyHead = ListNode(-1, head)
        temp = dummyHead
        while temp is not None:
            if temp.next.val == val:
                temp.next = temp.next.next
            else:
                temp = temp.next
        return dummyHead.next
```





## 递归法

链表定义具有递归的性质，因此链表题目通常也可以用递归法求解。

对于给定的链表，首先对除了头结点head以外的节点递推调用head->next = removeElements(head->next.val) ,然后判断head的节点值是否等于val，如果相等，则head需要被删除，删除后的头结点为head.next， 如果不相等，则head保留

递归的终止条件是head为空，此时直接返回head。当head不为空时，递归地进行删除操作，然后判断head的节点值是否等于val并决定是否要删除head。



**复杂度分析**

时间复杂度：O(n)O(n)，其中 nn 是链表的长度。递归过程中需要遍历链表一次。

空间复杂度：O(n)O(n)，其中 nn 是链表的长度。空间复杂度主要取决于递归调用栈，最多不会超过 nn 层。



### C++

```C++
class Solution
{
public:
    ListNode* removeElements(ListNode* head, int val)
    {
        if(head == nullptr)
        {
            return head;
		}
        head->next = removeELements(head->next, val);
        return head->val == val ? head->next : head;
    }
};
```



### Python

```python
class ListNode:
    def __init__(self, val = None, next = None):
        self.val = val
        self.next = next


class Solution:
    def removeElements(self, head: ListNode, val: int) -> ListNode:
        if head is None:
            return head
        head.next = self.removeElements(head.next, val)
        return head.next if head.val == val else head


if __name__ == '__main__':
    l1 = ListNode(4)
    l1 = ListNode(2, l1)
    l1 = ListNode(1, l1)

    s = Solution()
    val = 1
    ptr = s.removeElements(l1, val)
    while ptr:
        print(ptr.val)
        ptr = ptr.next
```







# 删除有序链表中的重复元素

由于给定的链表是有序的，因此**重复元素在链表中出现的位置也是连续的**，因此我们只需要对链表进行一次遍历即可删除重复元素



## C++

```C++
#include <iostream>
using namespace std;

struct ListNode
{
	//成员变量
	int val;
	ListNode* next;

	//构造函数
	ListNode(): val(0), next(nullptr){}
	ListNode(int x): val(x), next(nullptr){}
	ListNode(int x, ListNode* next): val(x), next(next){}
};


class Solution {
public:
	ListNode* deleteDuplicates(ListNode* head)
	{
		if (!head) {
			return head;
		}

		ListNode* cur = head;
		while (cur->next) {
			if (cur->val == cur->next->val) {
				cur->next = cur->next->next;
			}
			else {
				cur = cur->next;
			}
		}

		return head;
	}
};


void test01() {
	ListNode* l1 = new ListNode(3);
	l1 = new ListNode(3, l1);
	l1 = new ListNode(2, l1);
	l1 = new ListNode(1, l1);
	l1 = new ListNode(1, l1);

	Solution s;
	ListNode* ptr = s.deleteDuplicates(l1);
	while (ptr != nullptr)
	{
		cout << ptr->val << " ";
		ptr = ptr->next;
	}
}


int main() {

	test01();

	system("pause");
	return 0;

}
```



## Python

```python
class Solution:
    def deleteDuplicates(self, head:ListNode) -> ListNode:
        if not head:
            return head
        
        cur = head
        while cur.next:
            if cur.val == cur.next.val:
                cur.next = cur.next.next
            else:
                cur = cur.next
                
        return head
```







# 两两交换链表元素

## 递归

可以通过递归的方式实现两两交换链表中的节点。

递归的终止条件是链表中没有节点，或者链表中只有一个节点，此时无法进行交换。

如果链表中至少有两个节点，则在两两交换链表中的节点之后，原始链表的头节点变成新的链表的第二个节点，原始链表的第二个节点变成新的链表的头节点。链表中的其余节点的两两交换可以递归地实现。在对链表中的其余节点递归地两两交换之后，更新节点之间的指针关系，即可完成整个链表的两两交换。

用 `head` 表示原始链表的头节点，新的链表的第二个节点，用 `newHead` 表示新的链表的头节点，原始链表的第二个节点，则原始链表中的其余节点的头节点是 `newHead.next`。令 `head.next = swapPairs(newHead.next)`，表示将其余节点进行两两交换，交换后的新的头节点为 `head` 的下一个节点。然后令 `newHead.next = head`，即完成了所有节点的交换。最后返回新的链表的头节点 `newHead`。

### C++

```c++
#include <iostream>
using namespace std;

struct ListNode
{
	//成员变量
	int val;
	ListNode* next;

	//构造函数
	ListNode(): val(0), next(nullptr){}
	ListNode(int x): val(x), next(nullptr){}
	ListNode(int x, ListNode* next): val(x), next(next){}
};


class Solution {
public:
	ListNode* swapPairs(ListNode* head)
	{
		if (head == nullptr || head->next == nullptr) {
			return head;
		}

		ListNode* newHead = head->next;
		head->next = swapPairs(newHead->next);
		newHead->next = head;
		return newHead;
	}
};


void test01() {
	ListNode* l1 = new ListNode(4);
	l1 = new ListNode(3, l1);
	l1 = new ListNode(2, l1);
	l1 = new ListNode(1, l1);

	Solution s;
	ListNode* ptr = s.swapPairs(l1);
	while (ptr != nullptr)
	{
		cout << ptr->val << " ";
		ptr = ptr->next;
	}
}


int main() {

	test01();

	system("pause");
	return 0;

}
```



### Python

```Python
class ListNode:
    def __init__(self, val=None, next=None):
        self.val = val
        self.next = next


class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        if head == None or head.next == None:
            return head

        newHead = head.next
        head.next = self.swapPairs(newHead.next)
        newHead.next = head
        return newHead



if __name__ == '__main__':
    l1 = ListNode(4)
    l1 = ListNode(3, l1)
    l1 = ListNode(2, l1)
    l1 = ListNode(1, l1)

    s = Solution()
    val = 1
    ptr = s.swapPairs(l1)
    while ptr:
        print(ptr.val)
        ptr = ptr.next
```





## 迭代

创建哑结点 `dummyHead`，令`dummyHead.next = head`，令`cur`表示当前到达的节点，初始时`cur = dummyHead`。每次交换`cur`后面两个节点。

如果 `temp` 的后面没有节点或者只有一个节点，则没有更多的节点需要交换，因此结束交换。否则，获得 `temp` 后面的两个节点 `node1` 和 `node2`，通过更新节点的指针关系实现两两交换节点。

具体而言，交换之前的节点关系是 `temp -> node1 -> node2`，交换之后的节点关系要变成 `temp -> node2 -> node1`，因此需要进行如下操作。

```python
temp.next = node2 
node1.next = node2.next 
node2.next = node1
```

完成上述操作之后，节点关系即变成 `temp -> node2 -> node1`。再令 `temp = node1`，对链表中的其余节点进行两两交换，直到全部节点都被两两交换。

两两交换链表中的节点之后，新的链表的头节点是 `dummyHead.next`，返回新的链表的头节点即可。



### C++

```c++
#include <iostream>
using namespace std;

struct ListNode
{
	//成员变量
	int val;
	ListNode* next;

	//构造函数
	ListNode(): val(0), next(nullptr){}
	ListNode(int x): val(x), next(nullptr){}
	ListNode(int x, ListNode* next): val(x), next(next){}
};


class Solution {
public:
	ListNode* swapPairs(ListNode* head)
	{
		ListNode* dummyHead = new ListNode(0,head);
		ListNode* cur = nullptr, * ptr1 = nullptr, * ptr2 = nullptr;
		cur = dummyHead;

		while (cur->next && cur->next->next) {
			ptr1 = cur->next;
			ptr2 = cur->next->next;

			cur->next = ptr2;
			ptr1->next = ptr2->next;
			ptr2->next = ptr1;

			cur = ptr1;
		}

		return dummyHead->next;
	}
};


void test01() {
	ListNode* l1 = new ListNode(4);
	l1 = new ListNode(3, l1);
	l1 = new ListNode(2, l1);
	l1 = new ListNode(1, l1);

	Solution s;
	ListNode* ptr = s.swapPairs(l1);
	while (ptr != nullptr)
	{
		cout << ptr->val << " ";
		ptr = ptr->next;
	}
}


int main() {

	test01();

	system("pause");
	return 0;

}
```



### Python

```python
class ListNode:
    def __init__(self, val=None, next=None):
        self.val = val
        self.next = next


class Solution:
    def swapPairs(self, head: ListNode) -> ListNode:
        dummyHead = ListNode(0, head)
        cur = dummyHead

        while cur.next and cur.next.next:
            node1 = cur.next
            node2 = cur.next.next

            cur.next = node2
            node1.next = node2.next
            node2.next = node1

            cur = node1

        return dummyHead.next


if __name__ == '__main__':
    l1 = ListNode(4)
    l1 = ListNode(3, l1)
    l1 = ListNode(2, l1)
    l1 = ListNode(1, l1)

    s = Solution()
    val = 1
    ptr = s.swapPairs(l1)
    while ptr:
        print(ptr.val)
        ptr = ptr.next

```





# 有效的括号

**后遇到的左括号要先闭合**

自然可以联系到栈的特点：**后进先出**



遇到一个左括号，就将其放入栈顶，等遇到一个右括号时，就把栈顶的左括号取出并比较是否是相同类型的括号。如果不同，或者栈中无左括号，则返回False。



为了快速判断括号的类型，**可以使用哈希表存储**，其键为右括号，值为相同类型的左括号。



遍历结束后，如果栈中没有左括号，则说明s中所有左括号闭合，返回True，否则返回Flase。



注意到有效字符串的长度一定为偶数，因此如果字符串的长度为奇数，我们可以直接返回 False，省去后续的遍历判断过程。



## C++

```C++
#include <iostream>
using namespace std;
#include <string>
#include <unordered_map>
#include <stack>

class Solution {
public:
	bool isValid(string s) {

		int n = s.size();
		if (n % 2 == 1)
		{
			return false;
		}

		//创建哈希表
		unordered_map<char, char> hashMap = {
			{')', '('},
			{']', '['},
			{'}', '{'}
		};

		//创建栈
		stack<char> stk;
		for (char ch : s) {
			if (hashMap.count(ch))
			{
				if (stk.empty() || stk.top() != hashMap[ch])
				{
					return false;
				}

				stk.pop();
			}
			else
			{
				stk.push(ch);
			}
		}

		return stk.empty();
	}
};


void test01() {
	string str = "{[({[]})])";
	Solution s;
	cout << s.isValid(str) << endl;
}


int main() {

	test01();

	system("pause");
	return 0;

}
```



## Python

```python
class Solution:
    def isValid(self, s: str) -> bool:
        if len(s) % 2 == 1:
            return False

        hashMap = {
            ")": "(",
            "]": "[",
            "}": "{",
        }

        stack = list()
        for ch in s:
            if ch in h:
                if not stack or stack[-1] != hashMap[ch]:
                    return False
                stack.pop()
            else:
                stack.append(ch)

        return not stack


if __name__ == '__main__':
    s1 = '{[{[()]}]}'

    s = Solution()
    print(s.isValid(s1))
```







# 零钱兑换

## 动态规划

**时间复杂度**：$O(Sn)$，其中 $S$ 是金额，$n $是面额数。我们一共需要计算 $O(S) $个状态，$S $为题目所给的总金额。对于每个状态，每次需要枚举$ n$ 个面额来转移状态，所以一共需要 $O(Sn) $的时间复杂度。
**空间复杂度**：$O(S)$，数组 $dp$ 需要开长度为总金额 $S$ 的空间。

### C++

```C++
#include <iostream>
#include <vector>
#include <limits.h>
#include <algorithm>
using namespace std;


class Solution {
public:
	int coinChange(vector<int>& coins, int amount)
	{
		//开一个用于存放每个子问题解的数组，子问题共有amount+1个（算上0）
		vector<int> f(amount + 1);
		

		//初始条件
		f[0] = 0;

		//按照顺序依次计算每个子问题的解:f[1],f[2],...,f[amount]
		for (int i = 1; i <= amount; ++i)
		{
			//每个子问题的解先初始化为无穷大，算出来更小的就更新
			f[i] = INT_MAX;

			//n表示硬币数
			int n = coins.size();
			//每个子问题需要分别对每种硬币进行求解，哪个更小就用哪个，如果没有解就保持为正无穷
			for (int j = 0; j < n; ++j)
			{
				//转移方程为：f[i] = min{f[i-coins[0]]+1, f[i-coins[1]]+1,..., f[i-coins[n-1]]+1}
				//当前的总金额至少要大于硬币面值，即i-coins[j]不能是负数
				//可能某个总金额拼不出来，即某一个子问题无解。此时f[i - coins[j] 还是正无穷
				//正无穷就不能再放到下面的式子里去+1，因为计算机可能会越界报错，或者变成其补码跑到INT_MIN去
				if (i >= coins[j] && f[i -coins[j]] != INT_MAX)
				{
					if (f[i - coins[j]] < f[i])
					{
						f[i] = f[i - coins[j]] + 1;
					}
				}
			}
		}

		//如果拼不出来，返回-1
		if (f[amount] == INT_MAX)
		{
			return -1;
		}

		return f[amount];
	}
};


void test01() {
	int a[3] = { 2, 5, 7 };
	vector<int> coins(a, a + 3);

	int amount = 27;

	Solution s;
	cout << s.coinChange(coins, amount) <<endl;
}


int main() {

	test01();

	system("pause");
	return 0;

}
```



**更精炼的版本**

```C++
class Solution {
public:
	int coinChange(vector<int>& coins, int amount)
	{
        //初始化数组，数组容量为子问题数量+1，元素值可以用无穷大来填充，但下面还得考虑越界的问题
        //这里可以用amount+1来填充，因为硬币面值也就是正整数最小值是1，那么数量最多的硬币就是全用1，也不会超过amount
        //妙啊！
		vector<int> dp(amount + 1, amount + 1);
		dp[0] = 0;
		for (int i = 0; i < (int)coins.size(); ++i)
		{
			for (int j = coins[i]; j <= amount; ++j)
			{
				dp[j] = min(dp[j], dp[j - coins[i]] + 1);
			}
		}
		return dp[amount] > amount ? -1 : dp[amount];
	}
};
```







### Python

```python
class Solution:
    def coinChange(self, coins: list[int], amount: int) -> int:
        #python不用担心无穷大+1越界的问题
        dp = [float('inf')] * (amount + 1)
        dp[0] = 0

        for coin in coins:
            for x in range(coin, amount + 1):
                dp[x] = min(dp[x], dp[x - coin] + 1)
        return dp[amount] if dp[amount] != float('inf') else -1
```





## 记忆化搜索

### C++

```c++
```



### Python

```PYTHON

```









# 不同路径

## C++

```c++
#include <iostream>
#include <vector>
#include <limits.h>
using namespace std;


class Solution {
public:
	int uniquePaths(int m, int n)
	{
		vector<vector<int>> f(m, vector<int>(n));
		for (int i = 0; i < m; ++i)
		{
			for (int j = 0; j < n; ++j)
			{
				if (i == 0 || j == 0)
				{
					f[i][j] = 1;
				}
				else 
				{
					f[i][j] = f[i - 1][j] + f[i][j - 1];
				}
			}
		}

		return f[m - 1][n - 1];
		
	}
};


void test01() {

	Solution s;
	cout << s.uniquePaths(3, 7) <<endl;
}


int main() {

	test01();

	system("pause");
	return 0;

}
```



## Python

```python
import numpy


class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        f = numpy.zeros((m, n))
        for i in range(m):
            for j in range(n):
                if i == 0 or j == 0:
                    f[i][j] = 1
                else:
                    f[i][j] = f[i - 1][j] + f[i][j - 1]
        return f[m - 1][n - 1]
```





# 不同路径II

## 动态规划

当我们不熟悉的时候，怎么想到用动态规划来解决这个问题呢？

1. **无后效性：**$f(i,j)$​​只与$f(i-1,j)$​​和$f(i,j-1)$​​​​有关，而与任何的$f(i',j')(i'>i,j'>j)$​无关
2. **最优子结构：**动态规划的题目分为两大类，一种是求最优解类，典型问题是背包问题，另一种就是计数类，比如这里的统计方案数的问题，它们都存在一定的递推性质。前者的递推性质还有一个名字，叫做 「最优子结构」 ——**即当前问题的最优解取决于子问题的最优解**，后者类似，**当前问题的方案数取决于子问题的方案数**。所以在遇到求方案数的问题时，我们可以往动态规划的方向考虑。



### C++

```C++
#include <iostream>
#include <vector>
using namespace std;


class Solution {
public:
	int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid)
	{
		//m为行数，n为列数
		int m = obstacleGrid.size(), n = obstacleGrid[0].size();

		//设置初始条件，起点设置为1
		//但要当心初始位置有障碍物的情况
		if (obstacleGrid[0][0] == 1)
		{
			return 0;
		}
		else
		{
			obstacleGrid[0][0] = 1;
		}

		//设置边界条件，第一列和第一行应全部赋值为1
		//同样要当心边界出现障碍物的情况
		//如果第一列碰到障碍物，那么障碍物下面全都没法走，应全部设置为0
		int a = 1;
		for (int i = 1; i < m; ++i)
		{
			if (obstacleGrid[i][0] == 1)
			{
				a = 0;
			}
			obstacleGrid[i][0] = a;
		}
		//如果第一列碰到障碍物，那么障碍物右面全都没法走，应全部设置为0
		int b = 1;
		for (int j = 1; j < n; ++j)
		{
			if (obstacleGrid[0][j] == 1)
			{
				b = 0;
			}
			obstacleGrid[0][j] = b;
		}

		//用转移方程按顺序计算，碰到障碍物就设为0，表示走不通
		for (int i = 1; i < m; ++i)
		{
			for (int j = 1; j < n; ++j)
			{
				if (obstacleGrid[i][j] == 1)
				{
					obstacleGrid[i][j] = 0;
				}
				else 
				{
					obstacleGrid[i][j] = obstacleGrid[i - 1][j] + obstacleGrid[i][j - 1];
				}
				
			}
		}

		return obstacleGrid[m - 1][n - 1];
		
	}
};



void test01() {
	vector<vector<int>> vec(3,vector<int>(3,0));

	vec[1][1] = 1;

	Solution s;
	cout << s.uniquePathsWithObstacles(vec) << endl;
}


int main() {

	test01();

	system("pause");
	return 0;

}
```



### Python

```PYTHON
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: list[list[int]]) -> int:
        # 构造一个DP table
        row = len(obstacleGrid)
        col = len(obstacleGrid[0])
        dp = [[0 for _ in range(col)] for _ in range(row)]

        dp[0][0] = 1 if obstacleGrid[0][0] != 1 else 0
        if dp[0][0] == 0: return 0  # 如果第一个格子就是障碍，return 0
        # 第一行
        for i in range(1, col):
            if obstacleGrid[0][i] != 1:
                dp[0][i] = dp[0][i-1]

        # 第一列
        for i in range(1, row):
            if obstacleGrid[i][0] != 1:
                dp[i][0] = dp[i-1][0]
        print(dp)

        for i in range(1, row):
            for j in range(1, col):
                if obstacleGrid[i][j] != 1:
                    dp[i][j] = dp[i-1][j] + dp[i][j-1]
        return dp[-1][-1]


if __name__ == '__main__':
    array = [[0 for i in range(3)] for j in range(3)]
    array[1][1] = 1

    s = Solution
    print(s.uniquePathsWithObstacles(s, array))
```













## 用滚动数组思想优化的动态规划*

**「滚动数组思想」是一种常见的动态规划优化方法，在我们的题目中已经多次使用到，例如「剑指 Offer 46. 把数字翻译成字符串」、「70. 爬楼梯」等，当我们定义的状态在动态规划的转移方程中只和某几个状态相关的时候，就可以考虑这种优化方法，目的是给空间复杂度「降维」。**

回顾这道题，其实这类动态规划的题目在题库中也出现过多次，例如「221. 最大正方形」、「1162. 地图分析」等。他们都以二维坐标作为状态，大多数都可以使用滚动数组进行优化。如果我们熟悉这类问题，可以一眼看出这是一个动态规划问题。



### C++

```C++
class Solution {
public:
	int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid)
	{
		int n = obstacleGrid.size(), m = obstacleGrid[0].size();
		vector<int> f(m);

		f[0] = (obstacleGrid[0][0] == 0);
		for (int i = 0; i < n; ++i)
		{
			for (int j = 0; j < m; ++j)
			{
				if (obstacleGrid[i][j] == 1)
				{
					f[j] = 0;
					continue;
				}
				if (j - 1 >= 0 && obstacleGrid[i][j - 1] == 0)
				{
					f[j] += f[j - 1];
				}
			}
		}

		return f.back();
		
	}
};
```



### Python

```python
class Solution:
    def canJump(self, nums: list[int]) -> bool:
        n = len(nums)
        f = [None] * n
        f[0] = True

        for j in range(1, n):
            f[j] = False
            for i in range(0, j):
                f[j] = f[j] or (f[i] and (i + nums[i] >= j))

        return f[n - 1]
```





# 跳跃游戏

## 动态规划

### C++

这道题是一个典型的存在性问题，可以用动态规划。但是太慢了，虽然时间复杂度是$O(n^2)$​，但可能逻辑运算比较花时间，导致数组过大时运算会超时。

因此应该用**贪心算法**求解

```C++
#include <iostream>
#include <vector>
using namespace std;


class Solution {
public:
	bool canJump(vector<int>& nums) {
		int n = nums.size();
		vector<bool> f(n);

		//初始条件
		f[0] = true;

		//依次计算f[1],f[2],...,f[n-1]
		for (int j = 1; j < n; ++j)
		{
			//f[j]的状态转移方程比较特殊，是一个j-1项的或逻辑运算，还需要一个for循环实现
			f[j] = false;
			for (int i = 0; i < j; ++i)
			{
				f[j] = f[j] || (f[i] && (i + nums[i] >= j));
				//如果已经为true，就不需要再往后算了
				if (f[j])
				{
					break;
				}
			}
		}
		return f[n - 1];
	}
};


void test01() {
	int arr[9] = {2,3,1,1,4};
	vector<int> vec(begin(arr),end(arr));

	Solution s;
	cout << s.canJump(vec) << endl;
}


int main() {

	test01();

	system("pause");
	return 0;

}
```



### Python

```python
```







## 贪心算法

我们依次遍历数组中的每一个位置，并实时维护 最远可以到达的位置。对于当前遍历到的位置 $x$​，如果它在 最远可以到达的位置 的范围内，那么我们就可以从起点通过若干次跳跃到达该位置，因此我们可以用 $x+nums[x]$​ 更新 最远可以到达的位置。

在遍历的过程中，如果 最远可以到达的位置 大于等于数组中的最后一个位置，那就说明最后一个位置可达，我们就可以直接返回 $True$ 作为答案。反之，如果在遍历结束后，最后一个位置仍然不可达，我们就返回 $False$ 作为答案。



### C++

```C++
class Solution {
public:
	bool canJump(vector<int>& nums) {
		int n = nums.size();
		int rightmost = 0;
		for (int i = 0; i < n; ++i)
		{
			if (i <= rightmost)
			{
				rightmost = max(rightmost, i + nums[i]);
				if (rightmost >= n - 1)
				{
					return true;
				}
			}
		}
		return false;
	}
};
```





### Python

```python
class Solution:
    def canJump(self, nums: list[int]) -> bool:
        n, rightmost = len(nums), 0
        for i in range(n):
            if i <= rightmost:
                rightmost = max(rightmost, i + nums[i])
                if rightmost >= n - 1:
                    return True

        return False


if __name__ == '__main__':
    array = [3, 2, 1, 0, 4]

    s = Solution
    print(s.canJump(s, array))

```







# 整数反转

## 数学方法

- 时间复杂度：$O(log∣x∣)$​。翻转的次数即 $x$ 十进制的位数。
- 空间复杂度：$O(1)$。

### C++

```C++
class Solution {
public:
    int reverse(int x) {
        int rev = 0;
        while (x != 0) {
            if (rev < INT_MIN / 10 || rev > INT_MAX / 10) {
                return 0;
            }
            int digit = x % 10;
            x /= 10;
            rev = rev * 10 + digit;
        }
        return rev;
    }
};
```





### Python

```python
lass Solution:
    def reverse(self, x: int) -> int:
        INT_MIN, INT_MAX = -2**31, 2**31 - 1

        rev = 0
        while x != 0:
            # INT_MIN 也是一个负数，不能写成 rev < INT_MIN // 10
            if rev < INT_MIN // 10 + 1 or rev > INT_MAX // 10:
                return 0
            digit = x % 10
            # Python3 的取模运算在 x 为负数时也会返回 [0, 9) 以内的结果，因此这里需要进行特殊判断
            if x < 0 and digit > 0:
                digit -= 10

            # 同理，Python3 的整数除法在 x 为负数时会向下（更小的负数）取整，因此不能写成 x //= 10
            x = (x - digit) // 10
            rev = rev * 10 + digit
        
        return rev
```







## 字符串方法

### C++

```C++
#include <iostream>
#include <string>
using namespace std;


class Solution {
public:
	int reverse(int x) {
		int absOfX = abs(x);
		string num = to_string(absOfX);
		string revOfNum = "";

		for (auto ch : num)
		{
			revOfNum = ch + revOfNum;
		}

		long long ans = stoll(revOfNum);
		if (ans / 10 > INT_MAX / 10 || ans / 10 < INT_MIN / 10)
		{
			return 0;
		}

		return absOfX == x ? ans : -ans;
	}

};


void test01() {
	int x = 15334312419;

	Solution s;
	cout << s.reverse(x) << endl;
}


int main() {

	test01();

	system("pause");
	return 0;

}
```









### Python

```python
class Solution:
    def reverse(self, x: int) -> int:
        INT_MIN, INT_MAX = -2 ** 31, 2 ** 31 - 1
        absOfX = abs(x)
        numStr = str(absOfX)
        revOfNumStr = ""

        for ch in numStr:
            revOfNumStr = ch + revOfNumStr

        ans = int(revOfNumStr)
        if (ans // 10 > INT_MAX // 10) or (ans // 10 < INT_MIN // 10):
            return 0;

        return ans if absOfX == x else -ans
```









# 回文数

## C++

```C++
class Solution {
public:
    bool isPalindrome(int x) {
        if(x < 0)
        {
            return false;
        }

        long long rev= 0;
        int num = x;
        while(num != 0)
        {
            rev = rev * 10 + num % 10;
            num /= 10;
        }
        
        if(rev == x)
        {
            return true;
        }

        return false;
    }
};
```



**只需反转一半数字：**

```c++
class Solution {
public:
    bool isPalindrome(int x) {
        // 特殊情况：
        // 如上所述，当 x < 0 时，x 不是回文数。
        // 同样地，如果数字的最后一位是 0，为了使该数字为回文，
        // 则其第一位数字也应该是 0
        // 只有 0 满足这一属性
        if (x < 0 || (x % 10 == 0 && x != 0)) {
            return false;
        }

        int revertedNumber = 0;
        while (x > revertedNumber) {
            revertedNumber = revertedNumber * 10 + x % 10;
            x /= 10;
        }

        // 当数字长度为奇数时，我们可以通过 revertedNumber/10 去除处于中位的数字。
        // 例如，当输入为 12321 时，在 while 循环的末尾我们可以得到 x = 12，revertedNumber = 123，
        // 由于处于中位的数字不影响回文（它总是与自己相等），所以我们可以简单地将其去除。
        return x == revertedNumber || x == revertedNumber / 10;
    }
};
```



## Python

```python
class Solution:
    def isPalindrome(self, x: int) -> bool:
        if x < 0 or (x % 10 == 0 and x != 0):
            return False

        revNum = 0
        while x > revNum:
            revNum = revNum * 10 + x % 10
            x = x // 10

        return x == revNum or x == revNum // 10
```





# 盛水最多的容器

## C++

```C++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int l = 0, r = height.size() - 1;
        int ans = 0;
        while (l < r) {
            int area = min(height[l], height[r]) * (r - l);
            ans = max(ans, area);
            if (height[l] < height[r]) {
                ++l;
            }
            else {
                --r;
            }
        }
        return ans;
    }
};
```



## Python

```python
class Solution:
    def maxArea(self, height: List[int]) -> int:
        l, r = 0, len(height) - 1
        ans = 0
        while l < r:
            area = min(height[l], height[r]) * (r - l)
            ans = max(ans, area)
            if height[l] <= height[r]:
                l += 1
            else:
                r -= 1
        return ans
```







# 三数之和

## C++

```C++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;


class Solution {
public:
	vector<vector<int>> threeSum(vector<int>& nums)
	{
		int n = nums.size();
		//先对数组排序，避免遍历时碰到重复，也有利于双指针操作
		sort(nums.begin(), nums.end());
		vector<vector<int>> ans;

		//枚举a
		for (int first = 0; first < n; ++first)
		{
			//需和上次枚举的数不同
			if (first > 0 && nums[first] == nums[first - 1])
			{
				continue;
			}

			//c对应的指针刚开始指向数组最右端
			int third = n - 1;
			int target = 0 - nums[first];

			//此时问题转化成了两数之和的问题
            //由于数组有序，因此可以用双指针求解两数之和问题，时间复杂度为O(n)，再加上外面
			//枚举b
			for (int second = first + 1; second < n; ++second)
			{
				//需要和上次枚举的数不同
				if (second > first + 1 && nums[second] == nums[second - 1])
				{
					continue;
				}

				//因为数组有序，如果b+c大于目标值，就把c指针往左移，反之b指针往右移
				//需要保证b指针在c指针左侧
				while (second < third && nums[second] + nums[third] > target)
				{
					--third;
				}

				//指针重合就可以退出循环了
				if (second == third)
				{
					break;
				}

				if (nums[second] + nums[third] == target)
				{
					ans.push_back({ nums[first], nums[second], nums[third] });
				}
			}
		}
		return ans;
	}

};


void test01() {
	vector<int> v1 = { -1, 0, 1, 2, -1, -4 };

	Solution s;
	
	vector<vector<int>> vec = s.threeSum(v1);

	for (const auto& inner : vec) {
		for (const auto& item : inner) {
			cout << item << " ";
		}
	}
	cout << endl;
}


int main() {

	test01();

	system("pause");
	return 0;

}
```



## Python

```python
class Solution:
    def threeSum(self, nums: list[int]) -> list[list[int]]:
        n = len(nums)
        nums.sort()
        ans = list()

        for first in range(n):
            if first > 0 and nums[first] == nums[first - 1]:
                continue
            third = n - 1
            target = 0 - nums[first]
            for second in range(first + 1, n):
                if second > first + 1 and nums[second] == nums[second - 1]:
                    continue
                while second < third and nums[second] + nums[third] > target:
                    third -= 1

                if second == third:
                    break
                if nums[second] + nums[third] == target:
                    ans.append([nums[first], nums[second], nums[third]])

        return ans
```







# 电话号码的字母组合

首先使用哈希表存储每个数字对应的所有可能的字母，然后进行回溯操作。

回溯过程中维护一个字符串，表示已有的字母排列（如果未遍历完电话号码的所有数字，则已有的字母排列是不完整的）。该字符串初始为空。每次取电话号码的一位数字，从哈希表中获得该数字对应的所有可能的字母，并将其中的一个字母插入到已有的字母排列后面，然后继续处理电话号码的后一位数字，直到处理完电话号码中的所有数字，即得到一个完整的字母排列。然后进行回退操作，遍历其余的字母排列。

**回溯算法**用于寻找所有的可行解，如果发现一个解不可行，则会舍弃不可行的解。在这道题中，由于每个数字对应的每个字母都可能进入字母组合，因此不存在不可行的解，直接穷举所有的解即可。





## C++

```C++
#include <iostream>
#include <unordered_map>
using namespace std;


class Solution {
public:
	vector<string> letterCombinations(string digits) {
		vector<string> combinations;
		if (digits.empty()) {
			return combinations;
		}

		unordered_map<char, string> phoneMap{
		   {'2', "abc"},
		   {'3', "def"},
		   {'4', "ghi"},
		   {'5', "jkl"},
		   {'6', "mno"},
		   {'7', "pqrs"},
		   {'8', "tuv"},
		   {'9', "wxyz"}
		};

		string combination;
		backtrack(combinations, phoneMap, digits, 0, combination);
		return combinations;
	}

private:
	void backtrack(vector<string>& combinations, const unordered_map<char, string>& phoneMap, const string& digits, int index, string& combination) {
		if (index == digits.length()) {
			combinations.push_back(combination);
		}
		else {
			char digit = digits[index];
			const string& letters = phoneMap.at(digit);
			for (const char& letter : letters) {
				combination.push_back(letter);
				backtrack(combinations, phoneMap, digits, index + 1, combination);
				combination.pop_back();
			}
		}
	}
};


void test01() {
	string digits = "2356";

	Solution s;
	
	vector<string> vec = s.letterCombinations(digits);


	for (const auto& item : vec)
	{
		cout << item << " ";
	}
	cout << endl;
}


int main() {

	test01();

	system("pause");
	return 0;

}
```



## Python

```python
class Solution:
    def letterCombinations(self, digits: str) -> list[str]:
        if not digits:
            return list()

        phoneMap = {
            "2": "abc",
            "3": "def",
            "4": "ghi",
            "5": "jkl",
            "6": "mno",
            "7": "pqrs",
            "8": "tuv",
            "9": "wxyz",
        }

        def backtrack(index: int):
            if index == len(digits):
                combinations.append("".join(combination))
            else:
                digit = digits[index]
                for letter in phoneMap[digit]:
                    combination.append(letter)
                    backtrack(index + 1)
                    combination.pop()

        combination = list()
        combinations = list()
        backtrack(0)
        return combinations


if __name__ == '__main__':
    digits = "234"

    s = Solution
    print(s.letterCombinations(s, digits))

```







# 汉明距离

汉明距离广泛应用于多个领域。在编码理论中用于错误检测，在信息论中量化字符串之间的差异。

两个整数之间的汉明距离是对应位置上数字不同的位数。

根据以上定义，我们使用异或运算，记为 $⊕$，当且仅当输入位不同时输出为 $1$。



计算 $x$ 和 $y$ 之间的汉明距离，可以先计算 $x⊕y$，然后统计结果中等于 $1 $的位数。

现在，原始问题转换为位计数问题。位计数有多种思路，将在下面的方法中介绍。



## 内置位计数

```C++
class Solution{
public:
    int hammingDistance(int x, int y){
        return __builtin_popcount(x ^ y);
    }
};
```



## 移位实现位计数

```c++
class Solution{
public:
    int hammingDistance(int x, int y){
		//异或运算
        int s = x ^ y, ret = 0;
        while (s) {
            //检查s最低位是否为1，&是与运算
            ret += s & 1;
            //二进制右移位操作
            s >>= 1;
        }
        return ret;
    }
};
```









## Brian Kernighan 算法

在方法二中，对于 $s=(10001100)_2$ 的情况，我们需要循环右移 8 次才能得到答案。而实际上如果我们可以跳过两个 1 之间的 0，直接对 1 进行计数，那么就只需要循环 3 次即可。

我们可以使用 Brian Kernighan 算法进行优化，具体地，该算法可以被描述为这样一个结论：记 $f(x)$ 表示  $x$ 和 $x-1$ 进行与运算所得的结果, 即 `f(x)=x & (x−1)）`,那么 $f(x)$ 恰为 $x $删去其二进制表示中最右侧的 1  的结果。



![fig3](LeetCode%E5%88%B7%E9%A2%98.assets/3.png)



基于该算法，当我们计算出  $s=x⊕y$，只需要不断让$ s = f(s)$ ，直到 $s=0$  即可。这样每循环一次，$s$ 都会删去其二进制表示中最右侧的 1，最终循环的次数即为 $s$ 的二进制表示中 1 的数量。 



```C++
class Solution{
public:
    int hammingDistance(int x, int y) {
		int s = x ^ y, ret = 0;
        while (s) {
			s &= s - 1;
            ret++;
        }
        return ret;
    }
};
```























# 最长回文子串*

## 中心扩展算法

```C++
class Solution {
public:
    pair<int, int> expandAroundCenter(const string& s, int left, int right) {
        while (left >= 0 && right < s.size() && s[left] == s[right]) {
            --left;
            ++right;
        }
        return {left + 1, right - 1};
    }

    string longestPalindrome(string s) {
        int start = 0, end = 0;
        for (int i = 0; i < s.size(); ++i) {
            auto [left1, right1] = expandAroundCenter(s, i, i);
            auto [left2, right2] = expandAroundCenter(s, i, i + 1);
            if (right1 - left1 > end - start) {
                start = left1;
                end = right1;
            }
            if (right2 - left2 > end - start) {
                start = left2;
                end = right2;
            }
        }
        return s.substr(start, end - start + 1);
    }
};
```









## 动态规划









## Manacher算法











# 罗马数字转整数

```c++
#include <iostream>
#include <map>
#include <vector>
#include <string>
using namespace std;


class Solution {
public:
	int romanToInt(string s) {
		map<char, int> hashMap = {
			{'I', 1}, {'V', 5}, {'X', 10}, {'L', 50},
			{'C', 100}, {'D', 500}, {'M', 1000},
			{'a', 4}, {'b', 9}, {'c', 40}, {'d', 90},
			{'e', 400}, {'f', 900}
		};

		vector<string> v1 = { "IV", "IX", "XL", "XC", "CD", "CM" };
		vector<string> v2 = { "a", "b", "c", "d", "e", "f" };
		for (int i = 0; i < v1.size(); ++i) {
			int pos = s.find(v1[i]);
			if (pos != -1) {
				s.replace(pos, 2, v2[i]);
			}
		}

		int ans = 0;
		for (const char& ch : s) {
			ans += hashMap[ch];
		}
		return ans;
	}
};



void test01() {
	string str = "LVIII";

	Solution s;
	
	int ans = s.romanToInt(str);
	cout << ans << endl;

}


int main() {

	test01();

	system("pause");
	return 0;

}

```



**改进后：**

```C++
#include <iostream>
#include <unordered_map>
#include <string>
using namespace std;


class Solution {
private:
	unordered_map<char, int> hashMap = {
			{'I', 1}, {'V', 5}, {'X', 10}, {'L', 50},
			{'C', 100}, {'D', 500}, {'M', 1000}
	};

public:
	int romanToInt(string s) {
		int ans = 0;
		int n = s.size();
		for (int i = 0; i < n; ++i) { 
			if (i < n - 1 && hashMap[s[i]] < hashMap[s[i + 1]]) {
				ans -= hashMap[s[i]];
			}
			else {
				ans += hashMap[s[i]];
			}
		}
		return ans;
	}
};



void test01() {
	string str = "LVIII";

	Solution s;
	
	int ans = s.romanToInt(str);
	cout << ans << endl;

}


int main() {

	test01();

	system("pause");
	return 0;

}
```







# 环形链表

## 哈希集合法

```C++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        unordered_set<ListNode*> seen;
        while (head != nullptr) {
            if (seen.count(head)) {
                return true;
            }
            seen.insert(head);
            head = head->next;
        }
        return false;
    }
};
```

**时间复杂度：**$O(n)$

**空间复杂度：**$O(n)$

$n$是链表节点数



## 快慢指针法

两个指针，一快一慢，快的在慢的前面，如果快的追上慢的，就说明有环。

```C++
class Solution {
public:
    bool hasCycle(ListNode* head) {
        if (head == nullptr || head->next == nullptr) {
            return false;
        }
        ListNode* slow = head;
        ListNode* fast = head->next;
        while (slow != fast) {
            if (fast == nullptr || fast->next == nullptr) {
                return false;
            }
            slow = slow->next;
            fast = fast->next->next;
        }
        return true;
    }
};
```



**时间复杂度：**$O(n)$

**空间复杂度：**$O(1)$











# 相交链表

## 哈希集合

```c++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        unordered_set<ListNode *> visited;
        ListNode *temp = headA;
        while (temp != nullptr) {
            visited.insert(temp);
            temp = temp->next;
        }
        temp = headB;
        while (temp != nullptr) {
            if (visited.count(temp)) {
                return temp;
            }
            temp = temp->next;
        }
        return nullptr;
    }
};
```

**时间复杂度：**$O(m + n)$

**空间复杂度：**$O(m)$







## 双指针

**思路和算法：**

使用双指针的方法，可以将空间复杂度降至 $O(1)$。

只有当链表 $headA$ 和  $headB$ 都不为空时，两个链表才可能相交。因此首先判断链表 $ headA$ 和  $headB$ 是否为空，如果其中至少有一个链表为空，则两个链表一定不相交，返回 $null$。

当链表 $headA$ 和 $headB$ 都不为空时，创建两个指针 $pA$ 和 $pB$，初始时分别指向两个链表的头节点  $headA $和  $headB$，然后将两个指针依次遍历两个链表的每个节点。具体做法如下：

每步操作需要同时更新指针 $pA$ 和 $pB$。

如果指针 $pA$ 不为空，则将指针 $pA $ 移到下一个节点；如果指针  $pB$ 不为空，则将指针 $pB$ 移到下一个节点。

如果指针 $pA$ 为空，则将指针  $pA$ 移到链表  $headB$ 的头节点；如果指针  $pB $ 为空，则将指针  $pB$ 移到链表 $headA$ 的头节点。

当指针  $pA $和  $pB$ 指向同一个节点或者都为空时，返回它们指向的节点或者 $ null$。



**也可以理解为：**若两链表相交前的长度不同，则将两个链表进行拼接，A接到B后面，B接到A后面。则拼接后的两链表的第二个相交点之前的长度一定相等，一定能保证两个指针同时到达相交点。

```C++
class Solution{
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB){
		if(headA == nullptr || headB == nullptr) {
			return nullptr;
        }
        ListNode *pA = headA, *pB = headB;
        while (pA != pB) {
			pA = pA == nullptr ? headB : pA->next;
            pB = pB == nullptr ? headA : pB->next;
        }
        return pA;
    }
};
```

**时间复杂度：**$O(m + n)$

**空间复杂度：**$O(1)$







# 整数转罗马数字

```c++
class Solution {
public:
    string intToRoman(int num) {
        unordered_map<int, char> mp = {
            {1, 'I'}, {5, 'V'},
            {10, 'X'}, {50, 'L'},
            {100, 'C'}, {500, 'D'},
            {1000, 'M'},
        };

        string str = "";
		
        //依次取出每一位上的数字，将该数字乘以其位数，转成相应的罗马数字
        //如：1994，先把4转成"IV",再拿出90，转成"XC",再拿出900,...
        
        //位数
        int val = 1; 
        //每一位上的数
        int digit = 0; 
        while (num != 0) {
            //该位数字乘位数，得到其代表的数值
            digit = val * (num % 10);
            
            //注意，insert方法的两个重载版本：
            //1. 在指定位置插入字符串(string类型)
            //string& insert(int pos, const string& str); 
            //2. 在指定位置插入n个字符c(char类型)
            //string& insert(int pos, int n, char c);
            
            //如果在哈希表里，直接头插到结果字符串中
            if (mp.count(digit)) {
                str.insert(0, 1, mp[digit]);
            }
            //如果不在哈希表中，先看看是不是4,9,40,90,400,900这几种特殊情况
            else if ((digit / val) % 5 == 4) {    
                //如果是，先插入相应的'V','X','L','C','D','M'
                str.insert(0, 1, mp[5 * val * ((digit / val) / 5 + 1)]);
                //再插入相应的'I','X','C'
                str.insert(0, 1, mp[val]);
            }
            //如果不是那几种特殊情况，看看是小于5 * val 还是大于
            else if (digit < (5 * val)) {
                //小于，则插入相应个数的'I','X','C'
                str.insert(0, digit / val, mp[val]);
            }
            else {
                //大于，先插入相应的'V','X','L','C','D','M'
                str.insert(0, digit / val - 5, mp[val]);
                //再插入相应个数的'I','X','C'
                str.insert(0, 1, mp[5 * val]);
            }

            val *= 10;
            num /= 10;
        }

        return str;
    }
};
```







**改进后**：

```c++
const pair<int, string> valueSymbols[] = {
    {1000, "M"},
    {900,  "CM"},
    {500,  "D"},
    {400,  "CD"},
    {100,  "C"},
    {90,   "XC"},
    {50,   "L"},
    {40,   "XL"},
    {10,   "X"},
    {9,    "IX"},
    {5,    "V"},
    {4,    "IV"},
    {1,    "I"},
};

class Solution {
public:
    string intToRoman(int num) {
        string roman;
        for (const auto &[value, symbol] : valueSymbols) {
            while (num >= value) {
                num -= value;
                roman += symbol;
            }
            if (num == 0) {
                break;
            }
        }
        return roman;
    }
};
```









# 多数元素

```C++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int n = nums.size();
        unordered_map<int, int> hashMap;
        for (const int& num : nums) {
            if (!hashMap.count(num)) {
                hashMap[num] = 1;
            }
            else if (hashMap.count(num)) {
                ++hashMap[num];
            }
            
            if (hashMap[num] > (n / 2)){
                return num;
            }
        }
        return NULL;
    }
};
```



**改进后：**

```C++
lass Solution {
public:
    int majorityElement(vector<int>& nums) {
        int n = nums.size();
        unordered_map<int, int> hashMap;
        
        for (const int& num : nums) {
            ++hashMap[num];
            if (hashMap[num] > (n / 2)){
                return num;
            }
        }
        return NULL;
    }
};
```





# 快乐数

## 哈希集合法

```c++
class Solution {
public:
    bool isHappy(int n) {
        int num = n;
        int digit = 0;
        int sum = 0;
        unordered_set<int> hashSet;

        //如果有一个结果在哈希集合中出现过,就表明会开始无限循环,此时即可退出循环返回false
        while (!hashSet.count(sum)) {
            //算出来的结果放入哈希集合
            hashSet.insert(sum);
            sum = 0;
            //依次取出n的每一位上的数字，进行平方和运算
            while (num != 0) {
                digit = num % 10;
                sum += digit * digit;
                num /= 10;
            }
            num = sum;

            //如果num = 1，return true;
            if (num == 1) {
                return true;
            }
        }
        //如果开始无限循环，return flase;
        return false;
    }
};
```



```python
def isHappy(self, n: int) -> bool:
    def get_next(n):
        total_sum = 0
        while n > 0:
            n, digit = divmod(n, 10)
            total_sum += digit ** 2
        return total_sum
    
    seen = set()
    while n != 1 and n not in seen:
        seen.add(n)
        n = get_next(n)
        
    return n == 1
```





## 快慢指针法

通过反复调用 getNext(n) 得到的链是一个隐式的链表。隐式意味着我们没有实际的链表节点和指针，但数据仍然形成链表结构。起始数字是链表的头 “节点”，链中的所有其他数字都是节点。next 指针是通过调用 getNext(n) 函数获得。

意识到我们实际有个链表，那么这个问题就可以转换为检测一个链表是否有环。因此我们在这里可以使用弗洛伊德循环查找算法。这个算法是两个奔跑选手，一个跑的快，一个跑得慢。在龟兔赛跑的寓言中，跑的慢的称为 “乌龟”，跑得快的称为 “兔子”。

不管乌龟和兔子在循环中从哪里开始，它们最终都会相遇。这是因为兔子每走一步就向乌龟靠近一个节点（在它们的移动方向上）。

我们不是只跟踪链表中的一个值，而是跟踪两个值，称为快跑者和慢跑者。在算法的每一步中，慢速在链表中前进 1 个节点，快跑者前进 2 个节点（对 getNext(n) 函数的嵌套调用）。

如果 n 是一个快乐数，即没有循环，那么快跑者最终会比慢跑者先到达数字 1。

如果 n 不是一个快乐的数字，那么最终快跑者和慢跑者将在同一个数字上相遇。

```c++
class Solution{
public:
    bool isHappy(int n){
        int slowRunner = n;
        int fastRunner = getNext(n);
        while (fastRunner != 1 and slowRunner != fastRunner) {
			slowRunner = getNext(slowRunner);
            fastRunner = getNext(getNext(fastRunner));
        }
        return fastRunner == 1; 
    }
    
private:
    int getNext(int number){
        int totalSum = 0;
        while (number > 0){
            int digit = number % 10;
            number /= 10;
            totalSum += digit * digit;
        }
        return totalSum;
	}
};
```



```python
def isHappy(self, n: int) -> bool:
    def get_next(number):
        total_sum = 0
        while number > 0:
            number, digit = divmod(number, 10)
            total_sum += digit ** 2
        return total_sum
    
    
	slow_runner = n
    fast_runner = get_next(n)
    while fast_runner != 1 and slow_runner != fast_runner:
        slow_runner = get_next(slow_runner)
        fast_runner = get_next(get_next(fast_runner))
    return fast_runner == 1
```









# 有效的数独

```C++
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {

        //创建三个哈希表数组，分别用于对行、列、九宫格进行判断
        //注意：这里声明的是哈希表数组
        //声明格式：unordered_map<typename, typename> name[size];

        //row[i]中的i为行数，row[i][num]中的num是数组第i个哈希表的键，其对应的值即表示在第i行数字num出现的次数
        unordered_map<int, int> row[9];
        unordered_map<int, int> col[9];
        unordered_map<int, int> box[9];

        //开始遍历数组
        int num, boxIdx;
        for (int i = 0; i < 9; ++i) {
            for (int j = 0; j < 9; ++j) {
                num = board[i][j] - '0';   //符号数字化
                boxIdx = (i / 3) * 3 + j / 3; //确定是第几个九宫格
                if (board[i][j] == '.') continue;

                //mapName.find(key); 查找key键元素是否存在，存在则返回该元素的迭代器，不存在返回mapName.end()
                //即mapName.find(key) != mapName.end()为真，代表存在key
                //当然也可以用mapName.count(key)来判断key是否存在，但底层效率应该低于前者
                else if (row[i].find(num) != row[i].end())  return false;
                else if (col[j].find(num) != col[j].end())  return false;
                else if (box[boxIdx].find(num) != col[boxIdx].end())  return false;
                else {
                    row[i][num] = 1; //在第i行，num出现了1次
                    col[j][num] = 1; //在第j列，num出现了1次
                    box[boxIdx][num] = 1; //在第boxIdx个九宫格，num出现了1次
                }
            }
        }
        return true;
    }
};
```







# 同构字符串

```C++
class Solution {
public:
    bool isIsomorphic(string s, string t) {
        unordered_map<char, char> hashMap;
        unordered_set<char> hashSet;
        for (int i = 0; i < s.size(); ++i) {
            if(!hashMap.count(s[i]) && !hashSet.count(t[i])) {
                hashMap[s[i]] = t[i];
                hashSet.insert(t[i]);
            }
            else if (hashMap[s[i]] != t[i]) return false;
        }
        return true;
    }
};
```







# 存在重复元素

```C++
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        unordered_set<int> hashSet;
        for(const int& num : nums) {
            if(hashSet.find(num) == hashSet.end()) {
                hashSet.insert(num);
            }
            else{
                return true;
            }
        }
        return false;
    }
};
```







# 存在重复元素II

此题用暴力解法需要 $O(nmin(k, n))$ 的时间复杂度，$n$ 比较大时会超时



## 哈希映射

```C++
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        unordered_map<int, int> mp; //用unordered_map而不是map,前者查找效率为常数时间，后者为O(logn)
        for (int i = 0; i < nums.size(); ++i) {
            if (mp.count(nums[i])) {
                if (abs(mp[nums[i]] - i) <= k) return true;
                else mp[nums[i]] = i;
            }
            else {
                mp[nums[i]] = i;
            }
        }
        return false;
    }
};
```



## 哈希集合（滑动窗口）

```C++
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        unordered_set<int> st;
        for (int i = 0; i < nums.size(); ++i) {
            if (st.count(nums[i]))  return true;
            st.insert(nums[i]);
            if (st.size() > k) st.erase(nums[i - k]);
        }
        return false;
    }
};
```





# 最大子序和

## 动态规划

**思路和算法**

假设$nums$数组的长度是$n$，下标从$0$到$n-1$。

**状态：**$f(i) = 以第i个数结尾的连续子数组最大和$

求出每个位置的$f(i)$，然后返回数组中最大值即可。

**状态转移方程：**$f(i) = max(f(i -  1) + nums[i], nums[i])$



可以用一个$f$数组来保存$f(i)$的值，用一个循环求出所以$f(i)$，考虑到$f(i)$只与$f(i - 1)$相关，于是可以用一个变量$pre$来维护对于当前的$f(i)$的$f(i - 1)$的值是多少，从而让空间复杂度降低到$O(1)$，这有点类似**【滚动数组】**的思想



```C++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int pre = 0, maxAns = nums[0];
        for (const auto &x: nums) {
            pre = max(pre + x, x);
            maxAns = max(maxAns, pre);
        }
        return maxAns;
    }
};
 
```

**复杂度**

时间复杂度：$O(n)$，其中 $n$ 为 $nums$ 数组的长度。我们只需要遍历一遍数组即可求得答案。
空间复杂度：$O(1)$。我们只需要常数空间存放若干变量。

 



## 分治*

**思路和算法：**

**类似于【线段树求解最长公共上升子序列问题】的`pushUp`操作。**



关于**线段树**的详细内容，推荐阅读线段树区间合并法解决**多次询问**的【区间最长连续上升子序列问题】和【区间最大子段和问题】



定义一个操作`get(a, l, r)`表示查询$a$序列$[l,r]$区间内的最大子段和，那么最终我们要求的答案就是`get(nums, 0, nums.size() - 1)`。如何分治实现这一操作呢？

对于区间$[l, r]$，取$m = [(l+r) / 2]$，对区间$[l,m]$和$[m+1,r]$分治求解。当递归层深入直到区间长度缩小为$1$的时候，递归【开始回升】。这时考虑如何通过 $[l,m]$ 区间的信息和 $[m+1,r]$区间的信息合并成区间  $[l,r]$ 的信息。最关键的两个问题是：

我们要维护区间的哪些信息呢？
我们如何合并这些信息呢？
对于一个区间 $[l,r] $，我们可以维护四个量：

$ lSum$ 表示 $[l,r]$  内以$ l$  为左端点的最大子段和
$ rSum$ 表示 $[l,r]$  内以  $r$ 为右端点的最大子段和
 $mSum$ 表示 $[l,r]$  内的最大子段和
$ iSum$ 表示 $[l,r]$  的区间和
以下简称 $[l,m]$  为 $[l,r] $ 的「左子区间」，$[m+1,r]$  为 $[l,r]$  的「右子区间」。我们考虑如何维护这些量呢（如何通过左右子区间的信息合并得到 $[l,r]$  的信息）？对于长度为 $1$1 的区间 $[i, i]$ ，四个量的值都和 $\textit{nums}[i]$  相等。对于长度大于 $1$ 的区间： 



<img src="LeetCode%E5%88%B7%E9%A2%98.assets/image-20210830164012735.png" alt="image-20210830164012735" style="zoom: 80%;" />





```c++
class Solution {
public:
    struct Status {
        int lSum, rSum, mSum, iSum;
    };

    Status pushUp(Status l, Status r) {
        int iSum = l.iSum + r.iSum;
        int lSum = max(l.lSum, l.iSum + r.lSum);
        int rSum = max(r.rSum, r.iSum + l.rSum);
        int mSum = max(max(l.mSum, r.mSum), l.rSum + r.lSum);
        return (Status) {lSum, rSum, mSum, iSum};
    };

    Status get(vector<int> &a, int l, int r) {
        if (l == r) {
            return (Status) {a[l], a[l], a[l], a[l]};
        }
        int m = (l + r) >> 1;
        Status lSub = get(a, l, m);
        Status rSub = get(a, m + 1, r);
        return pushUp(lSub, rSub);
    }

    int maxSubArray(vector<int>& nums) {
        return get(nums, 0, nums.size() - 1).mSum;
    }
};
 
```

![image-20210830164140638](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210830164140638.png)







# 爬楼梯

```c++
class Solution {
public:
    int climbStairs(int n) {
        vector<int> f(n + 1);
        f[0] = 1, f[1] = 1;

        for( int i = 2; i <= n; ++i) {
            f[i] = f[i - 1] + f[i - 2];
        }
        
        return f[n];
    }
};
```



**用滚动数组思想优化：**

我们不难通过转移方程和边界条件给出一个时间复杂度和空间复杂度都是  $O(n)$ 的实现，但是由于这里的$  f(x)$ 只和 $ f(x−1) $与 $f(x−2)$ 有关，所以我们可以用**「滚动数组思想」**把空间复杂度优化成 $O(1)$ 。下面的代码中给出的就是这种实现。



 ![fig1](LeetCode%E5%88%B7%E9%A2%98.assets/70_fig1.gif)

```c++
class Solution {
public:
    int climbStairs(int n) {
        int p = 0, q = 0, r = 1;
        for (int i = 1; i <= n; ++i) {
            p = q; 
            q = r; 
            r = p + q;
        }
        return r;
    }
};
 
```





![image-20210830171851859](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210830171851859.png)



**矩阵快速幂**方法详见：https://leetcode-cn.com/problems/climbing-stairs/solution/pa-lou-ti-by-leetcode-solution/



# 二叉树前序遍历

## 递归

```C++
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        preorder(root, res);
        return res;
    }

    void preorder(TreeNode* root, vector<int>& res) {
        if(!root) return;

        res.push_back(root->val);
        preorder(root->left, res);
        preorder(root->right, res);
    }
};
```



## 迭代

```C++
template <class T>
struct binaryTreeNode
{
    T element;
    binaryTreeNode<T>* leftChild, * rightChild;

    binaryTreeNode() { leftChild = rightChild = NULL; }
    binaryTreeNode(const T& theElement)
    {
        element(theElement);
        leftChild = rightChild = NULL;
    }
    binaryTreeNode(const T& theElement,
        binaryTreeNode* theLeftChild,
        binaryTreeNode* theRightChild)
    {
        element(theElement);
        leftChild = theLeftChild;
        rightChild = theRightChild;
    }
};
 
class Solution {
public:
    vector<int> preorderTraversal(binaryTreeNode<int>* root) {
        vector<int> res;
        if (root == nullptr) return res;

        stack<binaryTreeNode<int>*> stk;
        binaryTreeNode<int>* node = root;
        while (!stk.empty() || node != nullptr) {
            while (node != nullptr) {
                res.emplace_back(node->element);
                stk.emplace(node);
                node = node->leftChild;
            }
            node = stk.top();
            stk.top();
            node = node->rightChild;
        }
        return res;
    }
};
```

![image-20210914111344620](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210914111344620.png)







# 二叉树中序遍历

## 递归

定义 `inorder(root)` 表示当前遍历到 `root` 节点的答案，那么按照定义，我们只要递归调用 `inorder(root.left)` 来遍历  `root` 节点的左子树，然后将  `root` 节点的值加入答案，再递归调用`inorder(root.right)` 来遍历  `root` 节点的右子树即可，递归终止的条件为碰到空节点。

```C++
class Solution {
public:
    void inorder(TreeNode* root, vector<int>& res) {
        if (!root) {
            return;
        }
        inorder(root->left, res);
        res.push_back(root->val);
        inorder(root->right, res);
    }
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        inorder(root, res);
        return res;
    }
};
```

**复杂度分析**

**时间复杂度**：$O(n)$，其中 $n $ 为二叉树节点的个数。二叉树的遍历中每个节点会被访问一次且只会被访问一次。

**空间复杂度**：$O(n)$。空间复杂度取决于递归的栈深度，而栈深度在二叉树为一条链的情况下会达到 $O(n)$ 的级别。

 

 

## 迭代

方法一的递归函数我们也可以用迭代的方式实现，两种方式是等价的，区别在于递归的时候隐式地维护了一个栈，而我们在迭代的时候需要显式地将这个栈模拟出来，其他都相同。

 ```C++
 class Solution{
 public:
     vector<int> inorderTraversal(TreeNode* root) {
 		vector<int> res;
         stack<TreeNode*> stk;
         while (root != nullptr || !stk.empty()) {
             //如果有左子树，就一直往左子树走，一边走一边把节点压入栈
             //被压入栈表示该节点待处理，要等它的左子树都处理完再处理该节点
             //然后再处理该节点的右子树
             while (root != nullptr) {
 				stk.push(root);
                 root = root->left;
             }
             //如果左子树到底了，就把root设为栈顶元素，开始处理栈顶及栈顶的右子树
             root = stk.top();
             //栈顶元素值放入res中，即可出栈
             stk.pop();
             res.push_back(root->val);
             //然后继续对右子树进行中序遍历：右子树的左子树，右子树的根节点，右子树的右子树
             root = root->right;
         }
         return res;
     }
 };
 ```

**复杂度分析**

**时间复杂度**：$O(n)$，其中 $n$ 为二叉树节点的个数。二叉树的遍历中每个节点会被访问一次且只会被访问一次。

空间复杂度：$O(n)$。空间复杂度取决于栈深度，而栈深度在二叉树为一条链的情况下会达到 $O(n)$ 的级别。

 

## Morris 遍历算法*

Morris遍历算法是一种遍历二叉树的方法，它能将非递归的中序遍历空间复杂度降为$O(1)$

详见https://leetcode-cn.com/problems/binary-tree-inorder-traversal/solution/er-cha-shu-de-zhong-xu-bian-li-by-leetcode-solutio/







# 二叉树后序遍历

## 递归

```C++
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> res;
        postorder(root, res);
        return res;
    }

    void postorder(TreeNode* root, vector<int>& res){
        if(!root) return;

        postorder(root->left, res);
        postorder(root->right, res);
        res.push_back(root->val);
    }
};
```





## 迭代

```C++
class Solution {
public:
    vector<int> postorderTraversal(binaryTreeNode<int>* root) {
        vector<int> res;
        if (root == nullptr) return res;

        stack<binaryTreeNode<int>*> stk;
        binaryTreeNode<int>* pre = nullptr;
        while (root != nullptr || !stk.empty()) {
            while (root != nullptr) {
                stk.emplace(root);
                root = root->leftChild;
            }
            root = stk.top();
            stk.pop();
            if (root->rightChild == nullptr || root->rightChild == pre) {
                res.emplace_back(root->element);
                pre = root;
                root = nullptr;
            }
            else {
                stk.emplace(root);
                root = root->rightChild;
            }
        }
        return res;
    }
};

```





# 对称二叉树

## 递归

我们可以通过[同步移动]两个指针的方法来遍历这棵树,  $p$指针和$q$指针一开始都指向这棵树的根, 随后$p$向右移动时, $q$左移, $p$左移时, $q$右移. 每次检查当前$p$和$q$的节点值是否相等, 相等再判断左右子树是否对称.

```C++
class Solution {
public:
    bool check(TreeNode *p, TreeNode *q) {
        if (!p && !q) return true;
        if (!p && !q) return false;
        return p->val == q->val && check(p->left, q->right) && check(p->right, q->left);
    }
    
    bool isSymmetric(TreeNode* root) {
        return check(root, root);
    }
};
```

复杂度分析

假设树上一共  $n$ 个节点。

时间复杂度：这里遍历了这棵树，渐进时间复杂度为 $O(n) $。
空间复杂度：这里的空间复杂度和递归使用的栈空间有关，这里递归层数不超过 $n$，故渐进空间复杂度为 $O(n) $。

 

## 迭代

首先我们引入一个队列，这是**把递归程序改成迭代程序的常用方法**。初始化时我们把根节点入队两次，每次提取两个节点并比较它们的值（队列中每两个连续节点应该是相等的，而且它们的子树互为镜像），然后将两个节点的左右子节点按相反顺序插入队列中。队列为空时，或者检测到树不对称（即从队列中取出两个不相等的连续节点）时，算法结束。

```C++
class Solution {
public:
    bool check(TreeNode *u, TreeNode *v) {
        queue<TreeNode*> q;
        //刚开始先把根节点入队两次
        q.push(u); q.push(v);
        
        //如果检测完整棵树了（队列空了），且没有检测到不对称（没有返回false），则返回true
        while (!q.empty()) {
            //每次从队列中连续取出两个
			u = q.front(); q.pop();
            v = q.front(); q.pop();
            //连续取出两个同时为空没有问题
            if (!u && !v) continue;
            //如果检测到只有一个为空，或者两个连续节点值不相等，返回false
            if ((!u || !v) || (u->val != v->val)) return false;
            
            //一个左移，则另一个右移
            q,push(u->left);
            q.push(v->right);
            
            //一个右移，则另一个左移
            q.push(u->right);
            q.push(u->left);
        }
        return true;
    }
    
    bool isSymmetric(TreeNode* root) {
        return check（root, root);
    }
};
```

**复杂度分析**

**时间复杂度**：$O(n)$
**空间复杂度**：这里需要用一个队列来维护节点，每个节点最多进队一次，出队一次，队列中最多不会超过  $n$ 个点，故渐进空间复杂度为  $O(n)$。

   





# 二叉树的最大深度

## 深度优先搜索

如果求出左子树和右子树的最大深度 $l$ 和 $r$，那么整棵树的最大深度就是 $max(l,r) + 1$

而对于左子树或右子树，也可以用同样的方法求其最大深度，由此想到递归。

```C++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root) return 0;
        return max(maxDepth(root->left), maxDepth(root->right)) + 1;
    }
};
```

**复杂度分析**

**时间复杂度：** **O(n)**，其中  **n** 为二叉树节点的个数。每个节点在递归中只被遍历一次。

**空间复杂度：** **O(height)**，其中  **height** 表示二叉树的高度。递归函数需要栈空间，而栈空间取决于递归的深度，因此空间复杂度等价于二叉树的高度。

 

## 广度优先搜索

广度优先搜索需要一个队列，开始先把根节点入队，然后进行如下循环：

把队首节点赋给`current`；

若`current`节点的左儿子存在，入队； 若`current`节点的右儿子存在，入队；

然后首节点出队。

进行上述循环直到队列为空。

**广度优先搜索的代码如下：**

```C++
struct TreeNode {
    int val;
    TreeNode* left, * right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode* left, TreeNode* right) : val(x), left(left), right(right) {}
};

class Solution {
public:
    void BFS(TreeNode* root) {
        queue<TreeNode*> Q;
        Q.push(root);
        while (!Q.empty()) {
            TreeNode* current = Q.front();Q.pop();
            if (current->left) Q.push(current->left);
            if (current->right) Q.push(current->right);
            cout << current->val << endl;
        } 
};
```





**此题需要在此基础上做一些调整：**

每次出队上一层的所有节点，并入队下一层的所有节点

每次将上一层所有节点清空完就把层数`level`加一

代码如下：

```C++
class Solution {
public:
    int maxDepth(TreeNode* root) {
		if (!root) return 0;
        queue<TreeNode*> Q;
        Q.push(root);
        int level = 0;
        
        while (!Q.empty()) {
            int sz = Q.size();
            while (sz > 0) {
				TreeNode* current = Q.front(); Q.pop();
                if(current->left) Q.push(current->left);
                if(current->right) Q.push(current->right);
                --sz;
            }
            ++level;
        }
        return level;
    }
};
```

复杂度分析

时间复杂度：$O(n)$，其中 $n $ 为二叉树的节点个数。与方法一同样的分析，每个节点只会被访问一次。

空间复杂度：此方法空间的消耗取决于队列存储的元素数量，其在最坏情况下会达到 $O(n)$。







# 只出现一次的数字

**如果不考虑时间复杂度和空间复杂度的限制，这道题有很多种解法，可能的解法有如下几种 :**

1. 使用集合存储数字。遍历数组中的每个数字，如果集合中没有该数字，则将该数字加入集合，如果集合中已经有该数字，则将该数字从集合中删除，最后剩下的数字就是只出现一次的数字。

2. 使用哈希表存储每个数字和该数字出现的次数。遍历数组即可得到每个数字出现的次数，并更新哈希表，最后遍历哈希表，得到只出现一次的数字。

3. 使用集合存储数组中出现的所有数字，并计算数组中的元素之和。由于集合保证元素无重复，因此计算集合中的所有元素之和的两倍，即为每个元素出现两次的情况下的元素之和。由于数组中只有一个元素出现一次，其余元素都出现两次，因此用集合中的元素之和的两倍减去数组中的元素之和，剩下的数就是数组中只出现一次的数字。

上述三种解法都需要额外使用 $O(n)$ 的空间，其中 $n$ 是数组长度。

如何才能做到**线性时间复杂度和常数空间复杂度**呢？

答案是使用**位运算**。对于这道题，可使用异或运算 $⊕$ 。异或运算有以下三个性质。

任何数和 0 做异或运算，结果仍然是原来的数，即 $a⊕0=a$。
任何数和其自身做异或运算，结果是 0，即  $a⊕a=0$。
异或运算满足交换律和结合律，即  $a⊕b⊕a=b⊕a⊕a=b⊕(a⊕a)=b⊕0=b$。

 因此，数组中的全部元素的异或运算结果即为数组中只出现一次的数字。



```C++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ret = 0;
        for (auto e: nums) ret ^= e;
        return ret;
    }
};
```

**复杂度分析**

- 时间复杂度：$O(n)$，其中 $n$ 是数组长度。只需要对数组遍历一次。
- 空间复杂度：$O(1)$ 。
复杂度分析

 





# 删除有序数组中的重复项

## STL

```C++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        if(nums.size() == 0) return 0;
        vector<int>::iterator it = nums.begin();
        int num = *it;
        it++;
        
        //这个while循环是vector数组遍历删除操作的精髓
        while(it != nums.end()) {
            if(*it == num) {
                it = nums.erase(it);
            }
            else{
                num = *it;
                it++;
            }
        }
        
        return nums.size();
    }
};
```



## 快慢指针

**核心思想： 直接把输出数组写在输入数组上，慢指针指向下一个将要赋值的元素，快指针指向当前要处理的元素**。进行如下循环：

- 快指针遍历数组，若碰到一个元素不同于上一个元素，则是输出数组的一个元素，把快指针指向的元素值复制到慢指针指向的位置处，然后将快慢指针同时右移；

- 如果快指针指向的元素和上一个元素相同，则它不能在输出数组里，此时慢指针不动，快指针右移一位



```C++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
		int n = nums.size();
        if(n == 0) return 0;
        
        int fast = 1, slow = 1;
        while (fast < n) {
            if (nums[fast] != nums[fast - 1]) {
				nums[slow] = nums[fast];
                ++slow;
            }
            ++fast;
        }
        return slow;
    }  
};
```







# 移除元素

## 双指针

**左指针指向下一个将要赋值的元素，右指针指向当前要处理的元素**。进行如下循环：

- 如果右指针指向的元素不等于 $val$，它一定是输出数组的一个元素，我们就将右指针指向的元素复制到左指针位置，然后将左右指针同时右移；

- 如果右指针指向的元素等于 $val$，它不能在输出数组里，此时左指针不动，右指针右移一位。

 整个过程保持不变的性质是：区间  $[0,left)$ 中的元素都不等于  $val$。当左右指针遍历完输入数组以后, $left$ 的值就是输出数组的长度。

这样的算法在最坏情况下（输入数组中没有元素等于$val$），左右指针各遍历了数组一次。

```C++
class Solution {
public:
    int removeElement (vector<int>& nums, int val) {
		int n = nums.size();
        int left = 0;
        for(int right = 0; right < n; right++) {
			if(nums[right] != val) {
				nums[left] = nums[right];
                left++;
            }
        }
        return left;
    }
};
```

**复杂度分析**

**时间复杂度：**最多遍历两次，$O(n)$

**空间复杂度：**只用了常数空间保存若干变量





## 双指针优化

**思路**

如果要移除的元素恰好在数组的开头，例如序列 $ [1,2,3,4,5]$，当 $ val$ 为 $ 1$ 时，我们需要把每一个元素都左移一位。注意到题目中说：「元素的顺序可以改变」。实际上我们可以直接将最后一个元素  $5$ 移动到序列开头，取代元素 $ 1$，得到序列 $[ 5,2,3,4]$，同样满足题目要求。这个优化在序列中 $ val $ 元素的数量较少时非常有效。

实现方面，我们依然使用双指针，两个指针初始时分别位于数组的首尾，向中间移动遍历该序列。

**算法**

如果左指针  $left$ 指向的元素等于 $val$，此时将右指针 $ right$ 指向的元素复制到左指针 $ left$ 的位置，然后右指针  $right $ 左移一位。如果赋值过来的元素恰好也等于 $  val$，可以继续把右指针 $ right$ 指向的元素的值赋值过来（左指针 $ left$ 指向的等于 $val$ 的元素的位置继续被覆盖），直到左指针指向的元素的值不等于 $ val$ 为止。

当左指针 $left$ 和右指针 $ right$ 重合的时候，左右指针遍历完数组中所有的元素。

这样的方法两个指针在最坏的情况下合起来只遍历了数组一次。与方法一不同的是，**方法二避免了需要保留的元素的重复赋值操作。**

```C++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
		int left = 0, right = nums.size();
        while (left < right) {
            if (nums[left] == val) {
                nums[left] == nums[right - 1];
                right--;
            }
            else {
                left++;
            }
        }
        return left;
    }
};
```

**复杂度分析**

**时间复杂度：** $O(n)$, 至多遍历一次

**空间复杂度：** $O(1)$





## STL

```C++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        vector<int>::iterator it = nums.begin();
        while (it != nums.end()) {
            if(*it == val) {
                it = nums.erase(it);
            }
            else {
                it++;
            }
        }
        return nums.size();
    }
};
```





# 将有序数组转换为二叉搜索树

## 向二叉搜索树中插入节点的算法

先给出一个**向二叉搜索树中插入节点的算法**作为引子：

```C++
struct TreeNode {
	int val;
    TreeNode* left, * right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode* left, TreeNode* right) : val(x), left(left), right(right) {}
};

class Solution{
public:
    //向根节点为root的二叉搜索树中插入值为val的一个节点
    bool insertBST(TreeNode* root, int val) {
        //递归终止条件：找到一个空位置就创建一个值为val的树节点
        if (!root) root = new TreeNode(val);
        //如果val等于某一个节点值，不符合二叉搜索树的定义，停止插入返回false
        else if (val == root->val) return false;
        //如果val比当前节点值小，就插入到当前节点的左子树中
        else if (val < root->val) insertBST(root->left, val);
        //如果val比当前节点值大，就插入到当前节点的右子树中
        else insertBST(root->right, val);
        return true;
    }
};
```





## 中序遍历，选择任意一个中间位置数字作为根节点

**二叉搜索树的中序遍历是升序序列**，题目给定的数组是按照升序排序的有序数组，因此可以确保数组是二叉搜索树的中序遍历序列。



**创建二叉树首先想到用递归，但具体如何实现呢？**

**或者如何想到递归？**

- 若找到一个中间位置作为根节点，那么数组中左侧的元素就是左子树，右侧元素就是右子树
- 而左侧的树也是有序的，**构造左子树的过程与原问题一致，只是规模变为原来的一半**
- 根据对称性，对于右子树也是同理

这样我们就**构造出一个子问题**：

**选取一个元素作为根节点，那么对该元素左侧的元素递归构成左子树，对该元素右侧的元素递归构成右子树**





那么如何选取根节点就成为了唯一的问题：

根据二叉搜索树的定义和本题的要求，发现根节点应设为中间位置的数字（如果数组大小为偶数，则设为中间左边或右边都可以）

```C++
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return helper(nums, 0, nums.size() - 1);
    }

    TreeNode* helper(vector<int>& nums, int left, int right) {
        if (left > right) {
            return nullptr;
        }

        // 选择任意一个中间位置数字作为根节点
        int mid = (left + right + rand() % 2) / 2;

        TreeNode* root = new TreeNode(nums[mid]);
        root->left = helper(nums, left, mid - 1);
        root->right = helper(nums, mid + 1, right);
        return root;
    }
};
```

复杂度分析

时间复杂度：$O(n)$ ，其中 $n$ 是数组的长度。每个数字只访问一次。

空间复杂度: $O(logn)$，其中 $n $ 是数组的长度。空间复杂度不考虑返回值，因此空间复杂度主要取决于递归栈的深度，递归栈的深度是  $O(logn)$。







# 翻转二叉树

## 递归

```C++
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (!root) {
            return nullptr;
        }

        TreeNode* node = invertTree(root->left);
        root->left = invertTree(root->right);
        root->right = node;

        return root;
    }
};
```

时间复杂度：$ O(N)$，其中 NN 为二叉树节点的数目。我们会遍历二叉树中的每一个节点，对每个节点而言，我们在常数时间内交换其两棵子树。

空间复杂度：$ O(N)$。使用的空间由递归栈的深度决定，它等于当前节点在二叉树中的高度。在平均情况下，二叉树的高度与节点个数为对数关系，即  $O(logN)$。而在最坏情况下，树形成链状，空间复杂度为 $O(N)$ 。









# 移动零

**思路：**先按照删除元素的思路把前面的数用非0数覆盖掉，再把剩余的数组空间用0补齐

```C++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int n = nums.size();
        //slow指向当前要填入的位置，fast指向当前遍历到的位置
        int slow = 0, fast = 0;
        while(slow < n) {
            if (fast == n) {
                nums[slow] = 0;
                slow++;
            }
            else if(nums[fast] != 0) {
                nums[slow] = nums[fast];
                slow++;
                fast++;
            }
            else {
                fast++;
            }
        }
        return;
    }
};
```

**复杂度分析**

时间复杂度：对数组遍历了两次，$O(n)$

空间复杂度：只需常数空间存储若干变量，$O(1)$









**改进：**

使用双指针，左指针指向当前已经处理好的序列的尾部，右指针指向待处理序列的头部。

右指针不断向右移动，每次右指针指向非零数，则将左右指针对应的数交换，同时左指针右移。

注意到以下性质：

左指针左边均为非零数；

右指针左边直到左指针处均为零。

因此每次交换，都是将左指针的零与右指针的非零数交换，且非零数的相对顺序并未改变。

 



```C++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
		int n = nums.size(), left = 0, right = 0;
        while(right < n) {
            if (nums[right]) {
                swap (nums[left], nums[right]);
                left++;
            }
            right++;
        }
    }
};
```









# 买卖股票的最佳时机

## 动态规划

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if (n < 2) return 0;
        vector<int> f(n + 1);
        //f[n] = 前n天的最大利润
        f[2] = max(0, prices[1] - prices[0]);

        int minVal = prices[0];
        for (int i = 3; i <= n; ++i) {
            //前i - 1天的最小值
            minVal = min(minVal, prices[i - 2]);
            //前i天的最大利润 = max(前i - 1天的最大利润, 第i天的价格-前i - 1天中的最低价格)
            f[i] = max(f[i - 1], prices[i - 1] - minVal);
        }

        return f[n];
    }
};
```

**复杂度分析**

时间复杂度：$O(n)$

空间复杂度：$O(n)$





## 一次遍历*（动态规划的优化？滚动数组的思想？）

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
		int minprice = prices[0], maxprofit = 0;
        for (int price: prices) {
            maxprofit = max(maxprofit, price - minprice);
            minprice = min(price, minprice);
        }
        return maxprofit;
    }
};
```

**复杂度分析**

- 时间复杂度：$O(n) $，只需要遍历一次。
- 空间复杂度：$O(1) $，只使用了常数个变量。





# 合并二叉树

## 深度优先搜索

```C++
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        if (!root1) return root2;
        if (!root2) return root1;

        return new TreeNode(root1->val + root2->val, mergeTrees(root1->left, root2->left), mergeTrees(root1->right, root2->right));
    }
};
```

**复杂度分析**

时间复杂度：$O(min(m,n))$，其中 $m$ 和  $n$ 分别是两个二叉树的节点个数。对两个二叉树同时进行深度优先搜索，只有当两个二叉树中的对应节点都不为空时才会对该节点进行显性合并操作，因此被访问到的节点数不会超过较小的二叉树的节点数。

空间复杂度： $O(min(m,n))$，其中 $m$ 和 $n $ 分别是两个二叉树的节点个数。空间复杂度取决于递归调用的层数，递归调用的层数不会超过较小的二叉树的最大高度，最坏情况下，二叉树的高度等于节点数。





## 广度优先搜索*

```C++
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* t1, TreeNode* t2) {
        if (t1 == nullptr) {
            return t2;
        }
        if (t2 == nullptr) {
            return t1;
        }
        auto merged = new TreeNode(t1->val + t2->val);
        auto q = queue<TreeNode*>();
        auto queue1 = queue<TreeNode*>();
        auto queue2 = queue<TreeNode*>();
        q.push(merged);
        queue1.push(t1);
        queue2.push(t2);
        while (!queue1.empty() && !queue2.empty()) {
            auto node = q.front(), node1 = queue1.front(), node2 = queue2.front();
            q.pop();
            queue1.pop();
            queue2.pop();
            auto left1 = node1->left, left2 = node2->left, right1 = node1->right, right2 = node2->right;
            if (left1 != nullptr || left2 != nullptr) {
                if (left1 != nullptr && left2 != nullptr) {
                    auto left = new TreeNode(left1->val + left2->val);
                    node->left = left;
                    q.push(left);
                    queue1.push(left1);
                    queue2.push(left2);
                } else if (left1 != nullptr) {
                    node->left = left1;
                } else if (left2 != nullptr) {
                    node->left = left2;
                }
            }
            if (right1 != nullptr || right2 != nullptr) {
                if (right1 != nullptr && right2 != nullptr) {
                    auto right = new TreeNode(right1->val + right2->val);
                    node->right = right;
                    q.push(right);
                    queue1.push(right1);
                    queue2.push(right2);
                } else if (right1 != nullptr) {
                    node->right = right1;
                } else {
                    node->right = right2;
                }
            }
        }
        return merged;
    }
};
```







# 括号生成*

## 暴力法

**思路：**

生成所有$2^{2n}$个`(`和`)`字符构成的序列，然后我们检查每个是否有效

**算法：**

为了生成所有序列，我们可以使用递归。长度为 `n` 的序列就是在长度为 `n - 1` 的序列前加一个 `(` 或 `)`。

为了检查序列是否有效，我们遍历序列，使用一个变量 `balance` 表示左括号的数量减去右括号的数量。如果在遍历过程中 `balance` 的值小于0，或者结束时 `balance` 不为0，那么该序列无效，否则有效。

```C++
class Solution {
private:
    bool valid (const string& str) {
        int balance = 0;
        for (char c : str) {
            if (c == 'c') {
                ++balance;
            } else {
                --balance;
            }
            if (balance < 0) {
                return false;
            }
        }
        return balance == 0;
    }
    
    void generate_all (string& current, int n, vector<string>& result) {
        if (n == current.size()) {
			if (valid(current)) {
				result.push_back(current);
            }
            return;
        }
        current += '(';
        generate_all(current, n, result);
        current.pop_back();
        current += ')';
        generate_all(current, n, result);
        current.pop_back();
    }
    
public:
    vector<string> generateParenthesis (int n) {
        vector<string> result;
        string current;
        generate_all(current, n * 2, result);
        return result;
    }
};
```

**复杂度分析**

- 时间复杂度：$O(2^{2n}n)$，对于 $2^{2n}$ 个序列中的每一个，我们用于建立和验证该序列的复杂度为 $O(n)$。
- 空间复杂度： $O(n)$， 除了答案数组之外，我们所需要的空间取决于递归栈的深度， 每一层递归函数需要 $O(1)$ 的空间， 最多递归 $2n$ 层，因此空间复杂度为 $O(n)$。





## 回溯法

我们可以只在序列仍然保持有效时才添加括号，即对递归树减枝。

```C++
class Solution {
    void backtrack(vector<string>& ans, string& cur, int open, int close,int n) {
		if (cur.size() == n * 2) {
            ans.push_back(cur);
            return;
        }
        
        if(open < n) {
			cur.push_back('(');
            backtrack(ans, cur, open + 1, close, n);
            cur.pop_back();
        }
        
        if(close < open) {
            cur.push_back(')');
            backtrack(ans, cur, open, close + 1, n);
            cur.pop_back();
        }
    }
    
public:
    vector<string> generateParenthesis(int n) {
        vector<string> result;
        string current;
        backtrack(result, current, 0, 0, n);
        return result;
    }
    
};
```







# 找出所有子集的异或总和再求和

```C++
class Solution {
public:
    int res, n;
    
    void dfs(int val, int idx, vector<int>& nums) {
        if (idx == n){
            //递归出口
            res += val;
            return;
        }
        //选择当前数字
        dfs(val ^ nums[idx], idx + 1, nums);
        //不选择当前数字
        dfs(val, idx + 1, nums);
    }
    
    int subsetXORSum(vector<int>& nums) {
        res = 0;
        n = nums.size();
        dfs(0, 0, nums);
        return res;
    }
};
```

**复杂度分析**

- 时间复杂度：$O(2^n)$，其中 $n$ 为 $nums$ 的长度
- 空间复杂度：$O(n)$









# 二进制手表

## 枚举时分

由题意可知，小时由4个比特表示，分钟由6个比特表示，比特位值为 0 表示灯灭，为1表示灯亮。

我们可以枚举小时的所有可能值 [0, 11], 以及分钟的所有可能值 [0, 59] ，并计算二者的二进制中1的个数之和，若为 $turnedOn$，则将其加入到答案中。

```C++
class Solution {
public:
    vector<string> readBinaryWatch(int turnedOn) {
        vector<string> ans;
        for (int h = 0; h < 12; ++h) {
            for (int m = 0; m < 60; ++m) {
                if(__builtin_popcount(h) + __builtin_popcount(m) == turnedOn) {
                    ans.push_back(to_string(h) + ":" + (m < 10 ? "0" : "") + to_string(m));
                }
            }
        }
        return ans;
    }
};
```

**复杂度分析**

时间复杂度： $O(1)$。枚举的次数是一个与输入无关的定值。

空间复杂度： $O(1)$。仅使用了常数大小的空间。注意返回值不计入空间复杂度。





## 二进制枚举

另一种枚举方法是枚举所有 $2^{10}=1024$ 种灯的开闭组合，即用一个二进制数表示灯的开闭，其高 4 位为小时，低 6 位为分钟。若小时和分钟的值均在合法范围内，且二进制中 1 的个数为 $turnedOn$，则将其加入到答案中。

```C++
class Solution {
public:
    vector<string> readBinaryWatch(int turnedOn) {
        vector<string> ans;
        for(int i = 0; i < 1024; ++i) {
            int h = i >> 6, m = i & 63;
            if (h < 12 && m < 60 && __builtin_popcount(i) == turnedOn) {
                ans.push_back(to_string(h) + ":" + (m < 10 ? "0" : "") + to_string(m));
            }
        }
        return ans;
    }
};
```

**复杂度分析**

时间复杂度：$O(1)$。枚举的次数是一个与输入无关的定值。

空间复杂度：$O(1)$。仅使用了常数大小的空间。注意返回值不计入空间复杂度。









# 最接近的三数之和

```C++
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        //可以考虑使用三重for循环
        //但后两重循环可以优化为两数之和的双指针问题

        //先对数组排序
        sort(nums.begin(), nums.end());
        int n = nums.size();
        
        //ans存放最接近target的三数之和,diff存放ans与target差的绝对值
        int ans = nums[0] + nums[1] + nums[n-1];
        int diff = abs(target - ans);

        for(int p1 = 0; p1 <= n - 3; ++p1) {

            //因为数组有序，可以把指针p3指向数组尾端
            //若两数之和小于target1，则p2右移，否则p3左移
            int p3 = n - 1;
            int p2 = p1 + 1;

            //指针重合时退出循环
            while(p2 != p3) {
                int sum = nums[p1] + nums[p2] + nums[p3];
                if(sum == target) return sum; //小优化

                //如果当前的三数之和与目标值更接近
                //就把当前和赋给ans，当前差值赋给diff
                if(abs(target - sum) < diff) {
                    ans = nums[p1] + nums[p2] + nums[p3];
                    diff = abs(target - ans);
                }
                
                if (sum < target)      ++p2;
                else if (sum > target) --p3;
            }
        }
        return ans;
    }
};
```

<img src="LeetCode%E5%88%B7%E9%A2%98.assets/image-20210907100805038.png" alt="image-20210907100805038" style="zoom:150%;" />



**复杂度分析**

时间复杂度：$O(N^2)$。其中 N 是数组  $nums$ 的长度。我们首先需要  $O(NlogN)$ 的时间对数组进行排序，随后在枚举的过程中，使用一重循环  $O(N)$ 枚举 $a$，双指针 $O(N)$ 枚举 $b$ 和 $c$，故一共是 $O(N^2)$。

空间复杂度：$O(logN)$。排序需要使用 $O(logN)$ 的空间。然而我们修改了输入的数组 $nums$，在实际情况下不一定允许，因此也可以看成使用了一个额外的数组存储了 $nums$ 的副本并进行排序，此时空间复杂度为 $O(N)$。







官方题解

```C++
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        sort(nums.begin(), nums.end());
        int n = nums.size();
        int best = 1e7;

        // 根据差值的绝对值来更新答案
        auto update = [&](int cur) {
            if (abs(cur - target) < abs(best - target)) {
                best = cur;
            }
        };

        // 枚举 a
        for (int i = 0; i < n; ++i) {
            // 保证和上一次枚举的元素不相等
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            // 使用双指针枚举 b 和 c
            int j = i + 1, k = n - 1;
            while (j < k) {
                int sum = nums[i] + nums[j] + nums[k];
                // 如果和为 target 直接返回答案
                if (sum == target) {
                    return target;
                }
                update(sum);
                if (sum > target) {
                    // 如果和大于 target，移动 c 对应的指针
                    int k0 = k - 1;
                    // 移动到下一个不相等的元素
                    while (j < k0 && nums[k0] == nums[k]) {
                        --k0;
                    }
                    k = k0;
                } else {
                    // 如果和小于 target，移动 b 对应的指针
                    int j0 = j + 1;
                    // 移动到下一个不相等的元素
                    while (j0 < k && nums[j0] == nums[j]) {
                        ++j0;
                    }
                    j = j0;
                }
            }
        }
        return best;
    }
};
```





# 搜索插入位置

```C++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int n = nums.size();
        int left = 0, right = n - 1, ans = n;
        while (left <= right) {
            int mid = ((right - left) >> 1) + left;
            if (target <= nums[mid]) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
};
```



# 加一

```C++
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int n = digits.size();
        return helper(digits, n - 1);
    }

    vector<int> helper(vector<int>& digits, int x){
        if(x == 0 && digits[x] == 9) {
            digits[0] = 0;
            digits.insert(digits.begin(), 1);
            return digits;
        }

        if(digits[x] != 9) {
            digits[x]++;
            return digits;
        }

        digits[x] = 0;
        return helper(digits, x - 1);
        
    }
};
```







# 四数之和

```C++
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        vector<vector<int>> ans;

        for(int p1 = 0; p1 <= n - 4; ++p1) {
            for(int p2 = p1 + 1; p2 <= n - 3; ++p2) {
                int p3 = p2 + 1; 
                int p4 = n - 1;
                while (p3 != p4) {
                    int sum = nums[p1] + nums[p2] + nums[p3] + nums[p4];
                    if (sum == target){
                        vector<int> vec = {nums[p1], nums[p2], nums[p3], nums[p4]};
                        ans.push_back(vec);
                    }
                    else if (sum > target) {
                        --p4;
                    }
                    else if (sum < target) {
                        ++p3;
                    }
                }
            }
        }
        return ans;
    }
};
```



优化并剪枝后：

```C++
class Solution{
	public: 
	vector<vector<int>> fourSum(vector<int>& nums, int target) {
        sort(nums.begin(),nums.end());
        vector<vector<int> > res;
        if(nums.size()<4)
        return res;
        int a,b,c,d,_size=nums.size();
        for(a=0;a<=_size-4;a++){
        	if(a>0&&nums[a]==nums[a-1]) continue;      //确保nums[a] 改变了
        	for(b=a+1;b<=_size-3;b++){
        		if(b>a+1&&nums[b]==nums[b-1])continue;   //确保nums[b] 改变了
        		c=b+1,d=_size-1;
        		while(c<d){
        			if(nums[a]+nums[b]-target<-(nums[c]+nums[d]))//原写法num[a]+num[b]+num[c]+num[d]<target为了防止溢出，见下面的补充修改
        			    c++;
        			else if(nums[a]+nums[b]-target>-(nums[c]+nums[d]))//同上
        			    d--;
        			else{
        				res.push_back({nums[a],nums[b],nums[c],nums[d]});
        				while(c<d&&nums[c+1]==nums[c])      //确保nums[c] 改变了
        				    c++;
        				while(c<d&&nums[d-1]==nums[d])      //确保nums[d] 改变了
        				    d--;
        				c++;
        				d--;
					}
				}
			}
		}
		return res;
    }
};

```

 





# 合并两个有序数组

## 直接合并后排序

可以直接先把第二个数组放进第一个数组尾部，再对整个数组排序

```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        for(int i = 0; i < n; ++i) {
            nums1[m + i] = nums2[i];
        }
        sort(nums1.begin(), nums1.end());
    }
};
```

![image-20210908194245898](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210908194245898.png)



## 双指针

方法一没有利用数组已经被排序的性质。为了利用这一性质，我们可以使用双指针方法。这一方法将两个数组看作队列，每次从两个数组头部取出比较小的数字放到结果中

```C++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int p1 = 0, p2 = 0;
        int sorted[m + n];
        int cur;
        while (p1 < m || p2 < n) {
            if (p1 == m) {
                cur = nums2[p2++];
            } else if (p2 == n) {
                cur = nums1[p1++];
            } else if (nums1[p1] < nums2[p2]) {
                cur = nums1[p1++];
            } else {
                cur = nums2[p2++];
            }
            sorted[p1 + p2 - 1] = cur;
        }
        for (int i = 0; i != m + n; ++i) {
            nums1[i] = sorted[i];
        }
    }
};
```

![image-20210908194302132](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210908194302132.png)



### 逆向双指针

![image-20210908194323250](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210908194323250.png)

```C++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int p1 = m - 1, p2 = n - 1;
        int tail = m + n - 1;
        int cur;
        while (p1 >= 0 || p2 >= 0) {
            if (p1 == -1) {
                cur = nums2[p2--];
            } else if (p2 == -1) {
                cur = nums1[p1--];
            } else if (nums1[p1] > nums2[p2]) {
                cur = nums1[p1--];
            } else {
                cur = nums2[p2--];
            }
            nums1[tail--] = cur;
        }
    }
};
```







# 全排列

```C++
class Solution {
private:
    void backtrack(vector<int>& nums, vector<vector<int>>& ans, vector<int>& res, unordered_set<int>& visited, int n, int idx) {
        //递归出口
        if (idx == n) {
            ans.push_back(res);
            return;
        }

        for (int i = 0; i < n; ++i) {
            int cur = nums[i];

            //按条件选择节点
            //哈希集合visited用于存放已选过的节点
            if (!visited.count(cur)) {
                visited.insert(cur);
                res[idx] = cur;
            }
            else continue;

            backtrack(nums, ans, res, visited, n, idx + 1);

            //取消选择
            visited.erase(cur);
        }
    }

public:
    vector<vector<int>> permute(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> ans; //最终结果
        vector<int> res(n); //临时存放每一个排列
        unordered_set<int> visited; //判断当前节点是否已被选择
        backtrack(nums, ans, res, visited, n, 0);
        return ans;
    }
};
```





**改进：**

![image-20210911154736002](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210911154736002.png)

```C++
class Solution {
public:
    void backtrack(vector<vector<int>>& res, vector<int>& output, int first, int len){
        //递归出口
        if (first == len) {
            res.emplace_back(output);
            return;
        }
        for (int i = first; i < len; ++i) {
            //动态维护数组
            swap(output[i], output[first]);
            //继续递归填下一个数
            backtrack(res, output, first + 1, len);
            //撤销操作
            swap(output[i], output[first]);
        }
    }
    
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> res;
        backtrack(res, nums, 0, (int)nums.szie());
        return res;
    }
};
```

![image-20210911155415051](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210911155415051.png)







# 最小栈

```C++
class MinStack {
    stack<int> stk;
    stack<int> minStk;
public:
    MinStack() {
		minStk.push(INT_MAX);
    }
    
    void push(int x) {
        stk.push(x);
        minStk.push(min(minStk.top(), x));
    }
    
    void pop() {
        stk.pop();
        minStk.pop();
    }
    
    int top() {
        return stk.top();
    }
    
    int getMin() {
        return minStk.top();
    }
};
```







# 用队列实现栈

```C++
class MyStack {
public:
	queue<int> queue1;
    queue<int> queue2;
    
    MyStack() {
        
    }
    
    void push(int x) {
        queue2.push(x);
        while(!queue1.empty()) {
			queue2.push(queue1.front());
            queue1.pop();
        }
        swap(queue1, queue2);
    }
    
    int pop() {
        int r = queue1.front();
        queue1.pop();
        return r;
    }
    
    int top() {
        return queue1.front();
    }
    
    bool empty() {
        return queue1.empty();
    }
};
```





# 下一个排列

![image-20210914100617159](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210914100617159.png)

```C++
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int i = nums.size() - 2;
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i--;
        }
        if (i >= 0) {
            int j = nums.size() - 1;
            while (j >= 0 && nums[i] >= nums[j]) {
                j--;
            }
            swap(nums[i], nums[j]);
        }
        reverse(nums.begin() + i + 1, nums.end());
    }
}
```

![image-20210914100654590](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210914100654590.png)





# 反转链表

## 迭代

```C++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* res = nullptr;
        while(head != nullptr) {
            res = new ListNode(head->val, res);
            head = head->next;
        }
        return res;
    }
};
```

**时间和空间复杂度均为 $O(n)$**



**改进后：**

```C++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        //原来的头节点应为尾节点，其下一个节点应为pullptr
		ListNode* pre = nullptr;
        while(head) {
            
            //把当前节点的下一个节点指向上一个节点，会导致原来的下一个节点丢失
            //因此先用next存储下一个节点
            ListNode* next = head->next;
            
            //当前节点的next指针指向原来的上一个节点
            head->next = pre;
            
            //head指针往移到原来的下一个节点，即next存储的节点
            //因为每次都要把当前节点的next指针指向原来的上一个节点
            //所有需要pre来存储原来的上一个节点
            pre = head;
            head = next;
        }
        return pre;
    }
};
```

**时间复杂度为：$O(n)$**

**空间复杂度为：$O(1)$ **







## 递归

![image-20210914104102885](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210914104102885.png)

**==考虑递归的出发点：假设链表其余部分已经被反转，现在该如何反转它前面的部分？==**

```C++
class Solution {
public:
    ListNode* reverseList (ListNode* head) {
        //递归出口
        if (!head || !head->next) return head;
        
        //要从后往前反转，所以先递归调用，走到最后面，再进行反转操作，再层层返回
        ListNode* newHead = reverseList(head->next);
        head->next->next = head;
        head->next = nullptr;
        return newHead;
    }
};
```

新开了一个链表，时间和空间复杂度均为：$O(n)$

![image-20210914105508636](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210914105508636.png)







# 杨辉三角

```C++
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ans = {{1}, {1, 1}};
        if (numRows == 0) return {};
        else if (numRows == 1) return {{1}};
        else if (numRows == 2) return ans;

        for (int i = 3; i <= numRows; ++i) {
            vector<int> vec(i, 1);
            for(int j = 0; j < ans[i - 2].size() - 1; ++j){
                vec[j + 1] = ans[i - 2][j] + ans[i - 2][j + 1];
            }
            ans.push_back(vec); 
        }
        return ans;
    }
};
```



**改进版本：**

```C++
class Solution {
public:
    vector<vector<int>> generate(int numRows) {
        vector<vector<int>> ret(numRows);
        for (int i = 0; i < numRows; ++i) {
            ret[i].resize(i + 1);
            ret[i][0] = ret[i][i] = 1;
            for (int j = 1; j < i; ++j) {
                ret[i][j] = ret[i - 1][j] + ret[i - 1][j - 1];
            }
        }
        return ret;
    }
};
```



# 杨辉三角II

```C++
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<vector<int>> ans(rowIndex +  1);
        for (i = 0; i <= rowIndex; ++i) {
            ans[i].resize(i + 1);
            ans[i][0] = ans[i][i] = 1;
            for (j = 1; j < i; ++j) {
                ans[i][j] = ans[i - 1][j - 1] + ans[i - 1][j];
            }
        }
        return ans[rowIndex];
};
```

时间和空间复杂度均为$O(n^2)$ 

每一行的数只与上一行有关，可用滚动数组的思想把空间复杂度优化到$O(n)$ （不考虑返回值的空间占用）：

```C++
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> cur(rowIndex + 1, 1);
        vector<int> pre = cur;
        for (int i = 2; i < rowIndex + 1; ++i) {
            for (int j = 1; j < i; ++j) {
                cur[j] = pre[j - 1] + pre[j]; 
            }
            pre = cur;
        }
        return cur;
    }
};
```

上面用了额外的数组`pre`来存放上一步结果，能否只用一个数组？

当前行第 $i$ 项的计算只与上一行第 $i−1$ 项及第 $i$ 项有关。因此我们可以倒着计算当前行，这样计算到第 $i$ 项时，第 $i−1$ 项仍然是上一行的值。

```C++
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        vector<int> ans(rowIndex + 1, 1);
        for (int i = 2; i <= rowIndex; ++i) {
            for (int j = i - 1; j > 0; --j) {
                ans[j] += ans[j - 1]; 
            }
        }
        return ans;
    }
};
```









# 回文链表

## 先把链表值复制到数组中再用双指针

```C++
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        vector<int> vals;
        while(head != nullptr) {
            vals.emplace_back(head->val);
            head = head->next;
        }
        for (int i = 0, j = (int)vals.size() - 1; i < j; ++i, --j){
            if (vals[i] != vals[j]) return false;
        }
        return true;
    }
};
```



## 递归

==**递归提供了一种优雅的方式来反向遍历链表节点：**==

```C++
void printValuesInReverse(ListNode* head) {
    if (head) {
        printValuesInReverse(head->next);
        print(head->val);
    }
}
```

![image-20210916092359648](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210916092359648.png)

```C++
class Solution {
    ListNode* frontPointer;
public:
    bool recursivelyCheck(ListNode* currentNode) {
		if (currentNode != nullptr) {
            if (!recursivelyCheck(currentNode->next)) return false;
            if (currentNode->val != frontPointer->val) return false;
            frontPointer = frontPointer->next;
        }
        return true;
    }
    
    bool isPalindrome (ListNode* head) {
        frontPointer = head;
        return recursivelyCheck(head);
    }
}
```

![image-20210916092929424](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210916092929424.png)





## 快慢指针

![image-20210915092001860](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210915092001860.png)

```C++
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        if (head == nullptr) return true;

        //找到前半部分链表的尾节点并反转后半部分链表
        ListNode* firstHalfEnd = endOfFirstHalf(head);
        ListNode * secondHalfStart = reverseList(firstHalfEnd->next);

        //判断是否回文
        ListNode* p1 = head;
        ListNode* p2 = secondHalfStart;
        bool result = true;
        while (result && p2 != nullptr) {
            if (p1->val != p2->val) result = false;
            p1 = p1->next;
            p2 = p2->next;
        }

        //还原链表并返回结果
        firstHalfEnd->next = reverseList(secondHalfStart);
        return result;
    }

    ListNode* reverseList(ListNode* head) {
        ListNode* pre = nullptr;
        ListNode* cur = head;
        while (cur != nullptr) {
            ListNode* nextTemp = cur->next;
            cur->next = pre;
            pre = cur;
            cur = nextTemp;
        }
        return pre;
    }

    ListNode* endOfFirstHalf(ListNode* head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast->next != nullptr && fast->next->next != nullptr) {
            fast = fast->next->next;
            slow = slow->next;
        }
        return slow;
    }
};
```

![image-20210915113240766](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210915113240766.png)











# 组合总和

```C++
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> res;
        vector<int> seleted;
        int curSum = 0, lastChoice = 0;
        sort(candidates.begin(), candidates.end());
        backtrack(res, candidates, seleted, curSum, target, lastChoice);
        return res;
    }

private:
    void backtrack(vector<vector<int>>& res, vector<int>& candidates, vector<int>& seleted, int curSum, int target, int lastChoice) {
        //[递归出口]
        if (curSum > target) return;
        if (curSum == target) {
            res.emplace_back(seleted);
            return;
        }

        for (const int& num : candidates) {
            //每次选择的数值不能小于上一轮选的
            if (num < lastChoice) continue;

            curSum += num;
            seleted.emplace_back(num);
            lastChoice = num;

            backtrack(res, candidates, seleted, curSum, target, lastChoice);

            //撤消选择
            seleted.pop_back();
            curSum -= num;
        }
    }
};
```

![image-20210916160248318](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210916160248318.png)





# 比特位计数

## `__builtin_popcount()`

```C++
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> ans(n + 1);
        for (int i = 0; i <= n; ++i) {
            ans.emplace_back(__builtin_popcount(i));
        }
        return ans;
    }
};
```





## Brian Kernighan 算法

**计算一个正整数的二进制数中1的个数：**

```C++
int countOnes(int x) {
    int ones = 0;
    while (x) {
        x &= x - 1;
        ones++;
    }
    return ones;
}
```

题解：

```C++
class Solution {
public:
    int countOnes(int x) {
        int ones = 0;
        while (x > 0) {
            x &= (x - 1);
            ones++;
        }
        return ones;
    }

    vector<int> countBits(int n) {
        vector<int> bits(n + 1);
        for (int i = 0; i <= n; i++) {
            bits[i] = countOnes(i);
        }
        return bits;
    }
};
```

![image-20210916173135754](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210916173135754.png)





## 动态规划

![image-20210917092226878](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210917092226878.png)

```C++
class Solution {
public:
    vector<int> bits(n + 1);
    int highBit = 0;
    for (int i = 1; i <= n; ++i) {
        if (i & (i - 1) == 0) highBit = i;
        bits[i] = bits[i - highBit] + 1;
    }
    return bits;
};
```





![image-20210917092906008](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210917092906008.png)



```C++
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> bits(n + 1);
        for (int i = 1; i <= n; i ++) {
            bits[i] = bits[i >> 1] + (i & 1);
        }
        return bits;
    }
};
```





![image-20210917093248057](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210917093248057.png)

```C++
class Solution {
public:
    vector<int> countBits(int n) {
        vector<int> bits(n + 1);
        for (int i = 1; i <= n; ++i) {
            bits[i] = bits[i & (i - 1)] + 1;
        }
        return bits;
    }
};
```

![image-20210917092647550](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210917092647550.png)









# 找到所有数组中消失的数字

![image-20210917101937474](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210917101937474.png)



```C++
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        int n = nums.size();
        for (auto& num : nums) {
            int x = (num - 1) % n;
            nums[x] += n;
        }
        vector<int> ret;
        for (int i = 0; i < n; i++) {
            if (nums[i] <= n) {
                ret.push_back(i + 1);
            }
        }
        return ret;
    }
};
```

![image-20210917102249587](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210917102249587.png)









# 最小路径和

```C++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        //m是行数，n是列数
        int m = grid.size(), n = grid[0].size();
        if (m == 0 || n == 0) return 0;

        //边界条件
        //第一行
        for (int a = 1; a < n; ++a) {
            grid[0][a] += grid[0][a - 1];
        }
        //第一列
        for (int b = 1; b < m; ++b) {
            grid[b][0] += grid[b - 1][0];
        }
        
        for (int i = 1; i < m; ++i) {
            for (int j = 1; j < n; ++j) {
                grid[i][j] += min(grid[i - 1][j], grid[i][j - 1]);
            }
        }
        return grid[m - 1][n - 1];
    }
};
```

时间复杂度：$O(mn)$ 

空间复杂度：$O(1)$ 。没有开辟新空间，如果开辟一个数组 $dp$，则需要 $O(mn)$ 大小的空间，用滚动数组的思想，每次只存储上一行的 $dp$ 值，则可优化到 $O(n)$







# 二叉树的直径

![image-20210917153209688](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210917153209688.png)

```C++
class Solution {
    int ans = 0;
    int depth(TreeNode* rt){
        if (rt == NULL) {
            return 0; // 访问到空节点了，返回0
        }
        int L = depth(rt->left); // 左儿子为根的子树的深度
        int R = depth(rt->right); // 右儿子为根的子树的深度
        ans = max(ans, L + R); // 计算d_node即L+R+1 并更新ans
        return max(L, R) + 1; // 返回该节点为根的子树的深度
    }
public:
    int diameterOfBinaryTree(TreeNode* root) {
        depth(root);
        return ans;
    }
};
```







# 子集

```C++
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) { 
        vector<int> temp;
        vector<vector<int>> res;
        res.emplace_back(temp);

        //先排好序方便后续处理
        sort(nums.begin(), nums.end());
        int lastNum = INT_MIN;
        backtrack(nums, temp, res, lastNum);
        return res;
    }

private:
    void backtrack(vector<int>& nums, vector<int>& temp, vector<vector<int>>& res, int lastNum){
        //递归出口
        if (lastNum == nums[nums.size() - 1]) return;

        for(const int& num : nums) {
            //每次选的数要大于上一次选的
            if (num <= lastNum) continue;
            //选择
            lastNum = num;
            temp.emplace_back(num);
            res.emplace_back(temp);
            
            backtrack(nums, temp, res, lastNum);
            
            //撤销选择
            temp.pop_back();
        }
    }
};
```



**考虑成每个位置有选择和不选择两种状态：**

```C++
class Solution {
public:
    vector<int> t;
    vector<vector<int>> ans;

    void dfs(int cur, vector<int>& nums) {
        if (cur == nums.size()) {
            ans.push_back(t);
            return;
        }
        t.push_back(nums[cur]);
        dfs(cur + 1, nums);
        t.pop_back();
        dfs(cur + 1, nums);
    }

    vector<vector<int>> subsets(vector<int>& nums) {
        dfs(0, nums);
        return ans;
    }
};
```

![image-20210918163121218](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210918163121218.png)





# 单词搜索*

![image-20210922153645688](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210922153645688.png)

```C++
class Solution {
public:
    bool check(vector<vector<char>>& board, vector<vector<int>>& visited, int i, int j, string& s, int k) {
        if (board[i][j] != s[k]) return false;
        else if (k == s.length() - 1) return true;

        visited[i][j] = true;
        vector<pair<int, int>> directions{ {0, 1}, {0, -1}, {1, 0}, {-1, 0} };
        bool res = false;
        for (const auto& dir : directions) {
            int newi = i + dir.first, newj = j + dir.second;
            if (newi >= 0 && newi < board.size() && newj >= 0 && newj < board[0].size()) {
                if (!visited[newi][newj]) {
                    bool flag = check(board, visited, newi, newj, s, k + 1);
                    if (flag) {
                        res = true;
                        break;
                    }
                }
            }
        }
        visited[i][j] = false;
        return res;
    }

    bool exist(vector<vector<char>>& board, string word) {
        int h = board.size(), w = board[0].size();
        vector<vector<int>> visited(h, vector<int>(w));
        for (int i = 0; i < h; i++) {
            for (int j = 0; j < w; j++) {
                bool flag = check(board, visited, i, j, word, 0);
                if (flag) return true;
            }
        }
        return false;
    }
};
```

![image-20210922153706351](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210922153706351.png)







# 根据身高重建队列*

```C++
class Solution {
public:
    vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
        sort(people.begin(), people.end(), [](const vector<int>& u, const vector<int>& v) {
            return u[0] < v[0] || (u[0] == v[0] && u[1] > v[1]);
        });
        int n = people.size();
        vector<vector<int>> ans(n);
        for (const vector<int>& person : people) {
            int spaces = person[1] + 1;
            for (int i = 0; i < n; ++i) {
                if (ans[i].empty()) {
                    --spaces;
                    if (!spaces) {
                        ans[i] = person;
                        break;
                    }
                }
            }
        }
        return ans;
    }
};
```







# 旋转图像

```C++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();
        for (int i = 0; i < n / 2; ++i) {
            for (int j = 0; j < (n + 1) / 2; ++j) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - j - 1][i];
                matrix[n - j - 1][i] = matrix[n - i - 1][n - j - 1];
                matrix[n - i - 1][n - j - 1] = matrix[j][n - i - 1];
                matrix[j][n - i - 1] = temp;
            }
        }
    }
};
```







# 设计哈希集合

```C++
class MyHashSet {
private:
    vector<list<int>> data;
    static const int base = 769;
    static int hash(int key) {
        return key % base;
    }

public:
    MyHashSet() : data(base) {}

    void add(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) return;
        }
        data[h].push_back(key);
    }

    void remove(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) {
                data[h].erase(it);
                return;
            }
        }
    }

    bool contains(int key) {
        int h = hash(key);
        for (auto it = data[h].begin(); it != data[h].end(); it++) {
            if ((*it) == key) return true;
        }
        return false;
    }
};
```











# 全排列II

```C++
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<int> vec;
        vector<vector<int>> ans;
        sort(nums.begin(), nums.end());
        backtrack(nums, vec, ans);
        return ans;
    }

private:
    void backtrack(vector<int>& nums, vector<int>& vec, vector<vector<int>>& ans) {
        if (nums.size() == 0) {
            ans.push_back(vec);
            return;
        }
        for (auto it = nums.begin(); it != nums.end(); it++) {
            int temp = *it;
            if (it != nums.begin()) {
                if(temp == *(it - 1)) continue;
            }

            vec.push_back(temp);
            it = nums.erase(it);
            backtrack(nums, vec, ans);
            nums.insert(it, temp);
            vec.pop_back();
        }
    }
};
```







# 除自身以外数组的乘积

## 左右乘积列表

![image-20210928095930484](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210928095930484.png)

```C++
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int length = nums.size();
        
        // L 和 R 分别表示左右两侧的乘积列表
        vector<int> L(length, 0), R(length, 0);
        
        vector<int> ans(length);
        
        // L[i] 为索引 i 左侧所有元素的乘积
        // 对于索引为 '0' 的元素，因为左侧没有元素， 所以 L[0] = 1
        L[0] = 1;
        for (int i = 1; i < length; i++) {
			L[i] = nums[i - 1] * L[i - 1];
        }
        
        // R[i] 为索引 i 右侧所有元素的乘积
        // 对于索引为 'length - 1' 的元素，因为右侧没有元素，所以 R[length - 1] = 1
        R[length - 1] = 1;
        for (int i = length - 2; i >= 0; i--) {
            R[i] = nums[i + 1] * R[i + 1];
        }
        
        //对于索引 i，除 nums[i] 之外其余各元素的乘积就是所有左侧元素的乘积乘所有右侧元素的乘积
        for (int i = 0; i < length; i++) {
            ans[i] = L[i] * R[i];
        }
        
        return ans;
    }
};
```





## 优化到空间复杂度为$O(1)$

![image-20210928100835126](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210928100835126.png)

```C++
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int length = nums.size();
        vector<int> ans(length);
        
        ans[0] = 1;
        for (int i = 1; i < length; ++i) {
            ans[i] = nums[i - 1] * ans[i - 1];
        }
        
        int R = 1;
        for (int i = length - 1; i >= 0; i--) {
            ans[i] = ans[i] * R;
            R *= nums[i];
        }
        return ans;
    }
};
```







# 两个数组的交集

## 两个集合

![image-20210928171501854](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210928171501854.png)

```C++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> set1, set2;
        for (auto& num : nums1) {
            set1.insert(num);
        }
        for (auto& num : nums2) {
            set2.insert(num);
        }
        return getIntersection(set1, set2);
    }

private:
    vector<int> getIntersection(unordered_set<int>& set1, unordered_set<int>& set2) {
        if (set1.size() > set2.size()) return getIntersection(set2, set1);

        vector<int> intersection;
        for (auto& num : set1) {
            if (set2.count(num)) intersection.push_back(num);
        }
        return intersection;
    }
};
```

![image-20210928201918228](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210928201918228.png)

## 排序 + 双指针

![image-20210928200407101](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210928200407101.png)

```C++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        sort(nums1.begin(), nums1.end());
        sort(nums2.begin(), nums2.end());
        int n1 = nums1.size(), n2 = nums2.size();

        vector<int> intersection;
        int i = 0, j = 0;
        while (i < n1 && j < n2) {
            if (nums1[i] == nums2[j]) {
                if (!intersection.size() || nums1[i] != intersection.back()){
                    intersection.push_back(nums1[i]);
                }
                i++;
                j++;
            }
            else if (nums1[i] < nums2[j]) i++;
            else j++;
        }
        return intersection;
    }
};
```

![image-20210928201842550](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210928201842550.png)



# 有序链表转换为二叉树

## 分治

![image-20210929105045692](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929105045692.png)

```C++
class Solution {
public:
    ListNode* getMedian(ListNode* left, ListNode* right) {
        ListNode* fast = left;
        ListNode* slow = left;
        while (fast != right && fast->next != right) {
            fast = fast->next->next;
            slow = slow->next;
        }
        return slow;
    }

    TreeNode* buildTree(ListNode* left, ListNode* right) {
        if (left == right) return nullptr;

        ListNode* mid = getMedian(left, right);
        TreeNode* root = new TreeNode(mid->val);
        root->left = buildTree(left, mid);
        root->right = buildTree(mid->next, right);
        return root;
    }

    TreeNode* sortedListToBST(ListNode* head) {
        return buildTree(head, nullptr);
    }
};
```

![image-20210929105120818](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929105120818.png)



## 分治 + 中序遍历优化

![image-20210929105739332](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929105739332.png)

```C++
class Solution {
public:
    int getLength(ListNode* head) {
        int ret = 0;
        for (; head != nullptr; ++ret, head = head->next);
        return ret;
    }

    TreeNode* buildTree(ListNode*& head, int left, int right) {
        if (left > right) return nullptr;
        
        int mid = (left + right + 1) / 2;
        TreeNode* root = new TreeNode();
        root->left = buildTree(head, left, mid - 1);
        root->val = head->val;
        head = head->next;
        root->right = buildTree(head, mid + 1, right);
        return root;
    }

    TreeNode* sortedListToBST(ListNode* head) {
        int length = getLength(head);
        return buildTree(head, 0, length - 1);
    }
};
```

![image-20210929110434110](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929110434110.png)





# 相同的树

## 深度优先遍历

```C++
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (p == nullptr && q == nullptr) return true;
        if (p == nullptr ^ q == nullptr) return false;

        if (p->val == q->val) return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
        else return false;
    }
};
```

![image-20210929113546905](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929113546905.png)



```C++
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (p == nullptr && q == nullptr) {
            return true;
        } else if (p == nullptr || q == nullptr) {
            return false;
        } else if (p->val != q->val) {
            return false;
        } else {
            return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
        }
    }
};
```

![image-20210929143955086](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929143955086.png)

## 广度优先遍历

![image-20210929144005268](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929144005268.png)



```C++
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (p == nullptr && q == nullptr) return true;
        else if (p == nullptr || q == nullptr) return false;

        queue<TreeNode*> queue1, queue2;
        queue1.push(p); queue2.push(q);
        while (!queue1.empty() && !queue2.empty()) {
            auto node1 = queue1.front(); queue1.pop();
            auto node2 = queue2.front(); queue2.pop();

            if (node1->val != node2->val) return false;
            auto left1 = node1->left, right1 = node1->right, left2 = node2->left, right2 = node2->right;
            if (left1 == nullptr ^ left2 == nullptr) return false;
            if (right1 == nullptr ^ right2 == nullptr) return false;
            if (left1 != nullptr) queue1.push(left1);
            if (right1 != nullptr) queue1.push(right1);
            if (left2 != nullptr) queue2.push(left2);
            if (right2 != nullptr) queue2.push(right2);
        }
        return queue1.empty() && queue2.empty();
    }
};
```

![image-20210929144017529](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929144017529.png)







# 平衡二叉树

## 自顶向下递归

![image-20210929145404538](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929145404538.png)



```C++
class Solution {
public:
    int height(TreeNode* root) {
        if (root == nullptr) return 0;
        else return max(height(root->left), height(root->right)) + 1;
    }

    bool isBalanced(TreeNode* root) {
        if (root == nullptr) return true;
        else return abs(height(root->left) - height(root->right)) <= 1 && isBalanced(root->left) && isBalanced(root->right);
    }
};
```





![image-20210929145430444](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929145430444.png)







## 自底向上递归

![image-20210929151134308](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929151134308.png)

```C++
class Solution {
public:
    int height(TreeNode* root) {
        if (root == nullptr) return 0;

        int leftHeight = height(root->left);
        int rightHeight = height(root->right);

        if (leftHeight == -1 || rightHeight == -1 || abs(leftHeight - rightHeight) > 1) return -1;
        else return max(leftHeight, rightHeight) + 1;
    }

    bool isBalanced(TreeNode* root) {
        return height(root) >= 0;
    }
};
```

![image-20210929151215514](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929151215514.png)





# 二叉树的最小深度

## 深度优先遍历

根节点的最小深度等于左右子树的最小深度的最小值加一

对于左右子树可以作为新的根节点递归处理

```C++
int minDepth(TreeNode* root) {
        if (root == nullptr) return 0;
        if (root->left == nullptr && root->right == nullptr) return 1;

        int min_depth = INT_MAX;
        if (root->left != nullptr) min_depth = min(minDepth(root->left), min_depth);
        if (root->right != nullptr) min_depth = min(minDepth(root->right), min_depth);

        return min_depth + 1;
    }
```

![image-20210929181250930](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210929181250930.png)





## 广度优先遍历

使用广度优先遍历，找到一个叶子节点即可返回其深度。广度优先搜索的性质保证了最先搜索到的叶子节点深度一定最小。

```C++
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (root == nullptr) return 0;
        
        queue<pair<TreeNode*, int>> que;
        que.emplace(root, 1);
        while (!que.empty()) {
            TreeNode* node = que.front().first;
            int depth = que.front().second;
            que.pop();

            if (node->left == nullptr && node->right == nullptr) return depth;
            if (node->left != nullptr) que.emplace(node->left, depth + 1);
            if (node->right != nullptr) que.emplace(node->right, depth + 1);
        }
        return 0;
    }
};
```

![image-20210930105026494](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210930105026494.png)





# 二叉搜索树的最近公共祖先

## 两次遍历

```C++
class Solution {
public:
    //得到节点target的路径，将其存放在一个数组中
    //利用二叉搜索树的性质去遍历
    vector<TreeNode*> getPath (TreeNode* root, TreeNode* target) {
        vector<TreeNode*> path;
        TreeNode* node = root; //防止root被修改
        while (node->val != target->val) {
            path.push_back(node);
            if (node->val < target->val) node = node->right;
            else node = node->left;
        }
        path.push_back(node);
        return path;
    }

    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        //分别得到两个节点的路径
        vector<TreeNode*> pathOfP = getPath(root, p);
        vector<TreeNode*> pathOfQ = getPath(root, q);

        //遍历两个路径，遇到不同的元素即为分岔路口，从而得到最近公共祖先
        int minPath = min(pathOfP.size(), pathOfQ.size());
        for (int i = 0; i < minPath; i++) {
            if (pathOfP[i]->val != pathOfQ[i]->val) return pathOfP[i - 1];
        }
        return pathOfP[minPath - 1];
    }
};
```

![image-20210930161756507](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210930161756507.png)



## 一次遍历

![image-20210930161825028](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210930161825028.png)

```C++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        TreeNode* ancestor = root;
        while (true) {
			if (p->val < ancestor->val && q->val < ancestor->val) anscestor = ancestor->left;
            else if (p->val > ancestor->val && q->val > ancestor->val) ancestor = ancestor->right;
            else break;
        }
        return ancestor;
    }
};
```

![image-20210930162206174](LeetCode%E5%88%B7%E9%A2%98.assets/image-20210930162206174.png)













# 二叉树的所有路径

```C++
class Solution {
public:
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> ans;
        string str = "";
        backtrack(root, ans, str);
        return ans;
    }

    void backtrack(TreeNode* root, vector<string>& ans, string str) {
        str += to_string(root->val);
        if (root->left == nullptr && root->right == nullptr) {
            ans.push_back(str);
            return;
        }
        str += "->";
        if (root->left != nullptr) backtrack(root->left, ans, str);
        if (root->right != nullptr) backtrack(root->right, ans, str);
    }
};
```



```C++
class Solution {
public:
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<string> paths;
        if (root == nullptr) {
            return paths;
        }
        queue<TreeNode*> node_queue;
        queue<string> path_queue;

        node_queue.push(root);
        path_queue.push(to_string(root->val));

        while (!node_queue.empty()) {
            TreeNode* node = node_queue.front(); 
            string path = path_queue.front();
            node_queue.pop();
            path_queue.pop();

            if (node->left == nullptr && node->right == nullptr) {
                paths.push_back(path);
            } else {
                if (node->left != nullptr) {
                    node_queue.push(node->left);
                    path_queue.push(path + "->" + to_string(node->left->val));
                }

                if (node->right != nullptr) {
                    node_queue.push(node->right);
                    path_queue.push(path + "->" + to_string(node->right->val));
                }
            }
        }
        return paths;
    }
};
```







# 左子叶之和

## 深度优先

```C++
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        return dfs(root);
    }

    int dfs(TreeNode* root) {
        int sum = 0;
        if (root->left != nullptr) {
            if (root->left->left == nullptr && root->left->right == nullptr) sum += root->left->val;
            else sum += dfs(root->left);
        }
        if (root->right) sum += dfs(root->right);
        return sum;
    }
};
```

![image-20211005174545214](LeetCode%E5%88%B7%E9%A2%98.assets/image-20211005174545214.png)







## 广度优先

```C++
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        int sum = 0;
        queue<TreeNode*> Q;
        Q.push(root);

        while (!Q.empty()) {
            TreeNode* cur = Q.front(); Q.pop();

            if (cur->left) {
                if (cur->left->left == nullptr && cur->left->right == nullptr) sum += cur->left->val;
                else Q.push(cur->left);
            }
            if (cur->right) Q.push(cur->right);
        }
        return sum;
    }
};
```

![image-20211005174554121](LeetCode%E5%88%B7%E9%A2%98.assets/image-20211005174554121.png)







# 二叉搜索树的最小绝对差

二叉搜索树中序遍历得到的值序列是递增有序的,  升序数组任意两个元素之差的绝对值的最小值一定为相邻两个元素之差的最小值

```C++
class Solution {
public:
    int getMinimumDifference(TreeNode* root) {
        int ans = INT_MAX, pre = -1;
        dfs(root, pre, ans);
        return ans;
    }

    void dfs(TreeNode* root, int& pre, int& ans) {
        if (root == nullptr) return;
        dfs(root->left, pre, ans);
        if (pre == -1) pre = root->val;
        else {
            ans = min(ans, root->val - pre);
            pre = root->val;
        }
        dfs(root->right, pre, ans);
    }
};
```



