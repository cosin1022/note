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
