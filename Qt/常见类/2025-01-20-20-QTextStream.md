---
category: Qt
date: 2025-01-20 09:00:00 +0800
layout: post
title: QTextStream
tag: Qt-class
---
## 摘要

+ QTextStream 类 相关学习笔记

<!--more-->

## Qt QTextStream类 是什么

### **QTextStream 类详解**

`QTextStream` 是 Qt 框架中的一个类，用于处理文本数据的输入和输出操作。它支持对文件、字符串以及标准输入/输出（如 `stdin`、`stdout` 和 `stderr`）进行流式读写操作。

`QTextStream` 是高层次的文本 I/O 类，特别适合处理文本文件的读取和写入、字符串操作以及终端交互。

---

## **核心特性**
1. **多种数据源**：支持文件、字符串和标准输入/输出。
2. **支持 Unicode**：可以处理多种编码格式的文本（默认 UTF-8）。
3. **灵活的格式化选项**：支持对数字、浮点数、字符串进行格式化操作。
4. **流式操作**：采用类似 C++ 标准流（如 `std::cout` 和 `std::cin`）的流式接口。
5. **跨平台支持**：在不同平台上行为一致。

---

## **常见用法**

### **1. 文件读写**
`QTextStream` 可直接与文件对象配合使用，用于读取和写入文本文件。

#### **示例：写入文件**
```cpp
#include <QFile>
#include <QTextStream>

int main() {
    QFile file("example.txt");
    if (file.open(QIODevice::WriteOnly | QIODevice::Text)) {
        QTextStream out(&file);  // 将 QTextStream 与文件关联
        out << "Hello, QTextStream!\n";
        out << "This is an example of writing to a file.\n";
    }
    file.close();
    return 0;
}
```

#### **示例：读取文件**
```cpp
#include <QFile>
#include <QTextStream>
#include <QDebug>

int main() {
    QFile file("example.txt");
    if (file.open(QIODevice::ReadOnly | QIODevice::Text)) {
        QTextStream in(&file);  // 将 QTextStream 与文件关联
        while (!in.atEnd()) {
            QString line = in.readLine();  // 按行读取
            qDebug() << line;
        }
    }
    file.close();
    return 0;
}
```

---

### **2. 标准输入/输出**
`QTextStream` 也可以与标准输入（`stdin`）、标准输出（`stdout`）和标准错误输出（`stderr`）关联，用于控制台应用程序。

#### **示例：从终端获取输入**
```cpp
#include <QTextStream>

int main() {
    QTextStream in(stdin);  // 关联标准输入
    QTextStream out(stdout); // 关联标准输出

    out << "请输入你的名字: " << flush;  // 输出提示
    QString name = in.readLine();  // 从终端读取一行输入
    out << "你好, " << name << "!" << endl;

    return 0;
}
```

---

### **3. 字符串操作**
`QTextStream` 可以与 `QString` 配合，用于处理字符串流。

#### **示例：操作字符串流**
```cpp
#include <QTextStream>

int main() {
    QString str;
    QTextStream stream(&str);  // 将 QTextStream 与 QString 关联

    stream << "Hello, " << 42 << "!" << Qt::endl;  // 写入数据到 QString
    stream << "Qt QTextStream example.";
    qDebug() << str;

    return 0;
}
```

输出：
```plaintext
"Hello, 42!\nQt QTextStream example."
```

---

## **核心方法**

### **构造函数**
```cpp
QTextStream();                                // 默认构造
QTextStream(QIODevice *device);               // 关联 QIODevice 对象
QTextStream(QString *string, QIODevice::OpenMode openMode = QIODevice::ReadWrite);
QTextStream(FILE *fileHandle, QIODevice::OpenMode openMode = QIODevice::ReadWrite);
```

### **设置数据源**
- **与设备关联**：
  ```cpp
  void setDevice(QIODevice *device);  // 设置数据源
  QIODevice *device() const;          // 获取当前关联的设备
  ```
- **与字符串关联**：
  ```cpp
  void setString(QString *string, QIODevice::OpenMode openMode = QIODevice::ReadWrite);
  QString *string() const;
  ```

