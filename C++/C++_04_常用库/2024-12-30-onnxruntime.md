---
category: Cpp
date: 2024-12-30 09:00:00 +0800
layout: post
title: onnxruntime
tag: CppLibrary
---
## 摘要

+ onnxruntime库相关学习笔记

## onnxruntime库 是什么

**`ONNX Runtime`** 是一个高性能推理引擎，专门用于执行基于 ONNX（Open Neural Network Exchange） 格式的机器学习模型。它由 **微软（Microsoft）** 开发，旨在提供跨平台、高效、灵活的推理支持，支持从云到边缘设备的多种部署场景。

---

### **核心特点**
1. **跨平台支持**：
   - 可在 Windows、Linux、MacOS 上运行，也支持移动设备（iOS 和 Android）和嵌入式系统。

2. **高性能**：
   - 提供高效的硬件加速，支持 CPU、GPU，以及特殊硬件（如 Intel OpenVINO、NVIDIA TensorRT）。

3. **多语言支持**：
   - 提供 Python、C++、C# 等语言绑定，方便开发者集成到不同语言的项目中。

4. **灵活性**：
   - 支持从多种框架（如 PyTorch、TensorFlow、Keras、MXNet）导出的模型。
   - 提供优化工具（如 `onnxruntime-tools`），帮助提升模型推理性能。

5. **云与边缘兼容**：
   - 适用于云端服务和边缘设备（如 IoT 和移动端），满足多种场景的部署需求。

---

### **主要功能**

1. **模型推理（Inference）**：
   - 通过加载 ONNX 格式的模型，快速进行预测或分类。
   - 示例（Python）：
     ```python
     import onnxruntime as ort

     # 加载模型
     session = ort.InferenceSession("model.onnx")

     # 准备输入数据
     input_data = {"input": some_numpy_array}

     # 推理
     output = session.run(None, input_data)
     print(output)
     ```

2. **硬件加速支持**：
   - 自动选择最优的硬件执行提供程序（Execution Provider），如：
     - CPU（默认）
     - GPU（CUDA、DirectML）
     - 专用加速（TensorRT、OpenVINO 等）

3. **动态与静态模型支持**：
   - 兼容静态计算图和动态计算图模型。

4. **优化和量化**：
   - 提供模型优化工具（如模型裁剪、量化）以减少模型大小，提高推理速度。

---

### **使用场景**
1. **Web 服务**：部署深度学习模型进行实时推理（如聊天机器人、图像分类服务）。
2. **移动应用**：将经过优化的 ONNX 模型嵌入到移动端以进行离线预测。
3. **边缘设备**：在嵌入式系统中运行 ONNX 模型（如工业物联网传感器）。
4. **高性能计算**：利用 GPU 或其他加速器大规模推理。

---

### **安装**
使用 pip 安装 ONNX Runtime 的 Python 包：
```bash
pip install onnxruntime
```

如果需要 GPU 支持，可以安装 GPU 版本：
```bash
pip install onnxruntime-gpu
```

---

### **ONNX 的来源**
ONNX 是一种开放的深度学习模型格式，支持不同框架间的模型互操作。通过将模型转换为 ONNX 格式，ONNX Runtime 能够高效加载并执行这些模型。

---

### **ONNX Runtime 的优势**
- **简化开发和部署流程**：无需为每种硬件配置单独编写代码。
- **兼容多框架模型**：同一推理引擎支持从多个框架导出的模型。
- **高效运行**：内置优化能力，可在多种硬件上实现卓越性能。

---

如果你有更具体的需求（如如何优化模型或特定硬件部署），可以告诉我，我会详细解答！


## onnxruntime C++接口 详解

`ONNX Runtime` 提供了功能强大的 C++ 接口，用于高性能地加载和推理 ONNX 模型。以下是对其 C++ 接口的详细解析，包括核心概念、关键类、常用方法及完整示例。

---

### **核心概念与类**

ONNX Runtime 的 C++ API 基于以下核心组件：

1. **`Ort::Env`**：
   - 表示 ONNX Runtime 的环境，所有会话共享一个环境。
   - 主要负责初始化 ONNX Runtime，设置日志等。

2. **`Ort::Session`**：
   - 表示一个模型会话，用于加载和执行 ONNX 模型。
   - 会话加载模型后可以多次推理。

3. **`Ort::MemoryInfo`**：
   - 描述用于输入/输出张量的内存分配信息。

4. **`Ort::Allocator`**：
   - 管理内存分配，用于推理中获取分配的结果张量。

5. **`Ort::Value`**：
   - 表示 ONNX 模型的输入和输出张量。

---

### **关键步骤与方法**

1. **初始化环境**：
   - 使用 `Ort::Env` 创建 ONNX Runtime 环境：
     ```cpp
     Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXRuntime");
     ```

2. **加载模型**：
   - 使用 `Ort::Session` 加载 `.onnx` 模型文件：
     ```cpp
     Ort::SessionOptions session_options;
     session_options.SetIntraOpNumThreads(1);
     Ort::Session session(env, "model.onnx", session_options);
     ```

3. **准备输入和输出**：
   - 创建输入张量并指定维度和数据类型：
     ```cpp
     std::vector<int64_t> input_shape = {1, 3, 224, 224}; // Example shape for an image
     std::vector<float> input_data(1 * 3 * 224 * 224, 1.0f); // Initialize with dummy data
     Ort::MemoryInfo memory_info = Ort::MemoryInfo::CreateCpu(OrtArenaAllocator, OrtMemTypeDefault);
     Ort::Value input_tensor = Ort::Value::CreateTensor<float>(
         memory_info, input_data.data(), input_data.size(), input_shape.data(), input_shape.size());
     ```

4. **获取模型输入/输出名称**：
   - 用于绑定输入和输出：
     ```cpp
     size_t num_input_nodes = session.GetInputCount();
     const char* input_name = session.GetInputName(0, allocator);
     const char* output_name = session.GetOutputName(0, allocator);
     ```

5. **执行推理**：
   - 调用 `Run` 方法执行推理：
     ```cpp
     auto output_tensors = session.Run(
         Ort::RunOptions{nullptr},    // 默认运行选项
         &input_name,                // 输入名称
         &input_tensor,              // 输入张量
         1,                          // 输入数量
         &output_name,               // 输出名称
         1                           // 输出数量
     );
     ```

6. **处理输出**：
   - 输出通常是一个张量，可以通过 `GetTensorMutableData` 获取数据指针：
     ```cpp
     float* output_data = output_tensors[0].GetTensorMutableData<float>();
     ```

---

### **完整示例代码**

下面是一个完整的 C++ 程序，演示如何使用 ONNX Runtime 推理一个 ONNX 模型：

```cpp
#include <onnxruntime/core/providers/cpu/cpu_provider_factory.h>
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>
#include <vector>
#include <iostream>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXRuntime");
    Ort::SessionOptions session_options;
    session_options.SetIntraOpNumThreads(1);

    // 加载 ONNX 模型
    const char* model_path = "model.onnx";
    Ort::Session session(env, model_path, session_options);

    // 获取模型输入信息
    Ort::AllocatorWithDefaultOptions allocator;
    const char* input_name = session.GetInputName(0, allocator);
    std::vector<int64_t> input_shape = {1, 3, 224, 224};
    std::vector<float> input_data(1 * 3 * 224 * 224, 1.0f); // Dummy input

    // 创建输入张量
    Ort::MemoryInfo memory_info = Ort::MemoryInfo::CreateCpu(OrtArenaAllocator, OrtMemTypeDefault);
    Ort::Value input_tensor = Ort::Value::CreateTensor<float>(
        memory_info, input_data.data(), input_data.size(), input_shape.data(), input_shape.size());

    // 获取模型输出信息
    const char* output_name = session.GetOutputName(0, allocator);

    // 执行推理
    auto output_tensors = session.Run(
        Ort::RunOptions{nullptr},
        &input_name, &input_tensor, 1,
        &output_name, 1);

    // 获取输出张量数据
    float* output_data = output_tensors[0].GetTensorMutableData<float>();

    // 打印输出结果（示例）
    std::cout << "Output value: " << output_data[0] << std::endl;

    return 0;
}
```

---

### **优化与扩展**
1. **多线程优化**：
   - 使用 `session_options.SetIntraOpNumThreads()` 和 `SetInterOpNumThreads()` 配置多线程。

2. **GPU 支持**：
   - 安装 ONNX Runtime 的 GPU 版本，并通过 `session_options.AppendExecutionProvider_CUDA()` 启用 GPU。

3. **动态输入支持**：
   - 对于不确定输入形状的模型，可在运行时动态设置张量维度。

---

### **常见问题**
1. **模型加载失败**：
   - 确保模型是有效的 ONNX 格式。
   - 检查模型是否包含动态维度（需要正确处理）。

2. **输出数据为空**：
   - 确认输入张量的数据和维度与模型的预期一致。

