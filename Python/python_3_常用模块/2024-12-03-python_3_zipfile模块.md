---
category: Python
date: 2024-12-03 09:00:00 +0800
layout: post
title: python_3_zipfile模块
tag: Python
---
## python3 zipfile模块 详解

Python 的 `zipfile` 模块用于处理 ZIP 文件。它支持创建、读取、写入、添加和解压 ZIP 文件。以下是对 `zipfile` 模块的详细介绍和用法。

---

### 1. **基本用法**

#### 导入模块
```python
import zipfile
```

---

### 2. **创建 ZIP 文件**

#### 创建一个新 ZIP 文件并写入内容
- 模式选项：
  - `'w'`：创建一个新文件（如果文件已存在，将覆盖）。
  - `'a'`：添加到现有的 ZIP 文件中。

```python
with zipfile.ZipFile('example.zip', 'w') as zipf:
    zipf.write('file1.txt')  # 添加文件到 ZIP 中
    zipf.write('file2.txt', arcname='renamed_file2.txt')  # 指定压缩时的文件名
```

---

### 3. **读取 ZIP 文件**

#### 列出 ZIP 文件中的内容
```python
with zipfile.ZipFile('example.zip', 'r') as zipf:
    print(zipf.namelist())  # 列出压缩包内所有文件和目录
```

#### 读取文件信息
```python
with zipfile.ZipFile('example.zip', 'r') as zipf:
    for info in zipf.infolist():
        print(info.filename, info.file_size, info.compress_size)
```

#### 解压 ZIP 文件
- 解压到当前目录：
```python
with zipfile.ZipFile('example.zip', 'r') as zipf:
    zipf.extractall()  # 解压到当前目录
```

- 解压到指定目录：
```python
with zipfile.ZipFile('example.zip', 'r') as zipf:
    zipf.extractall('output_directory')  # 解压到指定目录
```

- 解压单个文件：
```python
with zipfile.ZipFile('example.zip', 'r') as zipf:
    zipf.extract('file1.txt', 'output_directory')  # 解压指定文件到目录
```

---

### 4. **追加文件到 ZIP**

```python
with zipfile.ZipFile('example.zip', 'a') as zipf:
    zipf.write('new_file.txt', arcname='folder/new_file.txt')  # 指定路径
```

---

### 5. **压缩和解压密码保护的 ZIP 文件**

#### 压缩带密码的 ZIP
Python 的 `zipfile` 本身不支持加密 ZIP 文件，但可以使用第三方库如 `pyminizip` 或 `zipfile_with_password`。

#### 解压带密码的 ZIP
```python
with zipfile.ZipFile('protected.zip', 'r') as zipf:
    zipf.extractall(pwd=b'password')  # 密码需要以字节形式提供
```

---

### 6. **检查文件是否为 ZIP**

```python
if zipfile.is_zipfile('example.zip'):
    print("是一个有效的 ZIP 文件")
else:
    print("不是有效的 ZIP 文件")
```

---

### 7. **高级操作**

#### 获取压缩信息
```python
with zipfile.ZipFile('example.zip', 'r') as zipf:
    info = zipf.getinfo('file1.txt')
    print(info.filename)
    print(info.compress_type)  # 压缩方式
    print(info.file_size)      # 原始大小
    print(info.compress_size)  # 压缩后大小
```

#### 自定义压缩级别
```python
import zipfile
import zlib

with zipfile.ZipFile('compressed.zip', 'w', compression=zipfile.ZIP_DEFLATED, compresslevel=9) as zipf:
    zipf.write('file1.txt')
```

---

### 8. **支持的压缩类型**
- `zipfile.ZIP_STORED`：不压缩。
- `zipfile.ZIP_DEFLATED`：标准压缩（需要 `zlib` 支持）。
- `zipfile.ZIP_BZIP2`：bzip2 压缩（需要 `bz2` 支持）。
- `zipfile.ZIP_LZMA`：LZMA 压缩（需要 `lzma` 支持）。

---

### 9. **完整示例：批量压缩文件**
```python
import zipfile
import os

def compress_directory(dir_path, output_file):
    with zipfile.ZipFile(output_file, 'w', compression=zipfile.ZIP_DEFLATED) as zipf:
        for foldername, subfolders, filenames in os.walk(dir_path):
            for filename in filenames:
                file_path = os.path.join(foldername, filename)
                arcname = os.path.relpath(file_path, dir_path)
                zipf.write(file_path, arcname)

compress_directory('my_folder', 'archive.zip')
```

---

### 10. **完整示例：批量解压 ZIP 文件**
```python
import zipfile
import os

def extract_all(zip_file, output_dir):
    with zipfile.ZipFile(zip_file, 'r') as zipf:
        zipf.extractall(output_dir)

extract_all('archive.zip', 'extracted_files')
```

希望这些内容能帮助你全面理解 `zipfile` 模块！如需更详细的实例或功能扩展，请告诉我。