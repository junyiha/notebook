---
category: Qt
date: 2025-01-13 09:00:00 +0800
layout: post
title: QSqlDatabase
tag: Qt-class
---
## 摘要

+ Qt中数据库模块相关学习笔记

<!--more-->

## QSqlDatabase 详解

`QSqlDatabase` 是 Qt SQL 模块中的核心类，用于管理与数据库的连接。它为创建、配置和管理数据库连接提供了完整的接口，并支持多种数据库类型（如 SQLite、MySQL、PostgreSQL、ODBC 等）。以下是对 `QSqlDatabase` 的详细解析。

---

## **1. 常用功能概述**

### **主要职责**
- **加载数据库驱动**：通过驱动名称 (`QSQLITE`, `QMYSQL`, `QPSQL` 等) 选择数据库类型。
- **配置数据库连接**：设置主机名、端口、数据库名、用户名、密码等。
- **管理多个连接**：支持使用不同的连接名管理多个数据库连接。
- **提供事务支持**：支持事务的开始、提交和回滚。

---

## **2. 常用方法**

### **（1）静态方法**

| 方法名称                                                                     | 说明                                                               |
| ---------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| `addDatabase(const QString &type)`                                           | 添加数据库连接并指定数据库驱动类型，返回一个 `QSqlDatabase` 对象。 |
| `addDatabase(const QString &type, const QString &connectionName)`            | 添加具有特定连接名称的数据库连接。                                 |
| `database(const QString &connectionName = QLatin1String(defaultConnection))` | 返回已存在的数据库连接对象。                                       |
| `drivers()`                                                                  | 返回可用的数据库驱动名称列表。                                     |
| `removeDatabase(const QString &connectionName)`                              | 移除指定名称的数据库连接。                                         |
| `isDriverAvailable(const QString &type)`                                     | 检查指定数据库驱动是否可用。                                       |

---

### **（2）实例方法**

| 方法名称                               | 说明                                                                |
| -------------------------------------- | ------------------------------------------------------------------- |
| `setHostName(const QString &host)`     | 设置数据库服务器的主机名。                                          |
| `setPort(int port)`                    | 设置数据库服务器端口号。                                            |
| `setDatabaseName(const QString &name)` | 设置数据库名称或文件路径（如 SQLite）。                             |
| `setUserName(const QString &name)`     | 设置登录数据库的用户名。                                            |
| `setPassword(const QString &password)` | 设置登录数据库的密码。                                              |
| `open()`                               | 打开与数据库的连接。如果失败，可以使用 `lastError()` 获取错误信息。 |
| `close()`                              | 关闭数据库连接。                                                    |
| `isOpen()`                             | 检查数据库连接是否已打开。                                          |
| `transaction()`                        | 启动事务，适用于支持事务的数据库。                                  |
| `commit()`                             | 提交事务。                                                          |
| `rollback()`                           | 回滚事务。                                                          |
| `lastError()`                          | 返回最近一次错误的描述信息。                                        |

---

## **3. 使用方法详解**

### **（1）加载驱动和创建连接**

#### 默认连接
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QMYSQL");
db.setHostName("localhost");
db.setPort(3306);
db.setDatabaseName("testdb");
db.setUserName("root");
db.setPassword("password");

if (!db.open()) {
    qDebug() << "Database connection failed:" << db.lastError().text();
} else {
    qDebug() << "Database connection succeeded!";
}
```

#### 多连接管理
每个连接都有一个唯一的连接名，可以通过指定名称管理多个连接：
```cpp
QSqlDatabase db1 = QSqlDatabase::addDatabase("QSQLITE", "sqliteConnection");
db1.setDatabaseName("local.db");

