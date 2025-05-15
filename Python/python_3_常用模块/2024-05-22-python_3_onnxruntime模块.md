---
category: Python
date: 2024-05-22 09:00:00 +0800
layout: post
title: python_3_onnxruntime模块
tag: Python
---
## 简介

+ onnxruntime库相关笔记

## python3 onnxruntime库 是什么

ONNX Runtime是一个用于在Python中执行ONNX模型的库。ONNX（Open Neural Network Exchange）是一种开放式的深度学习模型表示格式，它允许在不同的深度学习框架之间轻松转换模型。ONNX Runtime提供了一个高性能的推理引擎，可以在CPU、GPU和其他加速器上运行ONNX模型。通过使用ONNX Runtime，您可以在Python中轻松加载、优化和执行ONNX模型，这使得您可以快速部署和执行各种类型的深度学习模型。ONNX Runtime支持各种常见的深度学习框架，如PyTorch、TensorFlow等，并提供了用于与这些框架集成的API。

## python3 onnxruntime库 详解

ONNX Runtime是一个用于在Python中执行ONNX模型的库。下面是ONNX Runtime库的一些详细解释：

1. **用途**：ONNX Runtime用于加载、优化和执行ONNX格式的深度学习模型。它是一个用于推理（即模型预测）的高性能引擎。

2. **跨平台性**：ONNX Runtime是一个跨平台的库，可以在不同的设备上运行，包括CPU、GPU和特定硬件加速器。这使得您可以将模型轻松部署到各种设备上，并利用硬件加速以获得更高的性能。

3. **支持的框架**：ONNX Runtime支持各种常见的深度学习框架，如PyTorch、TensorFlow等。这意味着您可以使用这些框架训练模型，并在ONNX Runtime中执行这些模型，而无需进行复杂的转换。

4. **高性能**：ONNX Runtime旨在提供高性能的推理体验。它经过优化，以在各种设备上实现快速的模型推理，并且通常比使用原始深度学习框架进行推理更加高效。

5. **API接口**：ONNX Runtime提供了用于加载、运行和评估模型的Python API接口。这些接口易于使用，并且提供了各种功能，包括异步推理、模型优化和性能调优等。

总之，ONNX Runtime是一个强大的工具，使得您可以在Python中轻松地加载、优化和执行ONNX模型，从而实现快速且高效的深度学习推理。

## python3 onnxruntime库 示例

以下是一个简单的示例，演示如何使用Python中的ONNX Runtime库加载并执行一个ONNX模型：

```python
import onnxruntime as rt
import numpy as np

# 加载ONNX模型
model_path = 'model.onnx'
sess = rt.InferenceSession(model_path)

# 模型输入名称和形状
input_name = sess.get_inputs()[0].name
input_shape = sess.get_inputs()[0].shape
print("模型输入名称:", input_name)
print("模型输入形状:", input_shape)

# 创建模拟输入数据
input_data = np.random.rand(*input_shape).astype(np.float32)

# 执行推理
output = sess.run(None, {input_name: input_data})

# 输出结果
print("模型输出:", output)
```

在这个示例中：

- 首先，我们导入了`onnxruntime`库。
- 然后，我们使用`rt.InferenceSession`加载了一个ONNX模型，该模型的路径存储在`model_path`变量中。
- 我们通过`sess.get_inputs()`获取了模型的输入信息，包括输入名称和形状。
- 接下来，我们创建了一个随机的模拟输入数据`input_data`，其形状与模型输入相匹配。
- 最后，我们使用`sess.run`方法执行推理，将输入数据传递给模型，并获取模型的输出结果。

这只是一个简单的示例，演示了如何使用ONNX Runtime库加载和执行模型。实际应用中，您可能需要根据模型的具体要求进行更复杂的输入数据准备和后处理操作。

## python3 onnxruntime.InferenceSession.run()函数 详解

`onnxruntime.InferenceSession.run()`函数是ONNX Runtime库中用于执行推理（inference）的主要函数。这个函数允许您将输入数据传递给ONNX模型，并获取模型的输出结果。以下是该函数的详解：

### 参数：
- **inputs**：一个字典，表示模型的输入。字典的键是输入张量的名称，值是相应的输入张量数据。如果模型有多个输入，那么这个字典将包含多个键值对。
- **outputs**：一个列表，表示您希望获取的模型输出。列表中的每个元素都是一个字符串，表示模型输出张量的名称。

### 返回值：
- **outputs**：一个列表，包含了模型的输出结果。列表中的每个元素都是一个NumPy数组，表示相应的输出张量数据。如果模型有多个输出，那么返回的列表将包含多个数组。

### 示例：
```python
import onnxruntime as rt
import numpy as np

# 加载ONNX模型
model_path = 'model.onnx'
sess = rt.InferenceSession(model_path)

# 准备输入数据
input_data = np.random.rand(1, 3, 224, 224).astype(np.float32)

# 执行推理
output = sess.run(None, {'input': input_data})

# 输出结果
print("模型输出:", output)
```

在这个示例中，我们首先加载了一个ONNX模型，并准备了输入数据。然后，我们使用`sess.run()`函数执行推理，将输入数据传递给模型，并获取模型的输出结果。最后，我们打印了输出结果。

## onnxruntime get_inputs() 函数 详解

`onnxruntime` 是一个高性能的 ONNX 模型推理库，常用于在 Python 中加载和运行 `.onnx` 格式的模型。`get_inputs()` 是 `InferenceSession` 类的一个方法，用于获取模型的输入信息。下面是对这个函数的详细解释。

---

## 一、基本用法

```python
import onnxruntime as ort

session = ort.InferenceSession("model.onnx")
inputs = session.get_inputs()
```

`get_inputs()` 返回的是一个包含模型输入信息的列表，每个元素是一个 `onnxruntime.NodeArg` 对象，表示一个输入节点。

---

## 二、NodeArg 对象属性详解

每个输入对象（`NodeArg`）有如下常用属性：

| 属性      | 说明                                      |
| ------- | --------------------------------------- |
| `name`  | 输入张量的名称，调用 `run()` 推理时必须使用这个名称          |
| `type`  | 数据类型，比如 `tensor(float)`、`tensor(int64)` |
| `shape` | 输入张量的形状（维度），维度可能是整数或字符串（表示动态维度）         |

### 示例

```python
for i, input in enumerate(inputs):
    print(f"Input {i}:")
    print("  name:", input.name)
    print("  type:", input.type)
    print("  shape:", input.shape)
```

输出示例：

```
Input 0:
  name: input_1
  type: tensor(float)
  shape: ['batch_size', 3, 224, 224]
```

说明该模型输入是一个 4 维张量，数据类型是 float，形状是 `[batch_size, 3, 224, 224]`，其中 `batch_size` 是动态维度。

---

## 三、配合推理使用

你需要使用这些输入信息来构造推理输入字典：

```python
import numpy as np

input_name = session.get_inputs()[0].name
input_shape = session.get_inputs()[0].shape
input_data = np.random.randn(1, 3, 224, 224).astype(np.float32)

output = session.run(None, {input_name: input_data})
```

---

## 四、其他相关函数

* `session.get_outputs()`：获取输出节点的信息，格式与 `get_inputs()` 类似。
* `session.get_modelmeta()`：获取模型元数据（如生产者名、版本等）。

---

如果你有具体的 ONNX 模型文件或需要解析多输入模型的结构，我也可以帮你做进一步分析。你可以上传模型或提供输入信息。