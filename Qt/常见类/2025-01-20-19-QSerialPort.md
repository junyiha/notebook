---
category: Qt
date: 2025-01-20 09:00:00 +0800
layout: post
title: QSerialPort
tag: Qt-class
---
## 摘要

+ QSerialPort 类 相关学习笔记

<!--more-->

## C++ Qt QSerialPort类 是什么

`QSerialPort` 是 **Qt 框架**中的一个类，位于 **Qt SerialPort 模块**中，用于处理串口通信的高效、跨平台接口。它允许开发者在 C++ 中轻松实现对串口的操作，包括数据的发送、接收以及串口配置。

---

## **主要特性**
1. **跨平台支持**:
   - 支持在 Windows、Linux 和 macOS 上进行串口通信。
2. **简单的 API**:
   - 提供同步和异步操作的支持。
   - 支持串口参数的轻松配置，例如波特率、数据位、校验位等。
3. **事件驱动**:
   - 利用 Qt 的信号和槽机制，可以处理异步事件（如数据到达或错误发生）。
4. **兼容性**:
   - 可与其他 Qt 模块（如 GUI 和网络模块）无缝集成。

---

## **使用步骤**

### 1. **添加模块依赖**
在 Qt 项目文件 (`.pro`) 中添加：
```plaintext
QT += serialport
```

### 2. **创建和配置串口对象**

```cpp
#include <QSerialPort>
#include <QSerialPortInfo>
#include <QDebug>

QSerialPort serial;

serial.setPortName("COM1"); // 设置串口名称 (在 Windows 下如 COM1, 在 Linux 下如 /dev/ttyS0)
serial.setBaudRate(QSerialPort::Baud9600); // 设置波特率
serial.setDataBits(QSerialPort::Data8); // 数据位
serial.setParity(QSerialPort::NoParity); // 校验位
serial.setStopBits(QSerialPort::OneStop); // 停止位
serial.setFlowControl(QSerialPort::NoFlowControl); // 流控

if (!serial.open(QIODevice::ReadWrite)) {
    qDebug() << "Failed to open port" << serial.portName() << ", error:" << serial.errorString();
} else {
    qDebug() << "Serial port opened successfully!";
}
```

---

### 3. **发送和接收数据**

#### **发送数据**
```cpp
serial.write("Hello, Serial Port!");
```

#### **接收数据**
接收数据可以通过连接信号 `readyRead()` 进行异步处理：
```cpp
QObject::connect(&serial, &QSerialPort::readyRead, [&]() {
    QByteArray data = serial.readAll();
    qDebug() << "Received data:" << data;
});
```

---

### 4. **列出可用的串口**
使用 `QSerialPortInfo` 列出系统中可用的串口：
```cpp
#include <QSerialPortInfo>

foreach (const QSerialPortInfo &info, QSerialPortInfo::availablePorts()) {
    qDebug() << "Port:" << info.portName();
    qDebug() << "Description:" << info.description();
    qDebug() << "Manufacturer:" << info.manufacturer();
}
```

---

## **常用功能**

| 功能          | 方法                                     |
| ------------- | ---------------------------------------- |
| 打开/关闭串口 | `open(QIODevice::ReadWrite)` / `close()` |
| 设置波特率    | `setBaudRate(QSerialPort::Baud9600)`     |
| 设置数据位    | `setDataBits(QSerialPort::Data8)`        |
| 设置校验位    | `setParity(QSerialPort::NoParity)`       |
| 设置停止位    | `setStopBits(QSerialPort::OneStop)`      |
| 检测错误      | 通过 `error()` 或 `errorOccurred` 信号   |
| 检查是否打开  | `isOpen()`                               |
| 获取串口状态  | `isWritable()`, `isReadable()`           |

---

## **优点**
1. **简单易用**: 提供直观的接口，适合初学者和高级用户。
2. **异步支持**: 与 Qt 的信号和槽机制配合，可以实现事件驱动的程序设计。
3. **跨平台**: 在不同操作系统上，代码一致，编写一次即可运行。

---

## **适用场景**
- 与嵌入式设备通信（如 Arduino、单片机）。
- 读取传感器数据。
- 工业自动化和设备监控系统。
- 串口调试工具的开发。

通过 `QSerialPort`，你可以快速开发功能强大且可靠的串口通信应用。