QSqlDatabase db2 = QSqlDatabase::addDatabase("QPSQL", "postgresConnection");
db2.setHostName("localhost");
db2.setPort(5432);
db2.setDatabaseName("testdb");
db2.setUserName("postgres");
db2.setPassword("password");
```

---

### **（2）事务管理**
事务操作适用于支持事务的数据库（如 MySQL 和 PostgreSQL）：
```cpp
if (db.transaction()) {
    QSqlQuery query;
    query.exec("INSERT INTO users (name, age) VALUES ('Alice', 30)");
    query.exec("INSERT INTO users (name, age) VALUES ('Bob', 25)");

    if (db.commit()) {
        qDebug() << "Transaction committed.";
    } else {
        db.rollback();
        qDebug() << "Transaction failed and rolled back.";
    }
} else {
    qDebug() << "Failed to start transaction.";
}
```

---

### **（3）获取驱动列表**
查看当前 Qt 支持的所有数据库驱动：
```cpp
QStringList drivers = QSqlDatabase::drivers();
qDebug() << "Supported drivers:" << drivers;
```

---

### **（4）移除连接**
移除不再需要的数据库连接：
```cpp
QSqlDatabase::removeDatabase("sqliteConnection");
```
> 注意：在移除连接之前，必须确保没有其他对象（如 `QSqlQuery`）正在使用该连接。

---

## **4. 使用注意事项**

### **（1）线程安全**
- 每个线程应使用独立的数据库连接。
- 可以使用 `QSqlDatabase::addDatabase` 创建新的连接，并在当前线程使用。

### **（2）支持的驱动**
以下是常见驱动及其依赖：
| **驱动名称** | **说明**    | **依赖库**            |
| ------------ | ----------- | --------------------- |
| `QSQLITE`    | SQLite      | 无需额外库，内置支持  |
| `QMYSQL`     | MySQL       | 需要 `libmysqlclient` |
| `QPSQL`      | PostgreSQL  | 需要 `libpq`          |
| `QODBC`      | ODBC 数据源 | 系统 ODBC 驱动        |
| `QOCI`       | Oracle OCI  | 需要 Oracle 客户端库  |
| `QDB2`       | IBM DB2     | 需要 IBM DB2 客户端库 |

### **（3）错误处理**
任何操作失败时，都可以调用 `lastError()` 查看具体的错误信息：
```cpp
QSqlError error = db.lastError();
qDebug() << "Error text:" << error.text();
```

---

## **5. 常见问题与解决方案**

### **（1）驱动不可用**
- 错误信息：`Driver not loaded` 或 `Driver not available`。
- 解决方案：
  1. 检查是否安装了正确的数据库客户端库。
  2. 确保 Qt 安装包含对应驱动插件（`plugins/sqldrivers/` 文件夹）。
  3. 如果使用的是静态编译的 Qt，请确保驱动已正确编译。

### **（2）连接失败**
- 错误信息：`Could not connect to server` 或 `Connection refused`。
- 解决方案：
  1. 检查主机名、端口号、数据库名、用户名和密码是否正确。
  2. 确保数据库服务器正在运行。
  3. 检查防火墙或网络设置。

### **（3）多线程访问问题**
- 错误信息：`Database connection is not open`。
- 解决方案：
  1. 在每个线程中创建独立的连接。
  2. 避免在多个线程间共享同一个 `QSqlDatabase` 实例。

---

通过掌握 `QSqlDatabase` 的这些方法和注意事项，您可以在 Qt 项目中轻松管理数据库连接，实现高效的数据库操作。

## QSqlDatabase::drivers() 详解

`QSqlDatabase::drivers()` 是一个静态方法，用于列出当前 Qt 环境中支持的数据库驱动。了解它有助于判断是否正确配置了数据库支持，并确认是否可以使用某个数据库类型。

---

## **1. 函数定义**

```cpp
QStringList QSqlDatabase::drivers()
```

- **返回值**：返回一个包含支持的数据库驱动名称的 `QStringList`。
- **说明**：
  - 该方法返回当前 Qt 安装中所有可用的 SQL 驱动名称。
  - 驱动名称是区分大小写的字符串，比如 `QMYSQL`, `QSQLITE`, `QPSQL`。

---

## **2. 常见驱动及用途**

| **驱动名称** | **用途**                  | **说明**                                   |
| ------------ | ------------------------- | ------------------------------------------ |
| `QSQLITE`    | SQLite                    | 内置支持，适用于单机应用。                 |
| `QMYSQL`     | MySQL                     | 支持 MySQL 和 MariaDB 数据库。             |
| `QPSQL`      | PostgreSQL                | 支持 PostgreSQL 数据库。                   |
| `QODBC`      | ODBC 数据源               | 通过 ODBC 访问各种数据库。                 |
| `QOCI`       | Oracle OCI                | 支持 Oracle 数据库，需配置 Oracle 客户端。 |
| `QDB2`       | IBM DB2                   | 支持 IBM DB2 数据库。                      |
| `QTDS`       | Sybase ASE / SQL Anywhere | 支持 Sybase 数据库。                       |

---

## **3. 示例代码**

### **（1）获取支持的驱动**
以下代码列出当前环境中所有可用的数据库驱动：
```cpp
#include <QCoreApplication>
#include <QSqlDatabase>
#include <QDebug>

