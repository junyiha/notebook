---
category: DeepLearning
date: 2025-01-05 09:00:00 +0800
layout: post
title: ov::InferRequest
tag: OpenVINO
---
## 摘要

+ ov::InferRequest 类 相关学习笔记

<!--more-->

## ov::InferRequest 详解

`ov::InferRequest` 是 OpenVINO Runtime 的核心类之一，代表一次推理执行的具体实例。它提供了配置输入、执行推理、获取结果等功能，是 OpenVINO 推理任务的基础。

---

## **主要功能**

1. **输入输出配置**  
   设置模型输入和输出张量，准备推理数据。

2. **推理执行**  
   支持同步和异步两种推理模式，适应不同的应用场景。

3. **获取推理结果**  
   提供接口获取推理输出张量。

4. **多线程支持**  
   可以创建多个 `InferRequest` 实例，实现并行推理。

5. **异步回调**  
   在异步推理完成后执行指定的回调函数，用于处理推理结果。

---

## **创建 InferRequest**

`ov::InferRequest` 通常通过 `ov::CompiledModel::create_infer_request()` 创建：

```cpp
ov::Core core;
auto model = core.read_model("model.xml");
auto compiled_model = core.compile_model(model, "CPU");

// 创建推理请求
ov::InferRequest infer_request = compiled_model.create_infer_request();
```

---

## **主要方法**

### 1. **配置输入**
`InferRequest` 提供了多种方式来设置输入数据：

#### 自动匹配输入：
```cpp
ov::Tensor input_tensor = ov::Tensor(input_element_type, input_shape);
infer_request.set_input_tensor(input_tensor);
```

#### 按名称设置输入：
```cpp
infer_request.set_tensor("input_name", input_tensor);
```

#### 获取输入张量：
```cpp
ov::Tensor input_tensor = infer_request.get_input_tensor();
```
- 如果不手动设置，可以通过 `get_input_tensor()` 获取默认输入张量。

---

### 2. **执行推理**
#### 同步推理：
```cpp
infer_request.infer();
```
- 阻塞调用，直到推理完成。

#### 异步推理：
```cpp
infer_request.start_async();
infer_request.wait();
```
- `start_async()`：启动推理但不阻塞主线程。
- `wait()`：阻塞直到推理完成。

---

### 3. **获取输出**
#### 自动获取输出：
```cpp
ov::Tensor output_tensor = infer_request.get_output_tensor();
```

#### 按名称获取输出：
```cpp
ov::Tensor output_tensor = infer_request.get_tensor("output_name");
```

---

### 4. **异步回调**
设置回调函数，在异步推理完成时执行：
```cpp
infer_request.set_callback([](std::exception_ptr exception_ptr) {
    if (exception_ptr) {
        try {
            std::rethrow_exception(exception_ptr);
        } catch (const std::exception& e) {
            std::cerr << "Inference error: " << e.what() << "\n";
        }
    } else {
        std::cout << "Inference completed successfully.\n";
    }
});
infer_request.start_async();
```

---

### 5. **动态批量设置**
动态批量推理可以通过以下方式实现：
```cpp
infer_request.set_batch(4); // 设置批量大小为4
```

---

### 6. **性能计时**
获取推理性能数据：
```cpp
auto perf_counts = infer_request.get_profiling_info();
for (const auto& item : perf_counts) {
    std::cout << "Layer: " << item.node_name << ", Time: " << item.real_time_uSec << " μs\n";
}
```

---

## **典型用法示例**

以下代码展示了从加载模型到获取推理结果的完整流程：

```cpp
#include <openvino/openvino.hpp>
#include <iostream>

int main() {
    // 初始化 OpenVINO Runtime 核心
    ov::Core core;

    // 读取模型
    auto model = core.read_model("model.xml");

    // 编译模型到目标设备
    auto compiled_model = core.compile_model(model, "CPU");

    // 创建推理请求
    ov::InferRequest infer_request = compiled_model.create_infer_request();

    // 配置输入
    auto input = compiled_model.input();
    ov::Tensor input_tensor = ov::Tensor(input.get_element_type(), input.get_shape());
    float* input_data = input_tensor.data<float>();
    for (size_t i = 0; i < input_tensor.get_size(); ++i) {
        input_data[i] = static_cast<float>(i);
    }
    infer_request.set_input_tensor(input_tensor);

    // 执行同步推理
    infer_request.infer();

    // 获取输出
    auto output = compiled_model.output();
    ov::Tensor output_tensor = infer_request.get_output_tensor();
    float* output_data = output_tensor.data<float>();
    for (size_t i = 0; i < output_tensor.get_size(); ++i) {
        std::cout << "Output[" << i << "] = " << output_data[i] << "\n";
    }

    return 0;
}
```

---

## **多线程推理**

通过创建多个 `InferRequest` 实例实现多线程推理：
```cpp
std::vector<ov::InferRequest> infer_requests;
for (int i = 0; i < num_threads; ++i) {
    infer_requests.push_back(compiled_model.create_infer_request());
}

// 在多个线程中执行推理
#pragma omp parallel for
for (int i = 0; i < num_threads; ++i) {
    infer_requests[i].infer();
}
```

---

## **注意事项**

1. **线程安全性**  
   单个 `InferRequest` 不是线程安全的。多线程环境下，应为每个线程创建独立的 `InferRequest`。

2. **设备资源限制**  
   每个 `InferRequest` 占用一定的设备资源。创建过多实例可能导致性能下降，应根据硬件能力合理设置实例数量。

3. **动态形状支持**  
   如果模型支持动态形状，需根据每次输入数据的具体形状设置输入张量。

4. **性能调优**  
   - 使用 `set_batch()` 或硬件特定配置选项优化性能。
   - 获取性能计时信息，分析模型瓶颈。

---

## **总结**

`ov::InferRequest` 是 OpenVINO 推理执行的核心接口，提供了灵活的输入输出配置和推理方式。通过结合同步和异步推理、回调机制和性能调优，开发者可以高效实现从单任务到多任务、多线程的推理应用。