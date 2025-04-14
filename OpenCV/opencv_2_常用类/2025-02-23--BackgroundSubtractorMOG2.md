---
category: OpenCV
date: 2025-02-23 09:00:00 +0800
layout: post
title: cv::BackgroundSubtractorMOG2
tag: OpenCV
---
## 简介

+ cv::BackgroundSubtractorMOG2 类相关笔记

<!--more-->

## OpenCV cv::BackgroundSubtractorMOG2 类 详细解释

OpenCV 中的 `cv::BackgroundSubtractorMOG2` 是一种基于高斯混合模型（Gaussian Mixture Model, GMM）的背景减除算法，专门用于从视频序列中提取运动前景目标（如行人、车辆等）。它是经典 MOG（Mixture of Gaussians）的改进版本，支持自适应建模和阴影检测。以下是详细解释：

---

### **1. 核心原理**
- **高斯混合模型 (GMM)**：  
  对每个像素的颜色值（如 RGB 或灰度）建模为多个高斯分布的混合。动态场景中的像素可能属于背景（稳定的高斯分布）或前景（突变的高斯分布）。
- **自适应更新**：  
  根据新帧的数据实时更新高斯分布的参数（均值、方差、权重），适应光照变化或背景运动。
- **阴影检测**：  
  通过颜色空间（如 HSV）分析，区分真实前景和阴影区域。

---

### **2. 类功能与参数**
#### **创建对象**
```cpp
cv::Ptr<cv::BackgroundSubtractorMOG2> bg_subtractor = cv::createBackgroundSubtractorMOG2();
```
- **构造函数参数**（可省略，使用默认值）：
  ```cpp
  createBackgroundSubtractorMOG2(
      int history = 500,        // 用于建模的帧数（记忆长度）
      double varThreshold = 16, // 方差阈值，决定像素是否匹配背景模型
      bool detectShadows = true // 是否检测阴影
  )
  ```

#### **关键参数详解**
| 参数名            | 作用                                                                 |
| ----------------- | -------------------------------------------------------------------- |
| `history`         | 控制模型更新的速度。值越大，模型对变化的适应越慢，但更稳定。         |
| `varThreshold`    | 像素与背景模型的匹配阈值。值越小，对变化越敏感，可能产生更多噪声。   |
| `detectShadows`   | 若为 `true`，阴影会被标记为灰色（通常值为 127），需后续处理过滤。    |
| `backgroundRatio` | 背景模型的高斯分布权重占比阈值（默认 0.9），用于选择保留的分布数量。 |
| `nmixtures`       | 每个像素的高斯分布最大数量（默认 5）。动态场景需更多分布。           |

---

### **3. 使用步骤**
#### **基本流程**
```cpp
cv::VideoCapture cap("video.mp4");
cv::Ptr<cv::BackgroundSubtractorMOG2> bg_sub = cv::createBackgroundSubtractorMOG2();
cv::Mat frame, fg_mask;

while (cap.read(frame)) {
    // 更新背景模型并获取前景掩码
    bg_sub->apply(frame, fg_mask);
    
    // 可选：后处理（去噪、形态学操作等）
    cv::threshold(fg_mask, fg_mask, 250, 255, cv::THRESH_BINARY);
    cv::medianBlur(fg_mask, fg_mask, 5);
    
    // 显示结果
    cv::imshow("Foreground", fg_mask);
    if (cv::waitKey(30) == 27) break;
}
```

#### **获取背景图像**
```cpp
cv::Mat background;
bg_sub->getBackgroundImage(background); // 需模型稳定后调用
cv::imshow("Background", background);
```

---

### **4. 参数调优建议**
#### **场景适应性**
- **静态背景**：  
  减小 `history`（如 200）以快速适应短暂变化（如飘动的树叶）。  
- **动态背景**（如水面波纹）：  
  增加 `nmixtures`（如 5~7）和 `varThreshold`（如 20~30），减少误检。  
- **光照突变**：  
  增大 `history`（如 1000）使模型更新更缓慢，避免将突然光照变化误判为前景。