int main(int argc, char *argv[]) {
    QCoreApplication app(argc, argv);

    QStringList drivers = QSqlDatabase::drivers();
    qDebug() << "Supported database drivers:";
    for (const QString &driver : drivers) {
        qDebug() << driver;
    }

    return app.exec();
}
```

### **（2）示例输出**

假设当前环境支持 SQLite 和 MySQL，输出可能如下：
```
Supported database drivers:
"QSQLITE"
"QMYSQL"
```

如果未配置任何驱动，输出为空列表：
```
Supported database drivers:
()
```

---

## **4. 驱动不可用时的排查方法**

如果预期的驱动未列出，可能是以下原因：

### **（1）Qt 未编译或安装对应驱动**
- 默认的 Qt 安装通常只包含 SQLite 支持。
- 如果需要 MySQL、PostgreSQL 等其他驱动，需安装对应的客户端库，并确保 Qt 的 SQL 模块已编译支持这些驱动。

#### 检查驱动插件：
驱动文件通常位于 `plugins/sqldrivers/` 文件夹下。例如：
```
libqsqlmysql.so  (Linux)
qsqlmysql.dll    (Windows)
```

### **（2）未正确安装数据库客户端**
- 某些驱动（如 `QMYSQL`, `QPSQL`）依赖数据库客户端库。如果未安装或未正确配置这些库，驱动将不可用。

### **（3）Qt 环境变量未正确配置**
- 确保 Qt 找到了插件路径。
- 检查是否设置了 `QT_PLUGIN_PATH` 环境变量。

---

## **5. 增加驱动支持**

### **（1）安装依赖**
以 MySQL 为例：
- **Linux**: 安装 `libmysqlclient`。
- **Windows**: 安装 MySQL Connector。
- **macOS**: 安装 `mysql` 或通过 Homebrew 安装 MySQL。

### **（2）重新编译 Qt 的 SQL 模块**
如果使用的是自定义编译的 Qt，可以重新编译 SQL 模块以添加支持：
```bash
cd qtbase/src/plugins/sqldrivers
qmake
make
make install
```

---

## **6. 常见问题**

### **（1）驱动未列出**
**问题**：调用 `QSqlDatabase::drivers()` 不显示某些预期的驱动，如 `QMYSQL`。

**解决方案**：
- 确保安装了对应的数据库客户端。
- 检查 Qt 的 `plugins/sqldrivers/` 目录中是否存在对应插件。
- 使用 `QSqlDatabase::isDriverAvailable()` 检查驱动是否可用：
  ```cpp
  if (QSqlDatabase::isDriverAvailable("QMYSQL")) {
      qDebug() << "QMYSQL driver is available.";
  } else {
      qDebug() << "QMYSQL driver is not available.";
  }
  ```

### **（2）加载驱动失败**
**问题**：即使驱动列在 `QSqlDatabase::drivers()` 中，尝试使用时仍报错 `Driver not loaded`。

**解决方案**：
1. 检查是否缺少依赖库：
   - 使用工具（如 `ldd` 或 `Dependency Walker`）检查驱动插件的依赖是否满足。
2. 确保 `QT_PLUGIN_PATH` 指向插件目录。

---

## **7. 总结**

`QSqlDatabase::drivers()` 是快速检查当前环境支持哪些数据库驱动的重要工具。结合驱动安装和环境配置，可以确保数据库功能正常运行。如果某些驱动不可用，可以通过检查插件路径、依赖库和 Qt 安装配置解决问题。

##  QSqlDatabase::addDatabase() 函数 详解

`QSqlDatabase::addDatabase()` 是 Qt 中用于创建和注册数据库连接的一个静态方法。它允许应用程序与不同的数据库（如 SQLite、MySQL、PostgreSQL 等）进行交互。

---

## **1. 函数定义**

```cpp
static QSqlDatabase QSqlDatabase::addDatabase(const QString &type, const QString &connectionName = QLatin1String(defaultConnection))
```

### **参数**
1. **`type`**：
   - 数据库驱动的名称。
   - 必须是 `QSqlDatabase::drivers()` 返回的值之一，例如：`QSQLITE`, `QMYSQL`, `QPSQL` 等。

2. **`connectionName`**（可选）：
   - 数据库连接的名称。
   - 如果不指定，使用默认连接（名为 `defaultConnection`）。
   - 每个连接名必须是唯一的，否则会覆盖同名连接。

### **返回值**
- 返回一个 `QSqlDatabase` 对象，用于配置和操作数据库连接。
- 如果指定的驱动不存在，返回的数据库对象是无效的。

---

## **2. 使用步骤**

1. 调用 `QSqlDatabase::addDatabase()` 添加数据库连接。
2. 配置数据库参数（如主机名、端口、用户名、密码、数据库名称等）。
3. 使用 `open()` 方法尝试连接数据库。
4. 在程序结束时关闭连接，并调用 `QSqlDatabase::removeDatabase()` 移除连接。

---

## **3. 常用驱动及示例**

以下是不同数据库驱动的配置示例：

### **（1）SQLite**
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
db.setDatabaseName("test.db");

if (!db.open()) {
    qDebug() << "Failed to connect to database:" << db.lastError().text();
} else {
    qDebug() << "Database connected!";
}
```

