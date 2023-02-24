---
title: C++的STL容器
date: 2023-02-24 09:17:03
tags: [cpp, stl, 编程]
categories: [清浅集]
ccover: https://picx.zhimg.com/v2-ca4e5a8efa7a08812468d7fdc778174d_1440w.jpg?source=172ae18b
---

# C++的STL容器

> 本文参考自《C语言中文网》中的[STL](http://c.biancheng.net/stl/)部分，侵删。

C++的标准模板库(STL)的容器主要可以分为序列式容器, 关联式容器, 无序关联式容器. 

1. 序列式容器:  包括 array、vector、deque、list 和 forward_list 容器。

   所谓STL序列式容器，其共同的特点是不会对存储的元素进行排序，元素排列的顺序取决于存储它们的顺序。

2. 关联式容器: 包括map, multimap, set以及multiset这四种容器. 和序列式容器不同的是, 关联式容器在存储容器时, 还会为每个元素配备一个间, 整体以键值对的方式存储在容器中. 相比于前者, 关联式容器可以通过键值直接找到对应的元素, 而无需遍历整个容器. 另外, 关联式容器在存储元素, 默认会根据各元素键值的大小做升序排序.

3. 无序关联式容器: 包括unordered_map, unordered_multimap, unordered_set, unordered_multiset. 又称哈希容器. 和关联式容器一样, 此类容器存储的也是键值对元素, 关联式容器默认情况下会对存储的元素做升序排序, 而无序关联式容器不会. 和其他类容器相比, 无序关联容器擅长通过指定键查找对应的值, 而遍历容器中存储元素的效率不如关联式容器. 

4. 容器适配器: 包括 stack、queue、priority_queue. 容器适配器是一个封装了序列容器的类模板，它在一般序列容器的基础上提供了一些不同的功能。之所以称作适配器类，是因为它可以通过适配容器现有的接口来提供不同的功能。

## 序列式容器

由于本文主要是自用, 因此在成员函数部分仅记录了一些自己不熟悉的函数, 如果需要更多的讲解, 可以详见[C语言中文网](http://c.biancheng.net/stl/)

### STL array

array 容器是 C++ 11 标准中新增的序列容器，简单地理解，它就是在 C++ 普通数组的基础上，添加了一些成员函数和全局函数。在使用上，它比普通数组更安全，且效率并没有因此变差。和其它容器不同，`array 容器的大小是固定的，无法动态的扩展或收缩`，这也就意味着，在使用该容器的过程无法借由增加或移除元素而改变其大小，`它只允许访问或者替换存储的元素`。

array 容器以类模板的形式定义在 <array> 头文件，并位于命名空间 std 中,  array 容器有多种初始化方式，如下代码展示了如何创建具有 10 个 double 类型元素的 array 容器, `array 容器不会做默认初始化操作`。

```c++
// 使用这种方式创建的容器中，各个元素的值是不确定的
std::array<double, 10> values;
// 使用该语句，容器中所有的元素都会被初始化为 0.0。
std::array<double, 10> values {};
// 只初始化了前 4 个元素，剩余的元素都会被初始化为 0.0
std::array<double, 10> values {0.5,1.0,1.5,,2.0};
```

array的操作:

| **成员函数**        | **功能**                                                     |
| ------------------- | ------------------------------------------------------------ |
| front()             | 返回容器中第一个元素的直接引用，该函数不适用于空的 array 容器。 |
| back()              | 返回容器中最后一个元素的直接应用，该函数同样不适用于空的 array 容器。 |
| data()              | 返回一个指向容器首个元素的指针。利用该指针，可实现复制容器中所有元素等类似功能。 |
| fill(val)           | 将 val 这个值赋值给容器中的每个元素。                        |
| array1.swap(array2) | 交换 array1 和 array2 容器中的所有元素，但前提是它们具有相同的长度和类型。 |

另外，在 \<array\> 头文件中还重载了 get() 全局函数，该重载函数的功能是访问容器中指定的元素，并返回该元素的引用。

```c++
#include <iostream>
//需要引入 array 头文件
#include <array>
using namespace std;
int main()
{
    std::array<int, 4> values{};
    //初始化 values 容器为 {0,1,2,3}
    for (int i = 0; i < values.size(); i++) {
        values.at(i) = i;
    }
    //使用 get() 重载函数输出指定位置元素
    cout << get<3>(values) << endl;
    //如果容器不为空，则输出容器中所有的元素
    if (!values.empty()) {
        for (auto val = values.begin(); val < values.end(); val++) {
            cout << *val << " ";
        }
    }
}
```

array通过`容器名[]`的方式直接访问和使用容器中的元素, 但使用这样方式，由于没有做任何边界检查，所以即便使用越界的索引值去访问或存储元素，也不会被检测到。为了能够有效地避免越界访问的情况，可以使用 array 容器提供的 at() 成员函数, 当传给 at() 的索引是一个越界值时，程序会抛出 std::out_of_range 异常。

### STL vector

array 实现的是静态数组（容量固定的数组），而 vector 实现的是一个动态数组，即可以进行元素的插入和删除. vector 常被称为向量容器，因为该容器擅长在尾部插入或删除元素，在常量时间内就可以完成，时间复杂度为`O(1)`；而对于在容器头部或者中部插入或删除元素，则花费时间要长一些（移动元素需要耗费时间），时间复杂度为线性阶`O(n)`。

vector的操作:

| 函数成员         | 函数功能                                                     |
| ---------------- | ------------------------------------------------------------ |
| max_size()       | 返回元素个数的最大值。这通常是一个很大的值，一般是 232-1，所以我们很少会用到这个函数。 |
| resize()         | 改变实际元素的个数。                                         |
| capacity()       | 返回当前容量。                                               |
| reserve()        | 增加容器的容量。                                             |
| shrink _to_fit() | 将内存减少到等于当前元素实际所使用的大小。                   |
| assign()         | 用新元素替换原有内容。                                       |
| swap()           | 交换两个容器的所有元素。                                     |
| emplace()        | 在指定的位置直接生成一个元素。                               |
| emplace_back()   | 在序列尾部生成一个元素。                                     |

```c++
	std::vector<int> demo{1,2};
    //第一种格式用法
    demo.insert(demo.begin() + 1, 3);//{1,3,2}
    //第二种格式用法
    demo.insert(demo.end(), 2, 5);//{1,3,2,5,5}
    //第三种格式用法
    std::array<int,3>test{ 7,8,9 };
    demo.insert(demo.end(), test.begin(), test.end());//{1,3,2,5,5,7,8,9}
    //第四种格式用法
    demo.insert(demo.end(), { 10,11 });//{1,3,2,5,5,7,8,9,10,11}
	//emplace() 每次只能插入一个 int 类型元素, emplace效率高于insert
    demo1.emplace(demo1.begin(), 3);
```

### STL deque

deque 是 double-ended queue 的缩写，又称双端队列容器。和 vector 不同的是，deque 还擅长在序列头部添加或删除元素，所耗费的时间复杂度也为常数阶`O(1)`. 并且更重要的一点是，`deque 容器中存储元素并不能保证所有元素都存储到连续的内存空间中。当需要向序列两端频繁的添加或删除元素时，应首选 deque 容器。`

成员函数内容和Vector基本一致. 

```c++
//初始化一个空deque容量
deque<int>d;
//向d容器中的尾部依次添加 1，2,3
d.push_back(1); //{1}
d.push_back(2); //{1,2}
d.push_back(3); //{1,2,3}
//向d容器的头部添加 0 
d.push_front(0); //{0,1,2,3}

```

和 array、vector 容器一样，可以采用普通数组访问存储元素的方式，访问 deque 容器中的元素.  如果想有效地避免越界访问，可以使用 deque 模板类提供的 at() 成员函数, 如果想有效地避免越界访问，可以使用 deque 模板类提供的 at() 成员函数.

```c++
d.front() = 10;
d.back() = 20;
```

### STL list

又称`双向链表容器`，即该容器的底层是以双向链表的形式实现的。这意味着，list 容器中的元素可以分散存储在内存空间里，而不是必须存储在一整块连续的内存空间中。每个元素都配备了 2 个指针，分别指向它的前一个元素和后一个元素。其中第一个元素的前向指针总为 null，因为它前面没有元素；同样，尾部元素的后向指针也总为 null。

![img](http://c.biancheng.net/uploads/allimg/180912/2-1P912134314345.jpg)

| 成员函数        | 功能                                                         |
| --------------- | ------------------------------------------------------------ |
| emplace_front() | 在容器头部生成一个元素。该函数和 push_front() 的功能相同，但效率更高。 |
| push_front()    | 在容器头部插入一个元素。                                     |
| pop_front()     | 删除容器头部的一个元素。                                     |
| emplace_back()  | 在容器尾部直接生成一个元素。该函数和 push_back() 的功能相同，但效率更高。 |
| push_back()     | 在容器尾部插入一个元素。                                     |
| pop_back()      | 删除容器尾部的一个元素。                                     |
| emplace()       | 在容器中的指定位置插入元素。该函数和 insert() 功能相同，但效率更高。 |
| erase()         | 删除容器中一个或某区域内的元素。                             |
| swap()          | 交换两个容器中的元素，必须保证这两个容器中存储的元素类型是相同的。 |
| splice()        | 将一个 list 容器中的元素插入到另一个容器的指定位置。         |
| remove(val)     | 删除容器中所有等于 val 的元素。                              |
| remove_if()     | 删除容器中满足条件的元素。                                   |
| unique()        | 删除容器中相邻的重复元素，只保留一个。                       |
| merge()         | 合并两个事先已排好序的 list 容器，并且合并之后的 list 容器依然是有序的。 |
| sort()          | 通过更改容器中元素的位置，将它们进行排序。                   |
| reverse()       | 反转容器中元素的顺序。                                       |

和 insert() 成员方法相比，splice() 成员方法的作用对象是其它 list 容器，其功能是将其它 list 容器中的元素添加到当前 list 容器中指定位置处。

```c++
// 第一个参数是目标位置, 后面的参数是被移动对象
//创建并初始化 2 个 list 容器
list<int> mylist1{ 1,2,3,4 }, mylist2{10,20,30};
list<int>::iterator it = ++mylist1.begin(); //指向 mylist1 容器中的元素 2
   
//调用第一种语法格式
mylist1.splice(it, mylist2); // mylist1: 1 10 20 30 2 3 4
                             // mylist2:
                             // it 迭代器仍然指向元素 2，只不过容器变为了 mylist1
//调用第二种语法格式，将 it 指向的元素 2 移动到 mylist2.begin() 位置处
mylist2.splice(mylist2.begin(), mylist1, it);   // mylist1: 1 10 20 30 3 4
                                                // mylist2: 2
                                                // it 仍然指向元素 2

//调用第三种语法格式，将 [mylist1.begin(),mylist1.end())范围内的元素移动到 mylist.begin() 位置处                  
mylist2.splice(mylist2.begin(), mylist1, mylist1.begin(), mylist1.end());//mylist1:
                                                                         //mylist2:1 10 20 30 3 4 2
```

### STL forward_list

forward_list 是 C++ 11 新添加的一类容器，其底层实现和 list 容器一样，采用的也是链表结构，只不过 forward_list 使用的是单链表，而 list 使用的是双向链表. 

![单链表和双向链表](http://c.biancheng.net/uploads/allimg/191219/2-191219135239561.gif)

forward_list 容器中是不提供 size() 函数的，但如果想要获取 forward_list 容器中存储元素的个数，可以使用头文件 \<iterator\> 中的 [distance()](http://c.biancheng.net/ref/tan.html) 函数。举个例子：

```c++
std::forward_list<int> my_words{1,2,3,4};
int count = std::distance(std::begin(my_words), std::end(my_words));
```

并且，forward_list 容器迭代器的移动除了使用 ++ 运算符单步移动，还能使用 advance() 函数.

```c++
// 结果: 3,4
std::forward_list<int> values{1,2,3,4};
auto it = values.begin();
advance(it, 2);
while (it!=values.end())
{
    cout << *it << " ";
    ++it;
}
```

## 关联式容器

关联式容器存储的是“键值对”形式的数据,  基于各个关联式容器存储数据的特点，只有各个键值对中的键和值全部对应相等时，才能使用 set 和 multiset 关联式容器存储，否则就要选用 map 或者 multimap 关联式容器。

### STL pair

考虑到“键值对”并不是普通类型数据，[C++](http://c.biancheng.net/cplus/) [STL](http://c.biancheng.net/stl/) 标准库提供了 pair 类模板，其专门用来将 2 个普通元素 first 和 second. pair 类模板定义在`<utility>`头文件中. 

下面程序演示了以上几种创建 pair 对象的方法:

```c++
// 调用构造函数 1，也就是默认构造函数
pair <string, double> pair1;
// 调用第 2 种构造函数
pair <string, string> pair2("STL教程","http://c.biancheng.net/stl/");  
// 调用拷贝构造函数
pair <string, string> pair3(pair2);
//调用移动构造函数
pair <string, string> pair4(make_pair("C++教程", "http://c.biancheng.net/cplus/"));
// 调用第 5 种构造函数
pair <string, string> pair5(string("Python教程"), string("http://c.biancheng.net/python/"));
```

`<utility>`头文件中除了提供创建 pair 对象的方法之外，还为 pair 对象重载了 <、<=、>、>=、==、!= 这 6 的运算符，其运算规则是：对于进行比较的 2 个 pair 对象，先比较 pair.first 元素的大小，如果相等则继续比较 pair.second 元素的大小。(二维偏序)

最后需要指出的是，pair类模板还提供有一个 swap() 成员函数，能够互换 2 个 pair 对象的键值对，其操作成功的前提是这 2 个 pair 对象的键和值的类型要相同.

```c++
pair <string, int> pair1("pair", 10);                   
pair <string, int> pair2("pair2", 20);
//交换 pair1 和 pair2 的键值对
pair1.swap(pair2);
//pair1: pair2 20
//pair2: pair 10
```

### STL map

map 容器存储的都是 pair 对象，也就是用 pair 类模板创建的键值对。与此同时，在使用 map 容器存储多个键值对时，该容器会自动根据各键值对的键的大小，按照既定的规则进行排序,  根据实际情况的需要，我们可以手动指定 map 容器的排序规则. `使用 map 容器存储的各个键值对，键的值既不能重复也不能被修改。`这意味着只要键值对被存储到 map 容器中，其键的值将不能再做任何修改。

```c++
//如下语句可以指定升序排列键值
std::map<std::string, int, std::greater<std::string> >myMap{ {"C语言教程",10},{"STL教程",20} };
```

| 成员函数         | 功能                                                         |
| ---------------- | ------------------------------------------------------------ |
| find(key)        | 在 map 容器中查找键为 key 的键值对，如果成功找到，则返回指向该键值对的双向迭代器；反之，则返回和 end() 方法一样的迭代器。另外，如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。 |
| lower_bound(key) | 返回一个指向当前 map 容器中第一个大于或等于 key 的键值对的双向迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。 |
| upper_bound(key) | 返回一个指向当前 map 容器中第一个大于 key 的键值对的迭代器。如果 map 容器用 const 限定，则该方法返回的是 const 类型的双向迭代器。 |
| equal_range(key) | 该方法返回一个 pair 对象（包含 2 个双向迭代器），其中 pair.first 和 lower_bound() 方法的返回值等价，pair.second 和 upper_bound() 方法的返回值等价。也就是说，该方法将返回一个范围，该范围中包含的键为 key 的键值对（map 容器键值对唯一，因此该范围最多包含一个键值对）。 |
| emplace()        | 在当前 map 容器中的指定位置处构造新键值对。其效果和插入键值对一样，但效率更高。 |
| emplace_hint()   | 在本质上和 emplace() 在 map 容器中构造新键值对的方式是一样的，不同之处在于，使用者必须为该方法提供一个指示键值对生成位置的迭代器，并作为该方法的第一个参数。 |
| count(key)       | 在当前 map 容器中，查找键为 key 的键值对的个数并返回。注意，由于 map 容器中各键值对的键的值是唯一的，因此该函数的返回值最大为 1。 |

![C++ STL map部分成员方法示意图](http://c.biancheng.net/uploads/allimg/191128/2-19112Q14QE40.gif)

```c++
//创建并初始化 map 容器
std::map<std::string, std::string>myMap{ {"STL教程","http://c.biancheng.net/stl/"},
                                         {"C语言教程","http://c.biancheng.net/c/"},
                                         {"Java教程","http://c.biancheng.net/java/"} };
//找到第一个键的值不小于 "Java教程" 的键值对
auto iter = myMap.lower_bound("Java教程");
//lower：Java教程 http://c.biancheng.net/java/
cout << "lower：" << iter->first << " " << iter->second << endl;

//找到第一个键的值大于 "Java教程" 的键值对
iter = myMap.upper_bound("Java教程");
//upper：STL教程 http://c.biancheng.net/stl/
cout <<"upper：" << iter->first << " " << iter->second << endl;
```

和 insert() 方法一样，虽然 emplace_hint() 方法指定了插入键值对的位置，但 map 容器为了保持存储键值对的有序状态，可能会移动其位置。

```c++
//创建并初始化 map 容器
std::map<string, string>mymap;
//指定在 map 容器插入键值对
map<string, string>::iterator iter = mymap.emplace_hint(mymap.begin(),"STL教程", "http://c.biancheng.net/stl/");
cout << iter->first << " " << iter->second << endl;
iter = mymap.emplace_hint(mymap.begin(), "C语言教程", "http://c.biancheng.net/c/");
cout << iter->first << " " << iter->second << endl;
//插入失败样例
iter = mymap.emplace_hint(mymap.begin(), "STL教程", "http://c.biancheng.net/java/");
cout << iter->first << " " << iter->second << endl;
```

只有当 map 容器中确实存有包含该指定键的键值对，借助重载的 [ ] 运算符才能成功获取该键对应的值；反之，若当前 map 容器中没有包含该指定键的键值对，则此时使用 [ ] 运算符将不再是访问容器中的元素，而变成了向该 map 容器中增添一个键值对。

### STL multimap

multimap 容器具有和 map 相同的特性，即 multimap 容器也用于存储 pair<const K, T> 类型的键值对（其中 K 表示键的类型，T 表示值的类型），其中各个键值对的键的值不能做修改；并且，该容器也会自行根据键的大小对存储的所有键值对做排序操作。`和 map 容器的区别在于，multimap 容器中可以同时存储多（≥2）个键相同的键值对。`和 map 容器一样，实现 multimap 容器的类模板也定义在`<map>`头文件，并位于 std 命名空间中。在某些特定场景中，我们还可以为 multimap 容器自定义排序规则. multimap的操作和成员函数基本与map完全一致. 但和 map 容器相比，`multimap 未提供 at() 成员方法，也没有重载 [] 运算符`。这意味着，map 容器中通过指定键获取指定指定键值对的方式，将不再适用于 multimap 容器。其实这很好理解，因为 multimap 容器中指定的键可能对应多个键值对，而不再是 1 个。另外，由于maltimap容器可存储多个具有相同键的键值对，因此lower_bound()、upper_bound()、equal_range()以及count()方法经常会用到。

### STL set 

和 map、multimap 容器不同，使用 set 容器存储的各个键值对，要求键 key 和值 value 必须相等。基于 set 容器的这种特性，当使用 set 容器存储键值对时，只需要为其提供各键值对中的 value 值（也就是 key 的值）即可。set 容器也会自行根据键的大小对存储的键值对进行排序. `使用 set 容器存储的各个元素的值必须各不相同`。更重要的是，从语法上讲 set 容器并没有强制对存储元素的类型做 const 修饰，即 set 容器中存储的元素的值是可以修改的。但是，C++ 标准为了防止用户修改容器中元素的值，对所有可能会实现此操作的行为做了限制，`使得在正常情况下，用户是无法做到修改 set 容器中元素的值的。`

`对于初学者来说，切勿尝试直接修改 set 容器中已存储元素的值，这很有可能破坏 set 容器中元素的有序性，最正确的修改 set 容器中元素值的做法是：先删除该元素，然后再添加一个修改后的元素。`

set的成员函数和multimap基本一致, `set 容器类模板中未提供 at() 成员函数，也未对 [] 运算符进行重载。因此，要想访问 set 容器中存储的元素，只能借助 set 容器的迭代器。`

`C++ STL 标准库为 set 容器配置的迭代器类型为双向迭代器。这意味着，假设 p 为此类型的迭代器，则其只能进行 ++p、p++、--p、p--、*p 操作，并且 2 个双向迭代器之间做比较，也只能使用 == 或者 != 运算符。`

如果只想遍历 set 容器中指定区域内的部分数据，则可以借助 find()、lower_bound() 以及 upper_bound() 实现。通过调用它们，可以获取一个指向指定元素的迭代器。equal_range(val) 函数的返回值是一个 pair 类型数据，其包含 2 个迭代器，表示 set 容器中和指定参数 val 相等的元素所在的区域，但由于 set 容器中存储的元素各不相等，因此该函数返回的这 2 个迭代器所表示的范围中，最多只会包含 1 个元素。

### STL multiset

和 set 容器不同的是，`multiset 容器可以存储多个值相同的元素。`虽然 multiset 容器和 set 容器拥有的成员方法完全相同，但由于 multiset 容器允许存储多个值相同的元素，因此诸如 count()、find()、lower_bound()、upper_bound()、equal_range()等方法，更常用于 multiset 容器。

```c++
std::multiset<int> mymultiset{1,2,2,2,3,4,5};
//multiset size = 7
cout << "multiset size = " << mymultiset.size() << endl;
//multiset count(2) =3
cout << "multiset count(2) =" << mymultiset.count(2) << endl;
//向容器中添加元素 8
mymultiset.insert(8);
//删除容器中所有值为 2 的元素
int num = mymultiset.erase(2);
//删除了 3 个元素 2
cout << "删除了 " << num << " 个元素 2" << endl;
//输出容器中存储的所有元素 1 3 4 5 8
for (auto iter = mymultiset.begin(); iter != mymultiset.end(); ++iter) {
    cout << *iter << " ";
}
```

### 关联式容器自定义排序

模板库中常用的可供关联容器使用的排序规则为![在这里插入图片描述](https://img-blog.csdnimg.cn/20210422104233471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDQ4NDcxNQ==,size_16,color_FFFFFF,t_70#pic_center)

同时也可以自定义:

```c++
class cmp {
public:
    //重载 () 运算符
    bool operator ()(const string &a,const string &b) {
        //按照字符串的长度，做升序排序(即存储的字符串从短到长)
        return  (a.length() < b.length());
    }
};
int main() {
    //创建 set 容器，并使用自定义的 cmp 排序规则
    std::set<string, cmp>myset{"http://c.biancheng.net/stl/",
                               "http://c.biancheng.net/python/",
                               "http://c.biancheng.net/java/"};
    //输出容器中存储的元素
    for (auto iter = myset.begin(); iter != myset.end(); ++iter) {
            cout << *iter << endl;
    }
    return 0;
}
//结果：
//http://c.biancheng.net/stl/
//http://c.biancheng.net/java/
//http://c.biancheng.net/python/

```

当关联式容器中存储的数据类型为自定义的结构体变量或者类对象时，通过对现有排序规则中所用的关系运算符进行重载，也能实现自定义排序规则的目的。注意，当关联式容器中存储的元素类型为结构体指针变量或者类的指针对象时，只能使用函数对象的方式自定义排序规则，此方法不再适用。

## 无序关联式容器

无序关联式容器，又称哈希容器。和关联式容器一样，此类容器存储的也是键值对元素；不同之处在于，关联式容器默认情况下会对存储的元素做升序排序，而无序关联式容器不会。`无序关联式容器擅长通过指定键查找对应的值，而遍历容器中存储元素的效率不如关联式容器。`

**关联式容器的底层实现采用的树存储结构，更确切的说是红黑树结构；无序容器的底层实现采用的是哈希表的存储结构, 并且当数据存储位置发生冲突时，解决方法选用的是“链地址法”。**

基于底层实现采用了不同的数据结构，因此和关联式容器相比，无序容器具有以下 2 个特点：

1. 无序容器内部存储的键值对是无序的，各键值对的存储位置取决于该键值对中的键，
2. 和关联式容器相比，无序容器擅长通过指定键查找对应的值（平均时间复杂度为 O(1)）；但对于使用迭代器遍历容器中存储的元素，无序容器的执行效率则不如关联式容器。

| 无序容器           | 功能                                                         |
| ------------------ | ------------------------------------------------------------ |
| unordered_map      | 存储键值对 <key, value> 类型的元素，其中各个键值对键的值不允许重复，且该容器中存储的键值对是无序的。 |
| unordered_multimap | 和 unordered_map 唯一的区别在于，该容器允许存储多个键相同的键值对。 |
| unordered_set      | 不再以键值对的形式存储数据，而是直接存储数据元素本身（当然也可以理解为，该容器存储的全部都是键 key 和值 value 相等的键值对，正因为它们相等，因此只存储 value 即可）。另外，该容器存储的元素不能重复，且容器内部存储的元素也是无序的。 |
| unordered_multiset | 和 unordered_set 唯一的区别在于，该容器允许存储值相同的元素。 |

### unordered_map 

unordered_map 定义在`<unordered_map>`头文件

| 成员方法           | 功能                                                         |
| ------------------ | ------------------------------------------------------------ |
| bucket_count()     | 返回当前容器底层存储键值对时，使用桶（一个线性链表代表一个桶）的数量。 |
| max_bucket_count() | 返回当前系统中，unordered_map 容器底层最多可以使用多少桶。   |
| bucket_size(n)     | 返回第 n 个桶中存储键值对的数量。                            |
| bucket(key)        | 返回以 key 为键的键值对所在桶的编号。                        |
| load_factor()      | 返回 unordered_map 容器中当前的负载因子。负载因子，指的是的当前容器中存储键值对的数量（size()）和使用桶数（bucket_count()）的比值，即 load_factor() = size() / bucket_count()。 |
| max_load_factor()  | 返回或者设置当前 unordered_map 容器的负载因子。              |
| rehash(n)          | 将当前容器底层使用桶的数量设置为 n。                         |
| reserve()          | 将存储桶的数量（也就是 bucket_count() 方法的返回值）设置为至少容纳count个元（不超过最大负载因子）所需的数量，并重新整理容器。 |
| hash_function()    | 返回当前容器使用的哈希函数对象。                             |

unordered_map 容器类模板中，实现了对 [ ] 运算符的重载，使得我们可以像“利用下标访问普通数组中元素”那样，通过目标键值对的键获取到该键对应的值。如果当前容器中并没有存储以 [ ] 运算符内指定的元素作为键的键值对，则此时 [ ] 运算符的功能将转变为：向当前容器中添加以目标元素为键的键值对. 

### STL unordered_multimap

unordered_multimap 容器可以存储多个键相等的键值对，而 unordered_map 容器不行。

### STL unordered_set

unordered_set 容器，可直译为“无序 set 容器”，即 unordered_set 容器和 set 容器很像，唯一的区别就在于 set 容器会自行对存储的数据进行排序，而 unordered_set 容器不会。实现 unordered_set 容器的模板类定义在<unordered_set>头文件

**总的来说，unordered_set 容器具有以下几个特性：**

1. **不再以键值对的形式存储数据，而是直接存储数据的值；**
2. **容器内部存储的各个元素的值都互不相等，且不能被修改。**
3. **不会对内部存储的数据进行排序**

### STL unordered_multiset

和 unordered_set 容器不同的是，unordered_multiset 容器可以同时存储多个值相同的元素，且这些元素会存储到哈希表中同一个桶（本质就是链表）上。

## 容器适配器

STL 提供了 3 种容器适配器，分别为 stack 栈适配器、queue 队列适配器以及 priority_queue 优先权队列适配器。

| 容器适配器     | 基础容器筛选条件                                             | 默认使用的基础容器 |
| -------------- | ------------------------------------------------------------ | ------------------ |
| stack          | 基础容器需包含以下成员函数：empty()size()back()push_back()pop_back()满足条件的基础容器有 vector、deque、list。 | deque              |
| queue          | 基础容器需包含以下成员函数：empty()size()front()back()push_back()pop_front()满足条件的基础容器有 deque、list。 | deque              |
| priority_queue | 基础容器需包含以下成员函数：empty()size()front()push_back()pop_back()满足条件的基础容器有vector、deque。 | vector             |

### STL stack

stack 栈适配器是一种单端开口的容器（如图 1 所示），实际上该容器模拟的就是栈存储结构，即无论是向里存数据还是从中取数据，都只能从这一个开口实现操作。

![stack适配器示意图](http://c.biancheng.net/uploads/allimg/180913/2-1P913101Q4T2.jpg)



| 成员函数                     | 功能                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| empty()                      | 当 stack 栈中没有元素时，该成员函数返回 true；反之，返回 false。 |
| size()                       | 返回 stack 栈中存储元素的个数。                              |
| top()                        | 返回一个栈顶元素的引用，类型为 T&。如果栈为空，程序会报错。  |
| push(const T& val)           | 先复制 val，再将 val 副本压入栈顶。这是通过调用底层容器的 push_back() 函数完成的。 |
| push(T&& obj)                | 以移动元素的方式将其压入栈顶。这是通过调用底层容器的有右值引用参数的 push_back() 函数完成的。 |
| pop()                        | 弹出栈顶元素。                                               |
| emplace(arg...)              | arg... 可以是一个参数，也可以是多个参数，但它们都只用于构造一个对象，并在栈顶直接生成该对象，作为新的栈顶元素。 |
| swap(stack<T> & other_stack) | 将两个 stack 适配器中的元素进行互换，需要注意的是，进行互换的 2 个 stack 适配器中存储的元素类型以及底层采用的基础容器类型，都必须相同。 |

### STL queue

queue 容器适配器有 2 个开口，其中一个开口专门用来输入数据，另一个专门用来输出数据

![queue容器适配器](http://c.biancheng.net/uploads/allimg/180913/2-1P913113140553.jpg)



| 成员函数                    | 功能                                                         |
| --------------------------- | ------------------------------------------------------------ |
| empty()                     | 如果 queue 中没有元素的话，返回 true。                       |
| size()                      | 返回 queue 中元素的个数。                                    |
| front()                     | 返回 queue 中第一个元素的引用。如果 queue 是常量，就返回一个常引用；如果 queue 为空，返回值是未定义的。 |
| back()                      | 返回 queue 中最后一个元素的引用。如果 queue 是常量，就返回一个常引用；如果 queue 为空，返回值是未定义的。 |
| push(const T& obj)          | 在 queue 的尾部添加一个元素的副本。这是通过调用底层容器的成员函数 push_back() 来完成的。 |
| emplace()                   | 在 queue 的尾部直接添加一个元素。                            |
| push(T&& obj)               | 以移动的方式在 queue 的尾部添加元素。这是通过调用底层容器的具有右值引用参数的成员函数 push_back() 来完成的。 |
| pop()                       | 删除 queue 中的第一个元素。                                  |
| swap(queue<T> &other_queue) | 将两个 queue 容器适配器中的元素进行互换，需要注意的是，进行互换的 2 个 queue 容器适配器中存储的元素类型以及底层采用的基础容器类型，都必须相同。 |

`和 stack 一样，queue 也没有迭代器，因此访问元素的唯一方式是遍历容器，通过不断移除访问过的元素，去访问下一个元素。`

### STL priority_queue

priority_queue 容器适配器模拟的也是队列这种存储结构，即使用此容器适配器存储元素只能“从一端进（称为队尾），从另一端出（称为队头）”，且每次只能访问 priority_queue 中位于队头的元素。但是，priority_queue 容器适配器中元素的存和取，遵循的并不是 “First in,First out”（先入先出）原则，而是“First in，Largest out”原则。直白的翻译，指的就是先进队列的元素并不一定先出队列，而是优先级最大的元素最先出队列。使用`std::less<T>`按照元素值从大到小进行排序，还可以使用`std::greater<T>`按照元素值从小到大排序，但更多情况下是使用自定义的排序规则。 priority_queue 容器适配器模板位于`<queue>`头文件中. 

| 成员函数                       | 功能                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| empty()                        | 如果 priority_queue 为空的话，返回 true；反之，返回 false。  |
| size()                         | 返回 priority_queue 中存储元素的个数。                       |
| top()                          | 返回 priority_queue 中第一个元素的引用形式。                 |
| push(const T& obj)             | 根据既定的排序规则，将元素 obj 的副本存储到 priority_queue 中适当的位置。 |
| push(T&& obj)                  | 根据既定的排序规则，将元素 obj 移动存储到 priority_queue 中适当的位置。 |
| emplace(Args&&... args)        | Args&&... args 表示构造一个存储类型的元素所需要的数据（对于类对象来说，可能需要多个数据构造出一个对象）。此函数的功能是根据既定的排序规则，在容器适配器适当的位置直接生成该新元素。 |
| pop()                          | 移除 priority_queue 容器适配器中第一个元素。                 |
| swap(priority_queue<T>& other) | 将两个 priority_queue 容器适配器中的元素进行互换，需要注意的是，进行互换的 2 个 priority_queue 容器适配器中存储的元素类型以及底层采用的基础容器类型，都必须相同。 |

优先级队列默认**使用vector作为其底层存储数据**的容器，在vector上又使用了堆算法将vector中元素构造成堆的结构，因此priority_queue就是堆，所有需要用到堆的位置，都可以考虑使用priority_queue。
