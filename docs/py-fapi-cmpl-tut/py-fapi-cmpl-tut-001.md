# 001：第 1 部分

![](img/61b5aa4c68627fe37ce2e42649211167_1.png)

![](img/61b5aa4c68627fe37ce2e42649211167_2.png)

![](img/61b5aa4c68627fe37ce2e42649211167_3.png)

## 概述

![](img/61b5aa4c68627fe37ce2e42649211167_5.png)

在本课程中，我们将学习如何使用 FastAPI 框架构建 Web 应用程序。FastAPI 是一个流行的 Python Web 框架，因其高性能、简洁性以及对现代 Python 特性的支持而广受欢迎。在本系列视频中，我们将探讨该框架的不同方面，并学习如何利用它们在一个简单的 Web 应用程序中实现功能。

![](img/61b5aa4c68627fe37ce2e42649211167_7.png)

![](img/61b5aa4c68627fe37ce2e42649211167_9.png)

![](img/61b5aa4c68627fe37ce2e42649211167_10.png)

![](img/61b5aa4c68627fe37ce2e42649211167_12.png)

![](img/61b5aa4c68627fe37ce2e42649211167_13.png)

![](img/61b5aa4c68627fe37ce2e42649211167_15.png)

![](img/61b5aa4c68627fe37ce2e42649211167_17.png)

## 环境设置

![](img/61b5aa4c68627fe37ce2e42649211167_19.png)

![](img/61b5aa4c68627fe37ce2e42649211167_21.png)

![](img/61b5aa4c68627fe37ce2e42649211167_23.png)

![](img/61b5aa4c68627fe37ce2e42649211167_25.png)

![](img/61b5aa4c68627fe37ce2e42649211167_27.png)

![](img/61b5aa4c68627fe37ce2e42649211167_28.png)

上一节我们介绍了课程概述，本节中我们来看看如何设置开发环境。

要开始我们的项目，首先需要创建一个文件夹来安装项目依赖。

以下是创建和激活虚拟环境的步骤：

1.  打开 VS Code 并创建一个新文件夹，例如 `bookley`。
2.  在 VS Code 中打开该文件夹。
3.  打开终端，使用 Python 的 `venv` 模块创建虚拟环境：
    ```bash
    python3 -m venv venv
    ```
4.  激活虚拟环境：
    *   **Linux/MacOS**:
        ```bash
        source venv/bin/activate
        ```
    *   **Windows**:
        ```bash
        venv\Scripts\activate
        ```
5.  激活后，确认 Python 解释器指向虚拟环境：
    ```bash
    which python3
    ```

![](img/61b5aa4c68627fe37ce2e42649211167_30.png)

![](img/61b5aa4c68627fe37ce2e42649211167_31.png)

![](img/61b5aa4c68627fe37ce2e42649211167_33.png)

![](img/61b5aa4c68627fe37ce2e42649211167_34.png)

## 安装 FastAPI

![](img/61b5aa4c68627fe37ce2e42649211167_36.png)

![](img/61b5aa4c68627fe37ce2e42649211167_38.png)

![](img/61b5aa4c68627fe37ce2e42649211167_39.png)

![](img/61b5aa4c68627fe37ce2e42649211167_41.png)

![](img/61b5aa4c68627fe37ce2e42649211167_43.png)

现在我们已经激活了虚拟环境，接下来安装 FastAPI。

![](img/61b5aa4c68627fe37ce2e42649211167_45.png)

![](img/61b5aa4c68627fe37ce2e42649211167_47.png)

使用 `pip` 安装 FastAPI：
```bash
pip install fastapi
```

![](img/61b5aa4c68627fe37ce2e42649211167_49.png)

![](img/61b5aa4c68627fe37ce2e42649211167_51.png)

安装完成后，可以通过导入模块来验证安装：
```python
import fastapi
```

![](img/61b5aa4c68627fe37ce2e42649211167_53.png)

![](img/61b5aa4c68627fe37ce2e42649211167_54.png)

或者，通过运行 FastAPI CLI 来验证：
```bash
fastapi
```

## 创建简单的 Web 服务器

![](img/61b5aa4c68627fe37ce2e42649211167_56.png)

![](img/61b5aa4c68627fe37ce2e42649211167_58.png)

上一节我们完成了环境设置和 FastAPI 安装，本节中我们来看看如何创建一个简单的 Web 服务器。

![](img/61b5aa4c68627fe37ce2e42649211167_60.png)

![](img/61b5aa4c68627fe37ce2e42649211167_62.png)

首先，创建一个名为 `main.py` 的文件。

