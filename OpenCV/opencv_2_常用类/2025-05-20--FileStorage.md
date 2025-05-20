---
category: OpenCV
date: 2025-05-20 09:00:00 +0800
layout: post
title: cv::FileStorage
tag: OpenCV
---
## 简介

+ cv::FileStorage 类相关笔记

<!--more-->

## opencv cv::FileStorage 详解

`cv::FileStorage` 是 OpenCV 中用于读写结构化数据（XML、YAML、JSON 格式）的类，常用于保存配置参数、矩阵数据、标量值、自定义对象等。以下是对其功能的详细解析及用法示例：

---

### **核心功能**
1. **支持格式**：XML、YAML、JSON（需 OpenCV 4.5+ 版本）。
2. **数据类型**：
   - 基础类型：`int`, `float`, `double`, `string`
   - OpenCV 对象：`cv::Mat`, `cv::Point`, `cv::Rect`, `cv::Size`, `cv::Scalar` 等
   - STL 容器：`std::vector`, `std::map`
   - 自定义数据结构（需重载序列化接口）

---

### **基本用法**
#### **1. 打开文件**
```cpp
#include <opencv2/opencv.hpp>
using namespace cv;

// 写入模式（WRITE）
FileStorage fs("data.yaml", FileStorage::WRITE);

// 读取模式（READ）
FileStorage fs("data.yaml", FileStorage::READ);

// 追加模式（APPEND，修改现有文件）
FileStorage fs("data.yaml", FileStorage::APPEND);
```

#### **2. 写入数据**
使用 `<<` 运算符写入键值对或序列：
```cpp
fs << "frame_count" << 5;                // 标量
fs << "camera_matrix" << Mat::eye(3,3, CV_64F); // 矩阵
fs << "points" << "[";                   // 序列（列表）
fs << Point2f(1,2) << Point2f(3,4);
fs << "]";                               // 结束序列

// 字典（映射）
fs << "config" << "{";
fs << "width" << 1280 << "height" << 720;
fs << "fps" << 30.0;
fs << "}";
```

#### **3. 读取数据**
使用 `operator[]` 和 `>>` 读取数据：
```cpp
int frameCount;
Mat cameraMat;
std::vector<Point2f> points;

fs["frame_count"] >> frameCount;
fs["camera_matrix"] >> cameraMat;

FileNode node = fs["points"];
for (FileNodeIterator it = node.begin(); it != node.end(); ++it) {
    Point2f p;
    *it >> p;
    points.push_back(p);
}
```

#### **4. 关闭文件**
```cpp
fs.release();  // 确保数据写入磁盘
```

---

### **高级用法**
#### **1. 自定义对象的序列化**
为自定义类型实现序列化接口：
```cpp
struct MyData {
    int id;
    std::string name;
    cv::Mat data;
};

// 写入函数重载
void write(FileStorage& fs, const MyData& x) {
    fs << "{"
       << "id" << x.id
       << "name" << x.name
       << "data" << x.data
       << "}";
}

// 读取函数重载
void read(const FileNode& node, MyData& x, const MyData& default_val = MyData()) {
    if (node.empty()) x = default_val;
    else {
        node["id"] >> x.id;
        node["name"] >> x.name;
        node["data"] >> x.data;
    }
}

// 使用自定义对象
MyData data;
fs << "my_data" << data;
fs["my_data"] >> data;
```

#### **2. 检查文件是否成功打开**
```cpp
if (!fs.isOpened()) {
    std::cerr << "Failed to open file!" << std::endl;
    return;
}
```

#### **3. 处理空节点**
```cpp
FileNode node = fs["non_existing_key"];
if (node.empty()) {
    std::cout << "Key not found." << std::endl;
}
```

---

### **文件格式示例（YAML）**
```yaml
%YAML:1.0
frame_count: 5
camera_matrix: !!opencv-matrix
   rows: 3
   cols: 3
   dt: d
   data: [ 1., 0., 0., 0., 1., 0., 0., 0., 1. ]
points:
   - [ 1., 2. ]
   - [ 3., 4. ]
config:
   width: 1280
   height: 720
   fps: 30.
```

---

### **注意事项**
1. **文件扩展名**：`.xml`, `.yaml`, `.json` 决定输出格式。
2. **JSON 支持**：需 OpenCV 4.5+，且写入时需显式指定格式（如 `FileStorage::WRITE | FileStorage::FORMAT_JSON`）。
3. **性能**：频繁读写大文件时，优先考虑二进制格式（如 `.bin`）。
4. **编码问题**：非 ASCII 字符建议使用 UTF-8。

---

通过 `cv::FileStorage`，可以高效管理结构化数据，适用于参数配置、模型持久化、实验结果保存等场景。

## opencv cv::FileStorage 常用函数 详解