### **文本读取**
- 按行读取：
  ```cpp
  QString readLine(qint64 maxlen = 0);
  ```
- 全部读取：
  ```cpp
  QString readAll();
  ```

### **文本写入**
- 写入数据：
  ```cpp
  QTextStream &operator<<(const QString &string);
  QTextStream &operator<<(int number);
  QTextStream &operator<<(double number);
  ```

### **格式设置**
- 设置字段宽度：
  ```cpp
  void setFieldWidth(int width);
  int fieldWidth() const;
  ```
- 设置对齐方式：
  ```cpp
  void setFieldAlignment(Qt::Alignment alignment);
  ```
- 设置小数位数：
  ```cpp
  void setRealNumberPrecision(int precision);
  ```

---

## **QTextStream 的格式化选项**

| 功能         | 方法                                       |
| ------------ | ------------------------------------------ |
| 设置进制     | `setIntegerBase(int base)`                 |
| 设置小数位数 | `setRealNumberPrecision(int precision)`    |
| 设置数字格式 | `setNumberFlags(QTextStream::NumberFlags)` |

示例：
```cpp
QTextStream out(stdout);
out.setFieldWidth(10);
out.setFieldAlignment(Qt::AlignRight);
out << "Name" << 42 << endl;
```

输出：
```plaintext
      Name        42
```

---

## **QTextStream 的优点**
1. **简洁易用**：提供了类似 C++ 标准流的接口，代码直观易读。
2. **Unicode 支持**：能够处理多种编码，适合国际化应用。
3. **与 Qt 生态系统无缝集成**：支持 `QString`、`QIODevice` 等 Qt 对象。

---

## **适用场景**
- 文本文件的读写操作。
- 控制台程序的用户交互（标准输入/输出）。
- 字符串流操作，例如动态生成文本内容。
- 格式化输出，如日志生成。

---

`QTextStream` 是处理文本数据的强大工具，特别适合需要处理 Unicode 数据、与 Qt 类集成的场景。在实际项目中，它可以极大地简化文本处理逻辑，同时提供跨平台一致性。

## Qt QTextStream类 详解

### **Qt QTextStream 类详解**

`QTextStream` 是 Qt 框架中一个强大的文本流处理类，提供了对文本数据的高层次读写功能。它可以用于读取和写入文件、字符串，以及与标准输入/输出流交互。它支持 Unicode，适合国际化应用，并提供了灵活的格式化功能。

---

## **基本概述**

- **头文件**：
  ```cpp
  #include <QTextStream>
  ```
- **功能定位**：
  - 操作文本文件（读取和写入）。
  - 操作字符串流。
  - 控制台输入输出（标准输入 `stdin`、标准输出 `stdout`、标准错误 `stderr`）。
  - 提供流式 I/O 接口，支持格式化输出。
- **特点**：
  - 支持 Unicode 编码。
  - 灵活的格式设置。
  - 与 Qt 的 `QString` 和 `QIODevice` 无缝集成。

---

## **核心功能**

### **数据源**
`QTextStream` 可以绑定到以下类型的数据源：
- **QIODevice**（例如 `QFile`、`QBuffer`）。
- **QString**。
- **C 风格的 `FILE*` 指针（如标准输入/输出）。**

---

## **QTextStream 的构造与析构**

### **构造函数**
```cpp
QTextStream();  // 默认构造函数
QTextStream(QIODevice *device);  // 绑定 QIODevice
QTextStream(QString *string, QIODevice::OpenMode openMode = QIODevice::ReadWrite); // 绑定 QString
QTextStream(FILE *fileHandle, QIODevice::OpenMode openMode = QIODevice::ReadWrite); // 绑定 FILE*
```

### **析构函数**
```cpp
~QTextStream();
```

---

## **数据源绑定**
- 设置或获取数据源：
  ```cpp
  void setDevice(QIODevice *device);  // 设置数据源
  QIODevice *device() const;          // 获取当前数据源
  ```