3. **硬件加速问题**：
   - 检查是否正确安装了支持 GPU 的 ONNX Runtime 版本。

---

如果有具体需求或问题，比如如何处理动态输入、支持特定硬件等，可以进一步讨论！

## C++ onnxruntime Ort::Env 详解

`Ort::Env` 是 ONNX Runtime 的 C++ API 中的一个核心类，用于初始化 ONNX Runtime 环境并设置相关的全局配置。ONNX Runtime 是一个高性能的推理引擎，`Ort::Env` 是使用 C++ API 的入口点之一。

以下是关于 `Ort::Env` 的详细解析：

---

### **`Ort::Env` 的功能**

1. **初始化 ONNX Runtime**：
   - `Ort::Env` 是一个 RAII（资源获取即初始化）类，在创建时初始化 ONNX Runtime，销毁时释放相关资源。
   - 在一个应用中，只需创建一个 `Ort::Env` 实例即可。

2. **配置日志和线程池**：
   - 允许设置日志级别、日志输出路径等全局配置。
   - 配置线程池的行为（如并行度）。

3. **管理会话的上下文环境**：
   - 所有的推理会话 (`Ort::Session`) 都依赖于 `Ort::Env` 的存在。

---

### **`Ort::Env` 的构造函数**

`Ort::Env` 提供多种构造方式，允许用户灵活设置日志和多线程相关的参数。

#### 1. **简单构造**
```cpp
Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXRuntime");
```
- **参数说明**：
  - `ORT_LOGGING_LEVEL_WARNING`：日志级别，ONNX Runtime 只记录警告及更高级别的日志。
  - `"ONNXRuntime"`：日志的默认记录器名称，用于区分日志源。

- **日志级别选项**：
  ```cpp
  ORT_LOGGING_LEVEL_VERBOSE   // 记录所有日志
  ORT_LOGGING_LEVEL_INFO      // 记录信息级别及以上的日志
  ORT_LOGGING_LEVEL_WARNING   // 记录警告及以上的日志
  ORT_LOGGING_LEVEL_ERROR     // 记录错误及以上的日志
  ORT_LOGGING_LEVEL_FATAL     // 记录致命错误日志
  ```

#### 2. **高级构造**
```cpp
Ort::Env env(ORT_LOGGING_LEVEL_VERBOSE, "ONNXRuntime", OrtThreadingOptions{});
```
- 使用 `OrtThreadingOptions` 配置线程池行为。
- **`OrtThreadingOptions` 示例**：
  ```cpp
  OrtThreadingOptions* options = Ort::GetApi().CreateThreadingOptions();
  Ort::GetApi().SetGlobalIntraOpThreadAffinity(options, true);
  Ort::GetApi().ReleaseThreadingOptions(options);
  ```

---

### **常见用法**

#### **初始化 ONNX Runtime 环境**
```cpp
#include <onnxruntime/core/providers/cpu/cpu_provider_factory.h>
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>

int main() {
    // 初始化环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXRuntimeExample");

    // 其他操作，如创建会话等
    return 0;
}
```

#### **结合推理会话**
```cpp
#include <onnxruntime/core/providers/cpu/cpu_provider_factory.h>
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>

int main() {
    // 初始化环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXRuntimeExample");

    // 创建推理会话
    Ort::SessionOptions session_options;
    session_options.SetIntraOpNumThreads(1); // 设置线程数
    session_options.SetGraphOptimizationLevel(GraphOptimizationLevel::ORT_ENABLE_EXTENDED);

    Ort::Session session(env, "model.onnx", session_options);

    return 0;
}
```

---

### **`Ort::Env` 的主要接口**

#### **设置日志级别**
设置日志的输出级别。
```cpp
env.SetLogLevel(ORT_LOGGING_LEVEL_INFO);
```

#### **设置线程池选项**
可以通过 `OrtThreadingOptions` 配置线程池行为，如线程数、线程亲和性等。

---

### **注意事项**

1. **单实例原则**：
   - 一个应用中通常只需要一个 `Ort::Env` 实例。
   - 多个实例可能导致资源冲突或性能问题。

2. **线程安全性**：
   - `Ort::Env` 本身是线程安全的，可以在多线程环境中安全使用。

3. **销毁时机**：
   - 当 `Ort::Env` 实例销毁时，ONNX Runtime 的资源会被自动释放。

---

### **完整示例**

以下是一个完整的推理流程，展示了如何使用 `Ort::Env`：
```cpp
#include <onnxruntime/core/providers/cpu/cpu_provider_factory.h>
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>
#include <iostream>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXRuntimeExample");

    // 配置会话选项
    Ort::SessionOptions session_options;
    session_options.SetIntraOpNumThreads(2);
    session_options.SetGraphOptimizationLevel(GraphOptimizationLevel::ORT_ENABLE_EXTENDED);

    // 加载模型
    const char* model_path = "model.onnx";
    Ort::Session session(env, model_path, session_options);

    // 模型输入输出信息
    size_t num_input_nodes = session.GetInputCount();
    size_t num_output_nodes = session.GetOutputCount();

    std::cout << "Number of inputs: " << num_input_nodes << std::endl;
    std::cout << "Number of outputs: " << num_output_nodes << std::endl;

    return 0;
}
```

---

如果需要进一步了解某些特性（如日志配置、线程选项等）或其他问题，可以继续提问！

## Ort::SessionOptions 详解

`Ort::SessionOptions` 是 ONNX Runtime C++ API 中的重要类，用于配置推理会话 (`Ort::Session`) 的运行行为。通过该类，可以设置线程数、优化级别、自定义执行提供程序等。

以下是对 `Ort::SessionOptions` 的详细解析：

---

### **`Ort::SessionOptions` 的功能**

1. **线程配置**：
   - 控制并行线程数。
   - 配置线程间的交互模式（单线程或多线程）。

2. **图优化**：
   - 优化计算图以提升推理性能。

3. **执行提供程序**：
   - 指定使用的计算设备（如 CPU、GPU）。

4. **自定义选项**：
   - 支持自定义分配器、上下文、会话行为等。

5. **内存分配和 IO 绑定**：
   - 允许用户自定义内存分配器和 IO 绑定。

---

### **构造和基本用法**

#### **默认构造函数**
创建一个默认的 `Ort::SessionOptions` 对象：
```cpp
Ort::SessionOptions session_options;
```

---

### **主要成员函数**

#### **1. 设置线程数**

##### **`SetIntraOpNumThreads`**
- 设置单个运算操作（如矩阵乘法）的最大线程数。
- 示例：
  ```cpp
  session_options.SetIntraOpNumThreads(4);
  ```
- 默认值为 0，表示由 ONNX Runtime 自动决定线程数。

##### **`SetInterOpNumThreads`**
- 设置多个操作之间的最大线程数。
- 示例：
  ```cpp
  session_options.SetInterOpNumThreads(2);
  ```
- 默认值为 0，表示自动决定线程数。

---

#### **2. 设置优化级别**

##### **`SetGraphOptimizationLevel`**
- 设置 ONNX 模型图的优化级别。
- 可用优化级别：
  ```cpp
  ORT_DISABLE_ALL            // 禁用所有优化
  ORT_ENABLE_BASIC           // 启用基本优化（默认值）
  ORT_ENABLE_EXTENDED        // 启用扩展优化（包括内存优化）
  ORT_ENABLE_ALL             // 启用所有优化
  ```
- 示例：
  ```cpp
  session_options.SetGraphOptimizationLevel(GraphOptimizationLevel::ORT_ENABLE_EXTENDED);
  ```

---

#### **3. 配置执行提供程序**

##### **CPU 提供程序**
- 默认情况下，ONNX Runtime 使用 CPU 提供程序。
- 如果明确指定：
  ```cpp
  #include <onnxruntime/core/providers/cpu/cpu_provider_factory.h>
  Ort::SessionOptions session_options;
  session_options.AppendExecutionProvider_CPU(0); // 0 表示使用默认配置
  ```

##### **GPU 提供程序（CUDA）**
- 需要安装 ONNX Runtime 的 CUDA 支持版本。
- 示例：
  ```cpp
  #include <onnxruntime/core/providers/cuda/cuda_provider_factory.h>
  Ort::SessionOptions session_options;
  session_options.AppendExecutionProvider_CUDA(0); // 0 表示 GPU ID
  ```

---

#### **4. 配置内存分配**

##### **`EnableMemPattern`**
- 启用/禁用内存模式优化（默认启用）。
- 示例：
  ```cpp
  session_options.EnableMemPattern(true);
  ```

##### **`DisableMemPattern`**
- 禁用内存模式优化。
- 示例：
  ```cpp
  session_options.DisableMemPattern();
  ```

---

#### **5. 使用分配器**

##### **设置分配器（可选）**
用户可以指定自定义的内存分配器：
```cpp
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>
OrtAllocator* custom_allocator = ...; // 用户自定义分配器
session_options.AddConfigEntry("memory.custom_allocator", custom_allocator);
```