`cv::FileStorage` 是 OpenCV 中用于读写 XML/YAML/JSON 格式文件的类，常用于存储和加载配置参数、矩阵数据、特征点等。以下是其常用函数及详细说明：

---

### **1. 构造函数与基本操作**
#### **构造函数**
```cpp
cv::FileStorage::FileStorage(); // 默认构造函数
cv::FileStorage::FileStorage(const String& filename, int flags, const String& encoding = String());
```
- **功能**：创建 `FileStorage` 对象。
- **参数**：
  - `filename`: 文件名（如 `"data.yml"`）。
  - `flags`: 操作模式，可选：
    - `FileStorage::READ`: 读取模式。
    - `FileStorage::WRITE`: 写入模式（覆盖现有文件）。
    - `FileStorage::APPEND`: 追加模式（在现有文件末尾添加数据）。
  - `encoding`: 文件编码（通常默认即可）。

---

#### **open()**
```cpp
bool cv::FileStorage::open(const String& filename, int flags, const String& encoding = String());
```
- **功能**：打开文件。
- **返回值**：成功返回 `true`，失败返回 `false`。
- **用途**：配合默认构造函数使用。

---

#### **isOpened()**
```cpp
bool cv::FileStorage::isOpened() const;
```
- **功能**：检查文件是否成功打开。

---

#### **release()**
```cpp
void cv::FileStorage::release();
```
- **功能**：关闭文件并释放资源。写入模式下会自动调用，但建议显式调用。

---

### **2. 写入数据**
#### **写入基本数据**
使用 `<<` 运算符写入键值对或序列：
```cpp
FileStorage fs("data.yml", FileStorage::WRITE);
fs << "key_int" << 42;                // 写入整数
fs << "key_float" << 3.14f;           // 写入浮点数
fs << "key_string" << "Hello";        // 写入字符串
fs << "key_vector" << cv::Vec3i(1,2,3); // 写入向量
fs << "key_mat" << cv::Mat::eye(3,3, CV_32F); // 写入矩阵
```

---

#### **写入嵌套结构**
```cpp
// 写入映射（类似字典）
fs << "config" << "{";
fs << "width" << 640 << "height" << 480 << "fps" << 30;
fs << "}";

// 写入序列（类似数组）
fs << "points" << "[";
fs << cv::Point2f(1, 2) << cv::Point2f(3, 4);
fs << "]";
```

---

### **3. 读取数据**
#### **读取基本数据**
使用 `>>` 运算符或 `FileNode`：
```cpp
FileStorage fs("data.yml", FileStorage::READ);
int val;
fs["key_int"] >> val; // 直接读取

// 使用 FileNode
FileNode node = fs["key_mat"];
cv::Mat mat;
node >> mat;
```

---

#### **检查节点是否存在**
```cpp
if (!fs["non_existing_key"].empty()) {
    // 节点存在
}
```

---

#### **遍历序列或映射**
```cpp
FileNode pointsNode = fs["points"];
if (pointsNode.isSeq()) { // 检查是否为序列
    for (size_t i = 0; i < pointsNode.size(); ++i) {
        cv::Point2f pt;
        pointsNode[i] >> pt;
    }
}

FileNode configNode = fs["config"];
if (configNode.isMap()) { // 检查是否为映射
    int width = (int)configNode["width"];
    int height = (int)configNode["height"];
}
```

---

### **4. 其他实用函数**
#### **getFirstTopLevelNode()**
```cpp
FileNode cv::FileStorage::getFirstTopLevelNode() const;
```
- **功能**：获取文件中的第一个顶层节点（通常用于遍历所有顶层节点）。

---

#### **root()**
```cpp
FileNode cv::FileStorage::root(int streamIdx = 0) const;
```
- **功能**：获取根节点（可通过迭代器遍历子节点）。

---

#### **FileNodeIterator**
```cpp
FileNodeIterator it = fs["points"].begin();
FileNodeIterator end = fs["points"].end();
for (; it != end; ++it) {
    cv::Point2f pt;
    *it >> pt;
}
```

---

### **5. 注意事项**
1. **文件扩展名**：OpenCV 根据扩展名（`.xml`、`.yml`、`.json`）自动选择格式。
2. **JSON 支持**：需 OpenCV 4.5+ 并启用 `OPENCV_JSON` 编译选项。
3. **类型检查**：读取时需确保数据类型匹配，可用 `FileNode::type()` 检查类型。
4. **错误处理**：始终检查 `isOpened()` 和节点是否存在。

---

### **示例代码**
#### **写入文件**
```cpp
cv::FileStorage fs("config.yml", cv::FileStorage::WRITE);
fs << "camera_matrix" << cv::Mat::eye(3, 3, CV_64F);
fs << "dist_coeffs" << cv::Mat::zeros(5, 1, CV_64F);
fs.release();
```

