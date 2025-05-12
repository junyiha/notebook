---
category: Cpp
date: 2025-02-23 09:00:00 +0800
layout: post
title: C++17中<filesystem>标准库
tag: CppSTL
---
## 简介

+  C++17中<filesystem>标准库相关学习笔记

<!--more-->

## C++17 <filesystem>标准库 详细解释

C++17 引入的 `<filesystem>` 标准库提供了一套跨平台的文件系统操作接口，基于 Boost.Filesystem 实现。它简化了文件和目录的常见操作（如路径处理、遍历、复制、删除等），并支持跨平台兼容性。以下是详细解释：

---

### **1. 头文件与命名空间**
```cpp
#include <filesystem>
namespace fs = std::filesystem; // 常用别名简化代码
```

---

### **2. 核心类**
#### **`path` 类**
- **功能**：表示文件系统路径，自动处理平台差异（如 `/` vs `\`）。
- **关键操作**：
  ```cpp
  fs::path p = "dir/file.txt";
  p /= "subdir";             // 路径拼接：dir/file.txt/subdir
  p.filename();              // 获取文件名（subdir）
  p.extension();             // 获取扩展名（如 .txt）
  p.parent_path();           // 父路径（dir/file.txt）
  p.is_absolute();           // 是否为绝对路径
  p.string(), p.wstring()    // 转换为字符串（平台相关编码）
  ```

#### **`directory_entry`**
- 表示目录中的一个条目（文件/子目录），可快速访问属性：
  ```cpp
  fs::directory_entry entry("file.txt");
  if (entry.is_regular_file()) {
    std::cout << entry.file_size() << " bytes";
  }
  ```

#### **目录迭代器**
- **`directory_iterator`**：遍历目录中的条目（非递归）。
- **`recursive_directory_iterator`**：递归遍历目录树。
  ```cpp
  for (auto& entry : fs::directory_iterator("dir")) {
    std::cout << entry.path() << std::endl;
  }
  ```

#### **文件状态**
- **`file_status`**：表示文件类型和权限，通过 `status(path)` 获取。
- **文件类型检查函数**：
  ```cpp
  is_regular_file(p), is_directory(p), is_symlink(p), is_empty(p)
  ```

#### **`space_info`**
- 获取磁盘空间信息：
  ```cpp
  fs::space_info si = fs::space("/");
  std::cout << "Free space: " << si.free / (1024*1024) << " MB";
  ```

---

### **3. 常用函数**
#### **路径操作**
- `absolute(p)`: 获取绝对路径。
- `canonical(p)`: 获取规范化绝对路径（解析符号链接）。
- `relative(p, base)`: 计算相对于 `base` 的路径。
- `current_path()`: 获取/设置当前工作目录。

#### **文件操作**
- **复制/移动/删除**：
  ```cpp
  copy("src.txt", "dst.txt", fs::copy_options::overwrite_existing);
  rename("old.txt", "new.txt");
  remove("file.txt");                   // 删除文件或空目录
  remove_all("dir");                    // 递归删除目录
  ```
- **创建目录**：
  ```cpp
  create_directory("new_dir");          // 创建单级目录
  create_directories("a/b/c");          // 递归创建多级目录
  ```

#### **属性查询**
- `exists(p)`: 路径是否存在。
- `file_size(p)`: 文件大小（字节）。
- `last_write_time(p)`: 最后修改时间。
- `equivalent(p1, p2)`: 两个路径是否指向同一文件。

---

### **4. 错误处理**
- **异常类** `filesystem_error`：
  ```cpp
  try {
    fs::copy("nonexistent.txt", "dst.txt");
  } catch (const fs::filesystem_error& e) {
    std::cerr << "Error: " << e.what() << "\nPath: " << e.path1();
  }
  ```
- **错误选项**：可通过参数禁用异常，返回错误码：
  ```cpp
  std::error_code ec;
  fs::remove("file.txt", ec);
  if (ec) { /* 处理错误 */ }
  ```

---

### **5. 示例代码**
#### 遍历目录树
```cpp
void list_files(const fs::path& dir) {
  for (const auto& entry : fs::recursive_directory_iterator(dir)) {
    if (entry.is_regular_file()) {
      std::cout << entry.path() << " Size: " << entry.file_size() << "\n";
    }
  }
}
```

#### 复制目录
```cpp
fs::copy("source_dir", "target_dir", 
         fs::copy_options::recursive | 
         fs::copy_options::overwrite_existing);