---

#### **6. 打开和关闭调试选项**

##### **启用调试**
用于调试模型推理的配置。
```cpp
session_options.EnableProfiling("profiling_file.json");
```

##### **禁用调试**
```cpp
session_options.DisableProfiling();
```

---

### **综合示例**

以下是一个使用 `Ort::SessionOptions` 的完整示例，展示了如何配置线程数、优化级别以及提供程序：

```cpp
#include <onnxruntime/core/providers/cpu/cpu_provider_factory.h>
#include <onnxruntime/core/providers/cuda/cuda_provider_factory.h>
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>
#include <iostream>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXRuntimeExample");

    // 创建会话选项
    Ort::SessionOptions session_options;

    // 配置线程数
    session_options.SetIntraOpNumThreads(4);
    session_options.SetInterOpNumThreads(2);

    // 设置优化级别
    session_options.SetGraphOptimizationLevel(GraphOptimizationLevel::ORT_ENABLE_EXTENDED);

    // 添加执行提供程序（使用 CUDA 或 CPU）
    session_options.AppendExecutionProvider_CPU(0);  // 使用 CPU 提供程序
    // session_options.AppendExecutionProvider_CUDA(0); // 若有 CUDA 支持，则使用 GPU

    // 加载模型并创建会话
    const char* model_path = "model.onnx";
    Ort::Session session(env, model_path, session_options);

    // 输出模型的输入和输出信息
    size_t num_inputs = session.GetInputCount();
    size_t num_outputs = session.GetOutputCount();

    std::cout << "Model Inputs: " << num_inputs << std::endl;
    std::cout << "Model Outputs: " << num_outputs << std::endl;

    return 0;
}
```

---

### **注意事项**

1. **线程配置**：
   - 如果设置了不合理的线程数（如过大或过小），可能导致性能下降或内存问题。

2. **执行提供程序的顺序**：
   - ONNX Runtime 会按照添加的顺序选择第一个支持的提供程序。

3. **图优化**：
   - 高级优化（如 `ORT_ENABLE_ALL`）可能导致模型行为的细微变化，需要进行充分测试。

4. **CUDA 提供程序的依赖**：
   - 使用 GPU 时，请确保系统正确配置了 CUDA 和 cuDNN。

5. **资源释放**：
   - `Ort::SessionOptions` 在生命周期结束时会自动释放资源。

如果需要更深入了解某个功能或遇到问题，可以进一步探讨！

## Ort::Session 详解

`Ort::Session` 是 ONNX Runtime C++ API 的核心类之一，负责加载 ONNX 模型并执行推理操作。一个 `Ort::Session` 对象对应一个加载的 ONNX 模型，并提供接口来查询模型信息和执行推理。

以下是对 `Ort::Session` 的详细解析：

---

## **`Ort::Session` 的主要功能**

1. **加载模型**：
   - 使用 ONNX Runtime 环境 (`Ort::Env`) 和会话选项 (`Ort::SessionOptions`) 加载一个 ONNX 模型文件。

2. **获取模型元信息**：
   - 查询模型的输入和输出节点、数据类型、维度等。

3. **执行推理**：
   - 使用模型的输入数据进行推理，并返回结果。

4. **管理资源**：
   - `Ort::Session` 在其生命周期内负责维护模型相关的资源，并在销毁时自动释放。

---

## **构造和基本用法**

### **构造函数**

#### 1. **默认构造**
```cpp
Ort::Session session(env, "model.onnx", session_options);
```
- **参数说明**：
  - `env`：一个有效的 `Ort::Env` 实例。
  - `"model.onnx"`：ONNX 模型文件的路径。
  - `session_options`：一个 `Ort::SessionOptions` 对象，用于配置推理行为。

---

## **主要成员函数**

### **1. 获取模型输入输出信息**

#### **`GetInputCount` 和 `GetOutputCount`**
- 分别返回模型的输入和输出节点数量。
- 示例：
  ```cpp
  size_t num_inputs = session.GetInputCount();
  size_t num_outputs = session.GetOutputCount();
  ```

#### **`GetInputName` 和 `GetOutputName`**
- 返回指定输入或输出节点的名称。
- 示例：
  ```cpp
  Ort::AllocatorWithDefaultOptions allocator;
  const char* input_name = session.GetInputName(0, allocator);
  const char* output_name = session.GetOutputName(0, allocator);
  ```

#### **`GetInputTypeInfo` 和 `GetOutputTypeInfo`**
- 返回输入或输出节点的数据类型和维度信息。
- 示例：
  ```cpp
  auto input_info = session.GetInputTypeInfo(0);
  auto input_shape = input_info.GetTensorTypeAndShapeInfo();
  auto input_dims = input_shape.GetShape(); // 获取输入的维度
  ```

---

### **2. 推理操作**

#### **`Run`**
- 使用输入数据执行推理并返回结果。
- **原型**：
  ```cpp
  std::vector<Ort::Value> Run(
      Ort::RunOptions& run_options,
      const char* const* input_names,
      const Ort::Value* input_tensors,
      size_t input_count,
      const char* const* output_names,
      size_t output_count
  );
  ```
- **参数说明**：
  - `run_options`：推理选项，通常可以传入默认值。
  - `input_names`：输入节点名称数组。
  - `input_tensors`：输入数据张量数组。
  - `input_count`：输入节点数量。
  - `output_names`：输出节点名称数组。
  - `output_count`：输出节点数量。
- **返回值**：
  - 推理结果的 `Ort::Value` 对象数组。

---

### **综合示例：执行推理**

以下代码展示了如何加载模型并执行推理：

```cpp
#include <onnxruntime/core/providers/cpu/cpu_provider_factory.h>
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>
#include <iostream>
#include <vector>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXRuntimeExample");

    // 配置会话选项
    Ort::SessionOptions session_options;
    session_options.SetIntraOpNumThreads(1);
    session_options.SetGraphOptimizationLevel(GraphOptimizationLevel::ORT_ENABLE_EXTENDED);

    // 加载模型
    const char* model_path = "model.onnx";
    Ort::Session session(env, model_path, session_options);

    // 查询模型输入信息
    Ort::AllocatorWithDefaultOptions allocator;
    const char* input_name = session.GetInputName(0, allocator);
    auto input_info = session.GetInputTypeInfo(0);
    auto input_shape = input_info.GetTensorTypeAndShapeInfo().GetShape();

    std::cout << "Input Name: " << input_name << std::endl;
    std::cout << "Input Shape: ";
    for (auto dim : input_shape) std::cout << dim << " ";
    std::cout << std::endl;

    // 创建输入数据
    std::vector<float> input_tensor_values = {1.0f, 2.0f, 3.0f, 4.0f};
    std::vector<int64_t> input_dims = {1, 4}; // 假设输入是一个 1x4 的张量
    Ort::Value input_tensor = Ort::Value::CreateTensor<float>(
        allocator, input_tensor_values.data(), input_tensor_values.size(),
        input_dims.data(), input_dims.size()
    );

    // 执行推理
    const char* output_name = session.GetOutputName(0, allocator);
    std::vector<Ort::Value> output_tensors = session.Run(
        Ort::RunOptions{nullptr},              // 默认推理选项
        &input_name, &input_tensor, 1,         // 输入名和输入张量
        &output_name, 1                        // 输出名
    );

    // 解析输出
    float* output_data = output_tensors[0].GetTensorMutableData<float>();
    auto output_shape = output_tensors[0].GetTensorTypeAndShapeInfo().GetShape();

    std::cout << "Output Shape: ";
    for (auto dim : output_shape) std::cout << dim << " ";
    std::cout << std::endl;

    std::cout << "Output Values: ";
    for (size_t i = 0; i < output_tensors[0].GetTensorTypeAndShapeInfo().GetElementCount(); ++i) {
        std::cout << output_data[i] << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

---

## **注意事项**

1. **线程安全性**：
   - `Ort::Session` 是线程安全的，可以在多线程环境中同时使用。

2. **内存管理**：
   - 使用 ONNX Runtime 的默认分配器 (`Ort::AllocatorWithDefaultOptions`) 来管理内存。
   - 所有使用分配器分配的内存需要显式释放或交由分配器管理。

3. **推理性能**：
   - 使用 `Ort::SessionOptions` 配置优化选项（如线程数和图优化级别）可以显著提升推理性能。

4. **模型输入/输出验证**：
   - 在执行推理之前，确保输入数据的维度和类型与模型的输入定义一致。

5. **错误处理**：
   - 如果模型文件路径错误或输入数据不匹配，ONNX Runtime 会抛出异常，需要进行捕获和处理。

---

如果需要进一步深入了解 `Ort::Session` 或相关操作（如自定义提供程序、动态输入处理等），可以继续提问！

## Ort::AllocatorWithDefaultOptions 详解

`Ort::AllocatorWithDefaultOptions` 是 ONNX Runtime C++ API 提供的一个简化工具类，用于分配和管理内存资源，尤其在操作模型输入输出信息时非常常用。

它封装了 ONNX Runtime 的默认分配器，简化了内存分配、释放的操作，是处理 `Ort::Session` 返回的字符串、张量等动态内存资源时的重要工具。

---

## **`Ort::AllocatorWithDefaultOptions` 的主要功能**

1. **动态分配内存**：
   - 用于获取模型的输入输出名称、类型信息等，这些信息通常是动态分配的内存。

2. **简化内存管理**：
   - `Ort::AllocatorWithDefaultOptions` 自动处理分配的内存释放，避免手动管理内存的复杂性。

3. **轻量级**：
   - 这是一个无状态的 RAII（资源获取即初始化）类，创建和销毁的开销非常小。

---

## **用法和接口**

### **1. 构造函数**
`Ort::AllocatorWithDefaultOptions` 无需任何参数即可默认构造：
```cpp
Ort::AllocatorWithDefaultOptions allocator;
```

---

### **2. 常用方法**

#### **`operator OrtAllocator*`**
- 提供一个隐式转换操作符，将对象转换为底层的 `OrtAllocator*` 类型。
- 示例：
  ```cpp
  OrtAllocator* raw_allocator = allocator;
  ```

#### **内存管理功能**
- `Ort::AllocatorWithDefaultOptions` 自动调用 ONNX Runtime 的默认分配器接口，例如分配和释放内存：
  - **分配内存**：`allocator` 在调用 API（如 `GetInputName`）时会自动分配内存。
  - **释放内存**：当 `allocator` 作用域结束时，分配的内存会被自动释放。

---

## **使用场景**

### **1. 获取输入输出名称**

在获取模型输入和输出名称时，返回的名称是动态分配的内存，`Ort::AllocatorWithDefaultOptions` 自动管理这部分内存。

#### 示例：
```cpp
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>
#include <iostream>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "Example");

    // 加载模型
    Ort::SessionOptions session_options;
    const char* model_path = "model.onnx";
    Ort::Session session(env, model_path, session_options);

    // 创建默认分配器
    Ort::AllocatorWithDefaultOptions allocator;

    // 获取输入名称
    const char* input_name = session.GetInputName(0, allocator);
    std::cout << "Input Name: " << input_name << std::endl;

    // 获取输出名称
    const char* output_name = session.GetOutputName(0, allocator);
    std::cout << "Output Name: " << output_name << std::endl;

    return 0;
}
```

---

### **2. 与动态分配相关的 API**

许多 ONNX Runtime 的 C++ API 使用动态内存分配，`Ort::AllocatorWithDefaultOptions` 是处理这些 API 的关键工具。例如：

#### 获取输入输出类型信息
```cpp
auto input_info = session.GetInputTypeInfo(0);
auto input_tensor_info = input_info.GetTensorTypeAndShapeInfo();