## 详细介绍QSerialPort类

### **QSerialPort 类详细介绍**

`QSerialPort` 是 Qt 框架中提供的一个串口通信类，用于实现跨平台的串口操作。它是 **Qt SerialPort 模块**的一部分，提供了对串口的基本操作，包括数据收发、串口参数配置、错误处理等。

---

## **类概述**

### **头文件**
```cpp
#include <QSerialPort>
```

### **继承关系**
`QSerialPort` 继承自 `QIODevice`，因此支持 Qt 的 I/O 机制（如 `read()`, `write()` 和 `readyRead()` 信号）。

### **命名空间**
类位于 `QtSerialPort` 模块中，通常需要链接该模块。

---

## **常见用途**
1. 串口通信（与设备交互）。
2. 工业自动化设备数据采集。
3. 嵌入式设备（如 Arduino）的串口通信。
4. 调试和监控串口设备。

---

## **QSerialPort 的主要功能**

| **功能**      | **描述**                                                        |
| ------------- | --------------------------------------------------------------- |
| 打开/关闭串口 | 打开串口设备用于读写操作。                                      |
| 设置串口参数  | 设置波特率、数据位、停止位、校验位等。                          |
| 数据读写      | 提供同步和异步的数据读写功能。                                  |
| 错误检测      | 提供错误检测功能，可获取或处理通信中的错误。                    |
| 信号与槽机制  | 通过信号 `readyRead()`、`errorOccurred()` 等处理异步事件。      |
| 列举可用串口  | 使用 `QSerialPortInfo` 类获取当前系统中的所有可用串口设备信息。 |

---

## **QSerialPort 的构造与析构**

```cpp
// 默认构造函数
QSerialPort(QObject *parent = nullptr);

// 指定端口名称构造
QSerialPort(const QString &name, QObject *parent = nullptr);

// 指定端口信息构造
QSerialPort(const QSerialPortInfo &info, QObject *parent = nullptr);

// 析构函数
~QSerialPort();
```

---

## **重要方法**

### **打开和关闭串口**
```cpp
bool open(QIODevice::OpenMode mode);  // 打开串口
void close();                         // 关闭串口
```
- `mode` 通常为 `QIODevice::ReadWrite`，支持同时读写。

### **设置串口参数**
- 波特率设置：
  ```cpp
  bool setBaudRate(qint32 baudRate, QSerialPort::Directions direction = AllDirections);
  ```
- 数据位设置：
  ```cpp
  bool setDataBits(QSerialPort::DataBits dataBits);
  ```
- 校验位设置：
  ```cpp
  bool setParity(QSerialPort::Parity parity);
  ```
- 停止位设置：
  ```cpp
  bool setStopBits(QSerialPort::StopBits stopBits);
  ```
- 流控制设置：
  ```cpp
  bool setFlowControl(QSerialPort::FlowControl flowControl);
  ```

### **数据读写**
- 写数据：
  ```cpp
  qint64 write(const QByteArray &data);
  qint64 write(const char *data, qint64 len);
  ```
- 读数据：
  ```cpp
  QByteArray readAll();  // 读取所有可用数据
  qint64 read(char *data, qint64 maxSize);
  ```

### **串口状态检查**
- 检查是否打开：
  ```cpp
  bool isOpen() const;
  ```
- 检查设备是否可读或可写：
  ```cpp
  bool isReadable() const;
  bool isWritable() const;
  ```

### **错误处理**
- 获取错误状态：
  ```cpp
  QSerialPort::SerialPortError error() const;
  ```
- 信号：
  ```cpp
  void errorOccurred(QSerialPort::SerialPortError error);
  ```

---

## **常用信号**

| **信号**                    | **描述**                 |
| --------------------------- | ------------------------ |
| `void readyRead()`          | 当串口接收到数据时触发。 |
| `void errorOccurred()`      | 当发生错误时触发。       |
| `void bytesWritten(qint64)` | 当数据成功写入时触发。   |

---

## **QSerialPort 枚举类型**

### **波特率 (BaudRate)**
| 枚举值       | 描述          |
| ------------ | ------------- |
| `Baud1200`   | 1200 波特率   |
| `Baud9600`   | 9600 波特率   |
| `Baud115200` | 115200 波特率 |

---

