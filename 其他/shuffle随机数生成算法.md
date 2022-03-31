# std::random_device

random_device 类定义的函数对象可以生成用来作为种子的随机的无符号整数值。

```c++
std::random_device rd; 
```

构造函数有一个 string& 类型的参数，它有定义的默认值。在我们像这样省略它时，会得到我们环境中默认的 random_device 对象。

用 random_device 对象生成一个种子值:

```c++
auto seed_value = rd();
```



# std::seed_seq 种子序列

seed_seq 类是用来帮助设置随机数生成器的初始状态的，用来产生种子。



# std::mt19937

std::mt19937是C++11加入的新特性，是一种高效的随机数生成算法。它的用法和rand()相似，但是具有速度快，周期长的特点。它的名字就来自于周期长度219937-1。而rand()在windows下生成的数据范围为0-32767。而这个函数的随机范围大概在(-maxint,+maxint)之间。



# std::shuffle

可以将列表中的内容进行随机排列。

**简单的洗牌算法**：

```
#include <random>
#include <algorithm>
#include <iterator>
#include <iostream>

int main()
{
    std::vector<int> v = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

    std::random_device rd;
    std::mt19937 g(rd());

    std::shuffle(v.begin(), v.end(), g);

    std::copy(v.begin(), v.end(), std::ostream_iterator<int>(std::cout, " "));
    std::cout << "\n";

    system("pause");
    return 0;
}
```



```C++
// mersenne_twister_engine::seed example
#include <iostream>
#include <chrono>
#include <random>

int main ()
{
  typedef std::chrono::high_resolution_clock myclock;
  myclock::time_point beginning = myclock::now();

  // obtain a seed from a user string:
  std::string str;
  std::cout << "Please, enter a seed: ";
  std::getline(std::cin,str);
  std::seed_seq seed1 (str.begin(),str.end());

  // obtain a seed from the timer
  myclock::duration d = myclock::now() - beginning;
  unsigned seed2 = d.count();

  std::mt19937 generator (seed1);   // mt19937 is a standard mersenne_twister_engine
  std::cout << "Your seed produced: " << generator() << std::endl;

  generator.seed (seed2);
  std::cout << "A time seed produced: " << generator() << std::endl;

  return 0;
}
```