### **（2）MySQL**
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QMYSQL");
db.setHostName("localhost");
db.setPort(3306);
db.setUserName("root");
db.setPassword("password");
db.setDatabaseName("test_db");

if (!db.open()) {
    qDebug() << "Failed to connect to MySQL:" << db.lastError().text();
} else {
    qDebug() << "Connected to MySQL!";
}
```

### **（3）PostgreSQL**
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QPSQL");
db.setHostName("localhost");
db.setPort(5432);
db.setUserName("postgres");
db.setPassword("password");
db.setDatabaseName("test_db");

if (!db.open()) {
    qDebug() << "Failed to connect to PostgreSQL:" << db.lastError().text();
} else {
    qDebug() << "Connected to PostgreSQL!";
}
```

---

## **4. 连接名的使用**

如果创建了多个数据库连接，可以通过 `connectionName` 参数区分：
```cpp
QSqlDatabase db1 = QSqlDatabase::addDatabase("QSQLITE", "Conn1");
db1.setDatabaseName("database1.db");

QSqlDatabase db2 = QSqlDatabase::addDatabase("QSQLITE", "Conn2");
db2.setDatabaseName("database2.db");

if (db1.open()) qDebug() << "Conn1 opened!";
if (db2.open()) qDebug() << "Conn2 opened!";

// 使用连接名获取数据库对象
QSqlDatabase db = QSqlDatabase::database("Conn1");
qDebug() << "Database Conn1 name:" << db.databaseName();
```

