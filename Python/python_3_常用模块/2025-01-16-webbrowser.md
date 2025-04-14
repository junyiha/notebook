---
category: Python
date: 2025-01-16 09:00:00 +0800
layout: post
title: webbrowser
tag: PythonModule
---
## 摘要

+ webbrowser 模块 学习笔记
+ webbrowser模块提供了一个高层级接口，允许向用户显示基于web的文档。在大多数情况下，只需要调用此模块的open()函数就可以了。

<!--more-->

## python webbrowser模块 详解

Python 的 **`webbrowser`** 模块用于在默认或指定的浏览器中打开 URL。它提供了一种简单的方式调用系统中的 Web 浏览器，适合实现自动化、展示网页、或执行 Web 应用相关的任务。

---

## **导入方式**
```python
import webbrowser
```

---

## **常用函数**

### 1. **`webbrowser.open(url, new=0, autoraise=True)`**
打开指定的 URL。

- **参数说明**:
  - `url`：要打开的网页地址（可以是 `http://`、`https://` 或文件路径）。
  - `new`：
    - `0`：在同一窗口或标签页中打开（默认）。
    - `1`：在新窗口中打开。
    - `2`：在新标签页中打开。
  - `autoraise`：
    - `True`：将浏览器窗口置于前台（默认）。
    - `False`：不将窗口置于前台。

- **示例**:
  ```python
  webbrowser.open("https://www.google.com")  # 默认行为
  webbrowser.open("https://www.google.com", new=1)  # 新窗口
  webbrowser.open("https://www.google.com", new=2)  # 新标签页
  ```

---

### 2. **`webbrowser.open_new(url)`**
在新窗口中打开指定 URL。

- **示例**:
  ```python
  webbrowser.open_new("https://www.python.org")
  ```

---

### 3. **`webbrowser.open_new_tab(url)`**
在新标签页中打开指定 URL。

- **示例**:
  ```python
  webbrowser.open_new_tab("https://www.python.org")
  ```

---

## **浏览器控制类**

### **获取浏览器实例**
使用 `webbrowser.get()` 函数可以获取指定的浏览器对象。如果未指定，将返回默认浏览器。

- **`webbrowser.get(using=None)`**
  - `using`：指定浏览器的名称（如 `"firefox"`, `"chrome"` 等）。
  - 如果浏览器名称为空或未找到，将使用系统默认浏览器。

#### 示例：
```python
browser = webbrowser.get("firefox")  # 获取 Firefox 浏览器
browser.open("https://www.mozilla.org")  # 使用该浏览器打开链接
```

---

### **注册自定义浏览器**
如果系统中未注册特定浏览器，可以通过 `webbrowser.register` 方法手动添加。

- **语法**:
  ```python
  webbrowser.register(name, constructor, instance=None, preferred=False)
  ```
  - `name`：浏览器名称。
  - `constructor`：用于创建浏览器对象的函数。
  - `instance`：预先创建的浏览器对象（可选）。
  - `preferred`：是否将该浏览器设置为默认。

- **示例**:
  ```python
  import webbrowser
  webbrowser.register("firefox", None, webbrowser.BackgroundBrowser("/path/to/firefox"))
  browser = webbrowser.get("firefox")
  browser.open("https://www.google.com")
  ```

---

## **支持的浏览器**

常见的浏览器名称：
- `"mozilla"`：Mozilla 系列浏览器。
- `"firefox"`：Firefox。
- `"chrome"`：Google Chrome。
- `"safari"`：Safari。
- `"edge"`：Microsoft Edge。
- `"opera"`：Opera。
- `"windows-default"`：Windows 默认浏览器。

---

## **查看默认浏览器**
可以通过调用以下代码查看系统的默认浏览器：
```python
import webbrowser

default_browser = webbrowser.get()
print(default_browser)
```

---

## **异步打开网页**
如果需要并行运行，可以结合 `threading` 模块异步打开网页：
```python
import webbrowser
import threading

def open_url(url):
    webbrowser.open(url)

thread = threading.Thread(target=open_url, args=("https://www.example.com",))
thread.start()
```

---

## **总结**

`webbrowser` 模块的优点是跨平台、简单易用。以下是主要使用场景：
- 打开指定的网页或文件（如 `.html` 文件）。
- 脚本化自动打开多个 URL。
- 结合 GUI 应用程序，用于触发链接。

对于更复杂的 Web 自动化，可以结合 `selenium` 等工具实现。