// 获取输入的维度信息
auto input_dims = input_tensor_info.GetShape();
```

#### 获取分配器指针
当需要与 ONNX Runtime 的低层 API 交互时，可以通过 `Ort::AllocatorWithDefaultOptions` 提供的 `OrtAllocator*` 指针：
```cpp
OrtAllocator* raw_allocator = allocator;
```

---

## **内部工作原理**

`Ort::AllocatorWithDefaultOptions` 基于 ONNX Runtime 的默认分配器实现，以下是它的工作机制：

1. **初始化默认分配器**：
   - 使用 ONNX Runtime 的 `OrtCreateAllocatorWithDefaultOptions` 方法创建分配器。
   - 该分配器负责分配和释放内存。

2. **自动管理内存**：
   - 通过 RAII 模式，确保分配器的生命周期与 `Ort::AllocatorWithDefaultOptions` 实例绑定。
   - 在分配器超出作用域时，自动释放所有相关资源。

---

## **注意事项**

1. **作用域管理**：
   - 确保 `Ort::AllocatorWithDefaultOptions` 的生命周期覆盖所有需要使用其分配内存的地方。
   - 在使用动态内存后，分配器超出作用域时会自动释放内存。

2. **多线程使用**：
   - ONNX Runtime 的默认分配器是线程安全的，可以在多线程环境中使用。

3. **避免手动释放**：
   - 如果已经使用 `Ort::AllocatorWithDefaultOptions` 进行内存分配，无需手动释放内存，否则可能导致重复释放的错误。

---

## **完整示例：从模型中提取输入输出信息**

以下是一个综合示例，展示如何使用 `Ort::AllocatorWithDefaultOptions` 获取输入输出名称和类型信息：

```cpp
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>
#include <iostream>
#include <vector>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "Example");

    // 加载模型
    Ort::SessionOptions session_options;
    const char* model_path = "model.onnx";
    Ort::Session session(env, model_path, session_options);

    // 创建默认分配器
    Ort::AllocatorWithDefaultOptions allocator;

    // 获取输入信息
    size_t num_inputs = session.GetInputCount();
    std::cout << "Number of inputs: " << num_inputs << std::endl;

    for (size_t i = 0; i < num_inputs; ++i) {
        const char* input_name = session.GetInputName(i, allocator);
        auto input_info = session.GetInputTypeInfo(i);
        auto tensor_info = input_info.GetTensorTypeAndShapeInfo();
        auto input_shape = tensor_info.GetShape();

        std::cout << "Input " << i << ": " << input_name << std::endl;
        std::cout << "  Shape: ";
        for (auto dim : input_shape) std::cout << dim << " ";
        std::cout << std::endl;
    }

    // 获取输出信息
    size_t num_outputs = session.GetOutputCount();
    std::cout << "Number of outputs: " << num_outputs << std::endl;

    for (size_t i = 0; i < num_outputs; ++i) {
        const char* output_name = session.GetOutputName(i, allocator);
        auto output_info = session.GetOutputTypeInfo(i);
        auto tensor_info = output_info.GetTensorTypeAndShapeInfo();
        auto output_shape = tensor_info.GetShape();

        std::cout << "Output " << i << ": " << output_name << std::endl;
        std::cout << "  Shape: ";
        for (auto dim : output_shape) std::cout << dim << " ";
        std::cout << std::endl;
    }

    return 0;
}
```

---

### **总结**

- `Ort::AllocatorWithDefaultOptions` 是处理动态分配内存的便捷工具类，尤其在获取输入输出名称等操作中必不可少。
- 它与 ONNX Runtime 的默认分配器紧密集成，简化了内存管理。
- 使用时只需关注业务逻辑，分配器的内存生命周期会自动管理。

如果需要进一步探索其工作机制或更多使用场景，可以继续提问！

## Session::GetInputTypeInfo() 详解

`Session::GetInputTypeInfo()` 是 ONNX Runtime C++ API 中的一个重要方法，用于获取模型某个输入节点的类型和形状信息。通过这个方法，你可以详细了解模型输入的张量数据类型和维度，从而确保输入数据格式符合模型的要求。

---

## **函数原型**

```cpp
Ort::TypeInfo GetInputTypeInfo(size_t index) const;
```

### **参数**
- `index`：输入节点的索引，从 `0` 开始。

### **返回值**
- 返回一个 `Ort::TypeInfo` 对象，包含输入节点的数据类型和形状信息。

---

## **Ort::TypeInfo 的功能**

`Ort::TypeInfo` 是 ONNX Runtime 用于描述节点数据类型和形状的类。通过 `TypeInfo` 对象，你可以进一步获取以下信息：
1. **节点是否为张量**。
2. **张量的数据类型**。
3. **张量的形状**。

---

## **使用方法**

以下是一个完整的示例，展示如何使用 `GetInputTypeInfo()` 获取模型输入节点的类型和形状信息：

```cpp
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>
#include <iostream>
#include <vector>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "Example");

    // 加载模型
    Ort::SessionOptions session_options;
    const char* model_path = "model.onnx";
    Ort::Session session(env, model_path, session_options);

    // 获取模型的输入节点数量
    size_t num_inputs = session.GetInputCount();
    std::cout << "Number of inputs: " << num_inputs << std::endl;

    // 创建默认分配器
    Ort::AllocatorWithDefaultOptions allocator;

    // 遍历输入节点，获取类型和形状信息
    for (size_t i = 0; i < num_inputs; ++i) {
        // 获取输入节点名称
        const char* input_name = session.GetInputName(i, allocator);
        std::cout << "Input Name: " << input_name << std::endl;

        // 获取输入节点类型信息
        Ort::TypeInfo input_type_info = session.GetInputTypeInfo(i);
        auto tensor_info = input_type_info.GetTensorTypeAndShapeInfo();

        // 获取数据类型
        ONNXTensorElementDataType input_data_type = tensor_info.GetElementType();
        std::cout << "Data Type: " << input_data_type << std::endl;

        // 获取输入形状
        auto input_shape = tensor_info.GetShape();
        std::cout << "Shape: ";
        for (auto dim : input_shape) {
            std::cout << dim << " ";
        }
        std::cout << std::endl;
    }

    return 0;
}
```

---

## **Ort::TensorTypeAndShapeInfo 的功能**

通过 `TypeInfo` 对象的 `GetTensorTypeAndShapeInfo()` 方法，可以获取输入节点的详细张量信息，返回的是一个 `Ort::TensorTypeAndShapeInfo` 对象。

### **1. 获取张量数据类型**
```cpp
ONNXTensorElementDataType GetElementType() const;
```
- 返回值是一个枚举值，表示张量的数据类型。例如：
  - `ONNX_TENSOR_ELEMENT_DATA_TYPE_FLOAT` 表示 `float` 类型。
  - `ONNX_TENSOR_ELEMENT_DATA_TYPE_INT64` 表示 `int64_t` 类型。

---

### **2. 获取张量形状**
```cpp
std::vector<int64_t> GetShape() const;
```
- 返回一个包含张量每个维度大小的 `std::vector<int64_t>`。
- 对于动态维度，返回的值为 `-1`。

---

### **3. 获取元素总数**
```cpp
size_t GetElementCount() const;
```
- 返回张量中元素的总数量，等于所有维度大小的乘积。
- 如果张量包含动态维度，无法计算总数时会抛出异常。

---

## **输出示例**

假设我们加载的模型有一个输入节点，名称为 `input_0`，数据类型为 `float`，形状为 `[1, 3, 224, 224]`。运行上述代码的输出如下：

```
Number of inputs: 1
Input Name: input_0
Data Type: 1
Shape: 1 3 224 224
```

- 数据类型 `1` 对应 `ONNX_TENSOR_ELEMENT_DATA_TYPE_FLOAT`。
- 形状 `[1, 3, 224, 224]` 表示张量的维度。

---

## **注意事项**

1. **动态维度**：
   - 如果模型的某个维度是动态的，返回的值将是 `-1`。在这种情况下，需要根据具体推理场景动态确定实际的维度。

2. **节点索引越界**：
   - 如果输入 `index` 超出范围（大于或等于输入节点数量），会抛出异常，需要确保索引有效。

3. **数据类型匹配**：
   - 确保推理时提供的输入张量数据类型与模型的输入要求一致，否则会导致运行时错误。

4. **多输入模型**：
   - 如果模型有多个输入，需要分别对每个输入调用 `GetInputTypeInfo()` 获取信息。

---

## **综合小结**

- `Session::GetInputTypeInfo()` 是了解模型输入节点属性的核心方法。
- 它返回一个 `Ort::TypeInfo` 对象，进一步提供张量的数据类型和形状信息。
- 使用时需要注意动态维度的处理和数据类型的匹配。

如需进一步了解 `Ort::TypeInfo` 或动态输入处理，可以继续提问！

## TensorTypeAndShapeInfo::GetShape() 详解

`TensorTypeAndShapeInfo::GetShape()` 是 ONNX Runtime C++ API 中的一个方法，主要用于获取 ONNX 模型的张量输入或输出的形状信息。它返回一个包含张量维度大小的 `std::vector<int64_t>`，这些维度可以是静态或动态的。

---

## **函数原型**

```cpp
std::vector<int64_t> GetShape() const;
```

### **参数**
- 此方法无参数，它直接作用于 `TensorTypeAndShapeInfo` 对象。

### **返回值**
- 返回一个 `std::vector<int64_t>`，其中每个元素表示张量在该维度上的大小。如果维度是动态的，该维度的大小为 `-1`。

---

## **功能和用途**

`TensorTypeAndShapeInfo::GetShape()` 方法的主要作用是返回张量的形状信息，这些信息对于输入数据的格式化和验证至关重要。它允许你获取张量在各个维度上的大小，通常用于确定输入数据或输出数据是否符合模型的要求。

- **静态维度**：对于静态维度（在模型加载时确定的维度），返回的是实际的维度大小。
- **动态维度**：如果张量的某些维度是动态的（例如在模型训练时，模型的输入维度没有固定大小），返回的维度值为 `-1`，表示该维度的大小是动态变化的，通常需要根据输入数据在推理时动态确定。

---

## **使用示例**

假设你已经加载了一个 ONNX 模型并且调用了 `GetInputTypeInfo()` 或 `GetOutputTypeInfo()` 来获取模型输入或输出的张量类型信息。接下来，你可以使用 `GetShape()` 获取张量的形状。

### **示例代码：获取输入形状**

```cpp
#include <onnxruntime/core/session/onnxruntime_cxx_api.h>
#include <iostream>
#include <vector>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "Example");

    // 加载模型
    Ort::SessionOptions session_options;
    const char* model_path = "model.onnx";
    Ort::Session session(env, model_path, session_options);

    // 创建默认分配器
    Ort::AllocatorWithDefaultOptions allocator;

    // 获取输入数量
    size_t num_inputs = session.GetInputCount();
    std::cout << "Number of inputs: " << num_inputs << std::endl;

    // 遍历输入节点，获取形状
    for (size_t i = 0; i < num_inputs; ++i) {
        // 获取输入类型信息
        Ort::TypeInfo input_type_info = session.GetInputTypeInfo(i);
        auto tensor_info = input_type_info.GetTensorTypeAndShapeInfo();

        // 获取张量形状
        std::vector<int64_t> input_shape = tensor_info.GetShape();
        std::cout << "Input " << i << " shape: ";
        for (auto dim : input_shape) {
            std::cout << dim << " ";
        }
        std::cout << std::endl;
    }

    return 0;
}
```

### **输出示例**

假设模型的输入是一个 4D 张量，形状为 `[1, 3, 224, 224]`，那么输出将类似于：

```
Number of inputs: 1
Input 0 shape: 1 3 224 224
```

如果输入的某个维度是动态的，例如批量大小（batch size）是动态的，那么输出可能是：

```
Input 0 shape: -1 3 224 224
```

`-1` 表示批量大小是动态的，推理时由输入数据的批量大小来确定。

---

## **`GetShape()` 的返回值解析**

`GetShape()` 返回的 `std::vector<int64_t>` 每个元素表示一个维度的大小。下面是关于维度和返回值的一些常见情况：

### **1. 静态维度**

如果模型的输入或输出具有固定的维度，则 `GetShape()` 返回的是该张量的具体形状。例如：

- 对于一个 `1x3x224x224` 的图像张量（通常用于图像分类任务），`GetShape()` 返回：
  ```cpp
  {1, 3, 224, 224}
  ```

- 对于一个二维张量（例如一个大小为 `100x50` 的矩阵），`GetShape()` 返回：
  ```cpp
  {100, 50}
  ```

### **2. 动态维度**

如果模型的某些维度是动态的，则返回值中的该维度将是 `-1`，表示该维度在推理时会根据实际输入数据动态确定。常见的动态维度包括批量大小（batch size）。

- 假设模型的输入形状是 `[batch_size, 3, 224, 224]`，其中 `batch_size` 是动态的。`GetShape()` 可能返回：
  ```cpp
  {-1, 3, 224, 224}
  ```

  在这个例子中，`-1` 表示 `batch_size` 是动态的，具体的值将在实际推理时由输入数据的大小确定。

- 如果模型具有 `sequence_length` 等动态维度，类似地，返回的形状可能为：
  ```cpp
  {10, -1}  // 10 是固定维度，-1 表示第二维是动态的
  ```

### **3. 无效维度**

如果张量没有形状（例如是一个标量），`GetShape()` 返回的向量为空：
```cpp
{}  // 无形状的张量
```

---

## **常见问题和注意事项**

1. **动态维度的处理**：
   - 对于动态维度，返回的形状中相应位置的值为 `-1`，你无法直接知道该维度的大小。通常，实际的维度会在推理时根据输入的大小动态确定。

2. **如何获取元素数量**：
   - 如果模型的输入具有静态形状，可以通过 `GetShape()` 返回的维度大小计算张量的元素数量。
   - 例如，假设返回的形状是 `[1, 3, 224, 224]`，那么元素总数为 `1 * 3 * 224 * 224 = 150528`。

3. **性能优化**：
   - 当处理动态维度时，你可能需要动态调整输入的大小。通过 `GetShape()` 获取输入形状后，你可以根据实际情况调整输入数据的维度。

4. **错误处理**：
   - 如果在尝试获取形状时遇到问题（如 `session.GetInputTypeInfo()` 返回错误），确保模型输入的维度与预期一致。

---

## **总结**

`TensorTypeAndShapeInfo::GetShape()` 是 ONNX Runtime 中获取张量形状信息的关键方法。它返回张量的维度信息，帮助你在执行推理之前确认输入和输出数据的结构。了解如何处理静态和动态维度，对于正确地为模型准备输入数据至关重要。

如果你对 `GetShape()` 或其他 ONNX Runtime 方法有进一步的疑问，欢迎继续提问！

## [1, 3, 224, 224] 表示的什么

`[1, 3, 224, 224]` 是一个形状（Shape）表示，通常用于描述图像张量的维度。在计算机视觉和深度学习任务中，这种表示方法是非常常见的。让我们逐步分析它：

### **`[1, 3, 224, 224]` 解释**

- **1**：批量大小（Batch Size）
  - 这个数字表示图像数据批次中包含的图像数量。在这个例子中，批量大小为 `1`，意味着这只是一个单独的图像。批量大小是深度学习模型训练和推理过程中常用的概念。
  
- **3**：颜色通道数（Channels）
  - 这个数字表示图像的颜色通道数。在这种情况下，`3` 代表图像是 RGB 彩色图像，意味着图像包含三个颜色通道：红色（Red）、绿色（Green）和蓝色（Blue）。每个通道包含一个灰度值，表示颜色的强度。

- **224**：图像的高度（Height）
  - 这个数字表示图像的垂直尺寸，单位是像素。在这个例子中，图像的高度为 `224` 像素，表示图像的垂直方向有 224 个像素点。

- **224**：图像的宽度（Width）
  - 这个数字表示图像的水平尺寸，单位也是像素。在这个例子中，图像的宽度为 `224` 像素，表示图像的水平方向有 224 个像素点。

### **综合解释**

- `1`：批量大小为 `1`，意味着只有一个图像。
- `3`：该图像为彩色图像，采用 RGB 色彩空间，因此有 3 个颜色通道。
- `224` 和 `224`：该图像的尺寸为 224x224 像素，表示图像的高度和宽度都是 224 像素。

因此，`[1, 3, 224, 224]` 表示一个单独的 RGB 彩色图像，尺寸为 224x224 像素，具有 3 个颜色通道（红、绿、蓝）。这种形状常见于深度学习任务，特别是图像分类或卷积神经网络（CNN）的输入。

---

### **使用场景**

这个张量形状通常用于深度学习框架，如 PyTorch、ONNX 或 TensorFlow，在图像数据的训练和推理过程中经常出现。例如，如果你使用一个深度学习模型来分类图像，模型通常期望输入的形状是 `[B, C, H, W]`，其中：
- `B` 是批量大小。
- `C` 是颜色通道数。
- `H` 是图像的高度。
- `W` 是图像的宽度。

### **总结**

- `[1, 3, 224, 224]` 表示一个具有 224x224 像素尺寸的 RGB 彩色图像，批量大小为 1。


## 什么是图像张量

图像张量（Image Tensor）是一个用于表示图像数据的多维数组（张量），通常用于深度学习、计算机视觉和其他机器学习任务。张量是一个数学概念，广泛用于表示高维数据。图像张量是张量在计算机视觉领域的具体应用，它通过数值数组的形式表示图像。

在深度学习和计算机视觉任务中，图像通常作为输入数据，通过神经网络进行处理和分析。为了能够处理图像，图像数据需要以一种数值形式表示，而这正是张量的作用所在。

---

## **图像张量的基本结构**

图像张量的维度（Shape）通常与图像的表示方式、颜色通道数、分辨率和数据类型等因素相关。常见的图像张量表示方式包括以下几种：

### **1. 彩色图像张量**
对于彩色图像，通常采用**RGB**（红绿蓝）颜色空间来表示。每个颜色通道的值通常是一个整数（比如 `0` 到 `255` 之间）或浮动的值（例如标准化到 `[0, 1]` 的浮动值）。

- **形状**：`[C, H, W]`
  - **C**：颜色通道数（通常是 `3`，表示 RGB 三个颜色通道）。
  - **H**：图像的高度（即图像的行数，表示图像的垂直尺寸）。
  - **W**：图像的宽度（即图像的列数，表示图像的水平尺寸）。

例如，一个大小为 `224x224` 像素的 RGB 彩色图像可以用一个形状为 `[3, 224, 224]` 的张量表示。

### **2. 灰度图像张量**
对于灰度图像，通常只有一个颜色通道，即每个像素只有一个灰度值，表示图像的亮度。

- **形状**：`[1, H, W]`
  - **1**：表示灰度图像只有一个颜色通道。
  - **H**：图像的高度。
  - **W**：图像的宽度。

例如，一个 `28x28` 像素的灰度图像可以用一个形状为 `[1, 28, 28]` 的张量表示。

### **3. 批量图像张量**
在深度学习中，通常一次性处理多个图像。因此，图像张量通常以批量（batch）的形式输入到神经网络中。

- **形状**：`[B, C, H, W]`
  - **B**：批量大小（batch size），表示一次处理多少图像。
  - **C**：颜色通道数（通常是 `3` 表示 RGB，或 `1` 表示灰度）。
  - **H**：图像的高度。
  - **W**：图像的宽度。

例如，一个包含 32 张 `224x224` 的 RGB 彩色图像的批量张量，可以表示为形状为 `[32, 3, 224, 224]` 的张量。

---

## **图像张量的存储格式**

图像张量的存储格式依赖于数据的顺序和布局。最常见的两种存储格式是：

1. **CHW（Channel, Height, Width）格式**：
   - 这是深度学习框架如 PyTorch、ONNX、Caffe 中常见的图像张量格式。颜色通道在前，接着是高度和宽度。
   - 示例：`[3, 224, 224]` 表示一个 RGB 彩色图像，具有 3 个通道，尺寸为 `224x224`。

2. **HWC（Height, Width, Channel）格式**：
   - 这是图像处理库如 OpenCV 和 TensorFlow 中常见的图像表示格式。图像的高度和宽度在前，颜色通道在后。
   - 示例：`[224, 224, 3]` 表示一个 RGB 彩色图像，具有 3 个通道，尺寸为 `224x224`。

不同框架和工具可能采用不同的存储格式，但它们都可以通过相应的工具进行转换。

---

## **图像张量的用途**

图像张量在计算机视觉和深度学习中的用途非常广泛，主要包括：

1. **输入神经网络**：
   - 图像张量是深度学习模型，特别是卷积神经网络（CNN）的输入数据。通过神经网络的前向传播，模型会根据图像的特征进行分类、检测、分割等任务。

2. **图像预处理**：
   - 在将图像输入神经网络之前，通常会进行一些预处理操作，如调整图像的大小、标准化颜色值、数据增强（例如旋转、裁剪、翻转等）。这些操作通常会改变图像张量的形状或数值。

3. **图像生成**：
   - 在生成模型（如生成对抗网络 GAN）中，图像张量不仅作为输入，还作为模型的输出，生成新的图像。

4. **图像变换**：
   - 图像张量在图像处理任务中，涉及图像的各种变换操作，比如图像的滤波、边缘检测、图像增强等。

---

## **图像张量的示例**

以下是一个示例，展示如何使用 Python 和 NumPy 创建图像张量（以 NumPy 为例，虽然 Python 是常见的深度学习语言，但 ONNX Runtime 或 PyTorch 等框架也使用类似的张量表示方法）：

```python
import numpy as np