---

## **5. 常见问题**

### **（1）无效的驱动**
**问题**：调用 `addDatabase()` 时返回无效的数据库对象。

**原因**：
- `type` 参数不正确，未加载对应驱动。
- 驱动未正确安装或配置（如缺少 MySQL 客户端库）。

**解决方案**：
- 确保 `type` 是 `QSqlDatabase::drivers()` 返回的值之一。
- 检查数据库驱动的依赖是否已安装。

### **（2）数据库连接失败**
**问题**：调用 `open()` 方法失败。

**原因**：
- 数据库参数（如用户名、密码、主机名、端口等）错误。
- 数据库服务未启动。

**解决方案**：
- 确保数据库服务正在运行。
- 验证连接参数的正确性。

### **（3）默认连接冲突**
**问题**：多个连接未指定 `connectionName`，导致覆盖。

**解决方案**：
- 为每个连接提供唯一的名称。

---

## **6. 移除数据库连接**

当不再需要某个数据库连接时，可以调用 `QSqlDatabase::removeDatabase()` 释放资源：
```cpp
QSqlDatabase::removeDatabase("Conn1");
```
**注意**：
- 在调用 `removeDatabase()` 之前，确保所有使用该连接的对象（如 `QSqlQuery`）都已销毁，否则会导致未定义行为。

---

## **7. 总结**

`QSqlDatabase::addDatabase()` 是 Qt 数据库操作的起点，它负责注册和管理数据库连接。关键点包括：
1. 确保驱动已正确安装。
2. 配置正确的连接参数。
3. 始终为多连接提供唯一的 `connectionName`。
4. 使用后记得移除连接以释放资源。

通过合理的配置和管理，可以高效地利用 Qt 的数据库功能。

## QSqlDatabase::setDatabaseName() 函数 详解

`QSqlDatabase::setDatabaseName()` 是 Qt 中用于设置数据库名称或路径的重要方法。它适用于各种数据库驱动，但每种驱动对该方法的使用方式可能有所不同。

---

## **1. 函数定义**

```cpp
void QSqlDatabase::setDatabaseName(const QString &name)
```

### **参数**
- **`name`**：
  - 字符串，表示数据库的名称或路径。
  - 不同的数据库驱动对此参数的意义和格式有不同的要求。

---

## **2. 功能说明**
- **SQLite**：
  设置 SQLite 数据库文件的路径。如果文件不存在，会在调用 `open()` 时创建。
  ```cpp
  db.setDatabaseName("test.db"); // 设置数据库文件名
  ```

- **MySQL/PostgreSQL**：
  设置要连接的数据库名称（在服务器上的逻辑名称）。
  ```cpp
  db.setDatabaseName("my_database"); // 设置数据库名称
  ```

- **ODBC**：
  设置数据源名称 (DSN)。
  ```cpp
  db.setDatabaseName("MyDSN"); // 设置 ODBC 数据源名称
  ```

- **其他数据库**：
  对于其他数据库，请参考对应驱动的文档。

---

## **3. 用法示例**

### **（1）SQLite 示例**
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
db.setDatabaseName("test.db"); // SQLite 数据库文件路径

if (!db.open()) {
    qDebug() << "Error opening database:" << db.lastError().text();
} else {
    qDebug() << "SQLite database opened successfully!";
}
```

### **（2）MySQL 示例**
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QMYSQL");
db.setHostName("localhost");
db.setPort(3306);
db.setUserName("root");
db.setPassword("password");
db.setDatabaseName("test_db"); // MySQL 数据库名称

if (!db.open()) {
    qDebug() << "Error connecting to MySQL:" << db.lastError().text();
} else {
    qDebug() << "Connected to MySQL successfully!";
}
```

