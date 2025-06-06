---
category: Python
date: 2024-05-22 09:00:00 +0800
layout: post
title: python_3_threading模块
tag: Python
---
## 简介

+ threading模块笔记

## python threading 判断多个线程是否完成

在 Python 中，可以使用 `threading.Thread` 的 `is_alive()` 方法或者 `join()` 方法来判断线程是否完成。

以下是几种判断多个线程是否完成的方法：

---

### **方法 1：使用 `join()` 等待所有线程完成**
`join()` 会阻塞主线程，直到子线程完成。这是最常见的方式。

```python
import threading
import time

def worker(name, delay):
    time.sleep(delay)
    print(f"Thread {name} finished.")

# 创建多个线程
threads = []
for i in range(5):
    t = threading.Thread(target=worker, args=(f"Thread-{i}", i))
    threads.append(t)
    t.start()

# 等待所有线程完成
for t in threads:
    t.join()

print("All threads are complete.")
```

---

### **方法 2：循环检查 `is_alive()`**
`is_alive()` 返回线程是否仍在运行。

```python
import threading
import time

def worker(name, delay):
    time.sleep(delay)
    print(f"Thread {name} finished.")

# 创建多个线程
threads = []
for i in range(5):
    t = threading.Thread(target=worker, args=(f"Thread-{i}", i))
    threads.append(t)
    t.start()

# 检查是否所有线程完成
while any(t.is_alive() for t in threads):
    print("Waiting for threads to complete...")
    time.sleep(0.5)

print("All threads are complete.")
```

---

### **方法 3：使用 `Event` 对象通知主线程**
通过线程完成时触发 `Event`，主线程等待所有事件被设置。

```python
import threading
import time

def worker(name, delay, event):
    time.sleep(delay)
    print(f"Thread {name} finished.")
    event.set()  # 通知主线程

# 创建多个线程和事件
events = []
threads = []

for i in range(5):
    event = threading.Event()
    events.append(event)
    t = threading.Thread(target=worker, args=(f"Thread-{i}", i, event))
    threads.append(t)
    t.start()

# 等待所有事件完成
for event in events:
    event.wait()

print("All threads are complete.")
```

---

### **选择合适的方法**
- **简单场景**：使用 `join()` 是最简洁的方式。
- **动态监控**：使用 `is_alive()` 可实现更灵活的监控。
- **通知机制**：使用 `Event` 提供更多控制能力。

如果你的场景需要特定的解决方案，请详细描述，我可以进一步优化代码！

## python3 threading库 启动线程函数并传递参数

在 Python 的 `threading` 模块中，可以通过 `Thread` 类启动线程，并使用 `args` 和 `kwargs` 参数向线程函数传递参数。

以下是具体方法和示例：

### 使用 `args` 和 `kwargs` 传递参数
- **`args`**：用于传递位置参数，以元组形式提供。
- **`kwargs`**：用于传递关键字参数，以字典形式提供。

### 示例代码

```python
import threading
import time

# 定义线程函数
def worker(name, delay, repeat=5):
    for i in range(repeat):
        time.sleep(delay)
        print(f"Thread {name}: {i+1}")

# 创建线程并传递参数
thread1 = threading.Thread(target=worker, args=("A", 1), kwargs={"repeat": 3})
thread2 = threading.Thread(target=worker, args=("B", 2))

# 启动线程
thread1.start()
thread2.start()

# 等待线程完成
thread1.join()
thread2.join()

print("All threads are complete.")
```

### 输出示例
```
Thread A: 1
Thread B: 1
Thread A: 2
Thread B: 2
Thread A: 3
Thread B: 3
Thread B: 4
Thread B: 5
All threads are complete.
```

### 注意事项
1. **`args` 和 `kwargs` 必须匹配目标函数的参数要求**。
2. 使用 `join` 方法可以让主线程等待子线程执行完成。
3. 启动线程后，不能再调用 `start` 方法，否则会抛出 `RuntimeError`。

如果还有其他需求，随时告诉我！

## python3 threading 模块 详解

`threading` 模块是 Python 3 中用于多线程编程的标准库模块，它提供了一种方便的方式来创建和管理线程。多线程允许在同一程序中同时执行多个任务，可以提高程序的并发性和性能。以下是有关 `threading` 模块的详细解释和示例：

**线程的创建**：

1. `threading.Thread` 类：`Thread` 类是创建线程的主要工具。您可以通过创建 `Thread` 类的实例来定义一个新线程，并将要执行的任务（通常是一个函数）传递给 `target` 参数。

   ```python
   import threading

   def my_function():
       # 执行任务的代码

   my_thread = threading.Thread(target=my_function)
   ```