# 创建一个 224x224 的 RGB 图像张量（3 个颜色通道）
image_tensor = np.random.rand(3, 224, 224)  # 随机生成一个图像张量

print(image_tensor.shape)  # 输出张量的形状，应该是 (3, 224, 224)
```

如果是批量图像（例如批量大小为 32）：

```python
# 创建一个包含 32 张 224x224 RGB 图像的张量
batch_image_tensor = np.random.rand(32, 3, 224, 224)

print(batch_image_tensor.shape)  # 输出张量的形状，应该是 (32, 3, 224, 224)
```

---

## **总结**

- 图像张量是一个多维数组，表示图像的数值数据，通常用于深度学习中的输入数据。
- 图像张量的常见形状包括 `[C, H, W]`（单张图像）和 `[B, C, H, W]`（批量图像）。
- 图像张量可以是彩色图像（RGB）或灰度图像，并通过颜色通道、图像的高度和宽度来表示。
- 在深度学习和计算机视觉任务中，图像张量是神经网络输入和输出的重要形式。

图像张量为机器学习模型提供了可以处理的格式，是许多计算机视觉任务的基础。

## C++ onnxruntime Ort::Value 详解

在 ONNX Runtime 中，`Ort::Value` 是一个非常重要的类，用于表示张量（tensor）数据。它封装了模型的输入和输出数据，并提供了一种与 ONNX Runtime 进行交互的方式。

### **Ort::Value 介绍**

`Ort::Value` 是 ONNX Runtime API 中用于表示数据的一个类，它可以用于存储模型的输入和输出数据，并为数据提供访问接口。你可以将数据包装到 `Ort::Value` 中，从而通过 ONNX Runtime 执行推理操作。它提供了对张量的访问、内存管理以及各种用于操作张量数据的功能。

### **主要功能**

1. **张量表示**：`Ort::Value` 用于表示和存储模型的输入、输出数据，这些数据通常是多维数组，表示为张量（Tensor）。

2. **数据类型支持**：`Ort::Value` 支持各种数据类型，如浮动类型（`float`、`double`）、整数类型（`int`、`long`）、布尔类型等。它也支持不同的张量格式，例如：`float32`、`int64` 等。

3. **内存管理**：`Ort::Value` 内部管理内存，并负责在不再需要时释放内存。ONNX Runtime 会通过 RAII（资源获取即初始化）管理内存的生命周期。

### **创建 Ort::Value**

`Ort::Value` 可以通过几种不同的方式创建，以下是一些常见的方法：

1. **从原始数据创建**：
   使用 `Ort::Value::CreateTensor` 或类似函数，可以从原始内存或数组创建一个张量。常见的场景是创建张量并作为输入传递给模型。

   ```cpp
   Ort::AllocatorWithDefaultOptions allocator;
   std::vector<float> input_data = {1.0f, 2.0f, 3.0f};
   std::array<int64_t, 1> input_shape = {3};
   Ort::Value input_tensor = Ort::Value::CreateTensor<float>(allocator, input_data.data(), input_data.size(), input_shape.data(), input_shape.size());
   ```

2. **从现有的内存（例如：std::vector）创建**：
   可以直接使用 `Ort::Value` 来包装现有的数据。可以通过 `Ort::Value::CreateTensor` 方法指定数据类型和形状。

3. **从 C++ 标准容器（如 `std::vector` 或 `std::array`）创建张量**：
   `Ort::Value` 可以直接从容器对象创建张量，方便快速构建推理的输入数据。

### **常用方法**

#### 1. **获取数据类型**

`Ort::Value` 提供了方法来获取存储的数据类型。例如，获取张量的数据类型（如 `float32`，`int64` 等）：

```cpp
ONNXTensorElementDataType type = input_tensor.GetTensorTypeAndShapeInfo().GetElementType();
```

#### 2. **获取张量的形状**

通过 `Ort::Value` 的 `GetTensorTypeAndShapeInfo()` 方法，你可以获取张量的形状（即它的维度）。

```cpp
Ort::TensorTypeAndShapeInfo tensor_info = input_tensor.GetTensorTypeAndShapeInfo();
std::vector<int64_t> shape = tensor_info.GetShape();
```

#### 3. **获取数据指针**

`Ort::Value` 也允许你访问底层数据。可以使用 `GetTensorData` 来获取存储张量数据的指针：

```cpp
float* float_data = input_tensor.GetTensorData<float>();
```

#### 4. **设置张量的值**

你可以通过 `Ort::Value` 设置张量的数据：

```cpp
input_tensor.SetTensorData<float>(input_data.data());
```

#### 5. **转换为其他类型**

通过 ONNX Runtime 提供的 API，你可以将 `Ort::Value` 转换为其他类型（例如：从张量转为 NumPy 数组，或通过其他方式访问数据）。

### **示例代码：推理输入和输出**

下面是一个基本示例，展示了如何使用 `Ort::Value` 来执行 ONNX 模型推理：

```cpp
#include <onnxruntime/core/providers/shared_ptr.h>
#include <onnxruntime/core/providers/tensor/ort_value.h>
#include <onnxruntime/core/providers/tensor/tensor.h>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXModel");

    // 创建一个 SessionOptions 对象
    Ort::SessionOptions session_options;

    // 加载 ONNX 模型
    std::string model_path = "model.onnx";
    Ort::Session onnx_session(env, model_path.c_str(), session_options);

    // 准备输入数据 (例如: 3x3 数字)
    std::vector<float> input_data = {1.0f, 2.0f, 3.0f, 4.0f, 5.0f, 6.0f, 7.0f, 8.0f, 9.0f};
    std::array<int64_t, 2> input_shape = {1, 9};  // 假设这是一个 1x9 的输入

    // 创建 Ort::Value 对象作为输入张量
    Ort::AllocatorWithDefaultOptions allocator;
    Ort::Value input_tensor = Ort::Value::CreateTensor<float>(allocator, input_data.data(), input_data.size(), input_shape.data(), input_shape.size());

    // 创建 Ort::Value 对象作为输出张量
    std::vector<Ort::Value> output_tensors;

    // 执行推理
    onnx_session.Run(Ort::RunOptions{nullptr}, 
                     &input_name, &input_tensor, 1, // 输入
                     &output_name, &output_tensors, 1); // 输出

    // 输出结果
    float* output_data = output_tensors[0].GetTensorData<float>();
    std::cout << "Inference result: " << output_data[0] << std::endl;

    return 0;
}
```

### **总结**

- `Ort::Value` 是 ONNX Runtime 用于表示和操作张量的核心类之一。
- 它提供了一种接口来管理输入和输出数据，支持多种数据类型和形状。
- 你可以通过 `Ort::Value` 来包装原始数据（如 `std::vector`），并将其传递给模型进行推理。
- 通过 `Ort::Value` 提供的接口，你可以方便地访问张量的数据、形状以及数据类型。

`Ort::Value` 的使用使得与 ONNX Runtime 进行交互变得简便而高效，能够帮助开发者轻松处理模型推理所需的各种数据。

## Ort::Value::CreateTensor 详解

`Ort::Value::CreateTensor` 是 ONNX Runtime 中用于创建张量（Tensor）数据的一个静态函数。张量是 ONNX Runtime 中数据的核心表示形式，通常用于表示模型的输入和输出。通过 `CreateTensor`，你可以将数据封装成 `Ort::Value` 对象，供模型推理使用。

### **函数原型**

```cpp
static Ort::Value CreateTensor(
    Ort::Allocator& allocator, 
    void* data, 
    size_t size, 
    const int64_t* shape, 
    size_t shape_len, 
    ONNXTensorElementDataType type);
