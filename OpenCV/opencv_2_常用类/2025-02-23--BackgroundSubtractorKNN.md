---
category: OpenCV
date: 2025-02-23 09:00:00 +0800
layout: post
title: cv::BackgroundSubtractorKNN
tag: OpenCV
---
## 简介

+ cv::BackgroundSubtractorKNN 类相关笔记

<!--more-->

## OpenCV cv::BackgroundSubtractorKNN 类 详细解释

## **`cv::BackgroundSubtractorKNN` 详解**
`cv::BackgroundSubtractorKNN` 是 OpenCV 提供的一种背景建模方法，基于 K-近邻（K-Nearest Neighbors, KNN）算法，用于背景/前景分割。它与 `cv::BackgroundSubtractorMOG2` 相似，但在一些情况下对动态背景或光照变化的适应性更好。

---

## **1. `cv::BackgroundSubtractorKNN` 构造函数**
```cpp
cv::Ptr<cv::BackgroundSubtractorKNN> bgSubtractor = cv::createBackgroundSubtractorKNN(
    int history = 500, 
    double dist2Threshold = 400.0, 
    bool detectShadows = true
);
```
**参数说明：**
- `history`：用于背景建模的历史帧数（默认 500）。
- `dist2Threshold`：平方欧几里得距离的阈值（默认 400.0），用于决定像素是否属于前景。值越大，前景检测更宽松。
- `detectShadows`：是否检测阴影（默认 `true`，阴影区域在前景掩码中标记为 127）。

---

## **2. `apply()` - 应用背景分割**
```cpp
void apply(InputArray image, OutputArray fgmask, double learningRate = -1);
```
**功能**：对输入图像进行背景建模，并输出前景掩码。

**参数说明：**
- `image`：输入图像（`cv::Mat`）。
- `fgmask`：输出的前景掩码（`cv::Mat`，前景像素值为 255，阴影为 127）。
- `learningRate`：学习率，控制背景更新速度：
  - `> 0`：显式设置学习率（通常 `0.01 - 0.1`）。
  - `= 0`：背景不更新。
  - `< 0`（默认）：自动计算学习率。

**示例代码：**
```cpp
cv::Mat frame, fgMask;
cv::VideoCapture cap(0);
cv::Ptr<cv::BackgroundSubtractorKNN> bgSubtractor = cv::createBackgroundSubtractorKNN();

while (cap.read(frame)) {
    bgSubtractor->apply(frame, fgMask);
    cv::imshow("Foreground Mask", fgMask);
    if (cv::waitKey(30) == 27) break; // 按 ESC 退出
}
```

---

## **3. `getBackgroundImage()` - 获取背景图像**
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

## **4. `cv::BackgroundSubtractorKNN` 参数调整**

### **(1) 设置/获取历史帧数**
```cpp
void setHistory(int history);
int getHistory() const;
```
- `setHistory(n)`：设置背景建模所需的历史帧数。
- `getHistory()`：获取当前的历史帧数。

### **(2) 设置/获取阈值**
```cpp
void setDist2Threshold(double dist2Threshold);
double getDist2Threshold() const;
```
- `setDist2Threshold(v)`：设置像素分类的欧几里得距离阈值。
- `getDist2Threshold()`：获取当前的距离阈值。

### **(3) 设置/获取阴影检测**
```cpp
void setDetectShadows(bool detectShadows);
bool getDetectShadows() const;
```
- `setDetectShadows(true/false)`：开启/关闭阴影检测。
- `getDetectShadows()`：返回当前阴影检测状态。

### **(4) 设置/获取 KNN 近邻数**
```cpp
void setkNNSamples(int k);
int getkNNSamples() const;
```
- `setkNNSamples(k)`：设置用于 KNN 计算的近邻样本数（默认 3）。
- `getkNNSamples()`：获取当前 KNN 近邻样本数。

### **(5) 设置/获取背景比例**
```cpp
void setBackgroundRatio(double ratio);
double getBackgroundRatio() const;
```
- `setBackgroundRatio(r)`：设置背景像素占据的比例（默认 0.5）。
- `getBackgroundRatio()`：获取背景比例。

### **(6) 设置/获取阴影阈值**
```cpp
void setShadowThreshold(double threshold);
double getShadowThreshold() const;
```
- `setShadowThreshold(t)`：设置阴影检测的阈值（默认 0.5）。
- `getShadowThreshold()`：获取当前的阴影阈值。

---

## **5. `cv::BackgroundSubtractorMOG2` vs `cv::BackgroundSubtractorKNN`**
| 特性               | `MOG2`              | `KNN`         |
| ------------------ | ------------------- | ------------- |
| 算法               | 高斯混合模型（GMM） | K-近邻（KNN） |
| 计算复杂度         | 较低                | 略高          |
| 适用于             | 静态或少量动态背景  | 复杂动态背景  |
| 对光照变化的适应性 | 一般                | 更强          |
| 阴影检测           | 支持                | 支持          |
| 前景检测准确度     | 较好                | 更稳定        |
| 处理速度           | 快                  | 略慢          |

---

## **6. `cv::BackgroundSubtractorKNN` 示例代码**
```cpp
#include <opencv2/opencv.hpp>

int main() {
    cv::VideoCapture cap(0);
    if (!cap.isOpened()) {
        std::cerr << "无法打开摄像头！" << std::endl;
        return -1;
    }

    cv::Ptr<cv::BackgroundSubtractorKNN> bgSubtractor = cv::createBackgroundSubtractorKNN();
    bgSubtractor->setDist2Threshold(400.0); // 设置前景检测阈值
    bgSubtractor->setDetectShadows(true);   // 开启阴影检测

    cv::Mat frame, fgMask, bgImage;
    
    while (true) {
        cap >> frame;
        if (frame.empty()) break;

        bgSubtractor->apply(frame, fgMask);
        
        // 获取背景图像
        bgSubtractor->getBackgroundImage(bgImage);

        cv::imshow("Frame", frame);
        cv::imshow("Foreground Mask", fgMask);
        if (!bgImage.empty()) cv::imshow("Background Image", bgImage);

        if (cv::waitKey(30) == 27) break; // 按 ESC 退出
    }

    return 0;
}
```

---

## **7. 总结**
| 方法                                            | 作用                     |
| ----------------------------------------------- | ------------------------ |
| `apply()`                                       | 计算前景掩码             |
| `getBackgroundImage()`                          | 获取背景图像             |
| `setHistory()` / `getHistory()`                 | 设置/获取历史帧数        |
| `setDist2Threshold()` / `getDist2Threshold()`   | 设置/获取前景分类阈值    |
| `setDetectShadows()` / `getDetectShadows()`     | 开启/关闭阴影检测        |
| `setkNNSamples()` / `getkNNSamples()`           | 设置/获取 KNN 近邻样本数 |
| `setBackgroundRatio()` / `getBackgroundRatio()` | 设置/获取背景比例        |
| `setShadowThreshold()` / `getShadowThreshold()` | 设置/获取阴影检测阈值    |

`cv::BackgroundSubtractorKNN` 适用于动态背景和光照变化较大的场景，适合复杂环境下的前景检测任务。