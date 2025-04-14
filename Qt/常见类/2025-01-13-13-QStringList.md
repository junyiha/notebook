---
category: Qt
date: 2025-01-13 09:00:00 +0800
layout: post
title: QStringList
tag: Qt-class
---
## 摘要

+ QStringList 类 学习笔记

<!--more-->

## QStringList 类 详解

`QStringList` 是 Qt 提供的一个容器类，用于存储和操作字符串列表。它是 `QList<QString>` 的一个特殊化实现，专为处理字符串集合而设计，提供了许多方便的字符串处理功能。

---

## **1. QStringList 的基本功能**

### **定义**
```cpp
class QStringList : public QList<QString>
```
- **继承**：`QStringList` 是基于 `QList<QString>` 的，因此它继承了 `QList` 的所有功能。
- **特点**：提供了许多特定于字符串操作的方法，比如拼接、过滤、排序、分割等。

---

## **2. QStringList 的常用构造方法**

### **构造函数**
| 构造函数                                           | 说明                         |
| -------------------------------------------------- | ---------------------------- |
| `QStringList()`                                    | 创建一个空的字符串列表。     |
| `QStringList(const QString &str)`                  | 使用一个字符串初始化列表。   |
| `QStringList(const QList<QString> &other)`         | 从另一个字符串列表复制构造。 |
| `QStringList(std::initializer_list<QString> list)` | 允许使用初始化列表。         |

### 示例
```cpp
QStringList list1; // 空列表
QStringList list2("Hello"); // 包含一个元素 "Hello"
QStringList list3({"A", "B", "C"}); // 使用初始化列表
QStringList list4 = list3; // 从另一个 QStringList 构造
```

---

## **3. 常用方法**

以下是 `QStringList` 提供的核心方法：

### **（1）添加和插入**
| 方法                                | 说明                                     |
| ----------------------------------- | ---------------------------------------- |
| `append(const QString &str)`        | 在列表末尾添加一个字符串。               |
| `append(const QStringList &list)`   | 将另一个字符串列表的所有元素添加到末尾。 |
| `prepend(const QString &str)`       | 在列表开头插入一个字符串。               |
| `insert(int i, const QString &str)` | 在指定位置插入字符串。                   |

#### 示例
```cpp
QStringList list;
list.append("Apple");
list.append("Banana");
list.prepend("Orange");
list.insert(1, "Grape");

qDebug() << list; // 输出：("Orange", "Grape", "Apple", "Banana")
```

---

### **（2）访问和修改**
| 方法                 | 说明                               |
| -------------------- | ---------------------------------- |
| `at(int i)`          | 返回指定索引处的字符串（只读）。   |
| `operator[](int i)`  | 返回指定索引处的字符串（可读写）。 |
| `first()` / `last()` | 返回第一个或最后一个字符串。       |

#### 示例
```cpp
QStringList list = {"A", "B", "C"};
qDebug() << list.at(1); // 输出： "B"
list[1] = "Z"; // 修改第一个字符串
qDebug() << list.first(); // 输出： "A"
qDebug() << list.last(); // 输出： "C"
```

---

### **（3）查找**
| 方法                                             | 说明                           |
| ------------------------------------------------ | ------------------------------ |
| `contains(const QString &str)`                   | 检查列表是否包含指定字符串。   |
| `indexOf(const QString &str, int from = 0)`      | 返回第一个匹配字符串的索引。   |
| `lastIndexOf(const QString &str, int from = -1)` | 返回最后一个匹配字符串的索引。 |
| `count(const QString &str)`                      | 返回匹配字符串的数量。         |

#### 示例
```cpp
QStringList list = {"Apple", "Banana", "Apple", "Grape"};
qDebug() << list.contains("Apple"); // 输出：true
qDebug() << list.indexOf("Apple"); // 输出：0
qDebug() << list.lastIndexOf("Apple"); // 输出：2
qDebug() << list.count("Apple"); // 输出：2
```

---

### **（4）过滤和排序**
| 方法                                                                                                        | 说明                           |
| ----------------------------------------------------------------------------------------------------------- | ------------------------------ |
| `filter(const QString &str, Qt::CaseSensitivity cs = Qt::CaseSensitive)`                                    | 返回包含指定子字符串的所有项。 |
| `replaceInStrings(const QString &before, const QString &after, Qt::CaseSensitivity cs = Qt::CaseSensitive)` | 替换所有字符串中的子字符串。   |
| `sort(Qt::CaseSensitivity cs = Qt::CaseSensitive)`                                                          | 按升序排序。                   |

#### 示例
```cpp
QStringList list = {"Apple", "Banana", "apricot", "Grape"};
QStringList filtered = list.filter("Ap", Qt::CaseInsensitive);
qDebug() << filtered; // 输出：("Apple", "apricot")

list.replaceInStrings("a", "@", Qt::CaseInsensitive);
qDebug() << list; // 输出：("Apple", "B@n@n@", "@pricot", "Gr@pe")

list.sort();
qDebug() << list; // 输出：("@pricot", "Apple", "B@n@n@", "Gr@pe")
```

---

### **（5）分割和合并**
| 方法                                                                                                                     | 说明                                       |
| ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------ |
| `join(const QString &separator)`                                                                                         | 使用指定分隔符连接字符串列表为单个字符串。 |
| `split(const QString &str, Qt::SplitBehavior behavior = Qt::KeepEmptyParts, Qt::CaseSensitivity cs = Qt::CaseSensitive)` | 将字符串分割为字符串列表。                 |

#### 示例
```cpp
QStringList list = {"A", "B", "C"};
QString joined = list.join(", ");
qDebug() << joined; // 输出："A, B, C"

QStringList splitList = QString("X|Y|Z").split("|");
qDebug() << splitList; // 输出：("X", "Y", "Z")
```

---

### **（6）高级操作**
| 方法                                 | 说明                                         |
| ------------------------------------ | -------------------------------------------- |
| `removeDuplicates()`                 | 移除列表中重复的字符串（保留第一个出现的）。 |
| `removeAt(int i)`                    | 删除指定索引处的字符串。                     |
| `replace(int i, const QString &str)` | 替换指定索引处的字符串。                     |

#### 示例
```cpp
QStringList list = {"Apple", "Banana", "Apple", "Grape"};
list.removeDuplicates();
qDebug() << list; // 输出：("Apple", "Banana", "Grape")

list.removeAt(1);
qDebug() << list; // 输出：("Apple", "Grape")
```

---

## **4. 常见问题和注意事项**

### **（1）与空字符串的处理**
默认情况下，`filter` 和 `split` 方法会保留空字符串，除非显式指定：
```cpp
QStringList list = QString("A||C").split("|", Qt::SkipEmptyParts);
qDebug() << list; // 输出：("A", "C")
```

### **（2）区分大小写**
许多方法支持 `Qt::CaseSensitive` 和 `Qt::CaseInsensitive` 参数：
- 默认是区分大小写。
- 可以通过指定参数忽略大小写。

---

## **5. 总结**

`QStringList` 是一个功能强大的字符串列表类，为字符串的存储、过滤、排序、分割、合并等操作提供了简单高效的接口。它继承了 `QList<QString>` 的基础功能，并在此基础上扩展了许多专门针对字符串处理的功能，是处理字符串集合的理想选择。