### **数据位 (DataBits)**
| 枚举值  | 描述     |
| ------- | -------- |
| `Data5` | 5 数据位 |
| `Data8` | 8 数据位 |

---

### **校验位 (Parity)**
| 枚举值       | 描述   |
| ------------ | ------ |
| `NoParity`   | 无校验 |
| `EvenParity` | 偶校验 |
| `OddParity`  | 奇校验 |

---

### **停止位 (StopBits)**
| 枚举值    | 描述     |
| --------- | -------- |
| `OneStop` | 1 停止位 |
| `TwoStop` | 2 停止位 |

---

## **QSerialPort 示例代码**

### **完整示例**

```cpp
#include <QCoreApplication>
#include <QSerialPort>
#include <QSerialPortInfo>
#include <QDebug>

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);

    QSerialPort serial;

    // 设置串口参数
    serial.setPortName("COM1");
    serial.setBaudRate(QSerialPort::Baud9600);
    serial.setDataBits(QSerialPort::Data8);
    serial.setParity(QSerialPort::NoParity);
    serial.setStopBits(QSerialPort::OneStop);
    serial.setFlowControl(QSerialPort::NoFlowControl);

    if (!serial.open(QIODevice::ReadWrite)) {
        qDebug() << "Failed to open port" << serial.portName() << ", error:" << serial.errorString();
        return 1;
    }

    QObject::connect(&serial, &QSerialPort::readyRead, [&]() {
        QByteArray data = serial.readAll();
        qDebug() << "Received data:" << data;
    });

    serial.write("Hello, Serial Port!");
    return app.exec();
}
```

---

## **QSerialPortInfo 类**
用于列出系统中的所有可用串口。常用方法：
- `availablePorts()`: 返回所有可用串口的列表。
- `portName()`: 获取串口名称。

示例：
```cpp
foreach (const QSerialPortInfo &info, QSerialPortInfo::availablePorts()) {
    qDebug() << "Port:" << info.portName();
    qDebug() << "Description:" << info.description();
    qDebug() << "Manufacturer:" << info.manufacturer();
}
```

---

`QSerialPort` 提供了强大的功能，结合 Qt 的信号和槽机制，可以轻松实现高效的串口通信。

## Qt QSerialPort::setFlowControl() 函数 详解

### **Qt QSerialPort::setFlowControl() 函数详解**

`QSerialPort::setFlowControl()` 是 Qt 串口类 `QSerialPort` 提供的一个成员函数，用于设置串口的**流控制方式**（Flow Control）。流控制决定了通信中如何管理数据传输的节奏，确保接收端不会因发送端过快发送数据而超载。

---

## **函数原型**

```cpp
void QSerialPort::setFlowControl(QSerialPort::FlowControl flowControl);
```

### **参数说明**
- `flowControl`：指定流控制方式，类型为 `QSerialPort::FlowControl` 枚举。

### **返回值**
- 此函数没有返回值。

---

## **流控制方式**

`QSerialPort::FlowControl` 是一个枚举，定义了支持的流控制模式：

| **枚举值**                        | **说明**                                                                                    |
| --------------------------------- | ------------------------------------------------------------------------------------------- |
| `QSerialPort::NoFlowControl`      | **无流控制**：发送端与接收端之间没有任何流控制机制。适用于高可靠性通信或小规模数据传输。    |
| `QSerialPort::HardwareControl`    | **硬件流控制**（RTS/CTS）：通过硬件信号线 `RTS`（请求发送）和 `CTS`（清除发送）实现流控制。 |
| `QSerialPort::SoftwareControl`    | **软件流控制**（XON/XOFF）：通过特殊字符 `XON` 和 `XOFF` 进行软件协议的流控制。             |
| `QSerialPort::UnknownFlowControl` | **未知流控制**：不设置流控制。一般用于未初始化状态或无效配置。                              |

---

## **典型用法**

### **1. 设置无流控制（NoFlowControl）**
这是最简单的一种流控制方式，适合高可靠性或短距离通信的场景。

```cpp
#include <QSerialPort>

QSerialPort serial;
serial.setFlowControl(QSerialPort::NoFlowControl);
```

### **2. 设置硬件流控制（HardwareControl）**
适用于需要依赖硬件信号线（RTS/CTS）来管理数据流的情况。

