---
category: Tools
date: 2024-12-11 09:00:00 +0800
layout: post
title: Clang-Format
tag: Compiler
---
## clang-format 是什么

**`clang-format`** 是一款由 LLVM 项目开发的代码格式化工具，用于自动格式化 C、C++、JavaScript、Objective-C、C# 等多种语言的源代码。它是开发者用于统一代码风格的高效工具，尤其适用于大型团队和项目。

---

### **功能特点**
1. **自动化代码格式化**  
   `clang-format` 根据指定的格式规则（如缩进、空格、括号对齐）对代码进行调整，无需手动修改。

2. **支持多种配置**  
   使用 `.clang-format` 配置文件或命令行选项自定义规则。支持的规则包括：
   - 缩进宽度
   - 括号的换行位置
   - 操作符的对齐方式

3. **支持集成开发环境 (IDE)**  
   - 可以与主流 IDE（如 Visual Studio、VS Code、CLion 等）集成，提供即时格式化功能。
   - 常用于 Git hook 自动格式化提交代码。

4. **格式化范围灵活**  
   - 可以格式化整个文件、指定的行范围，或者通过标准输入指定内容。

5. **与版本控制配合**  
   可以使用 `git clang-format` 格式化特定的代码变更。

---

### **使用方法**
- **格式化文件：**
  ```bash
  clang-format -i <文件名>
  ```
  `-i` 参数表示直接修改文件。

- **格式化部分代码：**
  ```bash
  clang-format < 文件名 > 输出文件名
  ```

- **指定规则文件：**
  使用 `.clang-format` 文件指定项目的格式化规则：
  ```yaml
  BasedOnStyle: Google
  IndentWidth: 4
  ColumnLimit: 80
  ```

---

### **应用场景**
1. **代码风格统一**：在团队开发中避免因代码风格不一致导致的冲突。
2. **代码质量改进**：提升代码可读性，减少代码审查的时间。
3. **快速代码格式调整**：尤其适用于已有遗留代码的大规模格式化。

---

更多信息可参考：  
- [LLVM 官方文档](https://clang.llvm.org/docs/ClangFormat.html)  
- [GitHub 项目页面](https://github.com/llvm/llvm-project/tree/main/clang/tools/clang-format)

## clang-format 常用参数 详解

`clang-format` 是一个强大的代码格式化工具，支持丰富的配置选项，以满足不同团队或项目的代码风格要求。以下是其常用参数及配置选项的详解：

---

### **命令行常用参数**
1. **基本操作**
   - `-i`: 直接修改原文件（in-place）。
     ```bash
     clang-format -i example.cpp
     ```
   - `-style=<style>`: 指定格式风格，可以使用内置样式（如 `LLVM`, `Google`, `Chromium`）或自定义样式文件。
     ```bash
     clang-format -style=Google example.cpp
     ```

2. **格式化范围**
   - `-lines=<start>:<end>`: 格式化指定行的代码。
     ```bash
     clang-format -lines=5:10 example.cpp
     ```
   - `-offset=<offset> -length=<length>`: 格式化指定偏移和长度的代码。

3. **标准输入/输出**
   - `-`: 从标准输入读取代码，格式化后输出到标准输出。
     ```bash
     clang-format - < example.cpp > formatted.cpp
     ```

4. **其他参数**
   - `-dump-config`: 导出当前的 `.clang-format` 配置。
     ```bash
     clang-format -dump-config > .clang-format
     ```
   - `-assume-filename=<filename>`: 假定文件名以便确定语言类型（如 `.cpp`, `.js`）。

---

### **`.clang-format` 配置文件常用选项**
`.clang-format` 是一个 YAML 格式的文件，用于定义代码格式化规则。以下是常用选项：

#### **1. 基础选项**
- `BasedOnStyle`: 指定基础风格（`LLVM`, `Google`, `Chromium`, `Mozilla` 等）。
  ```yaml
  BasedOnStyle: Google
  ```

- `Language`: 指定语言（`Cpp`, `Java`, `JavaScript` 等）。

#### **2. 缩进与对齐**
- `IndentWidth`: 缩进的空格数。
  ```yaml
  IndentWidth: 4
  ```

- `UseTab`: 是否使用 Tab 替代空格（`Never`, `ForIndentation`, `Always`）。

- `AlignConsecutiveAssignments`: 对齐连续的赋值语句。
  ```yaml
  AlignConsecutiveAssignments: true
  ```

#### **3. 换行规则**
- `ColumnLimit`: 每行代码的最大字符数。
  ```yaml
  ColumnLimit: 80
  ```

- `BreakBeforeBraces`: 括号换行规则（`Attach`, `Linux`, `Allman`, `Stroustrup` 等）。

#### **4. 空格规则**
- `SpaceBeforeParens`: 函数声明和调用括号前是否加空格。
  ```yaml
  SpaceBeforeParens: Always
  ```

- `SpacesInParentheses`: 括号内是否添加空格。

#### **5. 注释规则**
- `SortIncludes`: 是否排序 include 语句。
- `ReflowComments`: 自动调整注释的行宽。

---

### **使用示例**
1. 格式化整个项目代码：
   ```bash
   find . -name "*.cpp" -o -name "*.h" | xargs clang-format -i
   ```

2. 配合 Git，仅格式化改动的代码：
   ```bash
   git clang-format
   ```

3. 导出当前项目的默认风格配置：
   ```bash
   clang-format -dump-config > .clang-format
   ```

---

### **更多参考资料**
- [Clang-Format 官方文档](https://clang.llvm.org/docs/ClangFormat.html)
- [Clang-Format 配置样例](https://zed0.co.uk/clang-format-configurator/)  

如果需要帮助配置 `.clang-format`，请提供具体需求！