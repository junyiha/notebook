---
category: Qt
date: 2025-01-14 09:00:00 +0800
layout: post
title: QJsonParseError
tag: Qt-class
---
## 摘要

+ QJsonParseError 类 相关学习笔记

<!--more-->

## QJsonParseError 类 详解

`QJsonParseError` 是 Qt 中的一个类，用于表示在解析 JSON 文本时可能发生的错误。通过使用该类，可以更好地诊断 JSON 数据的格式问题和错误的具体位置。

以下是对 `QJsonParseError` 类的详解：

---

## **1. 类概述**

`QJsonParseError` 通常与 `QJsonDocument::fromJson` 方法配合使用。解析 JSON 数据时，如果发生错误，会通过 `QJsonParseError` 对象提供错误类型和发生错误的位置。

---

## **2. 常量和数据成员**

### **2.1 枚举类型：`QJsonParseError::ParseError`**

此枚举定义了 JSON 解析中可能遇到的错误类型：

| 枚举值                  | 描述                                                |
| ----------------------- | --------------------------------------------------- |
| `NoError`               | 没有错误，解析成功。                                |
| `UnterminatedObject`    | JSON 对象没有正确结束。                             |
| `MissingNameSeparator`  | 属性名称和值之间缺少冒号（`:`）。                   |
| `UnterminatedArray`     | JSON 数组没有正确结束。                             |
| `MissingValueSeparator` | 数组元素之间缺少逗号（`,`）。                       |
| `IllegalValue`          | JSON 值不合法，例如未被引号包围的字符串或无效的值。 |
| `TerminationByNumber`   | 数字后出现非法字符。                                |
| `IllegalNumber`         | 数字格式不合法，例如多余的点号或无效的指数表示法。  |
| `IllegalEscapeSequence` | 字符串中存在无效的转义序列，例如 `\x`。             |
| `IllegalUTF8String`     | 字符串中包含非法的 UTF-8 数据。                     |
| `UnterminatedString`    | 字符串没有正确结束，例如缺少右引号。                |
| `MissingObject`         | JSON 文本缺少 JSON 对象。                           |
| `DeepNesting`           | JSON 文本嵌套层次过深，超过 Qt 的支持限制。         |
| `DocumentTooLarge`      | JSON 文档太大，超过了可解析的最大尺寸。             |
| `GarbageAtEnd`          | JSON 文档结束后存在多余的无效数据。                 |

---

### **2.2 数据成员**

#### **`error`**
- 类型：`QJsonParseError::ParseError`
- 描述：存储错误类型。如果没有错误，值为 `NoError`。

#### **`offset`**
- 类型：`qint64`
- 描述：错误发生的位置（以字节为单位），从 JSON 文本的开头计算。

---

## **3. 方法**

`QJsonParseError` 类本身是一个简单的数据结构，没有成员函数。它的主要作用是提供错误信息，通常作为参数从其他函数返回。

---

## **4. 示例代码**

### **4.1 基本用法**

```cpp
#include <QJsonDocument>
#include <QJsonParseError>
#include <QDebug>

int main() {
    // 一个包含错误的 JSON 字符串
    QByteArray jsonString = R"({"name": "Alice", "age": 25, "isStudent": true,)";

    // 解析 JSON 文本
    QJsonParseError parseError;
    QJsonDocument jsonDoc = QJsonDocument::fromJson(jsonString, &parseError);

    // 检查解析结果
    if (parseError.error != QJsonParseError::NoError) {
        qDebug() << "JSON parse error:";
        qDebug() << "Error type:" << parseError.error;
        qDebug() << "Error offset:" << parseError.offset;
        return -1;
    }

    qDebug() << "JSON parsed successfully.";
    return 0;
}
```

**输出：**
```
JSON parse error:
Error type: 4
Error offset: 37
```

### **4.2 错误类型对应的含义**

```cpp
QJsonParseError::ParseError errorType = parseError.error;
switch (errorType) {
    case QJsonParseError::NoError:
        qDebug() << "No errors detected.";
        break;
    case QJsonParseError::UnterminatedObject:
        qDebug() << "Unterminated JSON object.";
        break;
    case QJsonParseError::UnterminatedArray:
        qDebug() << "Unterminated JSON array.";
        break;
    // 其他错误类型...
    default:
        qDebug() << "Other error:" << errorType;
}
```

---

## **5. 示例解析多种情况**

### **5.1 正确的 JSON**

```cpp
QByteArray jsonString = R"({"name": "Alice", "age": 25, "isStudent": true})";
```
**输出：**
```
JSON parsed successfully.
```

### **5.2 缺少对象结束符**

```cpp
QByteArray jsonString = R"({"name": "Alice", "age": 25, "isStudent": true)";
```
**输出：**
```
JSON parse error:
Error type: 2
Error offset: 53
```

### **5.3 数组缺少结束符**

```cpp
QByteArray jsonString = R"([1, 2, 3)";
```
**输出：**
```
JSON parse error:
Error type: 3
Error offset: 8
```

---

## **6. 注意事项**

1. **`offset` 的位置：**
   - `offset` 是字节位置，而不是字符位置，特别是在处理多字节字符（如 UTF-8 编码）时要小心。
2. **解析成功检查：**
   - 始终检查 `error` 是否为 `NoError`，以确保 JSON 数据已正确解析。
3. **错误处理：**
   - 使用 `error` 和 `offset` 提供的信息，可以快速定位和修复 JSON 文本中的问题。

---

`QJsonParseError` 提供了详细的错误信息，对于调试和诊断 JSON 格式问题非常有用，尤其是在处理外部来源的 JSON 数据时。