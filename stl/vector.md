# std::vector 用法详解

`std::vector` 是 C++ 标准模板库中最常用的序列容器，封装了动态数组，支持随机访问，在尾部进行插入和删除的效率很高。

---

## 1. 头文件与基本定义

```cpp
#include <vector>

// 命名空间
using std::vector;
vector 是一个类模板：template <class T, class Allocator = std::allocator<T>> class vector;
元素连续存储，可通过下标 + 指针偏移快速访问。
2. 构造与初始化
Cpp
vector<int> v1;                // 空 vector
vector<int> v2(5);             // 5 个元素，值初始化为 0
vector<int> v3(5, 10);         // 5 个元素，每个都是 10
vector<int> v4 = {1, 2, 3};    // 列表初始化
vector<int> v5(v4);            // 拷贝构造
vector<int> v6(v4.begin(), v4.end()); // 迭代器范围构造
C++17 起支持类模板参数推导（CTAD）：

Cpp
vector v = {1, 2, 3}; // 自动推导为 vector<int>
3. 赋值操作
Cpp
vector<int> a = {1, 2, 3};
vector<int> b;
b = a;                  // 拷贝赋值
b.assign(3, 100);       // 赋值为 3 个 100
b.assign({4, 5, 6});    // 列表赋值
b.assign(a.begin(), a.end()); // 迭代器范围赋值
4. 元素访问
方法	说明	是否有边界检查
v[i]	下标访问，不检查越界	否
v.at(i)	下标访问，越界抛出 std::out_of_range	是
v.front()	返回第一个元素的引用	否（容器空时未定义行为）
v.back()	返回最后一个元素的引用	否（容器空时未定义行为）
v.data()	返回指向底层数组的原始指针	-
Cpp
vector<int> v = {10, 20, 30};
cout << v[1] << endl;      // 20
cout << v.at(1) << endl;   // 20
cout << v.front() << endl; // 10
cout << v.back() << endl;  // 30
5. 容量相关操作
方法	说明
v.size()	返回当前元素个数
v.capacity()	返回已分配内存可容纳的元素个数
v.empty()	判断是否为空（size() == 0）
v.reserve(n)	预分配内存，使容量至少为 n，不改变 size
v.shrink_to_fit()	请求减少容量以匹配 size（C++11）
v.resize(n)	调整 size 为 n，多出的元素值初始化；若缩小则截断
v.resize(n, val)	调整 size 为 n，新元素以 val 填充
v.max_size()	理论可以存储的最大元素数
重点理解：

size 是有效元素数量，capacity 是已经分配的内存能容纳多少元素。
当 size() > capacity() 时，插入操作会导致重新分配，所有迭代器、指针、引用失效。
使用 reserve 可以避免频繁的内存重分配。
Cpp
vector<int> v;
v.reserve(100);
for (int i = 0; i < 100; ++i)
    v.push_back(i); // 不会发生内存重分配
6. 修改容器（增删改）
6.1 插入元素
Cpp
v.push_back(val);                     // 尾部插入
v.emplace_back(args...);              // 尾部原位构造（C++11）
v.insert(pos, val);                   // 在迭代器 pos 前插入 val
v.insert(pos, n, val);                // 插入 n 个 val
v.insert(pos, first, last);           // 插入迭代器范围
v.insert(pos, {1, 2, 3});            // 插入初始化列表
v.emplace(pos, args...);              // 在 pos 前原位构造元素
6.2 删除元素
Cpp
v.pop_back();                  // 删除最后一个元素
v.erase(pos);                  // 删除 pos 指向的元素，返回下一个有效迭代器
v.erase(first, last);          // 删除 [first, last) 范围的元素
v.clear();                     // 清空所有元素
6.3 交换与赋值
Cpp
v1.swap(v2);                   // 交换两个 vector 的内容
std::swap(v1, v2);            // 同样是交换（C++11 后建议使用 std::swap）
注意：

erase 之后迭代器失效，返回值是删除后下一个元素的迭代器。
insert 可能导致所有迭代器失效（若发生重新分配），返回新插入元素的迭代器。
Cpp
vector<int> v = {1, 2, 3, 4};
// 删除所有偶数
for (auto it = v.begin(); it != v.end(); ) {
    if (*it % 2 == 0)
        it = v.erase(it);
    else
        ++it;
}
7. 迭代器
类型	说明
begin() / end()	起始 / 尾后迭代器
cbegin() / cend()	常量迭代器（C++11）
rbegin() / rend()	反向迭代器
crbegin() / crend()	常量反向迭代器
Cpp
vector<int> v = {10, 20, 30};
for (auto it = v.begin(); it != v.end(); ++it)
    cout << *it << " ";   // 10 20 30
for (auto rit = v.rbegin(); rit != v.rend(); ++rit)
    cout << *rit << " ";  // 30 20 10
vector 的迭代器是随机访问迭代器，支持：

算术运算：it + n, it - n, it1 - it2
比较：<, >, <=, >=
下标：it[n] 等价于 *(it + n)
8. 比较运算
两个 vector 的内容可以直接使用关系运算符进行比较，规则是字典序比较：

Cpp
vector<int> a = {1, 2, 3};
vector<int> b = {1, 2, 4};
bool equal   = (a == b);  // false
bool less    = (a < b);   // true
支持 ==, !=, <, <=, >, >=（C++20 前要求元素类型支持对应比较）。

9. 常用算法结合
vector 与 <algorithm> 完美搭配。

排序
Cpp
#include <algorithm>
vector<int> v = {3, 1, 4, 1, 5};
sort(v.begin(), v.end()); // 升序
sort(v.begin(), v.end(), greater<int>()); // 降序
查找
Cpp
auto it = find(v.begin(), v.end(), 3);
if (it != v.end()) cout << "Found at index " << it - v.begin();
去重（需先排序）
Cpp
sort(v.begin(), v.end());
auto last = unique(v.begin(), v.end());
v.erase(last, v.end());
最小值、最大值
Cpp
int minVal = *min_element(v.begin(), v.end());
int maxVal = *max_element(v.begin(), v.end());
反转
Cpp
reverse(v.begin(), v.end());
清除特定元素（Erase-remove 惯用法）
Cpp
v.erase(remove(v.begin(), v.end(), 1), v.end()); // 删除所有值为 1 的元素
v.erase(remove_if(v.begin(), v.end(), [](int x){ return x % 2 == 0; }), v.end()); // 删除所有偶数
10. 性能注意事项
尾部插入/删除：O(1) 均摊。
在中间或开头插入/删除：O(n)，因为需要移动元素。
随机访问：O(1)。
查找：O(n)（除非有序并配合二分查找）。
内存重分配：发生在 size() > capacity() 时，重新分配会导致所有元素移动或复制，迭代器、指针、引用全部失效。建议预分配 reserve 以减少开销。
push_back vs emplace_back：emplace_back 可以直接在尾部构造对象，避免临时对象的拷贝或移动，更适合不可拷贝或构造复杂的对象。
Cpp
vector<pair<int, string>> vp;
vp.push_back(make_pair(1, "one")); // 需要构造临时 pair
vp.emplace_back(2, "two");         // 直接构造，更高效
11. 自定义类型存储
当 vector 存储自定义类型对象时，需要注意：

该类型必须可拷贝/可移动（取决于使用的操作）。
插入时可能调用拷贝构造函数，emplace 系列可以原位构造。
如果需要指针稳定性，可以考虑存储智能指针（vector<shared_ptr<MyClass>>）。
Cpp
struct Point {
    double x, y;
    Point(double x, double y) : x(x), y(y) {}
};
vector<Point> points;
points.emplace_back(3.0, 4.0);
12. 多维 vector
vector 可以嵌套形成二维数组：

Cpp
vector<vector<int>> matrix(3, vector<int>(4, 0)); // 3行4列，初始化为0
matrix[1][2] = 5;
注意：这种结构内存不连续，每一行是单独分配的。如果需要连续存储的二维数组，可以使用一维 vector + 坐标计算或使用 std::vector<int> + std::mdspan (C++23)。

13. C++11 起的重要改进
移动语义：return 局部 vector 会移动而不是拷贝，极大提升效率。
shrink_to_fit() 可以请求释放未使用的内存。
cbegin/cend 方便写泛型代码。
std::initializer_list 支持，可以直接使用大括号初始化。
14. 与数组、C 接口交互
Cpp
// 从数组构造
int arr[] = {1, 2, 3, 4};
vector<int> v(begin(arr), end(arr));
// 获取原始指针传递给 C 函数
void c_func(int* p, size_t n);
c_func(v.data(), v.size());
data() 方法返回指向底层数组的指针，如果 v 为空，data() 可能返回非空但不可解引用的指针（C++11 起保证可安全获取 data() 和 size() 信息）。

15. 总结速查表
操作	代码示例
创建指定大小	vector<int> v(10, 2);
尾部添加	v.push_back(x);
原位构造尾部	v.emplace_back(args...);
插入	v.insert(pos, x);
删除最后一个	v.pop_back();
删除指定元素	v.erase(pos);
清空	v.clear();
大小/容量	v.size(), v.capacity()
预分配	v.reserve(100);
重设大小	v.resize(n);
判断空	v.empty()
访问元素	v[i], v.at(i), v.front(), v.back()
获取指针	v.data()
迭代器	begin(), end(), rbegin(), rend()
排序	sort(v.begin(), v.end());
遍历	for (auto& x : v)