```

### **参数详解**

1. **`allocator`**（类型：`Ort::Allocator&`）：
   - 用于分配内存的分配器，ONNX Runtime 会使用它来管理张量的内存。
   - 推荐使用 `Ort::AllocatorWithDefaultOptions` 来创建一个默认的内存分配器。

2. **`data`**（类型：`void*`）：
   - 一个指向存储数据的内存块的指针。数据将被存储在该指针指向的内存位置。
   - 你需要将数据放入这块内存中，数据的类型和结构应该符合所需的张量类型和形状。

3. **`size`**（类型：`size_t`）：
   - 数据的总大小，以字节为单位。
   - `size` 应该等于张量的元素数量乘以每个元素的字节大小。例如，如果每个元素是 `float`（4 字节），而张量包含 100 个元素，那么 `size` 应该是 `100 * 4`。

4. **`shape`**（类型：`const int64_t*`）：
   - 张量的形状，即每个维度的大小。形状是一个整型数组，表示张量的多维尺寸。
   - 例如，对于一个 2D 张量，形状可能是 `{3, 4}`，表示该张量有 3 行 4 列。

5. **`shape_len`**（类型：`size_t`）：
   - 张量形状的维度数量，即 `shape` 数组的长度。
   - 例如，如果张量是 2D，`shape_len` 应该是 2；如果是 3D，`shape_len` 应该是 3，依此类推。

6. **`type`**（类型：`ONNXTensorElementDataType`）：
   - 张量的数据类型，指定张量中元素的类型。ONNX 允许多种数据类型，包括：
     - `ONNX_TENSOR_ELEMENT_DATA_TYPE_FLOAT`（`float` 类型）
     - `ONNX_TENSOR_ELEMENT_DATA_TYPE_INT32`（`int32` 类型）
     - `ONNX_TENSOR_ELEMENT_DATA_TYPE_INT64`（`int64` 类型）
     - `ONNX_TENSOR_ELEMENT_DATA_TYPE_UINT8`（`uint8` 类型）
     - 等等。

### **返回值**

- 返回一个 `Ort::Value` 对象，表示创建的张量。
- `Ort::Value` 对象封装了张量的数据和形状，并可以在后续的推理过程中作为输入或输出使用。

### **示例代码**

以下是一个创建张量并将其用于模型推理的完整示例：

```cpp
#include <onnxruntime/core/providers/tensor/ort_value.h>
#include <onnxruntime/core/providers/tensor/tensor.h>
#include <onnxruntime/core/providers/shared_ptr.h>
#include <onnxruntime/core/providers/common.h>
#include <onnxruntime/core/providers/onnxruntime_typeinfo.h>

