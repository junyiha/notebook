---
category: Qt
date: 2024-11-18 09:00:00 +0800
layout: post
title: QStackedWidget
tag: Qt-class
---
# 简介

+ QStackedWidget类相关笔记

# QStackedWidget 详解

`QStackedWidget` 是 Qt 框架中的一个控件类，用于在同一位置显示多个小部件（widget），但一次只能显示其中的一个。它常用于需要在同一空间切换不同页面内容的场景，如实现向导界面、选项卡式界面或动态内容切换等。

### `QStackedWidget` 类的详细说明

#### 1. 基本概念
- **`QStackedWidget`** 继承自 `QFrame`，属于一个容器小部件。
- 该控件维护一组子小部件，这些子小部件按堆栈排列。一次只能显示一个小部件，其他小部件会被隐藏。

#### 2. 常用方法和成员函数

- **`addWidget(QWidget *widget)`**:
  将新的小部件添加到堆栈中，返回新页面的索引值。
  
  ```cpp
  QStackedWidget *stackedWidget = new QStackedWidget;
  QWidget *page1 = new QWidget;
  stackedWidget->addWidget(page1);
  ```

- **`insertWidget(int index, QWidget *widget)`**:
  在指定索引位置插入一个小部件。

- **`setCurrentIndex(int index)`**:
  设置当前显示的小部件的索引。可以通过此方法切换页面。
  
  ```cpp
  stackedWidget->setCurrentIndex(0); // 显示第一个页面
  ```

- **`setCurrentWidget(QWidget *widget)`**:
  通过指针设置当前显示的小部件。
  
- **`currentIndex()`**:
  返回当前显示的小部件的索引。

- **`currentWidget()`**:
  返回当前显示的小部件的指针。

- **`removeWidget(QWidget *widget)`**:
  从堆栈中移除指定的小部件，不会删除该小部件。

#### 3. 信号（Signals）
- **`currentChanged(int index)`**:
  当当前小部件的索引改变时触发。
  
- **`widgetRemoved(int index)`**:
  当小部件从堆栈中移除时触发。

#### 4. 使用示例

```cpp
#include <QApplication>
#include <QStackedWidget>
#include <QPushButton>
#include <QVBoxLayout>
#include <QWidget>

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    // 创建主窗口
    QWidget window;
    QVBoxLayout *layout = new QVBoxLayout(&window);

    // 创建 QStackedWidget 实例
    QStackedWidget *stackedWidget = new QStackedWidget;

    // 创建两个页面
    QWidget *page1 = new QWidget;
    QPushButton *button1 = new QPushButton("Page 1");
    QVBoxLayout *page1Layout = new QVBoxLayout(page1);
    page1Layout->addWidget(button1);

    QWidget *page2 = new QWidget;
    QPushButton *button2 = new QPushButton("Page 2");
    QVBoxLayout *page2Layout = new QVBoxLayout(page2);
    page2Layout->addWidget(button2);

    // 添加页面到 QStackedWidget
    stackedWidget->addWidget(page1);
    stackedWidget->addWidget(page2);

    // 将 QStackedWidget 添加到主布局
    layout->addWidget(stackedWidget);

    // 切换页面示例
    QObject::connect(button1, &QPushButton::clicked, [&]() {
        stackedWidget->setCurrentIndex(1);
    });
    QObject::connect(button2, &QPushButton::clicked, [&]() {
        stackedWidget->setCurrentIndex(0);
    });

    window.show();
    return app.exec();
}
```

### 5. 常见应用场景
- **向导程序**: `QStackedWidget` 非常适合用于实现多步骤的向导程序，每个页面代表向导的一个步骤。
- **动态内容切换**: 如设置窗口、选项卡界面等。
- **多功能面板**: 实现功能区的页面切换。

### 6. 样式和自定义
`QStackedWidget` 可以像其他 Qt 小部件一样通过 `QStyle` 和 `QSS`（Qt 样式表）来进行样式自定义。例如：

```css
QStackedWidget {
    background-color: #f0f0f0;
}
```

### 7. 注意事项
- 如果需要在切换页面时进行一些处理逻辑，可以连接 `currentChanged()` 信号来进行。
- `QStackedWidget` 只会在其当前显示的小部件上处理事件，如鼠标和键盘事件，因此需要确保切换逻辑合理。

### 总结
`QStackedWidget` 是一个非常有用的控件，用于实现页面切换或动态内容显示。通过其 API 和信号槽机制，开发者可以轻松创建复杂的用户界面，增强用户体验。