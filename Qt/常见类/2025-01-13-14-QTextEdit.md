---
category: Qt
date: 2025-01-13 09:00:00 +0800
layout: post
title: QTextEdit
tag: Qt-class
---
## 摘要

+ QTextEdit 类 相关学习笔记

<!--more-->

## QTextEdit 类 详解

`QTextEdit` 是 Qt 框架中的一个控件类，属于 `QtWidgets` 模块，用于处理多行文本的输入、显示和编辑。它比 `QLineEdit` 更加复杂和灵活，支持富文本、换行、自动换行、滚动条等功能。`QTextEdit` 适用于需要多行文本输入或展示的场景，如文本编辑器、聊天框、日志窗口等。

### 主要特性
1. **多行文本编辑**：
   - `QTextEdit` 可以处理和显示多行文本，适用于需要用户输入较长内容的场景。
   
2. **富文本支持**：
   - 支持富文本格式（如 HTML 和 RTF），用户可以插入各种格式的文本、图片、链接等。
   
3. **自动换行与滚动**：
   - `QTextEdit` 自动换行，并且可以显示垂直和水平滚动条，支持大文本区域的显示与滚动。

4. **文本高亮与样式**：
   - 通过 `QTextCharFormat` 和 `QTextBlockFormat` 等格式化类，可以实现文本的样式设置（如字体、颜色、背景等）。

5. **插入与编辑功能**：
   - 支持插入图片、链接、文本格式化等操作，允许富文本编辑。
   
6. **信号与槽**：
   - 提供了丰富的信号，如 `textChanged()`, `cursorPositionChanged()`, `undoAvailable()`, `redoAvailable()` 等，用于响应文本变化、光标移动等事件。

### 常用方法
1. **文本设置与获取**：
   - `setText(const QString &text)`：设置纯文本。
   - `toPlainText()`：获取纯文本。
   - `setHtml(const QString &html)`：设置 HTML 格式的文本。
   - `toHtml()`：获取 HTML 格式的文本。

2. **文本格式化**：
   - `setFont(const QFont &font)`：设置文本的字体。
   - `setTextColor(const QColor &color)`：设置文本颜色。
   - `setAlignment(Qt::Alignment alignment)`：设置文本对齐方式。

3. **滚动与光标控制**：
   - `setPlainText()`：设置纯文本，清除富文本格式。
   - `setTextCursor(const QTextCursor &cursor)`：设置光标。
   - `moveCursor(QTextCursor::MoveOperation op)`：移动光标。

4. **撤销与重做**：
   - `undo()`：撤销上一步操作。
   - `redo()`：重做上一步操作。

5. **信号**：
   - `textChanged()`：文本发生变化时触发。
   - `cursorPositionChanged()`：光标位置变化时触发。
   - `undoAvailable(bool available)`：撤销操作是否可用时触发。
   - `redoAvailable(bool available)`：重做操作是否可用时触发。

6. **富文本编辑**：
   - `insertHtml(const QString &html)`：插入 HTML 格式的文本。
   - `insertImage(const QImage &image)`：插入图片。
   - `append(const QString &text)`：将文本追加到当前内容后。

### 代码示例
```cpp
#include <QApplication>
#include <QTextEdit>
#include <QVBoxLayout>
#include <QPushButton>
#include <QWidget>

class MyWidget : public QWidget {
    Q_OBJECT
public:
    MyWidget(QWidget *parent = nullptr) : QWidget(parent) {
        QVBoxLayout *layout = new QVBoxLayout(this);

        // 创建 QTextEdit
        QTextEdit *textEdit = new QTextEdit(this);
        textEdit->setPlainText("这是一个多行文本编辑框。");
        layout->addWidget(textEdit);

        // 创建按钮
        QPushButton *button = new QPushButton("插入文本", this);
        layout->addWidget(button);

        connect(button, &QPushButton::clicked, [textEdit]() {
            textEdit->append("这是新插入的文本。");
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
- **QTextEdit::UndoRedoMode**：
  - `QTextEdit::UndoRedoDisabled`：禁用撤销/重做功能。
  - `QTextEdit::UndoRedoEnabled`：启用撤销/重做功能（默认）。

- **QTextBlockFormat**：文本块格式化类，用于设置段落样式，如对齐、边距等。
- **QTextCharFormat**：字符格式化类，用于设置单个字符的样式，如字体、颜色、粗体、斜体等。

### 常见用途
1. **文本编辑器**：可以用来开发简单的文本编辑器，支持多行输入和富文本编辑。
2. **聊天窗口**：在聊天应用中，`QTextEdit` 可以用于显示和输入多行消息。
3. **日志显示**：适合用来展示动态变化的日志内容，支持自动换行和滚动。
4. **代码编辑器**：可以配合语法高亮、自动换行等功能，开发一个简单的代码编辑器。

### 总结
`QTextEdit` 是一个功能强大的控件，适用于多行文本输入和编辑。它支持丰富的文本格式、样式设置、撤销重做、插入图片等功能。与 `QLineEdit` 相比，它能够处理更复杂的文本需求，广泛应用于文本编辑器、聊天窗口、日志显示等场景。