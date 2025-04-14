---
category: Qt
date: 2025-01-14 09:00:00 +0800
layout: post
title: QJsonDocument
tag: Qt-class
---
## 摘要

+ QJsonDocument 类 相关学习笔记

<!--more-->

## QJsonDocument 类 详解

`QJsonDocument` 是 Qt 中用于表示 JSON 文档的类，它可以包含一个 JSON 对象或一个 JSON 数组，并提供对 JSON 数据的序列化和反序列化功能。`QJsonDocument` 通常用来将 JSON 数据与文本或二进制形式进行转换。

以下是 `QJsonDocument` 类的详细介绍：

---

## **1. 概述**
`QJsonDocument` 是处理 JSON 数据的核心类，可以用来：
- 序列化（将 JSON 数据转换为文本或二进制格式）。
- 反序列化（从文本或二进制格式解析 JSON 数据）。
- 操作 JSON 对象（`QJsonObject`）和 JSON 数组（`QJsonArray`）。

一个 `QJsonDocument` 实例可以包含以下两种类型之一：
- **对象类型：** 包含一个 `QJsonObject`。
- **数组类型：** 包含一个 `QJsonArray`。

---

## **2. 构造函数**

- **默认构造函数：**
  ```cpp
  QJsonDocument();
  ```
  创建一个空的 JSON 文档。

- **从 `QJsonObject` 或 `QJsonArray` 构造：**
  ```cpp
  QJsonDocument(const QJsonObject &object);
  QJsonDocument(const QJsonArray &array);
  ```

- **拷贝构造函数：**
  ```cpp
  QJsonDocument(const QJsonDocument &other);
  ```

- **析构函数：**
  ```cpp
  ~QJsonDocument();
  ```

---

## **3. 常用方法**

### **3.1 序列化与反序列化**
- **将 JSON 文档转换为字符串：**
  ```cpp
  QByteArray toJson(QJsonDocument::JsonFormat format = QJsonDocument::Indented) const;
  ```
  参数说明：
  - `QJsonDocument::Indented`：格式化输出（带缩进）。
  - `QJsonDocument::Compact`：紧凑输出（无多余空格）。

- **从字符串解析 JSON 数据：**
  ```cpp
  static QJsonDocument fromJson(const QByteArray &json, QJsonParseError *error = nullptr);
  ```
  解析结果保存在 `QJsonParseError` 中。

- **将 JSON 文档转换为二进制格式：**
  ```cpp
  QByteArray toBinaryData() const;
  ```

- **从二进制格式解析 JSON 文档：**
  ```cpp
  static QJsonDocument fromBinaryData(const QByteArray &data);
  ```

---

### **3.2 操作 JSON 数据**
- **判断文档类型：**
  ```cpp
  bool isObject() const;
  bool isArray() const;
  ```

- **获取 JSON 数据：**
  ```cpp
  QJsonObject object() const;  // 返回 JSON 对象
  QJsonArray array() const;   // 返回 JSON 数组
  ```

- **设置 JSON 数据：**
  ```cpp
  void setObject(const QJsonObject &object);
  void setArray(const QJsonArray &array);
  ```

- **检查是否为空：**
  ```cpp
  bool isEmpty() const;
  ```

---

### **3.3 其他方法**
- **判断是否为二进制格式：**
  ```cpp
  static bool isBinaryData(const QByteArray &data);
  ```

- **创建二进制格式的 JSON 文档：**
  ```cpp
  QByteArray rawData() const;
  ```

---

## **4. 示例代码**

### **4.1 创建 JSON 文档**
```cpp
#include <QJsonDocument>
#include <QJsonObject>
#include <QJsonArray>
#include <QDebug>

int main() {
    // 创建 JSON 对象
    QJsonObject jsonObject;
    jsonObject.insert("name", "Alice");
    jsonObject.insert("age", 25);
    jsonObject.insert("isStudent", true);

    // 创建 JSON 数组
    QJsonArray jsonArray;
    jsonArray.append("Math");
    jsonArray.append("Physics");
    jsonArray.append("Chemistry");

    // 插入数组到对象中
    jsonObject.insert("subjects", jsonArray);

    // 创建 JSON 文档
    QJsonDocument jsonDoc(jsonObject);

    // 转换为字符串
    QByteArray jsonString = jsonDoc.toJson(QJsonDocument::Indented);
    qDebug() << "JSON String:\n" << jsonString;

    return 0;
}
```

输出：
```json
{
    "age": 25,
    "isStudent": true,
    "name": "Alice",
    "subjects": [
        "Math",
        "Physics",
        "Chemistry"
    ]
}
```

---

