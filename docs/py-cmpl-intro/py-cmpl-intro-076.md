Python超全入门教程：P76：Python多线程入门 🧵

在本节课中，我们将要学习Python中的多线程编程。多线程是一种允许程序同时执行多个任务的技术，类似于我们日常生活中的“多任务处理”。我们将通过一个简单的家务模拟例子，来理解多线程的基本概念、创建方法以及如何管理线程。

---

### 什么是多线程？🤔

多线程用于并发执行多个任务。可以将其想象成我们在同时处理几件不同的事情。例如，一个人可以同时学习、听音乐和吃东西。

多线程特别适用于**IO密集型任务**。IO即输入/输出，例如读取文件或从API获取数据。这类任务通常需要一些时间来完成，并且我们无法精确预知其结束时间。

### 导入模块与基础概念

要使用多线程，我们需要导入Python的`threading`模块。

```python
import threading
```

我们通过访问`threading`模块，然后调用`Thread`构造函数并传入一个目标函数来创建线程。

![](img/df30b99d4a6a270538b1500402a6549f_1.png)

### 一个顺序执行的例子

为了演示，假设我们有一系列家务要做：遛狗、取邮件和倒垃圾。我们首先定义三个函数来处理这些家务。

![](img/df30b99d4a6a270538b1500402a6549f_3.png)

```python
import time

![](img/df30b99d4a6a270538b1500402a6549f_5.png)

def walk_dog():
    time.sleep(8)  # 模拟遛狗需要8秒
    print("You finish walking the dog.")

def take_out_trash():
    time.sleep(2)  # 模拟倒垃圾需要2秒
    print("You take out the trash.")

def get_mail():
    time.sleep(4)  # 模拟取邮件需要4秒
    print("You get the mail.")
```

![](img/df30b99d4a6a270538b1500402a6549f_7.png)

现在，让我们按顺序调用这些函数，看看会发生什么。

```python
walk_dog()
take_out_trash()
get_mail()
```

运行结果将是：程序会先等待8秒完成遛狗，然后花2秒倒垃圾，最后花4秒取邮件。这些函数都在同一个线程（主线程）上运行，因此它们必须按顺序一个接一个地完成。

### 引入多线程：并发执行

上一节我们看到了顺序执行的局限性。本节中，我们来看看如何使用多线程来同时执行所有任务。

![](img/df30b99d4a6a270538b1500402a6549f_9.png)

我们可以为每个家务创建一个线程对象，并同时启动它们。

![](img/df30b99d4a6a270538b1500402a6549f_11.png)

```python
# 创建线程对象
chore1 = threading.Thread(target=walk_dog)
chore2 = threading.Thread(target=take_out_trash)
chore3 = threading.Thread(target=get_mail)

![](img/df30b99d4a6a270538b1500402a6549f_12.png)

# 启动所有线程
chore1.start()
chore2.start()
chore3.start()
```

现在，这三个函数将并发执行。由于倒垃圾只需2秒，它会最先完成；取邮件需4秒，其次完成；遛狗需8秒，最后完成。任务的完成顺序与它们被调用的顺序无关，只取决于各自所需的执行时间。

### 使用 `join()` 方法等待线程完成

![](img/df30b99d4a6a270538b1500402a6549f_14.png)

在上面的例子中，主线程在启动所有子线程后会立即继续执行。如果我们想在所有家务完成后打印一条确认信息，就需要让主线程等待所有子线程结束。

![](img/df30b99d4a6a270538b1500402a6549f_16.png)

这时，我们可以使用线程对象的 `join()` 方法。

![](img/df30b99d4a6a270538b1500402a6549f_18.png)

以下是使用 `join()` 方法的步骤：

```python
chore1 = threading.Thread(target=walk_dog)
chore2 = threading.Thread(target=take_out_trash)
chore3 = threading.Thread(target=get_mail)

chore1.start()
chore2.start()
chore3.start()

![](img/df30b99d4a6a270538b1500402a6549f_20.png)

# 等待所有线程完成
chore1.join()
chore2.join()
chore3.join()

![](img/df30b99d4a6a270538b1500402a6549f_22.png)

print("All chores are complete.")
```

![](img/df30b99d4a6a270538b1500402a6549f_24.png)

现在，`print(“All chores are complete.”)` 这行代码会等到所有线程（即所有家务）都完成后才执行。

![](img/df30b99d4a6a270538b1500402a6549f_26.png)

### 向线程函数传递参数

有时，我们的目标函数需要接收参数。例如，`walk_dog` 函数可能需要知道狗的名字。

![](img/df30b99d4a6a270538b1500402a6549f_28.png)

我们可以通过 `Thread` 构造函数的 `args` 关键字参数来传递参数。`args` 接收一个元组。

```python
def walk_dog(first_name, last_name):
    time.sleep(8)
    print(f"You finish walking {first_name} {last_name}.")

![](img/df30b99d4a6a270538b1500402a6549f_30.png)

# 创建线程并传递参数
chore1 = threading.Thread(target=walk_dog, args=("Scooby", "Doo",))
```

![](img/df30b99d4a6a270538b1500402a6549f_32.png)

**重要提示**：如果只有一个参数，必须在元组末尾加上逗号，例如 `args=(“Scooby”,)`，以向Python表明这是一个元组，而不是一个普通的括号表达式。

![](img/df30b99d4a6a270538b1500402a6549f_34.png)

---

### 总结

![](img/df30b99d4a6a270538b1500402a6549f_36.png)

本节课中我们一起学习了Python多线程的基础知识。

*   **核心概念**：多线程用于并发执行多个任务，尤其适合IO密集型操作。
*   **创建线程**：通过 `threading.Thread(target=function_name)` 创建线程对象，并使用 `start()` 方法启动。
*   **管理线程**：使用 `join()` 方法可以让主线程等待子线程完成后再继续执行。
*   **传递参数**：通过 `args` 参数向目标函数传递参数，注意参数需要放在元组中。

![](img/df30b99d4a6a270538b1500402a6549f_38.png)

通过模拟处理家务的例子，我们直观地看到了多线程如何提升程序效率，让多个任务能够同时进行。记住，多线程任务的完成顺序取决于各自任务的执行时间，而非启动顺序。