#include <iostream>
#include <vector>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXModel");

    // 创建一个默认分配器
    Ort::AllocatorWithDefaultOptions allocator;

    // 创建一个简单的 2D 张量数据（例如：2x3 的矩阵）
    std::vector<float> data = {1.0f, 2.0f, 3.0f, 4.0f, 5.0f, 6.0f};
    std::array<int64_t, 2> shape = {2, 3};  // 张量的形状是 2x3

    // 创建张量对象
    Ort::Value tensor = Ort::Value::CreateTensor<float>(
        allocator, 
        data.data(),            // 数据指针
        data.size() * sizeof(float),  // 数据大小，单位是字节
        shape.data(),           // 形状
        shape.size(),           // 形状的长度
        ONNX_TENSOR_ELEMENT_DATA_TYPE_FLOAT  // 数据类型
    );

    // 获取张量的类型和形状信息
    Ort::TensorTypeAndShapeInfo tensor_info = tensor.GetTensorTypeAndShapeInfo();
    std::vector<int64_t> tensor_shape = tensor_info.GetShape();

    // 打印张量形状
    std::cout << "Tensor Shape: ";
    for (auto dim : tensor_shape) {
        std::cout << dim << " ";
    }
    std::cout << std::endl;

    // 访问张量数据
    float* tensor_data = tensor.GetTensorData<float>();
    std::cout << "Tensor Data: ";
    for (size_t i = 0; i < data.size(); ++i) {
        std::cout << tensor_data[i] << " ";
    }
    std::cout << std::endl;

    return 0;
}
```

### **详细解释**

1. **内存分配**：
   - 使用 `Ort::AllocatorWithDefaultOptions` 创建一个分配器对象，它将在创建张量时分配内存。
   
2. **数据和形状**：
   - 数据是一个 `std::vector<float>`，代表了一个 2x3 张量的数据。
   - 形状 `shape` 是一个 `std::array<int64_t, 2>`，表示张量是 2 行 3 列的。

3. **创建张量**：
   - `Ort::Value::CreateTensor<float>` 方法用来创建一个 `float` 类型的张量。我们传入数据指针、数据大小、形状以及数据类型。
   - 该方法将返回一个 `Ort::Value` 对象，封装了张量的所有信息。

4. **获取张量信息**：
   - 使用 `tensor.GetTensorTypeAndShapeInfo()` 获取张量的类型和形状信息。
   - 使用 `tensor.GetTensorData<float>()` 获取存储在张量中的数据指针。

5. **打印结果**：
   - 打印张量的形状和数据内容。

### **注意事项**

- **内存管理**：`Ort::Value` 对象会自动管理张量的内存。因此，用户不需要手动释放张量的内存，只需保证它的生命周期在需要时有效。
- **数据类型**：确保传入的 `data` 的类型与指定的 `ONNXTensorElementDataType` 类型匹配。例如，如果选择 `ONNX_TENSOR_ELEMENT_DATA_TYPE_FLOAT`，则 `data` 必须是 `float` 类型的数据。

### **总结**

- `Ort::Value::CreateTensor` 是一个用于创建张量的静态方法，可以将数据、形状和数据类型封装成 `Ort::Value` 对象。
- 它为 ONNX Runtime 模型推理提供了输入和输出的数据结构。
- 使用时需要提供内存分配器、数据、数据大小、形状以及数据类型等信息。

## Session::Run() 函数 详解

`Session::Run()` 是 ONNX Runtime 中的一个关键函数，负责执行模型推理（即前向传播）。它的主要作用是根据输入数据进行推理，并返回推理结果。你通过它将输入数据传递给 ONNX 模型，并得到相应的输出。

### **函数定义**

```cpp
Status Run(const Session::RunOptions& run_options,
           const std::vector<std::string>& input_names,
           const std::vector<const Ort::Value*>& input_values,
           const std::vector<std::string>& output_names,
           std::vector<Ort::Value>& output_values);
