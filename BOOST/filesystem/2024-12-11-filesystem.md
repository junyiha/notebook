---
category: BOOST
date: 2024-12-11 09:00:00 +0800
layout: post
title: boost/filesystem概述
tag: boost
---
## boost/filesystem 详解

**Boost.Filesystem** 是 Boost 库中的一个模块，用于处理文件和目录操作。它提供了跨平台的接口，支持文件路径操作、文件系统查询、文件操作（如创建、删除、复制等），是开发跨平台文件管理工具的强大工具。

---

### **Boost.Filesystem 的主要功能**
1. **文件路径操作**：
   - 支持创建、解析、合并和操作文件路径。
   - 提供跨平台的路径表示方式，兼容 Windows 和 POSIX 系统。

2. **文件和目录操作**：
   - 创建、删除、重命名文件或目录。
   - 检查文件是否存在，以及其类型（文件、目录、符号链接等）。

3. **文件系统遍历**：
   - 提供目录遍历功能，可递归列出文件和子目录。

4. **文件属性查询**：
   - 查询文件大小、修改时间、权限等元数据。

5. **跨平台支持**：
   - 通过统一接口屏蔽了不同平台文件系统之间的差异。

---

### **核心类和函数**

#### **1. `boost::filesystem::path`**
表示文件或目录路径的类。

- 构造路径：
  ```cpp
  boost::filesystem::path p("C:/example/file.txt");
  std::cout << "Filename: " << p.filename() << std::endl;
  ```
- 常用方法：
  - `filename()`：返回路径的文件名部分。
  - `parent_path()`：返回父目录路径。
  - `extension()`：返回文件扩展名。
  - `replace_extension()`：替换文件扩展名。

---

#### **2. `boost::filesystem::file_status`**
表示文件的状态信息。

- 常用方法：
  - `exists()`：检查文件是否存在。
  - `is_regular_file()`：检查是否为常规文件。
  - `is_directory()`：检查是否为目录。

---

#### **3. 文件和目录操作函数**
- **文件操作**：
  - `boost::filesystem::copy_file()`：复制文件。
  - `boost::filesystem::remove()`：删除文件或目录。
  - `boost::filesystem::rename()`：重命名文件或目录。

- **目录操作**：
  - `boost::filesystem::create_directory()`：创建目录。
  - `boost::filesystem::remove_all()`：递归删除目录及其内容。

---

#### **4. 目录遍历**
使用 `directory_iterator` 和 `recursive_directory_iterator` 遍历目录内容。

```cpp
#include <boost/filesystem.hpp>
#include <iostream>

int main() {
    boost::filesystem::path dir("C:/example");
    for (auto& entry : boost::filesystem::directory_iterator(dir)) {
        std::cout << entry.path().string() << std::endl;
    }
    return 0;
}
```

---

#### **5. 文件属性查询**
- `boost::filesystem::file_size()`：获取文件大小。
- `boost::filesystem::last_write_time()`：获取文件最后修改时间。
- `boost::filesystem::permissions()`：查询和设置文件权限。

---

### **示例代码**

#### **文件和目录操作**
```cpp
#include <boost/filesystem.hpp>
#include <iostream>

namespace fs = boost::filesystem;

int main() {
    fs::path dir("example_dir");

    // 创建目录
    if (!fs::exists(dir)) {
        fs::create_directory(dir);
        std::cout << "Directory created: " << dir.string() << std::endl;
    }

    // 创建文件路径
    fs::path file = dir / "example.txt";

    // 创建文件
    std::ofstream(file.string()) << "Hello, Boost.Filesystem!";
    std::cout << "File created: " << file.string() << std::endl;

    // 删除文件和目录
    fs::remove(file);
    fs::remove(dir);
    std::cout << "Cleanup completed." << std::endl;

    return 0;
}
```

#### **遍历目录**
```cpp
#include <boost/filesystem.hpp>
#include <iostream>

namespace fs = boost::filesystem;

int main() {
    fs::path dir("C:/example");

    if (fs::exists(dir) && fs::is_directory(dir)) {
        for (auto& entry : fs::recursive_directory_iterator(dir)) {
            std::cout << entry.path().string() << std::endl;
        }
    } else {
        std::cout << "Directory does not exist." << std::endl;
    }

    return 0;
}
```

---

### **Boost.Filesystem 的优势**
1. **跨平台一致性**：统一的 API 屏蔽了底层平台差异。
2. **丰富的功能**：从路径操作到文件元数据查询应有尽有。
3. **与标准库兼容**：部分功能被 C++17 的 `std::filesystem` 所采纳。

---

### **Boost.Filesystem 与 C++ 标准库**
- Boost.Filesystem 是 `std::filesystem` 的前身，其大部分功能已纳入 C++17 标准。
- 如果需要支持更老版本的 C++ 或使用扩展功能，Boost.Filesystem 仍是一个强大的工具。

---

### **相关资源**
- [Boost.Filesystem 官方文档](https://www.boost.org/doc/libs/release/libs/filesystem/)
- [C++ std::filesystem 文档](https://en.cppreference.com/w/cpp/filesystem)  

如果需要更详细的示例或进一步的解释，可以继续讨论！