```

#### 获取文件信息
```cpp
fs::path p = "data/config.json";
if (fs::exists(p)) {
  auto ftime = fs::last_write_time(p);
  std::time_t cftime = decltype(ftime)::clock::to_time_t(ftime);
  std::cout << "Last modified: " << std::asctime(std::localtime(&cftime));
}
```

---

### **6. 注意事项**
1. **跨平台路径**：`path` 类自动处理路径分隔符，但避免硬编码 `/` 或 `\`。
2. **符号链接**：需注意 `canonical` 和 `read_symlink` 的行为。
3. **权限问题**：某些操作（如删除）可能需要管理员权限。
4. **编译器支持**：确保编译器启用 C++17（如 GCC 8+、Clang 7+、MSVC 2017+）。

---

通过 `<filesystem>` 库，开发者可以编写简洁、跨平台的文件系统操作代码，无需依赖平台特定 API（如 Win32 或 POSIX）。

## C++17 <filesystem>标准库 详细解释

### **C++17 `<filesystem>` 标准库详解**

C++17 引入了 `<filesystem>` 标准库，使得处理文件和目录变得更加直观和跨平台。`std::filesystem` 提供了一系列功能来操作文件系统，包括路径操作、目录遍历、文件属性查询、文件读写等。

---

## **1. 引入 `<filesystem>`**
```cpp
#include <iostream>
#include <filesystem>

namespace fs = std::filesystem;

int main() {
    fs::path p = "/home/user/test.txt";
    std::cout << "Path: " << p << "\n";
    return 0;
}
```
- 需要 `#include <filesystem>` 头文件。
- 命名空间 `std::filesystem` 可以使用 `namespace fs = std::filesystem;` 来简化调用。

---

## **2. `std::filesystem::path` - 路径处理**
`std::filesystem::path` 是 `std::filesystem` 的核心类型，用于表示文件或目录路径。

### **(1) 创建路径**
```cpp
fs::path p1("/home/user/file.txt");  // UNIX/Linux 风格
fs::path p2("C:\\Users\\User\\file.txt"); // Windows 风格
fs::path p3 = fs::current_path(); // 获取当前路径
```

### **(2) 访问路径信息**
```cpp
fs::path p("/home/user/test.txt");

std::cout << "Path: " << p << "\n";
std::cout << "Filename: " << p.filename() << "\n";  // test.txt
std::cout << "Extension: " << p.extension() << "\n"; // .txt
std::cout << "Parent Path: " << p.parent_path() << "\n"; // /home/user
std::cout << "Root Name: " << p.root_name() << "\n";  // Linux为空
std::cout << "Root Directory: " << p.root_directory() << "\n";  // "/"
std::cout << "Stem: " << p.stem() << "\n"; // test（无扩展名）
```

### **(3) 拼接路径**
```cpp
fs::path dir("/home/user");
fs::path file("test.txt");

fs::path fullPath = dir / file;  // "/home/user/test.txt"
std::cout << "Full Path: " << fullPath << "\n";
```

### **(4) 变换路径**
```cpp
fs::path p = "/home/user/test.txt";
p.replace_filename("newfile.txt"); // 变为 "/home/user/newfile.txt"
p.replace_extension(".cpp"); // 变为 "/home/user/newfile.cpp"
```

---

## **3. 文件与目录操作**

