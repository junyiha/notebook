---
category: DeepLearning
date: 2025-01-05 09:00:00 +0800
layout: post
title: ov::CompiledModel
tag: OpenVINO
---
## 摘要

+ ov::CompiledModel 类 相关学习笔记

<!--more-->

## ov::CompiledModel::create_infer_request() 函数 详解

`ov::CompiledModel::create_infer_request()` 是 OpenVINO Runtime 中用于创建推理请求的关键函数之一。推理请求是执行模型推理的具体实例，支持同步和异步两种推理方式。

---

## **函数定义**
```cpp
ov::InferRequest create_infer_request() const;
```

### **返回值**
- 返回一个 `ov::InferRequest` 对象。
  - `ov::InferRequest` 是执行推理操作的主要接口。
  - 它可以配置输入、输出数据，执行推理，并获取推理结果。

---

## **主要功能**
1. **实例化推理请求**
   - 每个推理请求是独立的，可以并行执行推理任务。
   - 通过 `create_infer_request()`，可以创建多个推理请求以实现并发推理。

2. **与编译模型绑定**
   - 创建的 `ov::InferRequest` 绑定到 `ov::CompiledModel`，共享模型的设备和配置。

3. **支持多种推理模式**
   - 提供同步推理（`infer()`）和异步推理（`start_async()`）两种模式，适应不同的应用场景。

---

## **典型用法**

### **1. 创建推理请求**
```cpp
// 初始化 OpenVINO 核心和编译模型
ov::Core core;
auto model = core.read_model("model.xml");
auto compiled_model = core.compile_model(model, "CPU");

// 创建推理请求
ov::InferRequest infer_request = compiled_model.create_infer_request();
```

---

### **2. 配置输入和输出**
#### 设置输入张量：
```cpp
// 获取输入节点信息
auto input = compiled_model.input();

// 创建输入张量
ov::Tensor input_tensor = ov::Tensor(input.get_element_type(), input.get_shape());

// 填充输入数据（假设为 float 类型）
float* input_data = input_tensor.data<float>();
for (size_t i = 0; i < input_tensor.get_size(); ++i) {
    input_data[i] = static_cast<float>(i);
}

// 设置输入到推理请求
infer_request.set_input_tensor(input_tensor);
```

#### 获取输出张量：
```cpp
// 获取输出节点信息
auto output = compiled_model.output();

// 执行推理
infer_request.infer();

// 获取输出张量
ov::Tensor output_tensor = infer_request.get_output_tensor();

// 访问输出数据
float* output_data = output_tensor.data<float>();
for (size_t i = 0; i < output_tensor.get_size(); ++i) {
    std::cout << "Output[" << i << "] = " << output_data[i] << "\n";
}
```

---

### **3. 执行推理**
#### 同步推理：
```cpp
infer_request.infer();
```
- 阻塞调用，直到推理任务完成。
- 简单易用，适合单任务场景。

#### 异步推理：
```cpp
infer_request.start_async();
infer_request.wait();  // 等待推理完成
```
- 非阻塞调用，适合需要在推理期间执行其他任务的场景。

---

### **4. 批量推理**
如果模型支持批量推理，可以一次性设置多个输入样本：
```cpp
ov::Tensor batch_tensor = ov::Tensor(input.get_element_type(), {batch_size, ...});
infer_request.set_input_tensor(batch_tensor);
```

---

## **性能优化**
1. **创建多个推理请求**  
   对于多线程应用，可以通过 `create_infer_request()` 创建多个 `ov::InferRequest` 实例，以最大化硬件利用率。
   ```cpp
   std::vector<ov::InferRequest> infer_requests;
   for (int i = 0; i < num_threads; ++i) {
       infer_requests.push_back(compiled_model.create_infer_request());
   }
   ```

2. **异步推理**  
   异步模式允许应用程序在等待推理结果的同时执行其他任务，提高吞吐量。

3. **输入输出预分配**  
   预先分配输入输出张量，避免重复创建张量导致的性能开销。

---

## **注意事项**
1. **线程安全性**
   - 单个 `ov::InferRequest` 对象不是线程安全的，但可以通过多个推理请求实现多线程推理。
   - 确保每个线程使用独立的 `ov::InferRequest`。

2. **设备资源限制**
   - 每个推理请求会占用设备资源，大量并发请求可能导致资源不足，应根据硬件能力合理配置并发数量。

3. **异步推理回调**
   - 异步模式下可以设置回调函数，处理推理完成后的任务：
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

## **总结**
`ov::CompiledModel::create_infer_request()` 是 OpenVINO 推理任务的入口，生成的 `ov::InferRequest` 提供了强大的输入输出配置、同步和异步推理支持。通过灵活使用推理请求，开发者可以高效构建多线程和高吞吐量的推理应用。