#### **读取文件**
```cpp
cv::FileStorage fs("config.yml", cv::FileStorage::READ);
cv::Mat camera_matrix, dist_coeffs;
fs["camera_matrix"] >> camera_matrix;
fs["dist_coeffs"] >> dist_coeffs;
fs.release();
```

---

通过 `cv::FileStorage`，可以方便地在 OpenCV 中实现结构化数据的持久化存储与加载。

## opencv cv::FileStorage 常用技巧

以下是使用 OpenCV 中 `cv::FileStorage` 的一些常用技巧与注意事项，帮助你高效地读写配置文件（YAML/XML/JSON）以及矩阵、标量、结构化数据等。

---

## 1. 打开与关闭文件

```cpp
// 以写模式打开（若文件存在则覆盖）
cv::FileStorage fs("config.yml", cv::FileStorage::WRITE);

// 以读模式打开
cv::FileStorage fs("config.yml", cv::FileStorage::READ);

// 使用完毕后一定要释放
fs.release();
```

* **WRITE**：创建或覆盖已有文件
* **APPEND**：在已有文件后追加内容
* **READ**：读取已有文件

---

## 2. 基本读写操作

### 写入基本类型

```cpp
fs << "threshold" << 128;
fs << "camera_name" << "MyCamera";
fs << "use_gpu" << true;
```

### 读取基本类型

```cpp
int thresh;
std::string cam;
bool gpu;
fs["threshold"] >> thresh;
fs["camera_name"] >> cam;
fs["use_gpu"] >> gpu;
```

---

## 3. 矩阵、向量与图像

### 写入 `cv::Mat`、`std::vector`

```cpp
cv::Mat K = (cv::Mat_<double>(3,3) << fx, 0, cx, 0, fy, cy, 0,0,1);
std::vector<int> v = {1,2,3,4};

fs << "intrinsics" << K;
fs << "my_list" << v;
```

### 读取

```cpp
cv::Mat K_read;
std::vector<int> v_read;

fs["intrinsics"] >> K_read;
fs["my_list"] >> v_read;
```

---

## 4. 结构化数据：节点与序列

### 写入结构化块

```cpp
fs << "people" << "[";               // 开始序列
for (auto& p : people) {
    fs << "{:"                       // 开始映射
       << "name" << p.name
       << "age"  << p.age
       << "}";
}
fs << "]";                           // 结束序列
```

### 读取

```cpp
cv::FileNode peopleNode = fs["people"];
for (auto it = peopleNode.begin(); it != peopleNode.end(); ++it) {
    std::string name = (std::string)(*it)["name"];
    int age = (int)(*it)["age"];
    // 处理...
}
```

---

## 5. 错误检查

* **检查文件是否成功打开**

  ```cpp
  if (!fs.isOpened()) {
      std::cerr << "无法打开文件！" << std::endl;
      return;
  }
  ```
* **检查节点是否存在**

  ```cpp
  if (fs["param"].empty()) {
      std::cerr << "param 节点不存在！" << std::endl;
  }
  ```

---

## 6. 自动序列化／反序列化自定义类型

可以通过重载 `write` 和 `read` 函数，将自定义结构与 `FileNode` 绑定。

```cpp
struct Point3D { double x,y,z; };

static void write(cv::FileStorage& fs, const std::string&, const Point3D& p) {
    fs << "{" << "x" << p.x << "y" << p.y << "z" << p.z << "}";
}

static void read(const cv::FileNode& node, Point3D& p, const Point3D& default_value = {}) {
    if (node.empty()) {
        p = default_value;
    } else {
        node["x"] >> p.x;
        node["y"] >> p.y;
        node["z"] >> p.z;
    }
}
```

使用时即可直接写入／读取 `Point3D` 对象：

```cpp
Point3D pt{1,2,3};
fs << "pt" << pt;

Point3D pt2;
fs["pt"] >> pt2;
```

---

## 7. 多格式支持

* 默认后缀 `.yml`/`.yaml` → YAML
* 后缀 `.xml` → XML
* 后缀 `.json` → JSON（OpenCV 4.5+ 支持）

---

## 8. 其他小技巧

1. **注释**：YAML 文件支持注释，用 `#` 开头即可。
2. **相对路径**：`FileStorage` 支持相对与绝对路径，可根据需要指定不同目录。
3. **控制缩进与格式**：在写入时可以指定 `fs.release()` 之前修改全局参数，比如 `fs << cv::FileStorage::FORMAT_YAML`。
4. **FileStorageFlags**：如 `cv::FileStorage::BASE64` 可以把二进制数据以 Base64 编码存储。

---

通过以上技巧，你可以灵活地使用 OpenCV 的 `cv::FileStorage` 来管理各种配置文件和数据序列，极大提升工程的可维护性与可读性。