![](img/61b5aa4c68627fe37ce2e42649211167_64.png)

![](img/61b5aa4c68627fe37ce2e42649211167_66.png)

在 `main.py` 中，导入 FastAPI 类并创建一个应用实例：
```python
from fastapi import FastAPI

![](img/61b5aa4c68627fe37ce2e42649211167_68.png)

![](img/61b5aa4c68627fe37ce2e42649211167_69.png)

app = FastAPI()
```

接下来，定义一个处理根路径 GET 请求的路由：
```python
@app.get("/")
async def read_root():
    return {"message": "Hello World"}
```

要运行这个服务器，可以使用 FastAPI 的开发服务器命令：
```bash
fastapi dev main.py
```

服务器将在 `http://localhost:8000` 启动。访问该地址，你将看到返回的 JSON 消息。

![](img/61b5aa4c68627fe37ce2e42649211167_71.png)

## 定义更多端点

![](img/61b5aa4c68627fe37ce2e42649211167_73.png)

![](img/61b5aa4c68627fe37ce2e42649211167_75.png)

![](img/61b5aa4c68627fe37ce2e42649211167_77.png)

上一节我们创建了一个简单的 Web 服务器，本节中我们来看看如何定义更多端点以返回不同的响应。

我们将创建一个用于问候用户的端点。

以下是创建带路径参数的问候端点的步骤：

1.  定义一个 GET 端点，路径为 `/greet/{name}`。
2.  在路径处理函数中接收 `name` 参数。
3.  返回包含问候消息的字典。

代码示例如下：
```python
@app.get("/greet/{name}")
async def greet_name(name: str):
    return {"message": f"Hello {name}"}
```

访问 `http://localhost:8000/greet/Jonathan`，服务器将返回 `{"message": "Hello Jonathan"}`。

## 使用 HTTP 客户端

到目前为止，我们一直使用浏览器作为 HTTP 客户端。但在实际开发中，使用专门的 HTTP 客户端工具（如 Postman、Insomnia 或 RESTfox）会更方便。

我们将使用 RESTfox 来测试我们的 API 端点。

![](img/61b5aa4c68627fe37ce2e42649211167_79.png)

![](img/61b5aa4c68627fe37ce2e42649211167_81.png)

![](img/61b5aa4c68627fe37ce2e42649211167_83.png)

![](img/61b5aa4c68627fe37ce2e42649211167_85.png)

以下是使用 RESTfox 的基本步骤：

1.  打开 RESTfox 并创建一个新的工作空间。
2.  在工作空间中创建一个新的 HTTP 请求。
3.  设置请求方法（如 GET）和目标 URL（如 `http://localhost:8000/`）。
4.  发送请求并查看响应。

## 查询参数

除了路径参数，FastAPI 还允许我们通过查询参数向服务器发送数据。

查询参数是通过 URL 中的键值对传递的，例如 `?name=Jonathan`。

以下是如何在 FastAPI 中定义和使用查询参数：

1.  在路径处理函数中定义一个参数（例如 `name`），但不将其包含在 URL 路径中。
2.  FastAPI 会自动将其视为查询参数。
3.  可以指定参数类型（如 `str`）和默认值。

代码示例如下：
```python
@app.get("/greet")
async def greet_user(name: str = "User"):
    return {"message": f"Hello {name}"}
```

访问 `http://localhost:8000/greet?name=Jonathan`，服务器将返回 `{"message": "Hello Jonathan"}`。

## 混合路径参数和查询参数

在某些情况下，我们可能需要在同一个端点中同时使用路径参数和查询参数。

以下是如何混合使用路径参数和查询参数：

1.  在 URL 路径中定义路径参数（如 `{name}`）。
2.  在路径处理函数中定义额外的参数作为查询参数（如 `age`）。
3.  发送请求时，在 URL 中提供路径参数，在查询字符串中提供查询参数。

代码示例如下：
```python
@app.get("/greet/{name}")
async def greet_user_with_age(name: str, age: int = 0):
    return {"message": f"Hello {name}, you are {age} years old"}
```

访问 `http://localhost:8000/greet/Jonathan?age=23`，服务器将返回 `{"message": "Hello Jonathan, you are 23 years old"}`。

## 可选查询参数

有时，我们希望查询参数是可选的，并在未提供时使用默认值。

以下是如何定义可选查询参数：

![](img/61b5aa4c68627fe37ce2e42649211167_87.png)

![](img/61b5aa4c68627fe37ce2e42649211167_89.png)

![](img/61b5aa4c68627fe37ce2e42649211167_90.png)

