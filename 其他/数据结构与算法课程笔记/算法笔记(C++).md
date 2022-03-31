# 递归计算一个数组元素之和

```C++
#include <iostream>
using namespace std;
#include <string>


int GetSum(int arr[], int len)
{
    //基线条件：数组为空,则返回0
    if (len == 0)
        return 0;
    
    //递归条件:如果数组非空，则返回第一个元素与其它元素之和，这里其它元素之和再次调用递归函数本身
    return arr[len - 1] + GetSum(arr, len - 1);
}

int main()
{

    int arr[] = {1,3,5,7,9,2,4,6,8,10};
    int len =  sizeof(arr) / sizeof(arr[0]);

    int result = GetSum(arr, len);

    cout << result << endl;
    return 0;
}
```

