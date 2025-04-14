---
category: Qt
date: 2025-01-14 09:00:00 +0800
layout: post
title: QJsonObject
tag: Qt-class
---
## 摘要

+ QJsonObject 类 相关学习笔记

<!--more-->

## C++ Qt QJsonObject 类 详解

`QJsonObject` 是 Qt 框架中用于表示 JSON 对象的类，它提供了操作 JSON 数据的功能，例如访问键值对、添加或删除元素、以及与其他 JSON 相关类（如 `QJsonArray` 和 `QJsonDocument`）的交互。

以下是 `QJsonObject` 类的详细介绍：

---

## **1. 概述**
`QJsonObject` 是一种键值对的集合，其中键是 `QString` 类型，值可以是以下类型之一：
- `QJsonValue::Null`
- `QJsonValue::Bool`
- `QJsonValue::Double`
- `QJsonValue::String`
- `QJsonValue::Array`
- `QJsonValue::Object`

`QJsonObject` 的主要用途是存储和操作 JSON 格式的数据，通常结合 `QJsonDocument` 使用。

---

## **2. 构造函数**

- **默认构造函数：**
  ```cpp
  QJsonObject();
  ```
  创建一个空的 JSON 对象。

- **拷贝构造函数：**
  ```cpp
  QJsonObject(const QJsonObject &other);
  ```
  创建一个与 `other` 相同的 JSON 对象。

- **析构函数：**
  ```cpp
  ~QJsonObject();
  ```

---

## **3. 常用方法**

### **3.1 插入与访问键值对**
- **插入键值对：**
  ```cpp
  void insert(const QString &key, const QJsonValue &value);
  ```
  或者使用重载操作符：
  ```cpp
  QJsonValue &operator[](const QString &key);
  ```

- **访问值：**
  ```cpp
  QJsonValue value(const QString &key) const;
  ```

- **检查是否包含键：**
  ```cpp
  bool contains(const QString &key) const;
  ```

- **删除键值对：**
  ```cpp
  int remove(const QString &key);
  ```

### **3.2 遍历 JSON 对象**
- **键列表：**
  ```cpp
  QStringList keys() const;
  ```

- **值列表：**
  ```cpp
  QList<QJsonValue> values() const;
  ```

- **迭代键值对：**
  使用 `for` 循环遍历：
  ```cpp
  for (auto it = jsonObject.begin(); it != jsonObject.end(); ++it) {
      QString key = it.key();
      QJsonValue value = it.value();
  }
  ```

### **3.3 大小与空检查**
- **获取键值对数量：**
  ```cpp
  int size() const;
  ```

- **检查是否为空：**
  ```cpp
  bool isEmpty() const;
  ```

---

## **4. 与其他 JSON 类的互操作**

- **转换为 `QJsonDocument`:**
  ```cpp
  QJsonDocument doc(jsonObject);
  ```

- **从 `QJsonDocument` 创建：**
  ```cpp
  QJsonObject jsonObject = doc.object();
  ```

- **与 `QJsonValue` 的互操作：**
  ```cpp
  QJsonValue value = jsonObject.value("key");
  ```

---

## **5. 示例代码**

### **5.1 创建与操作 JSON 对象**
```cpp
#include <QJsonObject>
#include <QJsonValue>
#include <QJsonDocument>
#include <QDebug>

int main() {
    // 创建 JSON 对象
    QJsonObject jsonObject;
    jsonObject.insert("name", QJsonValue("Alice"));
    jsonObject.insert("age", QJsonValue(25));
    jsonObject.insert("isStudent", QJsonValue(true));

    // 访问值
    qDebug() << "Name:" << jsonObject.value("name").toString();
    qDebug() << "Age:" << jsonObject.value("age").toInt();
    qDebug() << "Is student:" << jsonObject.value("isStudent").toBool();

    // 删除键值对
    jsonObject.remove("isStudent");

    // 转换为 QJsonDocument
    QJsonDocument doc(jsonObject);
    qDebug() << "JSON String:" << doc.toJson(QJsonDocument::Compact);

    return 0;
}
```

输出：
```
Name: "Alice"
Age: 25
Is student: true
JSON String: {"age":25,"name":"Alice"}
```

### **5.2 遍历键值对**
```cpp
QJsonObject jsonObject;
jsonObject.insert("key1", QJsonValue("value1"));
jsonObject.insert("key2", QJsonValue(42));

for (auto it = jsonObject.begin(); it != jsonObject.end(); ++it) {
    qDebug() << "Key:" << it.key() << "Value:" << it.value();
}
```

---

## **6. 注意事项**
1. `QJsonObject` 的键是区分大小写的。
2. JSON 的键通常是字符串类型，不能为非字符串类型。
3. 如果需要处理嵌套 JSON 数据，可以递归地使用 `QJsonObject` 和 `QJsonArray`。

以上就是 Qt 中 `QJsonObject` 的详细介绍和使用方法。