1.  从 `typing` 模块导入 `Optional` 类。
2.  将参数类型定义为 `Optional[str]` 或 `Optional[int]`。
3.  为参数提供默认值。

![](img/61b5aa4c68627fe37ce2e42649211167_92.png)

![](img/61b5aa4c68627fe37ce2e42649211167_94.png)

![](img/61b5aa4c68627fe37ce2e42649211167_96.png)

代码示例如下：
```python
from typing import Optional

![](img/61b5aa4c68627fe37ce2e42649211167_98.png)

![](img/61b5aa4c68627fe37ce2e42649211167_99.png)

@app.get("/greet")
async def greet_optional(name: Optional[str] = "User", age: Optional[int] = 0):
    return {"message": f"Hello {name}, you are {age} years old"}
```

如果访问 `http://localhost:8000/greet`，服务器将返回 `{"message": "Hello User, you are 0 years old"}`。如果提供 `name` 和 `age` 参数，则使用提供的值。

## 请求体

除了路径参数和查询参数，我们还可以通过请求体向服务器发送数据，通常用于创建或更新资源。

以下是如何使用请求体发送数据：

1.  定义一个 Pydantic 模型来验证请求体数据。
2.  在路径处理函数中将该模型作为参数注入。
3.  使用模型验证数据并返回响应。

![](img/61b5aa4c68627fe37ce2e42649211167_101.png)

![](img/61b5aa4c68627fe37ce2e42649211167_102.png)

![](img/61b5aa4c68627fe37ce2e42649211167_104.png)

![](img/61b5aa4c68627fe37ce2e42649211167_106.png)

代码示例如下：
```python
from pydantic import BaseModel

![](img/61b5aa4c68627fe37ce2e42649211167_108.png)

![](img/61b5aa4c68627fe37ce2e42649211167_109.png)

![](img/61b5aa4c68627fe37ce2e42649211167_111.png)

class BookCreate(BaseModel):
    title: str
    author: str

![](img/61b5aa4c68627fe37ce2e42649211167_113.png)

![](img/61b5aa4c68627fe37ce2e42649211167_115.png)

@app.post("/createbook")
async def create_book(book: BookCreate):
    return {"title": book.title, "author": book.author}
```

![](img/61b5aa4c68627fe37ce2e42649211167_117.png)

使用 RESTfox 发送 POST 请求到 `http://localhost:8000/createbook`，并在请求体中提供 JSON 数据（如 `{"title": "Learn FastAPI", "author": "John Doe"}`），服务器将返回创建的书本信息。

## 请求头

FastAPI 允许我们访问请求头信息，例如 `User-Agent`、`Host` 等。

以下是如何访问请求头：

![](img/61b5aa4c68627fe37ce2e42649211167_119.png)

![](img/61b5aa4c68627fe37ce2e42649211167_120.png)

1.  从 `fastapi` 导入 `Header` 函数。
2.  在路径处理函数中定义参数，并使用 `Header` 作为默认值。
3.  返回包含请求头信息的字典。

代码示例如下：
```python
from fastapi import Header

@app.get("/headers")
async def get_headers(user_agent: Optional[str] = Header(None), host: Optional[str] = Header(None)):
    return {"User-Agent": user_agent, "Host": host}
```

访问 `http://localhost:8000/headers`，服务器将返回请求头信息。

## 状态码

我们可以自定义端点返回的 HTTP 状态码。

以下是如何指定状态码：

![](img/61b5aa4c68627fe37ce2e42649211167_122.png)

![](img/61b5aa4c68627fe37ce2e42649211167_123.png)

1.  从 `fastapi` 导入 `status` 模块。
2.  在路由装饰器中指定 `status_code` 参数。

代码示例如下：
```python
from fastapi import status

@app.post("/createbook", status_code=status.HTTP_201_CREATED)
async def create_book(book: BookCreate):
    return {"title": book.title, "author": book.author}
```

当创建资源成功时，服务器将返回状态码 201（Created）。

## 总结

![](img/61b5aa4c68627fe37ce2e42649211167_125.png)

![](img/61b5aa4c68627fe37ce2e42649211167_126.png)

![](img/61b5aa4c68627fe37ce2e42649211167_128.png)

本节课中我们一起学习了如何使用 FastAPI 构建简单的 Web 服务器。我们探讨了如何定义路由、使用路径参数和查询参数、处理请求体、访问请求头以及自定义状态码。通过这些基础知识，你已经能够创建功能丰富的 API 端点。在接下来的课程中，我们将深入探讨更高级的主题，如数据库集成、身份验证和授权等。