```

### **参数说明**

1. **`run_options` (Session::RunOptions)**：
   - **类型**：`const Session::RunOptions&`
   - **描述**：设置推理过程中的一些选项，比如是否使用优化等。可以为空，表示使用默认选项。

2. **`input_names` (std::vector<std::string>)**：
   - **类型**：`const std::vector<std::string>&`
   - **描述**：输入张量的名称列表。对于每个输入张量，你需要提供对应的名称，这些名称必须与模型中定义的输入名称一致。

3. **`input_values` (std::vector<const Ort::Value*>)**：
   - **类型**：`const std::vector<const Ort::Value*>&`
   - **描述**：输入数据的值列表。每个输入对应一个 `Ort::Value` 对象，这些对象包含了输入数据。数据的类型和形状应与模型要求的输入一致。

4. **`output_names` (std::vector<std::string>)**：
   - **类型**：`const std::vector<std::string>&`
   - **描述**：输出张量的名称列表。指定你希望从模型中获取的输出名称。如果模型有多个输出，你需要提供所有输出的名称。

5. **`output_values` (std::vector<Ort::Value>)**：
   - **类型**：`std::vector<Ort::Value>&`
   - **描述**：模型推理的输出结果。执行完推理后，结果会被填充到这个 `Ort::Value` 的 vector 中。

### **返回值**

- **类型**：`Status`
- **描述**：函数返回一个 `Status` 对象，表示推理过程的状态。通常在成功时返回 `Status::OK()`，如果失败，则返回相应的错误信息。

### **函数工作流程**

`Session::Run()` 函数在 ONNX Runtime 中负责以下几个步骤：

1. **输入检查**：
   - 检查提供的输入数据是否与模型的输入要求（如名称、形状、数据类型）一致。
   
2. **推理执行**：
   - 使用提供的输入数据执行推理。ONNX Runtime 会根据模型的计算图进行推理操作。

3. **输出生成**：
   - 根据指定的输出名称，返回计算结果。输出的数据会被填充到 `output_values` 中，通常这些数据是 `Ort::Value` 对象，包含了推理结果（如分类的概率、图像的预测框等）。

### **示例代码**

以下是一个简单的使用 `Session::Run()` 函数的示例，它展示了如何加载一个 ONNX 模型并运行推理。

```cpp
#include <onnxruntime/core/providers/cpu/cpu_provider_factory.h>
#include <onnxruntime/core/providers/shared_library/provider_api.h>
#include <onnxruntime/core/providers/tensor/tensor.h>
#include <onnxruntime/core/providers/utils.h>
#include <onnxruntime/core/providers/common.h>
#include <onnxruntime/core/providers/session.h>
#include <iostream>
#include <vector>

int main() {
    // 初始化 ONNX Runtime 环境
    Ort::Env env(ORT_LOGGING_LEVEL_WARNING, "ONNXModel");

    // 加载模型
    const std::string model_path = "path_to_your_model.onnx";
    Ort::SessionOptions session_options;
    Ort::Session onnx_session(env, model_path.c_str(), session_options);

    // 设置输入数据
    std::vector<const char*> input_names = {"input_tensor_name"};  // 假设模型有一个输入张量
    std::vector<Ort::Value> input_values;

    // 创建输入 tensor（假设模型要求输入大小为 1x3x224x224）
    std::vector<float> input_data(1 * 3 * 224 * 224, 1.0f);  // 示例输入数据
    std::vector<int64_t> input_shape = {1, 3, 224, 224};  // 输入张量的形状
    input_values.push_back(Ort::Value::CreateTensor<float>(input_data.data(), input_shape));

    // 设置输出数据
    std::vector<const char*> output_names = {"output_tensor_name"};  // 假设模型有一个输出张量
    std::vector<Ort::Value> output_values;

    // 执行推理
    try {
        onnx_session.Run(Ort::RunOptions(), input_names, input_values.data(), input_names.size(), output_names, output_values);

        // 处理推理结果
        for (const auto& output : output_values) {
            // 处理每个输出的结果
            std::cout << "Output tensor: " << output.GetTensorTypeAndShapeInfo().GetShape()[0] << std::endl;
        }
    } catch (const std::exception& e) {
        std::cerr << "Error during inference: " << e.what() << std::endl;
    }

    return 0;
}
```

### **解释**

1. **初始化环境**：首先创建一个 `Ort::Env` 对象来初始化 ONNX Runtime 环境。
2. **加载模型**：通过 `Ort::Session` 加载一个 ONNX 模型，并使用 `SessionOptions` 进行配置。
3. **准备输入数据**：创建输入数据（如一张图像），并使用 `Ort::Value::CreateTensor` 方法将其转换为 `Ort::Value` 类型。
4. **执行推理**：使用 `Run` 方法执行推理，传入输入数据和请求的输出数据名称。
5. **输出结果**：推理结果被存储在 `output_values` 中，之后可以进一步处理或打印输出。

### **注意事项**

1. **输入和输出数据的类型一致性**：
   - 需要确保输入数据的类型（如 `float`）与模型要求的数据类型一致。
   - 输入数据的形状应与模型的输入形状匹配，否则推理会失败。

2. **异常处理**：
   - `Session::Run()` 可能会抛出异常（如数据类型不匹配、形状不匹配等），因此通常需要在调用时使用 `try-catch` 来捕获并处理异常。

3. **性能优化**：
   - `Session::Run()` 是一个同步操作，因此如果需要优化推理性能，可以考虑异步执行（如通过 `RunOptions` 或使用多线程）或使用硬件加速（如使用 GPU）。

### **总结**

`Session::Run()` 是 ONNX Runtime 推理流程中的核心函数，它负责将输入数据传递给模型并返回结果。理解和正确使用该函数对于模型推理至关重要。通过合适的参数配置（如输入输出的名称和数据类型），可以实现高效、正确的模型推理。