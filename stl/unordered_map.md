# std::unordered_map 用法详解
`std::unordered_map` 是 C++ 标准模板库中的无序关联容器，基于哈希表实现，存储键值对，键唯一，平均时间复杂度为 O(1)。
---
## 1. 头文件与基本定义
```cpp
#include <unordered_map>
// 命名空间
using std::unordered_map;
```
类模板：template <class Key, class T, class Hash = std::hash<Key>, class KeyEqual = std::equal_to<Key>, class Allocator = std::allocator<std::pair<const Key, T>>> class unordered_map;
元素是 std::pair<const Key, T>，键不可修改。
内部使用桶（bucket）组织，负载因子控制 rehash。

