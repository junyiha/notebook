---
category: Qt
date: 2024-11-18 09:00:00 +0800
layout: post
title: QMainWindow
tag: Qt-class
---
## QMainWindow 类 详解

`QMainWindow` 是 Qt 框架中用于创建主窗口的类。它提供了一个标准的窗口框架，通常用于构建图形用户界面（GUI）应用程序的主窗口。`QMainWindow` 提供了功能齐全的窗口部件，如菜单栏、工具栏、状态栏等，以及一个中央区域用于显示主要内容。

### 1. 类定义
```cpp
class QMainWindow : public QWidget
```
`QMainWindow` 继承自 `QWidget`，因此它也是一个 QWidget，具有所有 QWidget 的特性。

### 2. 主要功能
`QMainWindow` 提供了一些标准的窗口元素和布局管理功能：

- **菜单栏** (`QMenuBar`): 用于显示应用程序的菜单项。
- **工具栏** (`QToolBar`): 用于提供快捷工具按钮。
- **状态栏** (`QStatusBar`): 用于显示状态信息。
- **中央区域** (`QWidget`): 用于显示主内容区域，可以是任何其他的 QWidget。

### 3. 关键成员函数

#### 1. **设置和获取中央窗口部件**
   - `void setCentralWidget(QWidget *widget)`: 设置主窗口的中央部件。
   - `QWidget *centralWidget() const`: 获取当前的中央部件。

#### 2. **菜单栏管理**
   - `QMenuBar *menuBar() const`: 获取菜单栏对象。如果需要在窗口中添加菜单项，可以使用此函数。

#### 3. **工具栏管理**
   - `QToolBar *addToolBar(const QString &title)`: 添加工具栏到主窗口。
   - `QToolBar *toolBar(const QString &title) const`: 根据标题获取工具栏对象。

#### 4. **状态栏管理**
   - `QStatusBar *statusBar() const`: 获取状态栏对象，用于在窗口底部显示状态信息。
   - `void setStatusBar(QStatusBar *statusBar)`: 设置自定义状态栏。

#### 5. **菜单和工具栏操作**
   - `void removeToolBar(QToolBar *toolbar)`: 从主窗口中移除工具栏。
   - `void removeToolBarBreak(QToolBar *toolbar)`: 移除工具栏之间的分隔符。

### 4. 示例代码

以下是一个简单的使用 `QMainWindow` 的示例，展示了如何创建一个带有菜单栏、工具栏和状态栏的主窗口：

```cpp
#include <QApplication>
#include <QMainWindow>
#include <QMenuBar>
#include <QToolBar>
#include <QStatusBar>
#include <QPushButton>
#include <QWidget>

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);

    QMainWindow mainWindow;
    mainWindow.setWindowTitle("QMainWindow Example");

    // 创建并设置中央部件
    QWidget *centralWidget = new QWidget;
    QPushButton *button = new QPushButton("Click Me");
    QVBoxLayout *layout = new QVBoxLayout;
    layout->addWidget(button);
    centralWidget->setLayout(layout);
    mainWindow.setCentralWidget(centralWidget);

    // 创建菜单栏
    QMenuBar *menuBar = mainWindow.menuBar();
    QMenu *fileMenu = menuBar->addMenu("File");
    fileMenu->addAction("Open");
    fileMenu->addAction("Save");
    fileMenu->addSeparator();
    fileMenu->addAction("Exit");

    // 创建工具栏
    QToolBar *toolBar = mainWindow.addToolBar("Main Toolbar");
    toolBar->addAction("Open");
    toolBar->addAction("Save");

    // 创建状态栏
    QStatusBar *statusBar = new QStatusBar;
    mainWindow.setStatusBar(statusBar);
    statusBar->showMessage("Ready");

    // 显示主窗口
    mainWindow.resize(800, 600);
    mainWindow.show();

    return app.exec();
}
```

### 5. 继承和扩展
`QMainWindow` 可以被继承和扩展，以添加自定义功能或修改现有行为。例如，你可以创建一个自定义的主窗口类，添加额外的工具栏或菜单项：

```cpp
#include <QMainWindow>
#include <QAction>
#include <QMenuBar>
#include <QToolBar>
#include <QStatusBar>

class MyMainWindow : public QMainWindow {
    Q_OBJECT
public:
    MyMainWindow() {
        // 自定义菜单
        QMenu *fileMenu = menuBar()->addMenu("File");
        QAction *exitAction = fileMenu->addAction("Exit");
        connect(exitAction, &QAction::triggered, this, &QMainWindow::close);

        // 自定义工具栏
        QToolBar *toolbar = addToolBar("My Toolbar");
        toolbar->addAction("My Action");

        // 自定义状态栏
        statusBar()->showMessage("Welcome to My Application");
    }
};
```

### 6. 注意事项
- **中央部件**：`QMainWindow` 只允许设置一个中央部件（`centralWidget`）。如果你需要在中央区域放置多个部件，可以使用布局管理器将它们组织起来。
- **工具栏和菜单**：工具栏和菜单可以动态添加或移除，但要确保在主窗口显示之前设置好它们。
- **状态栏**：状态栏通常用于显示应用程序的状态信息，可以显示多条信息或者使用复杂的状态显示组件。

### 7. 总结
`QMainWindow` 是一个功能丰富的主窗口类，提供了标准的窗口元素，如菜单栏、工具栏和状态栏。它是构建 Qt GUI 应用程序的基础组件之一，并提供了丰富的接口来管理和定制窗口的各个部分。通过继承和扩展 `QMainWindow`，你可以创建符合需求的复杂主窗口。


