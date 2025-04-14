---
category: Qt
date: 2025-01-13 09:00:00 +0800
layout: post
title: QLineEdit
tag: Qt-class
---
## 摘要

+ QLineEdit 类 相关学习笔记

<!--more-->

## C++ QLineEdit 类 详解

`QLineEdit` 是 Qt 框架中的一个控件类，属于 `QtWidgets` 模块，用于创建和管理单行文本框。它允许用户输入、编辑和展示文本。`QLineEdit` 是一个非常常用的控件，适用于用户输入单行数据的场景，比如用户名、密码、搜索框等。

### 主要特性
1. **文本显示与编辑**：
   - `QLineEdit` 提供了文本输入和编辑功能，可以通过 `setText()` 设置文本，使用 `text()` 获取当前文本内容。
   - 支持对输入文本的格式、样式进行设置。
   
2. **输入限制**：
   - 可以设置输入的最大长度、输入类型等，支持正则表达式限制输入。
   
3. **信号与槽**：
   - 提供了很多信号，如 `textChanged()`, `editingFinished()`, `returnPressed()` 等，开发者可以连接这些信号以处理文本变化、编辑完成等事件。

### 常用方法
1. **文本设置与获取**：
   - `setText(const QString &text)`：设置文本。
   - `text()`：获取当前文本。
   - `clear()`：清除文本框内容。

2. **输入限制**：
   - `setMaxLength(int length)`：设置最大输入长度。
   - `setEchoMode(QLineEdit::EchoMode)`：设置显示模式，例如密码框可以设置为 `QLineEdit::Password` 来隐藏文本。

3. **选择与光标控制**：
   - `selectAll()`：选择所有文本。
   - `setCursorPosition(int position)`：设置光标位置。

4. **信号**：
   - `textChanged(const QString &text)`：文本变化时触发。
   - `editingFinished()`：编辑完成时触发。
   - `returnPressed()`：用户按下回车时触发。

5. **样式和格式设置**：
   - `setAlignment(Qt::Alignment alignment)`：设置文本的对齐方式。
   - `setPlaceholderText(const QString &text)`：设置占位符文本，在输入框为空时显示。

6. **输入验证**：
   - 使用 `QValidator` 类配合 `setValidator()` 来验证用户输入。

### 常见用途
1. **用户名/密码框**：用于输入用户名或密码，可以通过设置 `QLineEdit::Password` 模式来隐藏密码。
2. **搜索框**：可以提供搜索功能，当用户输入时触发信号。
3. **表单字段**：在表单中收集数据，用户通过该控件输入文本。

### 代码示例
```cpp
#include <QApplication>
#include <QLineEdit>
#include <QVBoxLayout>
#include <QPushButton>
#include <QWidget>

class MyWidget : public QWidget {
    Q_OBJECT
public:
    MyWidget(QWidget *parent = nullptr) : QWidget(parent) {
        QVBoxLayout *layout = new QVBoxLayout(this);

        // 创建 QLineEdit
        QLineEdit *lineEdit = new QLineEdit(this);
        lineEdit->setPlaceholderText("请输入文本");
        layout->addWidget(lineEdit);

        // 创建按钮
        QPushButton *button = new QPushButton("提交", this);
        layout->addWidget(button);

        connect(button, &QPushButton::clicked, [lineEdit]() {
            // 获取文本并打印
            QString text = lineEdit->text();
            qDebug() << "用户输入的文本：" << text;
        });

        setLayout(layout);
    }
};

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    MyWidget widget;
    widget.show();

    return app.exec();
}

#include "main.moc"
```

### 重要枚举与常量
- **QLineEdit::EchoMode**：表示文本显示模式，如：
  - `QLineEdit::Normal`：正常显示文本。
  - `QLineEdit::Password`：隐藏文本，常用于密码输入框。
  - `QLineEdit::NoEcho`：不显示任何文本。

- **QLineEdit::SelectionBehavior**：表示文本选择行为。
  - `QLineEdit::SelectAll`：选择所有文本。
  - `QLineEdit::MoveCursor`：通过光标移动选择文本。

### 总结
`QLineEdit` 是 Qt 中非常实用的控件，广泛用于处理用户的单行输入。它支持文本编辑、格式化、限制输入等多种功能，并提供了丰富的信号和槽机制，使得开发者能够轻松处理用户输入。