2. 启动线程：使用 `start()` 方法来启动线程，它将在后台执行线程的任务。

   ```python
   my_thread.start()
   ```

**线程的控制**：

1. `threading.Thread.is_alive()`：检查线程是否处于活动状态（正在运行）。

   ```python
   if my_thread.is_alive():
       print("Thread is alive")
   ```

2. `threading.Thread.join()`：等待线程执行完毕。主线程可以使用 `join()` 方法等待其他线程的完成。

   ```python
   my_thread.join()
   ```

**线程同步**：

1. `threading.Lock` 类：`Lock` 类用于创建锁，以确保多个线程之间的互斥访问共享资源。

   ```python
   my_lock = threading.Lock()

   def my_thread_function():
       with my_lock:
           # 访问共享资源的代码
   ```

2. `threading.Event` 类：`Event` 类用于线程之间的事件通知和等待。

   ```python
   my_event = threading.Event()

   def waiting_thread():
       my_event.wait()  # 等待事件被设置
       # 事件被设置后执行的代码

   def setting_thread():
       # 执行某些操作
       my_event.set()  # 设置事件
   ```

**线程间通信**：

1. 使用队列：`queue` 模块提供了线程安全的队列（`queue.Queue`）用于在线程之间安全地传递数据。

   ```python
   import threading
   import queue

   my_queue = queue.Queue()

   def producer():
       # 生产数据
       my_queue.put(data)

   def consumer():
       # 消费数据
       data = my_queue.get()
   ```

**线程池**：

`threading` 模块还提供了 `ThreadPoolExecutor` 类，它允许您管理线程池，以便在应用程序中管理和重复使用线程。这对于需要执行多个任务的情况非常有用。

```python
import concurrent.futures

def my_function():
    # 执行任务的代码

with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
    future = executor.submit(my_function)
    # 更多任务提交和管理
```

**注意事项**：

- Python 中的全局解释器锁（Global Interpreter Lock，GIL）限制了同一时间只能执行一个线程中的 Python 代码。这意味着多线程在 CPU 密集型任务中可能无法充分利用多核处理器。但对于 I/O 密集型任务，多线程通常仍然有用。

- 线程在共享数据时可能会引发竞争条件和死锁等问题，因此在多线程编程中需要小心谨慎，使用适当的同步工具。

总之，`threading` 模块提供了方便的工具来创建、管理和同步线程，允许您实现并发性和并行性，但需要注意线程安全和共享资源的管理问题。对于更高级的并发任务，您还可以考虑使用 `multiprocessing` 模块，它允许您创建多个进程以利用多核处理器。

## python3 threading模块 传入一个参数

如果你想在线程中传递一个参数，可以使用 `args` 参数来传递一个包含单个元素的元组。以下是一个简单的示例：

```python
import threading

def my_function(arg):
    print("Thread is running with argument:", arg)

# 创建一个线程，并传递单个参数
my_arg = "Hello"
my_thread = threading.Thread(target=my_function, args=(my_arg,))

# 启动线程
my_thread.start()

# 等待线程结束
my_thread.join()
```

在这个例子中，`my_function` 函数期望接收一个参数，我们将参数放入一个元组 `(my_arg,)` 中，并将该元组作为 `args` 参数传递给 `Thread` 类的构造函数。

确保参数传递的数据类型和数量与目标函数的定义相匹配。如果有多个参数，仍然需要将它们放入一个元组中，并将该元组作为 `args` 参数传递。

## python3 threading模块 将线程分离到后台

在Python中，可以使用 `threading.Thread` 的 `daemon` 属性将线程设置为后台线程。将线程设置为后台线程意味着当主线程退出时，后台线程也会被终止，而不管它是否完成。

以下是一个简单的示例：

```python
import threading
import time

def my_function():
    for i in range(5):
        print("Thread is running...")
        time.sleep(1)

# 创建一个后台线程
my_thread = threading.Thread(target=my_function)
my_thread.daemon = True

# 启动线程
my_thread.start()

# 主线程等待一段时间
time.sleep(2)

# 主线程退出，后台线程也会被终止
print("Main thread exiting.")
```

在这个例子中，`my_function` 是一个简单的线程函数，线程会在循环中打印消息并休眠1秒。通过将 `my_thread.daemon` 设置为 `True`，我们将线程设置为后台线程。当主线程执行 `print("Main thread exiting.")` 时，主线程会退出，同时后台线程也会被终止。

请注意，如果你的后台线程执行一些关键任务，可能需要谨慎使用后台线程，因为在主线程退出时，可能导致后台线程无法完成其任务。