### **（3）ODBC 示例**
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QODBC");
db.setDatabaseName("MyDSN"); // ODBC 数据源名称
db.setUserName("user");
db.setPassword("password");

if (!db.open()) {
    qDebug() << "Error connecting via ODBC:" << db.lastError().text();
} else {
    qDebug() << "Connected to ODBC successfully!";
}
```

---

## **4. 常见问题**

### **（1）SQLite 文件不存在**
**问题**：设置的 SQLite 文件路径不存在时，`open()` 会创建一个空的数据库文件。

**解决方案**：
- 确保文件路径正确，避免意外创建无效的数据库文件。
- 如果需要检查文件是否存在，可以使用 `QFile::exists()`：
  ```cpp
  QString dbPath = "test.db";
  if (!QFile::exists(dbPath)) {
      qDebug() << "Database file does not exist!";
  }
  db.setDatabaseName(dbPath);
  ```

### **（2）MySQL 数据库名称错误**
**问题**：指定的 MySQL 数据库名称不存在时，`open()` 会返回失败。

**解决方案**：
- 确保数据库名称已在 MySQL 服务器上创建：
  ```sql
  CREATE DATABASE test_db;
  ```

### **（3）ODBC DSN 配置错误**
**问题**：`setDatabaseName()` 中的 DSN 名称未正确配置。

**解决方案**：
- 确保 DSN 已正确配置，并可以通过 ODBC 数据源管理器访问。

---

## **5. 注意事项**

1. **调用顺序**：
   - 在调用 `QSqlDatabase::open()` 之前，必须先设置数据库名称。
   - 示例：
     ```cpp
     QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
     db.setDatabaseName("test.db");
     if (db.open()) {
         // 成功连接
     }
     ```

2. **数据库名称的格式**：
   - SQLite：文件路径（如 `test.db` 或 `/path/to/test.db`）。
   - MySQL/PostgreSQL：逻辑数据库名称。
   - ODBC：数据源名称（DSN）。

3. **错误处理**：
   - 使用 `QSqlDatabase::lastError()` 获取连接失败的详细信息：
     ```cpp
     if (!db.open()) {
         qDebug() << "Error:" << db.lastError().text();
     }
     ```

4. **默认值**：
   - 如果未设置 `setDatabaseName()` 或传递空字符串，许多驱动会返回错误，提示缺少数据库名称。

---

## **6. 总结**

`QSqlDatabase::setDatabaseName()` 是配置数据库连接的关键方法，作用因数据库驱动而异。正确使用此方法时需注意以下几点：
- 根据所用驱动提供正确的数据库名称或路径格式。
- 在调用 `open()` 之前必须设置数据库名称。
- 如果连接失败，通过 `QSqlDatabase::lastError()` 获取详细错误信息。

## QSqlDatabase::open() 函数 详解

`QSqlDatabase::open()` 是 Qt 数据库模块中用于打开数据库连接的关键方法。成功调用该方法后，应用程序可以通过 `QSqlQuery` 或其他方式与数据库交互。

---

## **1. 函数定义**

```cpp
bool QSqlDatabase::open()
```
```cpp
bool QSqlDatabase::open(const QString &user, const QString &password)
```

### **参数**
1. **无参数版本**：
   - 使用之前通过 `setUserName()` 和 `setPassword()` 设置的用户名和密码打开连接。

2. **参数版本**：
   - `user`：用于登录数据库的用户名。
   - `password`：对应用户的密码。

### **返回值**
- **`true`**：成功打开数据库连接。
- **`false`**：打开失败。

---

## **2. 功能说明**

- **打开数据库连接**：
  - 如果数据库连接参数（如驱动、主机名、用户名、密码等）正确，`open()` 将建立与数据库的连接。
  
- **错误处理**：
  - 如果连接失败，可以使用 `QSqlDatabase::lastError()` 获取详细的错误信息。

- **多次调用**：
  - 如果连接已经打开，调用 `open()` 会直接返回 `true`，而不会重新打开连接。

---

## **3. 使用示例**

### **（1）SQLite 示例**
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
db.setDatabaseName("test.db");

if (!db.open()) {
    qDebug() << "Failed to open SQLite database:" << db.lastError().text();
} else {
    qDebug() << "SQLite database opened successfully!";
}
```

