# C++ `unordered_map` 语法用法详解
## 1. 简介与头文件
`std::unordered_map` 是 C++ 标准库提供的关联容器，基于哈希表实现，用于存储**键值对（key-value）**，其中键（key）唯一。
- 头文件：`<unordered_map>`
- 命名空间：`std`
- 平均时间复杂度：插入、删除、查找均为 **O(1)**（最坏 O(n)）。
- 元素顺序：无序，取决于哈希策略和桶排列。
## 2. 定义与基本声明
```cpp
#include <iostream>
#include <unordered_map>
#include <string>
int main() {
    // 创建一个 unordered_map: 键为 string, 值为 int
    std::unordered_map<std::string, int> umap;
    // 使用 initializer_list 初始化
    std::unordered_map<std::string, int> umap2 = {
        {"apple", 2},
        {"banana", 5},
        {"cherry", 10}
    };
    // 指定桶数量的构造
    std::unordered_map<int, std::string> umap3(100); // 建议初始桶数
    // 拷贝构造、移动构造等
    auto umap4 = umap2;
    auto umap5 = std::move(umap3);
}
```

## 3. 常用成员函数
### 3.1 容量相关
| 函数 | 说明 |
|------|------|
| `empty()` | 判断是否为空 |
| `size()` | 返回元素个数 |
| `max_size()` | 返回最大可容纳元素数 |
### 3.2 插入与删除
| 函数 | 说明 |
|------|------|
| `empty()` | 判断是否为空 |
| `size()` | 返回元素个数 |
| `max_size()` | 返回最大可容纳元素数 |

| 函数 | 说明 |
|------|------|
| `insert(pair)` | 插入键值对（若键已存在则失败） |
| `emplace(key, value)` | 原地构造元素，比 insert 更高效 |
| `operator[]` | 访问或插入键，若键不存在则插入默认值 |
| `at(key)` | 带边界检查的访问，键不存在抛 std::out_of_range |
| `erase(key)` | 删除指定键的元素，返回删除个数（0 或 1） |
| `erase(iterator)` | 删除迭代器指向的元素 |
| `clear()` | 清空所有元素 |
| `swap(other)` | 交换两个 unordered_map 内容 |

### 3.3 查找与访问

| 函数 | 说明 |
|------|------|
| `find(key)` | 返回迭代器，指向找到的元素；未找到返回 end() |
| `count(key)` | 返回键出现的次数（0 或 1） |
| `contains(key)` | C++20 引入，返回是否存在（true/false） |
| `equal_range(key)` | 返回一对迭代器，表示范围 |

### 3.4 哈希策略与桶接口

| 函数 | 说明 |
|------|------|
| `bucket_count()` | 返回当前桶的数量 |
| `max_bucket_count()` | 返回最大可能的桶数量 |
| `bucket_size(n)` | 返回第 n 个桶中元素个数 |
| `bucket(key)` | 返回键所在的桶索引 |
| `load_factor()` | 返回当前负载因子（元素数 / 桶数） |
| `max_load_factor()` | 获取/设置最大负载因子（默认 1.0），超过则 rehash |
| `rehash(n)` | 设置桶数量至少为 n，并重新哈希所有元素 |
| `reserve(n)` | 预留空间，使桶数可容纳至少 n 个元素，避免多次 rehash |

### 3.5 迭代器

支持 begin(), end(), cbegin(), cend() 等，迭代顺序同桶遍历顺序（无序）。

```cpp
for (auto it = umap.begin(); it != umap.end(); ++it) {
    std::cout << it->first << ": " << it->second << '\n';
}
// 范围 for 循环
for (const auto& [key, value] : umap) {
    std::cout << key << ": " << value << '\n';
}
```
## 4. 基本操作示例
```cpp
#include <unordered_map>
#include <string>
#include <iostream>

int main() {
    std::unordered_map<std::string, int> fruit_map;

    // 1. 插入
    fruit_map.insert({"apple", 5});
    fruit_map.insert(std::make_pair("banana", 3));
    fruit_map.emplace("cherry", 10);

    // 2. 使用 operator[] （若不存在则创建并值初始化为 0）
    fruit_map["orange"] = 7;
    std::cout << "grape count: " << fruit_map["grape"] << std::endl; // 插入 grape: 0

    // 3. at() 访问
    try {
        std::cout << "apple: " << fruit_map.at("apple") << std::endl;
        std::cout << "pear: " << fruit_map.at("pear") << std::endl; // 抛出异常
    } catch (const std::out_of_range& e) {
        std::cerr << "Key not found: " << e.what() << std::endl;
    }

    // 4. 查找
    auto iter = fruit_map.find("banana");
    if (iter != fruit_map.end()) {
        std::cout << "Found banana: " << iter->second << std::endl;
    }

    // C++20 的 contains
    // if (fruit_map.contains("apple")) { ... }

    // 5. 删除
    fruit_map.erase("orange");   // 删除指定键
    fruit_map.erase(fruit_map.begin()); // 删除第一个迭代器元素

    // 6. 遍历
    for (const auto& pair : fruit_map) {
        std::cout << pair.first << " => " << pair.second << '\n';
    }

    // 7. 负载与哈希策略
    std::cout << "bucket count: " << fruit_map.bucket_count() << std::endl;
    std::cout << "load factor: " << fruit_map.load_factor() << std::endl;
    fruit_map.reserve(100); // 预分配桶数以容纳至少100个元素

    return 0;
}
```
## 5. 自定义哈希函数与键相等比较
std::unordered_map 的完整模板参数：

