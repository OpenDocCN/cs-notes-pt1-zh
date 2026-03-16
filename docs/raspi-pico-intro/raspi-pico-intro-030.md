树莓派Pico初学者入门：5.2：连接互联网

在本节课中，我们将学习如何将树莓派Pico连接到本地无线网络，并通过互联网查询或连接特定网站。这并非让Pico像普通电脑一样浏览网页，而是使其能够从特定网站发送或接收基于文本的数据。

上一节我们介绍了Pico的基本编程概念，本节中我们来看看如何让它接入网络。

我们不需要额外的硬件，只需一块Pico和连接电脑的USB线。我们将从头开始编写代码，并逐步解释每个部分的作用。完整的代码可在课程页面找到。

首先，导入所需的库。我们需要 `network` 和 `urequests`，它们都是MicroPython标准库的一部分，同时还需要导入 `time`。

```python
import network
import urequests
import time
```

接下来，创建变量来存储Wi-Fi网络的详细信息。这可以是家庭路由器或手机热点，只要它能提供互联网接入即可。

```python
SSID = "你的Wi-Fi名称"
PASSWORD = "你的Wi-Fi密码"
```

现在，创建一个用于连接互联网的函数。无线代码容易变得混乱，因此使用函数是一个好习惯。

以下是连接Pico到本地无线网络的核心代码。我们首先设置一个网络接口实例，类似于设置GPIO引脚。然后激活硬件，并使用凭证进行连接。

```python
def connect_to_wifi():
    wlan = network.WLAN(network.STA_IF)
    wlan.active(True)
    wlan.connect(SSID, PASSWORD)
```

在Pico上使用无线功能时，使用 `print` 语句进行调试非常重要，因为它可能在某些步骤失败。通过打印信息，我们可以看到问题所在。

我们在连接过程中添加等待和状态提示。

```python
    while not wlan.isconnected():
        print("正在连接，请稍候...")
        time.sleep(1)
    print("连接成功！")
```

连接成功后，获取Pico在无线网络中的IP地址非常有用。

```python
    ip_address = wlan.ifconfig()[0]
    print("IP地址:", ip_address)
    return ip_address
```

现在，调用这个函数来测试连接。运行代码后，我们将看到连接过程的状态打印。

处理无线连接时，错误处理是一个非常有用的工具。我们将使用 `try` 和 `except` 语句来实现。`try` 块中的代码将被尝试执行，如果出现错误或Pico崩溃，则会运行 `except` 块中的代码。

```python
try:
    ip = connect_to_wifi()
except Exception as e:
    print("连接过程中出现错误:", e)
```

现在，我们已经将Pico连接到本地网络，接下来让它向互联网上的一个网站发送请求。

首先，创建一个变量来存储我们想要查询的网站地址。

```python
site_url = "http://worldtimeapi.org/api/timezone/Etc/UTC"
```

然后，我们让Pico向该网站发出请求，并将返回的所有信息存储在一个变量中。返回的数据通常是JSON格式，`.json()` 方法会将其转换为Python可读的字符串。

```python
try:
    print(f"正在查询: {site_url}")
    response = urequests.get(site_url)
    data = response.json()
    print("收到的数据:", data)
    response.close()
except Exception as e:
    print("查询过程中出现错误:", e)
    response.close()
```

运行代码，我们应该能看到连接成功、查询进行以及返回的信息。我们仅用10美元的微控制器就能从世界另一端获取信息并存储为可用数据，这非常酷。

![](img/96f8bf85d15eb7e252ebe952b40397b9_1.png)

我们查询的这个网站会返回精确的日期和时间给Pico。我们可以使用字符串操作从中提取实际需要的数据。根据返回信息的结构，这可能简单或复杂。

至此，我们完成了无线章节的第一个基础步骤。仅仅从一个网站查询时间可能看起来有些刻意，但实际上，互联网上有大量网站可以为你的项目提供非常有用的信息。

然而，我们不能随意连接任何网站。它必须是能够返回某种JSON字符串的特定网站。例如，尝试连接Google可能会导致Pico崩溃。

以下是你可以查询信息的一些网站类型示例：
*   **趣味信息**：随机笑话、猫猫冷知识、禅意名言。
*   **实用技术信息**：国际空间站的实时位置、特定货币的全球汇率、美国地质调查局过去一小时检测到的所有地震。

课程页面将提供这些及更多网站的链接。如果你发现任何对Pico项目有用的酷网站，请告诉我们。

![](img/96f8bf85d15eb7e252ebe952b40397b9_3.png)

![](img/96f8bf85d15eb7e252ebe952b40397b9_4.png)

本节课中我们一起学习了：
1.  使用 `network` 库将Pico连接到本地无线网络。
2.  在Pico上进行无线编程时，使用 `try/except` 进行错误处理、使用 `print` 语句帮助调试问题以及使用函数是明智的做法。
3.  只要你的无线网络能访问互联网，Pico就可以查询网站以接收有用信息。