```cpp
#include <QSerialPort>

QSerialPort serial;
serial.setFlowControl(QSerialPort::HardwareControl);
```

### **3. 设置软件流控制（SoftwareControl）**
使用 `XON` 和 `XOFF` 特殊字符来管理数据流，通常用于不支持硬件流控制的场景。

```cpp
#include <QSerialPort>

QSerialPort serial;
serial.setFlowControl(QSerialPort::SoftwareControl);
```

---

## **注意事项**
1. **默认值**：`QSerialPort` 的默认流控制模式是 `NoFlowControl`（无流控制）。
2. **硬件支持**：
   - `HardwareControl` 需要串口硬件支持 RTS/CTS 引脚。
   - 如果使用硬件流控制，确保物理连接正确，并启用相关信号线。
3. **匹配设置**：
   - 发送端和接收端的流控制设置必须匹配，否则可能会导致通信失败。
   - 如果一端启用了硬件或软件流控制，而另一端未启用，数据可能无法正确传输。
4. **应用场景**：
   - **无流控制**：短距离通信、可靠性高的环境。
   - **硬件流控制**：大数据量或高可靠性通信，尤其是在工业自动化领域。
   - **软件流控制**：没有硬件支持的低成本设备。

---

## **示例代码**
以下是一个完整的示例，展示如何设置串口流控制并打开串口。

```cpp
#include <QCoreApplication>
#include <QSerialPort>
#include <QDebug>

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);

    QSerialPort serial;

    // 设置串口名称
    serial.setPortName("COM1");

    // 打开串口
    if (!serial.open(QIODevice::ReadWrite)) {
        qCritical() << "无法打开串口:" << serial.errorString();
        return -1;
    }

    // 配置串口参数
    serial.setBaudRate(QSerialPort::Baud9600);          // 波特率
    serial.setDataBits(QSerialPort::Data8);             // 数据位
    serial.setParity(QSerialPort::NoParity);            // 无校验位
    serial.setStopBits(QSerialPort::OneStop);           // 停止位
    serial.setFlowControl(QSerialPort::HardwareControl); // 硬件流控制

    qDebug() << "串口已打开，等待数据传输...";

    // 关闭串口
    serial.close();
    qDebug() << "串口已关闭。";

    return app.exec();
}
```

---

## **调试与问题排查**
1. **串口无法打开**：
   - 检查串口名称是否正确。
   - 确保串口未被其他程序占用。
   - 检查设备是否支持所选的流控制方式。
2. **数据丢失或未传输**：
   - 确认发送端和接收端的流控制设置一致。
   - 如果使用硬件流控制，确保信号线连接正确。
   - 检查设备是否支持硬件流控制 RTS/CTS。
3. **性能问题**：
   - 在高数据吞吐量场景下，优先选择硬件流控制。

---

### **总结**
`QSerialPort::setFlowControl()` 是设置串口通信中流控制方式的关键函数。正确选择流控制模式能够有效提高数据传输的可靠性和效率。在实际使用中，需要根据具体的硬件支持和应用场景合理配置该参数。

## C++ Qt QSerialPort::write() 函数 详解

在 C++ Qt 框架中，`QSerialPort::write()` 是 `QSerialPort` 类的一种用于向串口设备写入数据的函数。它将数据写入串口缓冲区，并准备发送数据给外部串口设备。`QSerialPort` 类是 Qt 提供的用于与串口设备进行通信的类，它封装了串口通信的细节，简化了与串口设备的交互。

### 1. **函数声明**
```cpp
qint64 QSerialPort::write(const QByteArray &data);
```

### 2. **参数**
- `data`: 这是一个 `QByteArray` 类型的参数，它包含了你希望通过串口发送的数据。`QByteArray` 是 Qt 中的字节数组类，适用于处理原始二进制数据。

### 3. **返回值**
- 函数返回一个 `qint64` 类型的值，表示实际写入的字节数。如果写入成功，则返回实际写入的字节数。如果写入失败，则返回 `-1`，可以通过检查错误来获得失败原因（通过 `QSerialPort::error()` 或 `QSerialPort::errorString()` 方法）。

### 4. **常见使用场景**
- 向串口设备发送命令、数据、传输协议等。
- 通过串口将信息传输到嵌入式系统或其他外部硬件。