- 绑定字符串：
  ```cpp
  void setString(QString *string, QIODevice::OpenMode openMode = QIODevice::ReadWrite);
  QString *string() const;
  ```

---

## **数据读写方法**

### **读取数据**
- **按行读取**：
  ```cpp
  QString readLine(qint64 maxlen = 0); // 读取一行，最多 maxlen 字符
  ```
- **读取所有数据**：
  ```cpp
  QString readAll();  // 读取到流末尾
  ```

### **写入数据**
- 写入字符串：
  ```cpp
  QTextStream &operator<<(const QString &string);
  ```
- 写入数字：
  ```cpp
  QTextStream &operator<<(int number);
  QTextStream &operator<<(double number);
  ```

---

## **格式化选项**

### **整数进制**
- 设置进制：
  ```cpp
  void setIntegerBase(int base); // 例如 10（十进制）、16（十六进制）
  ```

### **浮点数格式**
- 设置小数位数：
  ```cpp
  void setRealNumberPrecision(int precision);
  ```
- 设置格式（科学计数法、固定小数点等）：
  ```cpp
  void setRealNumberNotation(QTextStream::RealNumberNotation notation);
  ```

### **对齐和宽度**
- 设置字段宽度：
  ```cpp
  void setFieldWidth(int width);
  ```
- 设置对齐方式：
  ```cpp
  void setFieldAlignment(Qt::Alignment alignment);
  ```

---

## **示例代码**

### **1. 文件操作**
#### **写入文本文件**
```cpp
#include <QFile>
#include <QTextStream>

int main() {
    QFile file("example.txt");
    if (file.open(QIODevice::WriteOnly | QIODevice::Text)) {
        QTextStream out(&file);
        out << "Hello, QTextStream!" << Qt::endl;
        out << "This is a test file." << Qt::endl;
    }
    file.close();
    return 0;
}
```

#### **读取文本文件**
```cpp
#include <QFile>
#include <QTextStream>
#include <QDebug>

int main() {
    QFile file("example.txt");
    if (file.open(QIODevice::ReadOnly | QIODevice::Text)) {
        QTextStream in(&file);
        while (!in.atEnd()) {
            QString line = in.readLine();
            qDebug() << line;
        }
    }
    file.close();
    return 0;
}
```

---

### **2. 标准输入输出**
```cpp
#include <QTextStream>

int main() {
    QTextStream in(stdin);
    QTextStream out(stdout);

    out << "请输入你的名字: " << flush;
    QString name = in.readLine();
    out << "你好, " << name << "!" << Qt::endl;

    return 0;
}
```

---

### **3. 字符串操作**
```cpp
#include <QTextStream>
#include <QString>
#include <QDebug>

int main() {
    QString str;
    QTextStream stream(&str);

    stream << "This is a QTextStream example." << Qt::endl;
    stream << "You can write multiple lines.";
    qDebug() << str;

    return 0;
}
```

输出：
```plaintext
"This is a QTextStream example.\nYou can write multiple lines."
```

---

## **常见场景与优势**

### **典型应用**
1. **文件读写**：文本文件的读写是 `QTextStream` 的主要用途。
2. **控制台输入输出**：特别适合构建终端应用程序。
3. **字符串流操作**：生成复杂的文本内容或模板。

### **优点**
- **跨平台一致性**：同样的代码可以在不同平台上工作。
- **Unicode 支持**：支持多语言文本处理。
- **灵活性**：支持多种格式设置。

---

## **注意事项**
1. **字符编码**：
   - `QTextStream` 默认使用 UTF-8 编码。
   - 可以通过 `setCodec()` 设置不同的字符编码，例如：
     ```cpp
     out.setCodec("UTF-8");
     ```
2. **缓冲**：
   - `QTextStream` 是缓冲的，如果需要立即刷新输出，可以使用 `flush()`。

---

### **总结**
`QTextStream` 是 Qt 中一个强大且易用的文本流处理工具。无论是文件操作、终端交互还是字符串操作，它都提供了简洁高效的接口和灵活的功能，非常适合需要处理文本数据的应用场景。