```Cpp
template<
    class Key,
    class T,
    class Hash = std::hash<Key>,
    class KeyEqual = std::equal_to<Key>,
    class Allocator = std::allocator<std::pair<const Key, T>>
> class unordered_map;
```
若键是自定义类型，需提供哈希函数和相等比较。
### 5.1 自定义类型示例

```cpp
#include <iostream>
#include <unordered_map>
#include <string>

struct Person {
    std::string name;
    int age;

    // 必须定义 operator==
    bool operator==(const Person& other) const {
        return name == other.name && age == other.age;
    }
};

// 自定义哈希函数
struct PersonHash {
    std::size_t operator()(const Person& p) const {
        std::size_t h1 = std::hash<std::string>{}(p.name);
        std::size_t h2 = std::hash<int>{}(p.age);
        return h1 ^ (h2 << 1); // 简单组合哈希
    }
};

int main() {
    std::unordered_map<Person, std::string, PersonHash> persons;
    persons[{ "Alice", 30 }] = "Engineer";
    persons[{ "Bob", 25 }] = "Designer";

    for (const auto& [person, job] : persons) {
        std::cout << person.name << " (" << person.age << ") -> " << job << '\n';
    }
}
```
### 5.2 使用 lambda 作为哈希函数（C++20 起可用）

在 C++20 中，可以使用无捕获的 lambda 作为哈希类型。

```cpp
auto hash_lambda = [](const Person& p) -> std::size_t {
    return std::hash<std::string>{}(p.name) ^ (std::hash<int>{}(p.age) << 1);
};
auto equal_lambda = [](const Person& a, const Person& b) { return a == b; };
std::unordered_map<Person, std::string, decltype(hash_lambda), decltype(equal_lambda)>
    person_map(10, hash_lambda, equal_lambda);
```
## 6. 性能注意事项
- **负载因子**：默认最大负载因子为 1.0，当元素数超过 `bucket_count() * max_load_factor()` 时自动 rehash。
- **rehash 代价**：O(n)，会重建哈希表。可通过 `reserve` 提前分配足够桶数。
- **最坏情况**：若哈希函数产生大量冲突，单个桶可能退化为链表，操作降至 O(n)。
- **适用场景**：频繁查找、插入、删除，且不关心元素顺序。
- **内存占用**：哈希表通常比 `std::map`（红黑树）占用更多内存。

## 7. 与 std::map 对比
| 特性 | `std::unordered_map` | `std::map` |
|------|----------------------|-------------|
| 实现结构 | 哈希表 | 红黑树 |
| 元素顺序 | 无序 | 按键排序 |
| 查找/插入/删除 | 平均 O(1)，最坏 O(n) | O(log n) |
| 内存开销 | 较大 | 较小 |
| 哈希函数要求 | 需定义哈希和相等比较 | 仅需 `<` 运算符 |
| 适用场景 | 快速随机访问，不关心顺序 | 需要有序遍历、范围查询 |

### 8. 常见陷阱
- **使用 `operator[]` 会插入默认值**：仅查询时需要优先使用 `find()` 或 `at()`。
  ```cpp
  if (umap.find("key") != umap.end()) { /* ... */ }
  ```
  迭代器失效：插入元素可能导致 rehash，使所有迭代器失效。删除仅使被删除元素的迭代器失效。
  
自定义键必须提供哈希和相等比较，且哈希函数必须与相等比较保持一致：若 a == b，则 hash(a) == hash(b)。

浮点数作为键：避免直接使用，因浮点精度问题可能导致 == 判断异常
## 9. 总结
std::unordered_map 是高性能键值存储容器，适用于需要快速查找且不关心顺序的场景。通过自定义哈希函数和相等比较，可以灵活支持复杂键类型。合理使用 reserve 和负载因子控制可进一步提升性能。