### **(1) 判断文件或目录是否存在**
```cpp
if (fs::exists("/home/user/test.txt")) {
    std::cout << "File exists.\n";
}
```

### **(2) 判断是文件还是目录**
```cpp
fs::path p("/home/user/test.txt");

if (fs::is_regular_file(p)) {
    std::cout << "It's a file.\n";
} else if (fs::is_directory(p)) {
    std::cout << "It's a directory.\n";
}
```

### **(3) 获取文件大小**
```cpp
fs::path p("/home/user/test.txt");

if (fs::exists(p) && fs::is_regular_file(p)) {
    std::cout << "File size: " << fs::file_size(p) << " bytes\n";
}
```

### **(4) 创建目录**
```cpp
fs::create_directory("new_dir");  // 创建单层目录
fs::create_directories("parent_dir/child_dir"); // 递归创建
```

### **(5) 删除文件或目录**
```cpp
fs::remove("test.txt"); // 删除文件
fs::remove_all("directory"); // 递归删除目录及内容
```

---

## **4. 目录遍历**
### **(1) 遍历目录 (非递归)**
```cpp
for (const auto& entry : fs::directory_iterator("/home/user")) {
    std::cout << entry.path() << "\n";
}
```

### **(2) 递归遍历目录**
```cpp
for (const auto& entry : fs::recursive_directory_iterator("/home/user")) {
    std::cout << entry.path() << "\n";
}
```

---

## **5. 文件权限**
### **(1) 获取文件权限**
```cpp
fs::path p("/home/user/test.txt");
fs::perms permissions = fs::status(p).permissions();

if ((permissions & fs::perms::owner_read) != fs::perms::none) {
    std::cout << "Owner can read.\n";
}
```

### **(2) 修改文件权限**
```cpp
fs::permissions("/home/user/test.txt",
    fs::perms::owner_read | fs::perms::owner_write,
    fs::perm_options::replace);
```

---

## **6. 其他操作**
### **(1) 复制文件**
```cpp
fs::copy("source.txt", "destination.txt", fs::copy_options::overwrite_existing);
```

### **(2) 重命名/移动文件**
```cpp
fs::rename("old_name.txt", "new_name.txt");
```

### **(3) 硬链接与符号链接**
```cpp
fs::create_hard_link("existing.txt", "hardlink.txt");
fs::create_symlink("existing.txt", "symlink.txt");
```

---

## **7. 完整示例**
```cpp
#include <iostream>
#include <filesystem>

namespace fs = std::filesystem;

int main() {
    fs::path p = "example.txt";

    if (!fs::exists(p)) {
        std::cout << "File does not exist.\n";
        return 1;
    }

    std::cout << "Path: " << p << "\n";
    std::cout << "Filename: " << p.filename() << "\n";
    std::cout << "Extension: " << p.extension() << "\n";
    std::cout << "Parent Path: " << p.parent_path() << "\n";

    if (fs::is_regular_file(p)) {
        std::cout << "File size: " << fs::file_size(p) << " bytes\n";
    }

    return 0;
}
```

---

## **8. 注意事项**
1. **需要 C++17 或更新版本**，编译时添加 `-std=c++17` 或 `-std=c++20`。
2. **Windows 可能需要额外的链接库**：
   - 使用 MinGW 需要 `-lstdc++fs`
   - MSVC 默认支持，无需额外链接
3. **跨平台兼容性**：
   - Windows 和 Linux 处理路径的方式不同，`std::filesystem` 已经适配，但路径格式需注意。

---

## **9. 总结**
`<filesystem>` 提供了现代 C++ 处理文件和目录的强大能力，让文件操作变得更加安全、高效、可读。推荐在 C++17 及以上版本中使用 `std::filesystem` 替代传统 C 语言的 `stdio.h`、`dirent.h` 方式来操作文件系统。

你想实现具体的文件管理功能吗？可以告诉我你的需求，我可以帮你编写代码示例！ 🚀