### 5. **基本用法**
```cpp
#include <QSerialPort>
#include <QByteArray>

QSerialPort serialPort;

serialPort.setPortName("COM1"); // 设置串口号
serialPort.setBaudRate(QSerialPort::Baud9600); // 设置波特率
serialPort.setDataBits(QSerialPort::Data8); // 设置数据位
serialPort.setParity(QSerialPort::NoParity); // 设置校验位
serialPort.setStopBits(QSerialPort::OneStop); // 设置停止位
serialPort.setFlowControl(QSerialPort::NoFlowControl); // 设置流控

if (serialPort.open(QIODevice::WriteOnly)) {
    QByteArray data = "Hello, Serial Port!";
    qint64 bytesWritten = serialPort.write(data);
    
    if (bytesWritten == -1) {
        qDebug() << "Error writing to port:" << serialPort.errorString();
    } else {
        qDebug() << "Data written:" << bytesWritten << "bytes.";
    }

    serialPort.close();
} else {
    qDebug() << "Failed to open serial port.";
}
```

### 6. **注意事项**
- **阻塞与非阻塞模式**：`write()` 是一个同步方法，在默认情况下会阻塞当前线程，直到数据完全写入或发生错误。为了避免阻塞，可以使用异步模式，或者在写操作后使用 `QSerialPort::waitForBytesWritten()` 来控制。
  
  ```cpp
  serialPort.write(data);
  if (!serialPort.waitForBytesWritten(5000)) { // 等待最多5秒
      qDebug() << "Error: " << serialPort.errorString();
  }
  ```

- **数据分段写入**：如果你写入的数据量较大，`write()` 可能不会一次性将所有数据写入。你可以检查 `bytesWritten`，并继续写入剩余的数据直到完全写入。

- **返回值**：`write()` 返回的字节数是成功写入的字节数。如果返回 `-1`，可以通过 `QSerialPort::error()` 方法来获取错误类型，或者通过 `QSerialPort::errorString()` 来获取更详细的错误信息。

- **缓冲区**：`QSerialPort` 内部有缓冲区，当你调用 `write()` 后，数据并不立即从串口发送。可以使用 `QSerialPort::waitForBytesWritten()` 等方法等待写入操作完成。

### 7. **错误处理**
- 使用 `QSerialPort::error()` 检查发生的错误。
- 使用 `QSerialPort::errorString()` 获取详细的错误消息。

### 8. **示例代码（检查错误）**
```cpp
#include <QSerialPort>
#include <QDebug>

QSerialPort serialPort;

serialPort.setPortName("COM1");
serialPort.setBaudRate(QSerialPort::Baud9600);
serialPort.setDataBits(QSerialPort::Data8);
serialPort.setParity(QSerialPort::NoParity);
serialPort.setStopBits(QSerialPort::OneStop);
serialPort.setFlowControl(QSerialPort::NoFlowControl);

if (serialPort.open(QIODevice::WriteOnly)) {
    QByteArray data = "Test Data";
    qint64 bytesWritten = serialPort.write(data);

    if (bytesWritten == -1) {
        qDebug() << "Write Error:" << serialPort.errorString();
    } else {
        qDebug() << "Successfully wrote" << bytesWritten << "bytes.";
    }

    serialPort.close();
} else {
    qDebug() << "Failed to open port:" << serialPort.errorString();
}
```

通过这种方式，你可以将数据写入串口，并能处理可能的错误或异常情况。

### 总结
`QSerialPort::write()` 是 Qt 中进行串口数据写入的核心函数，使用时要特别注意错误处理和异步模式的管理。对于大数据量的写入，确保考虑到缓冲和写入成功的情况。

## QSerialPort::read() 函数 详解

在 Qt 中，`QSerialPort::read()` 函数是 `QIODevice` 类的一个重要方法，用于从串口中读取可用的数据。以下是对 `QSerialPort::read()` 的详细讲解。

---

## **基本功能**
`QSerialPort::read()` 用于从串口设备读取一段数据。这个函数通常会被用在串口通信场景中，结合信号槽机制读取数据。

### **函数定义**
```cpp
QByteArray QSerialPort::read(qint64 maxSize);
```

- **参数**:
  - `maxSize`: 指定要读取的最大字节数。如果可用的数据少于 `maxSize`，则只读取可用的部分。
  
