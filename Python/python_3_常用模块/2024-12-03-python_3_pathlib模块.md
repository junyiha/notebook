---
category: Python
date: 2024-12-03 09:00:00 +0800
layout: post
title: python_3_pathlib模块
tag: Python
---
## python3 pathlib模块 详解

`pathlib` 是 Python 3.4 引入的标准库模块，用于处理文件和路径。它提供了面向对象的接口，简洁优雅，功能强大，是现代 Python 开发操作路径的推荐选择。

以下是对 `pathlib` 模块的详细介绍及用法：

---

## **1. 基本概念**

### 导入模块
```python
from pathlib import Path
```

`Path` 类是 `pathlib` 模块的核心。它抽象了不同操作系统文件路径的细节，支持跨平台操作。

---

## **2. 创建 `Path` 对象**

### 创建路径对象
```python
# 使用字符串路径
path = Path("/home/user/documents")

# 使用相对路径
relative_path = Path("documents/example.txt")
```

### 当前工作目录
```python
current_dir = Path.cwd()
print(current_dir)
```

### 当前脚本所在目录
```python
script_dir = Path(__file__).resolve().parent
print(script_dir)
```

---

## **3. 路径的基本操作**

### 拼接路径
使用 `/` 运算符拼接路径，比字符串操作更简洁。
```python
path = Path("/home/user") / "documents" / "example.txt"
print(path)  # 输出: /home/user/documents/example.txt
```

### 获取路径的父目录
```python
path = Path("/home/user/documents/example.txt")
print(path.parent)  # 输出: /home/user/documents
```

### 获取路径的各部分
```python
path = Path("/home/user/documents/example.txt")
print(path.name)       # 输出: example.txt (文件名)
print(path.stem)       # 输出: example (文件名去掉后缀)
print(path.suffix)     # 输出: .txt (文件后缀)
print(path.parts)      # 输出: ('/', 'home', 'user', 'documents', 'example.txt')
```

### 判断路径类型
```python
path = Path("/home/user/documents/example.txt")
print(path.is_file())  # 检查是否是文件
print(path.is_dir())   # 检查是否是目录
```

### 检查路径是否存在
```python
path = Path("/home/user/documents/example.txt")
print(path.exists())  # 检查路径是否存在
```

---

## **4. 遍历目录**

### 遍历当前目录的文件和子目录
```python
path = Path("/home/user/documents")
for item in path.iterdir():
    print(item)  # 输出目录下的文件和文件夹
```

### 递归遍历目录
使用 `glob()` 或 `rglob()` 查找符合模式的文件。
```python
path = Path("/home/user/documents")

# 查找当前目录下所有 .txt 文件
for txt_file in path.glob("*.txt"):
    print(txt_file)

# 递归查找所有 .txt 文件
for txt_file in path.rglob("*.txt"):
    print(txt_file)
```

---

## **5. 文件操作**

### 创建目录
```python
path = Path("/home/user/new_folder")
path.mkdir(parents=True, exist_ok=True)  # 创建目录，父目录不存在时自动创建
```

### 删除目录或文件
```python
# 删除文件
file_path = Path("/home/user/example.txt")
file_path.unlink()

# 删除空目录
dir_path = Path("/home/user/empty_folder")
dir_path.rmdir()
```

### 读取文件内容
```python
file_path = Path("/home/user/example.txt")
content = file_path.read_text(encoding="utf-8")
print(content)
```

### 写入文件内容
```python
file_path = Path("/home/user/example.txt")
file_path.write_text("Hello, World!", encoding="utf-8")
```

---

## **6. 路径比较**

`pathlib` 支持路径的比较操作。
```python
path1 = Path("/home/user/documents")
path2 = Path("/home/user/documents/example.txt")

print(path1 < path2)  # 比较路径字典序
```

---

## **7. 高级功能**

### 获取绝对路径
```python
path = Path("documents/example.txt")
print(path.resolve())  # 输出绝对路径
```

### 文件大小
```python
file_path = Path("/home/user/example.txt")
print(file_path.stat().st_size)  # 文件大小（字节）
```

### 修改时间、创建时间等信息
```python
file_path = Path("/home/user/example.txt")
info = file_path.stat()
print(info.st_mtime)  # 修改时间
print(info.st_ctime)  # 创建时间
```

---

## **8. 跨平台操作**

`pathlib` 自动适配不同的操作系统，无需担心路径分隔符问题。  
但如果需要明确使用 Windows 或 Posix 风格路径，可以使用 `PureWindowsPath` 或 `PurePosixPath`：
```python
from pathlib import PureWindowsPath, PurePosixPath

windows_path = PureWindowsPath("C:/Windows/System32")
posix_path = PurePosixPath("/home/user/documents")
```

---

## **9. 示例：批量重命名文件**

以下示例将目录下的所有 `.txt` 文件重命名为 `.bak` 文件：
```python
path = Path("/home/user/documents")

for file in path.glob("*.txt"):
    new_name = file.with_suffix(".bak")
    file.rename(new_name)
```

---

`pathlib` 模块非常强大且易于使用，大大简化了文件和路径操作。如需进一步扩展或解决特定问题，请告诉我！