### **4.2 解析 JSON 字符串**
```cpp
#include <QJsonDocument>
#include <QJsonObject>
#include <QJsonArray>
#include <QDebug>

int main() {
    // JSON 字符串
    QByteArray jsonString = R"({
        "name": "Bob",
        "age": 30,
        "isEmployee": true,
        "skills": ["C++", "Qt", "Python"]
    })";

    // 解析 JSON
    QJsonParseError parseError;
    QJsonDocument jsonDoc = QJsonDocument::fromJson(jsonString, &parseError);

    if (parseError.error != QJsonParseError::NoError) {
        qDebug() << "JSON Parse Error:" << parseError.errorString();
        return -1;
    }

    // 获取 JSON 对象
    QJsonObject jsonObject = jsonDoc.object();
    qDebug() << "Name:" << jsonObject.value("name").toString();
    qDebug() << "Age:" << jsonObject.value("age").toInt();
    qDebug() << "Is Employee:" << jsonObject.value("isEmployee").toBool();

    // 获取 JSON 数组
    QJsonArray skillsArray = jsonObject.value("skills").toArray();
    qDebug() << "Skills:";
    for (const QJsonValue &skill : skillsArray) {
        qDebug() << "- " << skill.toString();
    }

    return 0;
}
```

输出：
```
Name: "Bob"
Age: 30
Is Employee: true
Skills:
-  "C++"
-  "Qt"
-  "Python"
```

---

## **5. 注意事项**
1. **性能优化：** `QJsonDocument` 是不可变的，这意味着它在每次修改时会创建新的数据副本。
2. **二进制格式的使用：** `toBinaryData` 和 `fromBinaryData` 提供更高效的存储格式，但不能直接用于人类阅读。
3. **错误处理：** 使用 `QJsonParseError` 检查解析错误，例如语法错误或格式不正确。

---

`QJsonDocument` 是处理 JSON 数据的强大工具，结合 `QJsonObject` 和 `QJsonArray`，可以高效完成各种 JSON 数据的读写与操作。

## QJsonDocument::JsonFormat 详解

`QJsonDocument::JsonFormat` 是 Qt 的 `QJsonDocument` 类中的一个枚举类型，用于指定 JSON 文档序列化为字符串时的输出格式。通过选择不同的 `JsonFormat` 值，可以控制生成的 JSON 字符串是紧凑还是易于阅读的格式。

---

## **1. 枚举值**

`QJsonDocument::JsonFormat` 枚举定义了以下两种格式：

### **1.1 Compact**
```cpp
QJsonDocument::Compact
```
- **用途：** 紧凑的 JSON 输出格式。
- **特点：** 
  - 没有多余的空格或换行符。
  - 更节省空间，适合传输或存储。
- **示例输出：**
  ```json
  {"name":"Alice","age":25,"isStudent":true}
  ```

### **1.2 Indented**
```cpp
QJsonDocument::Indented
```
- **用途：** 格式化的 JSON 输出，带缩进。
- **特点：**
  - 输出包含换行符和缩进（通常为 4 个空格）。
  - 更易于阅读和调试。
- **示例输出：**
  ```json
  {
      "name": "Alice",
      "age": 25,
      "isStudent": true
  }
  ```

---

## **2. 用法**

### **2.1 使用 `toJson` 方法**

`QJsonDocument::toJson` 方法可以将 JSON 文档转换为字符串，并接受一个 `JsonFormat` 参数以指定输出格式。

**函数签名：**
```cpp
QByteArray QJsonDocument::toJson(QJsonDocument::JsonFormat format = QJsonDocument::Indented) const;
```

- **`format` 参数：** 指定序列化的格式（`Compact` 或 `Indented`）。
- **默认值：** `QJsonDocument::Indented`。

**示例代码：**
```cpp
#include <QJsonDocument>
#include <QJsonObject>
#include <QDebug>

int main() {
    // 创建 JSON 对象
    QJsonObject jsonObject;
    jsonObject.insert("name", "Alice");
    jsonObject.insert("age", 25);
    jsonObject.insert("isStudent", true);

    // 创建 JSON 文档
    QJsonDocument jsonDoc(jsonObject);

    // 输出为紧凑格式
    QByteArray compactJson = jsonDoc.toJson(QJsonDocument::Compact);
    qDebug() << "Compact format:" << compactJson;

    // 输出为缩进格式
    QByteArray indentedJson = jsonDoc.toJson(QJsonDocument::Indented);
    qDebug() << "Indented format:" << indentedJson;

    return 0;
}
```

**输出：**
```
Compact format: {"name":"Alice","age":25,"isStudent":true}
Indented format: 
{
    "name": "Alice",
    "age": 25,
    "isStudent": true
}
```

---

## **3. 使用场景**

### **3.1 Compact 格式**
- **适用场景：**
  - 传输 JSON 数据，例如通过网络或嵌入在请求中。
  - 存储 JSON 数据时，减少文件大小。
- **优点：** 更节省空间。

### **3.2 Indented 格式**
- **适用场景：**
  - 调试 JSON 数据。
  - 以更具可读性的方式展示数据，例如日志输出或配置文件。
- **优点：** 更易于阅读和理解。

---

## **4. 注意事项**
1. **默认值：** 如果未显式指定格式，`toJson` 方法会使用 `Indented` 格式。
2. **性能：** 紧凑格式可能在传输和存储中更高效，但格式化输出会增加一点点处理时间。
3. **使用场景选择：** 对于人类可读性要求高的场景（如配置文件），应优先选择 `Indented` 格式；对于数据传输或存储，推荐使用 `Compact` 格式。

---

总结来说，`QJsonDocument::JsonFormat` 提供了两种输出格式的选择，根据实际需要在紧凑性和可读性之间进行权衡。