## ov::CompiledModel 详解

`ov::CompiledModel` 是 OpenVINO Runtime 的一个核心类，用于表示经过优化和加载到设备上的模型。它是 OpenVINO 推理管道中重要的一环，是通过 `ov::Core` 将一个 `ov::Model` 编译后生成的，包含了针对目标设备（如 CPU、GPU、VPU 等）的优化信息。

以下是 `ov::CompiledModel` 的详细解析，包括其主要功能和常用方法：

---

## **主要功能**
1. **优化模型**
   - `ov::CompiledModel` 表示经过优化的模型，与设备无关的原始模型相比，它经过了针对硬件的特定优化，可以更高效地运行推理任务。

2. **设备绑定**
   - 一个 `ov::CompiledModel` 实例绑定到特定的硬件设备（如 CPU、GPU 等）。编译过程会为目标设备生成最优的执行计划。

3. **推理支持**
   - 使用 `ov::CompiledModel` 可以创建推理请求（`ov::InferRequest`）来执行模型推理。

4. **模型信息访问**
   - 提供接口获取模型输入、输出的元信息（如形状、类型等），以及编译时的配置。

---

## **主要方法**
以下是 `ov::CompiledModel` 的一些常用方法及其作用：

### 1. **获取输入和输出信息**
```cpp
std::vector<ov::Output<const ov::Node>> inputs = compiled_model.inputs();
std::vector<ov::Output<const ov::Node>> outputs = compiled_model.outputs();
```
- **`inputs()` 和 `outputs()`**
  - 返回模型的输入和输出节点。
  - 每个节点包含了形状、数据类型等元信息。

---

### 2. **创建推理请求**
```cpp
ov::InferRequest infer_request = compiled_model.create_infer_request();
```
- **`create_infer_request()`**
  - 用于创建 `ov::InferRequest`，以便执行推理任务。
  - 推理请求可以同步或异步执行推理。

---

### 3. **获取编译模型的元信息**
```cpp
std::string model_name = compiled_model.get_friendly_name();
```
- **`get_friendly_name()`**
  - 返回编译模型的友好名称（通常是原始模型的名称）。
  
```cpp
std::string runtime_info = compiled_model.get_runtime_information();
```
- **`get_runtime_information()`**
  - 获取模型的运行时信息，例如设备、优化信息等。

---

### 4. **设置和获取编译选项**
```cpp
compiled_model.set_property({{"key", "value"}});
auto property = compiled_model.get_property("key");
```
- **`set_property()` 和 `get_property()`**
  - 设置或查询编译模型的配置选项。
  - 配置选项通常包括性能调优、硬件设置等。

---

### 5. **获取目标设备信息**
```cpp
std::string device_name = compiled_model.get_device_name();
```
- **`get_device_name()`**
  - 返回模型编译的目标设备名称。

---

### **典型用法示例**
```cpp
#include <openvino/openvino.hpp>

int main() {
    // 初始化 OpenVINO Runtime 核心
    ov::Core core;

    // 读取 IR 模型
    auto model = core.read_model("model.xml");

    // 编译模型到目标设备 (例如 CPU)
    ov::CompiledModel compiled_model = core.compile_model(model, "CPU");

    // 获取模型输入信息
    auto inputs = compiled_model.inputs();
    for (const auto& input : inputs) {
        std::cout << "Input name: " << input.get_any_name() << "\n";
        std::cout << "Input shape: " << input.get_partial_shape() << "\n";
    }

    // 创建推理请求
    auto infer_request = compiled_model.create_infer_request();

    // 执行推理 (以同步方式)
    infer_request.infer();

    // 访问推理结果
    auto output_tensor = infer_request.get_output_tensor();
    std::cout << "Output shape: " << output_tensor.get_shape() << "\n";

    return 0;
}
```

---

## **注意事项**
1. **模型优化**  
   编译后的模型会根据目标设备的特性进行深度优化，因此在推理时效率更高，但模型的可移植性可能会降低。

2. **设备兼容性**  
   确保模型编译时指定的设备支持目标模型的操作符。如果设备不支持某些操作，可能需要降级到其他设备。

3. **性能调优**  
   编译时可以通过 `set_property` 设置多种优化参数，例如线程数、批处理大小等，进一步提升推理性能。

---

## **结论**
`ov::CompiledModel` 是 OpenVINO 推理过程中不可或缺的一部分，它提供了与硬件无缝集成的优化模型执行能力。通过灵活使用其方法，用户可以高效地完成从模型加载到推理结果获取的整个流程。