### **（2）MySQL 示例**
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QMYSQL");
db.setHostName("localhost");
db.setPort(3306);
db.setUserName("root");
db.setPassword("password");
db.setDatabaseName("test_db");

if (!db.open()) {
    qDebug() << "Failed to connect to MySQL:" << db.lastError().text();
} else {
    qDebug() << "Connected to MySQL successfully!";
}
```

### **（3）指定用户名和密码**
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QPSQL");
db.setHostName("localhost");
db.setPort(5432);
db.setDatabaseName("test_db");

if (!db.open("postgres", "password")) {
    qDebug() << "Failed to connect to PostgreSQL:" << db.lastError().text();
} else {
    qDebug() << "Connected to PostgreSQL successfully!";
}
```

---

## **4. 注意事项**

### **（1）驱动的正确性**
在调用 `open()` 之前，必须通过 `QSqlDatabase::addDatabase()` 指定正确的驱动程序。例如：
- SQLite：`QSQLITE`
- MySQL：`QMYSQL`
- PostgreSQL：`QPSQL`

**示例：**
```cpp
QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE"); // 确保驱动名称正确
db.setDatabaseName("test.db");
```

---

### **（2）检查连接参数**
在调用 `open()` 之前，必须确保已正确设置数据库连接参数：
- **`setDatabaseName()`**：必须为大多数数据库设置。
- **`setHostName()`** 和 **`setPort()`**：适用于 MySQL、PostgreSQL 等。
- **`setUserName()`** 和 **`setPassword()`**：适用于需要身份验证的数据库。

---

### **（3）错误处理**
如果 `open()` 返回 `false`，需要使用 `lastError()` 检查错误原因。

**示例：**
```cpp
if (!db.open()) {
    qDebug() << "Error:" << db.lastError().text();
}
```

常见错误：
- **驱动错误**：驱动未正确加载。
- **参数错误**：主机名、用户名或密码不正确。
- **权限问题**：数据库用户没有足够的权限。

---

### **（4）多次调用 `open()`**
重复调用 `open()` 不会重新建立连接。如果连接已经打开，`open()` 会直接返回 `true`。

---

### **（5）连接释放**
调用 `open()` 成功后，必须在合适的时机关闭和移除连接：
```cpp
db.close();
QSqlDatabase::removeDatabase("defaultConnection");
```
**注意**：在调用 `removeDatabase()` 之前，确保所有与该连接相关的对象（如 `QSqlQuery`）都已销毁。

---

## **5. 常见问题**

### **（1）连接失败**
**问题**：调用 `open()` 返回 `false`。
**原因**：
- 数据库驱动未正确加载。
- 数据库参数错误。
- 数据库服务未运行。
**解决方案**：
- 检查驱动是否正确。
- 确保数据库参数设置正确。
- 确认数据库服务是否启动。

### **（2）SQLite 数据库文件无法创建**
**问题**：SQLite 数据库文件不存在，且无法创建。
**原因**：
- 文件路径无效或无写权限。
**解决方案**：
- 确保路径有效并有写权限。

---

## **6. 总结**

- `QSqlDatabase::open()` 是打开数据库连接的关键方法，需在设置正确的数据库参数后调用。
- 对于失败的连接，可以通过 `QSqlDatabase::lastError()` 获取详细信息。
- 必须合理管理连接的生命周期，在不需要时关闭和移除连接以释放资源。

通过合理的参数配置和错误处理，可以确保与数据库的连接高效且稳定。