- **返回值**:
  - 返回一个 `QByteArray` 对象，包含从串口中读取的数据。
  - 如果没有可用数据，返回的字节数组为空。

---

## **常用方法和步骤**

### **1. 检查数据是否可用**
在调用 `read()` 之前，通常需要通过以下方法检查是否有数据可以读取：
- `QSerialPort::bytesAvailable()`:
  返回当前可用的字节数。

```cpp
if (serialPort.bytesAvailable() > 0) {
    QByteArray data = serialPort.read();
    qDebug() << "Received:" << data;
}
```

- `QIODevice::waitForReadyRead()`:
  阻塞当前线程，等待一段时间，直到有数据可用。

```cpp
if (serialPort.waitForReadyRead(3000)) {  // 等待 3 秒
    QByteArray data = serialPort.read();
    qDebug() << "Received:" << data;
} else {
    qDebug() << "Timeout or no data available.";
}
```

---

### **2. 使用 `readyRead` 信号**
串口的读取通常结合异步机制使用，通过 `readyRead` 信号触发读取操作。

```cpp
QObject::connect(&serialPort, &QSerialPort::readyRead, [&]() {
    QByteArray data = serialPort.readAll();  // 读取所有可用数据
    qDebug() << "Received:" << data;
});
```

---

### **3. 连续读取较大数据**
对于较大的数据块，可能需要循环调用 `read()` 或使用 `readAll()`，直到所有数据都被读取。

- **逐步读取**:
  ```cpp
  QByteArray completeData;
  while (serialPort.bytesAvailable() > 0) {
      QByteArray chunk = serialPort.read(1024);  // 每次读取最多 1024 字节
      completeData.append(chunk);
  }
  qDebug() << "Complete Data:" << completeData;
  ```

- **使用 `readAll`**:
  ```cpp
  QByteArray data = serialPort.readAll();
  qDebug() << "Data:" << data;
  ```

---

## **注意事项**

### **1. 数据的长度可能不足**
`read(maxSize)` 会尽可能读取数据，但返回的数据长度可能小于 `maxSize`。需要检查实际读取的字节数。

```cpp
QByteArray data = serialPort.read(100);
qDebug() << "Read" << data.size() << "bytes:" << data;
```

### **2. 阻塞与非阻塞模式**
- 默认情况下，`QSerialPort` 工作在非阻塞模式，调用 `read()` 不会阻塞线程。
- 如果需要等待数据，可以使用 `waitForReadyRead()` 或手动轮询。

### **3. 数据解析**
从串口接收到的数据通常是二进制数据，可能需要解析为字符串或其他数据格式。

```cpp
QByteArray data = serialPort.readAll();
QString text = QString::fromUtf8(data);  // 转换为字符串
qDebug() << "Text:" << text;
```

---

## **示例代码**

以下是一个完整的串口读取示例：
```cpp
#include <QCoreApplication>
#include <QSerialPort>
#include <QSerialPortInfo>
#include <QDebug>

int main(int argc, char *argv[]) {
    QCoreApplication a(argc, argv);

    QSerialPort serialPort;

    // 设置串口信息
    serialPort.setPortName("COM3");  // 根据实际串口号修改
    serialPort.setBaudRate(QSerialPort::Baud9600);
    serialPort.setDataBits(QSerialPort::Data8);
    serialPort.setParity(QSerialPort::NoParity);
    serialPort.setStopBits(QSerialPort::OneStop);
    serialPort.setFlowControl(QSerialPort::NoFlowControl);

    if (!serialPort.open(QIODevice::ReadWrite)) {
        qDebug() << "Failed to open port" << serialPort.portName();
        return -1;
    }

    // 连接 readyRead 信号
    QObject::connect(&serialPort, &QSerialPort::readyRead, [&]() {
        QByteArray data = serialPort.readAll();  // 读取所有可用数据
        qDebug() << "Received:" << data;
    });

    return a.exec();
}
```

---

## **总结**
- `QSerialPort::read()` 是一个低级别的读取接口，通常配合 `bytesAvailable()` 或 `readyRead` 信号使用。
- 可以使用阻塞或非阻塞方式读取数据。
- 推荐结合 `readAll()` 和 `readyRead` 信号实现更高效的数据读取。