#### **阴影处理**
- 启用 `detectShadows` 后，通过阈值过滤阴影：
  ```cpp
  cv::threshold(fg_mask, fg_mask, 200, 255, cv::THRESH_BINARY); // 去除灰色阴影
  ```

#### **性能优化**
- 对输入帧进行下采样（降低分辨率）可加速处理，但会损失细节。
- 使用灰度图像（而非 RGB）减少计算量：
  ```cpp
  cv::cvtColor(frame, frame, cv::COLOR_BGR2GRAY);
  ```

---

### **5. 示例代码（完整流程）**
```cpp
#include <opencv2/opencv.hpp>

int main() {
    cv::VideoCapture cap("input.mp4");
    if (!cap.isOpened()) {
        std::cerr << "无法打开视频！" << std::endl;
        return -1;
    }

    // 创建 MOG2 背景减除器
    auto bg_sub = cv::createBackgroundSubtractorMOG2(500, 16.0, true);
    cv::Mat frame, fg_mask, bg_image;

    while (cap.read(frame)) {
        // 更新模型并获取前景掩码
        bg_sub->apply(frame, fg_mask);

        // 去除阴影（灰色区域）
        cv::threshold(fg_mask, fg_mask, 200, 255, cv::THRESH_BINARY);

        // 去噪：中值滤波 + 形态学开运算
        cv::medianBlur(fg_mask, fg_mask, 5);
        cv::morphologyEx(fg_mask, fg_mask, cv::MORPH_OPEN, cv::getStructuringElement(cv::MORPH_RECT, {3,3}));

        // 显示结果
        cv::imshow("Input", frame);
        cv::imshow("Foreground Mask", fg_mask);

        // 按 'b' 键显示背景模型
        if (cv::waitKey(30) == 'b') {
            bg_sub->getBackgroundImage(bg_image);
            cv::imshow("Background", bg_image);
        }
        if (cv::waitKey(30) == 27) break;
    }
    return 0;
}
```

---

### **6. 与其他算法的对比**
| 算法     | 特点                                                         |
| -------- | ------------------------------------------------------------ |
| **MOG2** | 自适应高斯分布数量，支持阴影检测，适合动态场景。             |
| **KNN**  | 基于最近邻的快速背景建模，对光照变化敏感，适合静态背景。     |
| **GMG**  | 基于统计初始化，适合固定摄像头场景，但需要较长的初始化时间。 |

---

### **7. 常见问题**
#### **前景掩码噪声多**
- **原因**：`varThreshold` 过小或动态背景干扰。
- **解决**：增大 `varThreshold`，或对输入帧进行高斯模糊预处理：
  ```cpp
  cv::GaussianBlur(frame, frame, {5,5}, 0);
  ```

#### **模型更新过慢**
- **原因**：`history` 值太大。
- **解决**：减小 `history`，如设置为 100~300。

#### **阴影误检为前景**
- **原因**：未启用阴影检测或阈值过滤不当。
- **解决**：确保 `detectShadows=true`，并通过阈值过滤灰色值。

---

### **总结**
`cv::BackgroundSubtractorMOG2` 是 OpenCV 中高效的背景减除工具，适用于监控、交通检测等场景。通过合理调整参数（如 `history`、`varThreshold`）和后处理（去噪、形态学操作），可以显著提升前景提取的准确性。对于复杂场景（如动态背景或剧烈光照变化），建议结合其他算法（如光流法）进行优化。

## OpenCV cv::BackgroundSubtractorMOG2 类 常用函数

`cv::BackgroundSubtractorMOG2` 是 OpenCV 提供的背景建模算法之一，使用高斯混合模型（GMM）来进行背景/前景分割。它是 `cv::BackgroundSubtractor` 的一个派生类，适用于运动检测、目标跟踪等任务。

## `cv::BackgroundSubtractorMOG2` 常用函数