## Qt Ui::MainWindow 类 详解

在 Qt 应用程序中，`Ui::MainWindow` 类通常是由 Qt Designer 生成的用于管理用户界面元素的类。它是 Qt UI 系统的重要组成部分，主要用于连接 UI 界面设计与应用程序逻辑。通常在使用 Qt Designer 设计主窗口时，生成的 `.ui` 文件会通过 `uic` 工具转换为 C++ 代码，其中包含一个 `Ui::MainWindow` 类。

### 1. **`Ui::MainWindow` 类的作用**
`Ui::MainWindow` 是自动生成的类，它负责管理和初始化设计时创建的 UI 元素。这个类是由 Qt Designer 创建的 `.ui` 文件转换而来的，通常位于生成的 `ui_mainwindow.h` 文件中。它主要负责以下内容：
- 创建和布局窗口控件。
- 初始化控件的属性和默认状态。
- 提供接口用于在代码中访问这些控件。

### 2. **`Ui::MainWindow` 的使用方式**
当你使用 Qt Designer 创建一个主窗口并将其保存为 `mainwindow.ui` 时，Qt 会自动生成一个 `ui_mainwindow.h` 文件，其中包含 `Ui::MainWindow` 类的定义。这个类可以通过在自定义的 `MainWindow` 类中包含 `ui_mainwindow.h` 文件来使用。

#### 示例代码：
假设我们在 Qt Designer 中创建了一个主窗口，并保存为 `mainwindow.ui`。生成的 `ui_mainwindow.h` 文件会包含如下内容：

```cpp
// ui_mainwindow.h（自动生成的文件）
namespace Ui {
class MainWindow {
public:
    QWidget *centralWidget;
    QMenuBar *menuBar;
    QStatusBar *statusBar;
    QPushButton *myButton;

    void setupUi(QMainWindow *MainWindow) {
        if (MainWindow->objectName().isEmpty())
            MainWindow->setObjectName(QString::fromUtf8("MainWindow"));
        MainWindow->resize(400, 300);
        centralWidget = new QWidget(MainWindow);
        menuBar = new QMenuBar(MainWindow);
        statusBar = new QStatusBar(MainWindow);
        myButton = new QPushButton(centralWidget);
        myButton->setText("Click Me");

        MainWindow->setCentralWidget(centralWidget);
        MainWindow->setMenuBar(menuBar);
        MainWindow->setStatusBar(statusBar);
    }
};
}
```

#### 在自定义主窗口类中的使用：
通常，我们会在自定义的主窗口类中使用 `Ui::MainWindow` 以便将设计的 UI 和应用逻辑结合在一起：

```cpp
#include <QMainWindow>
#include "ui_mainwindow.h"  // 包含自动生成的 UI 类

class MainWindow : public QMainWindow {
    Q_OBJECT

public:
    explicit MainWindow(QWidget *parent = nullptr)
        : QMainWindow(parent), ui(new Ui::MainWindow) {
        ui->setupUi(this);  // 初始化 UI
        connect(ui->myButton, &QPushButton::clicked, this, &MainWindow::onButtonClicked);
    }

    ~MainWindow() {
        delete ui;
    }

private slots:
    void onButtonClicked() {
        // 处理按钮点击事件
    }

private:
    Ui::MainWindow *ui;
};
```

### 3. **`setupUi()` 方法**
`setupUi()` 方法是 `Ui::MainWindow` 类中的核心方法。它负责初始化窗口控件并设置其布局、属性、信号槽连接等。通常在主窗口类的构造函数中调用该方法。这个方法需要一个 `QMainWindow` 对象作为参数，以便将控件添加到主窗口中。

### 4. **访问 UI 元素**
通过 `Ui::MainWindow` 类中的指针，可以轻松访问和操作设计时创建的控件。例如，使用 `ui->myButton` 可以访问按钮并设置它的文本或连接信号槽。

```cpp
ui->myButton->setText("New Text");
connect(ui->myButton, &QPushButton::clicked, this, &MainWindow::onButtonClicked);
```

### 5. **`Ui::MainWindow` 与 `QMainWindow` 的关系**
`Ui::MainWindow` 是一个辅助类，实际的窗口功能仍然由继承自 `QMainWindow` 的自定义类实现。`QMainWindow` 提供了主窗口的基本框架和功能（如菜单栏、状态栏、工具栏），而 `Ui::MainWindow` 主要负责管理控件和布局。

### 6. **自定义控件与扩展 UI**
如果你需要在设计时添加自定义控件或进行更多的 UI 扩展，可以在 Qt Designer 中通过提升控件的方式实现。此外，你也可以手动在 `MainWindow` 类的构造函数中添加新的控件，并将其与现有的 UI 进行整合。

### 7. **典型的工作流程**
- 使用 Qt Designer 设计主窗口，并保存为 `.ui` 文件。
- 通过 Qt 的构建工具（如 qmake 或 CMake）自动生成对应的 `ui_mainwindow.h` 文件。
- 在自定义的主窗口类中包含生成的 `ui_mainwindow.h` 文件，并使用 `Ui::MainWindow` 类来管理 UI 元素。
- 在应用程序逻辑中，使用 `ui->` 前缀来访问控件，并编写对应的事件处理代码。

### 总结
`Ui::MainWindow` 是一个自动生成的类，它负责将 Qt Designer 设计的 UI 界面与代码逻辑连接起来。通过调用 `setupUi()`，开发者可以方便地初始化和使用设计时创建的控件，从而简化了 UI 编程的流程。