### 1. **构造函数**
```cpp
cv::Ptr<cv::BackgroundSubtractorMOG2> bgSubtractor = cv::createBackgroundSubtractorMOG2(int history = 500, double varThreshold = 16, bool detectShadows = true);
```
**参数说明：**
- `history`：用于背景建模的历史帧数（默认 500）。
- `varThreshold`：阈值，用于确定像素是否属于前景（默认 16）。
- `detectShadows`：是否检测阴影（默认 `true`，会在前景掩码中将阴影区域标记为 127）。

---

### 2. **应用背景分割**
```cpp
void apply(InputArray image, OutputArray fgmask, double learningRate = -1);
```
**功能**：对输入帧进行背景建模，并输出前景掩码。

**参数说明：**
- `image`：输入的单帧图像（`cv::Mat`）。
- `fgmask`：输出的前景掩码（`cv::Mat`，二值图像，前景为 255，阴影区域若启用则为 127）。
- `learningRate`：学习率，控制背景更新速度。`-1` 表示自动计算。

**示例代码：**
```cpp
cv::Mat frame, fgMask;
cv::VideoCapture cap(0);
cv::Ptr<cv::BackgroundSubtractorMOG2> bgSubtractor = cv::createBackgroundSubtractorMOG2();

while (cap.read(frame)) {
    bgSubtractor->apply(frame, fgMask);
    cv::imshow("Foreground Mask", fgMask);
    if (cv::waitKey(30) == 27) break; // 按 ESC 退出
}
```

---

### 3. **设置/获取参数**
`cv::BackgroundSubtractorMOG2` 提供了一些参数的 `set` 和 `get` 方法，用于调整背景建模的行为。

#### (1) 设置/获取历史帧数
```cpp
void setHistory(int history);
int getHistory() const;
```
- `setHistory(n)`：设置背景建模所需的历史帧数。
- `getHistory()`：获取当前的历史帧数。

#### (2) 设置/获取方差阈值
```cpp
void setVarThreshold(double varThreshold);
double getVarThreshold() const;
```
- `setVarThreshold(v)`：设置高斯模型的方差阈值，控制像素分类的敏感度。
- `getVarThreshold()`：获取当前的方差阈值。

#### (3) 设置/获取阴影检测
```cpp
void setDetectShadows(bool detectShadows);
bool getDetectShadows() const;
```
- `setDetectShadows(true/false)`：开启/关闭阴影检测。
- `getDetectShadows()`：返回当前阴影检测状态。

#### (4) 设置/获取背景比例
```cpp
void setBackgroundRatio(double ratio);
double getBackgroundRatio() const;
```
- `setBackgroundRatio(r)`：设置背景像素占据的比例（默认 0.9）。
- `getBackgroundRatio()`：获取背景比例。

#### (5) 设置/获取阴影阈值
```cpp
void setShadowThreshold(double threshold);
double getShadowThreshold() const;
```
- `setShadowThreshold(t)`：设置阴影检测的阈值（默认 0.5）。
- `getShadowThreshold()`：获取当前的阴影阈值。

---

### 4. **获取背景图像**
```cpp
void getBackgroundImage(OutputArray backgroundImage) const;
```
**功能**：获取当前计算的背景图像。

**示例代码：**
```cpp
cv::Mat bgImage;
bgSubtractor->getBackgroundImage(bgImage);
cv::imshow("Background Image", bgImage);
```

---

## 总结
| 方法                                            | 作用                  |
| ----------------------------------------------- | --------------------- |
| `apply()`                                       | 计算前景掩码          |
| `getBackgroundImage()`                          | 获取背景图像          |
| `setHistory()` / `getHistory()`                 | 设置/获取历史帧数     |
| `setVarThreshold()` / `getVarThreshold()`       | 设置/获取阈值         |
| `setDetectShadows()` / `getDetectShadows()`     | 开启/关闭阴影检测     |
| `setBackgroundRatio()` / `getBackgroundRatio()` | 设置/获取背景比例     |
| `setShadowThreshold()` / `getShadowThreshold()` | 设置/获取阴影检测阈值 |

这就是 `cv::BackgroundSubtractorMOG2` 的主要用法，适用于运动目标检测和视频背景建模等任务。