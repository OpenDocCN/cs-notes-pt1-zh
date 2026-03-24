# 002：构建用户认证、授权、关系与部署

## 概述

![](img/7d5adcabc5e6be579c6937eaaf16dbca_1.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_2.png)

在本节课中，我们将学习如何使用 FastAPI 构建一个完整的 API 端点，包括用户认证、授权、数据库关系、错误处理、中间件、电子邮件发送、后台任务、API 文档以及最终部署。我们将从获取当前登录用户开始，逐步实现角色管理、数据库关系、错误处理、中间件、电子邮件验证、密码重置、后台任务、API 文档和部署。

---

![](img/7d5adcabc5e6be579c6937eaaf16dbca_4.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_6.png)

## 获取当前登录用户

上一节我们介绍了令牌验证，本节中我们来看看如何获取当前登录用户。

首先，我们需要创建一个依赖项来获取当前登录的用户。这个依赖项将使用我们之前创建的令牌验证依赖项。

```python
from fastapi import Depends
from sqlalchemy.ext.asyncio import AsyncSession
from source.db.main import get_session
from source.auth.service import UserService
from source.auth.models import User

async def get_current_user(
    token: str = Depends(AccessTokenBearer()),
    session: AsyncSession = Depends(get_session)
) -> User:
    token_data = decode_access_token(token)
    user_email = token_data.get("user", {}).get("email")
    user_service = UserService()
    user = await user_service.get_user_by_email(session, user_email)
    return user
```

接下来，我们创建一个路由来获取当前登录用户。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_8.png)

```python
from fastapi import APIRouter, Depends
from source.auth.dependencies import get_current_user

![](img/7d5adcabc5e6be579c6937eaaf16dbca_10.png)

router = APIRouter()

![](img/7d5adcabc5e6be579c6937eaaf16dbca_12.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_14.png)

@router.get("/me")
async def get_current_user_endpoint(user: User = Depends(get_current_user)):
    return user
```

现在，我们可以测试这个端点。首先，我们需要获取一个有效的访问令牌，然后在请求头中添加授权头。

---

## 角色管理

上一节我们介绍了如何获取当前登录用户，本节中我们来看看如何实现角色管理。

首先，我们在用户模型中添加一个角色字段。

```python
from sqlmodel import Field
from typing import Optional

![](img/7d5adcabc5e6be579c6937eaaf16dbca_16.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_18.png)

class User(SQLModel, table=True):
    role: Optional[str] = Field(default="user", sa_column_kwargs={"server_default": "user"})
```

接下来，我们创建一个依赖项来检查用户角色。

```python
from fastapi import HTTPException, status
from typing import List

class RoleChecker:
    def __init__(self, allowed_roles: List[str]):
        self.allowed_roles = allowed_roles

    async def __call__(self, current_user: User = Depends(get_current_user)):
        if current_user.role in self.allowed_roles:
            return True
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="You are not permitted to perform this action."
        )
```

现在，我们可以在路由中使用这个依赖项来限制访问。

```python
from source.auth.dependencies import RoleChecker

![](img/7d5adcabc5e6be579c6937eaaf16dbca_20.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_21.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_22.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_23.png)

admin_role_checker = RoleChecker(allowed_roles=["admin"])

@router.get("/admin-only", dependencies=[Depends(admin_role_checker)])
async def admin_only_endpoint():
    return {"message": "This is an admin-only endpoint."}
```

---

## 数据库关系

上一节我们介绍了角色管理，本节中我们来看看如何建立数据库关系。

首先，我们在书籍模型中添加一个外键，关联到用户表。

```python
from sqlmodel import Field, Relationship
from typing import Optional

class Book(SQLModel, table=True):
    user_uid: Optional[str] = Field(default=None, foreign_key="user.uid")
    user: Optional[User] = Relationship(back_populates="books")
```

接下来，我们在用户模型中添加反向关系。

```python
class User(SQLModel, table=True):
    books: List[Book] = Relationship(back_populates="user")
```

现在，我们可以在创建书籍时关联当前登录用户。

```python
@router.post("/books")
async def create_book(
    book_data: BookCreate,
    user: User = Depends(get_current_user),
    session: AsyncSession = Depends(get_session)
):
    book_service = BookService()
    book = await book_service.create_book(session, book_data, user.uid)
    return book
```

---

## 错误处理

上一节我们介绍了数据库关系，本节中我们来看看如何自定义错误处理。

首先，我们创建一个自定义异常类。

```python
class BooklyException(Exception):
    """Base class for all custom exceptions in the app."""
    pass

class InvalidTokenError(BooklyException):
    """Raised when an invalid or expired token is provided."""
    pass
```

接下来，我们创建一个异常处理器。

```python
from fastapi import Request
from fastapi.responses import JSONResponse

def create_exception_handler(status_code: int, detail: Any):
    async def exception_handler(request: Request, exc: Exception):
        return JSONResponse(
            content={"detail": detail},
            status_code=status_code
        )
    return exception_handler
```

然后，我们将自定义异常注册到 FastAPI 应用中。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_25.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_27.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_29.png)

```python
from source.errors import InvalidTokenError

app.add_exception_handler(InvalidTokenError, create_exception_handler(401, "Invalid token provided."))
```

---

## 中间件

上一节我们介绍了错误处理，本节中我们来看看如何使用中间件。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_31.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_32.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_34.png)

首先，我们创建一个日志中间件。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_36.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_38.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_40.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_42.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_44.png)

```python
import time
from fastapi import Request

@app.middleware("http")
async def custom_logging_middleware(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)
    process_time = time.time() - start_time
    print(f"{request.method} {request.url.path} completed in {process_time:.2f}s")
    return response
```

接下来，我们创建一个授权中间件。

```python
from fastapi.responses import JSONResponse

@app.middleware("http")
async def authorization_middleware(request: Request, call_next):
    if "authorization" not in request.headers:
        return JSONResponse(
            content={"message": "Not authenticated", "resolution": "Please provide valid credentials."},
            status_code=401
        )
    response = await call_next(request)
    return response
```

![](img/7d5adcabc5e6be579c6937eaaf16dbca_46.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_47.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_49.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_51.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_53.png)

---

![](img/7d5adcabc5e6be579c6937eaaf16dbca_55.png)

## 电子邮件发送

上一节我们介绍了中间件，本节中我们来看看如何发送电子邮件。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_57.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_59.png)

首先，我们配置电子邮件设置。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_61.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_62.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_64.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_66.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_68.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_70.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_72.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_74.png)

```python
from fastapi_mail import FastMail, ConnectionConfig

config = ConnectionConfig(
    MAIL_USERNAME="your-email@gmail.com",
    MAIL_PASSWORD="your-app-password",
    MAIL_FROM="your-email@gmail.com",
    MAIL_PORT=587,
    MAIL_SERVER="smtp.gmail.com",
    MAIL_STARTTLS=True,
    MAIL_SSL_TLS=False
)

fm = FastMail(config)
```

接下来，我们创建一个发送电子邮件的函数。

```python
from fastapi_mail import MessageSchema

async def send_email(recipients: List[str], subject: str, body: str):
    message = MessageSchema(
        recipients=recipients,
        subject=subject,
        body=body,
        subtype="html"
    )
    await fm.send_message(message)
```

![](img/7d5adcabc5e6be579c6937eaaf16dbca_76.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_78.png)

现在，我们可以在用户注册时发送验证电子邮件。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_80.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_82.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_83.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_85.png)

```python
@router.post("/signup")
async def signup(user_data: UserCreate, session: AsyncSession = Depends(get_session)):
    user_service = UserService()
    user = await user_service.create_user(session, user_data)
    verification_token = create_url_safe_token({"email": user.email})
    verification_link = f"{config.DOMAIN}/auth/verify/{verification_token}"
    html_message = f"<h1>Verify your email</h1><p>Click <a href='{verification_link}'>here</a> to verify your email.</p>"
    await send_email([user.email], "Verify your email", html_message)
    return {"message": "Account created. Please check your email to verify your account."}
```

---

## 密码重置

上一节我们介绍了电子邮件发送，本节中我们来看看如何实现密码重置。

首先，我们创建一个密码重置请求端点。

```python
@router.post("/password-reset-request")
async def password_reset_request(email_data: EmailSchema):
    user_service = UserService()
    user = await user_service.get_user_by_email(session, email_data.email)
    if not user:
        raise HTTPException(status_code=404, detail="User not found.")
    reset_token = create_url_safe_token({"email": user.email})
    reset_link = f"{config.DOMAIN}/auth/password-reset-confirm/{reset_token}"
    html_message = f"<h1>Reset your password</h1><p>Click <a href='{reset_link}'>here</a> to reset your password.</p>"
    await send_email([user.email], "Reset your password", html_message)
    return {"message": "Password reset link sent. Please check your email."}
```

接下来，我们创建一个密码重置确认端点。

```python
@router.post("/password-reset-confirm/{token}")
async def password_reset_confirm(token: str, passwords: PasswordResetConfirm, session: AsyncSession = Depends(get_session)):
    token_data = decode_url_safe_token(token)
    user_email = token_data.get("email")
    user_service = UserService()
    user = await user_service.get_user_by_email(session, user_email)
    if not user:
        raise HTTPException(status_code=404, detail="User not found.")
    if passwords.new_password != passwords.confirm_new_password:
        raise HTTPException(status_code=400, detail="Passwords do not match.")
    hashed_password = generate_password_hash(passwords.new_password)
    await user_service.update_user(session, user, {"password_hash": hashed_password})
    return {"message": "Password reset successfully."}
```

---

![](img/7d5adcabc5e6be579c6937eaaf16dbca_87.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_88.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_90.png)

## 后台任务

![](img/7d5adcabc5e6be579c6937eaaf16dbca_92.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_93.png)

上一节我们介绍了密码重置，本节中我们来看看如何使用后台任务。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_95.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_96.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_98.png)

首先，我们使用 FastAPI 的内置后台任务功能。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_100.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_102.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_104.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_105.png)

```python
from fastapi import BackgroundTasks

![](img/7d5adcabc5e6be579c6937eaaf16dbca_107.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_108.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_109.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_111.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_113.png)

@router.post("/send-email")
async def send_email_endpoint(
    email_data: EmailSchema,
    background_tasks: BackgroundTasks
):
    html_message = "<h1>Welcome to the app</h1>"
    background_tasks.add_task(send_email, email_data.addresses, "Welcome", html_message)
    return {"message": "Email sent successfully."}
```

![](img/7d5adcabc5e6be579c6937eaaf16dbca_115.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_116.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_118.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_119.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_120.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_121.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_123.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_125.png)

接下来，我们使用 Celery 处理后台任务。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_127.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_128.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_130.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_131.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_133.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_134.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_135.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_137.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_139.png)

```python
from celery import Celery

![](img/7d5adcabc5e6be579c6937eaaf16dbca_141.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_143.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_145.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_147.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_149.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_151.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_152.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_154.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_156.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_158.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_160.png)

celery_app = Celery("tasks", broker=config.REDIS_URL, backend=config.REDIS_URL)

@celery_app.task
def send_email_task(recipients: List[str], subject: str, body: str):
    # 发送电子邮件的逻辑
    pass
```

现在，我们可以在路由中调用 Celery 任务。

```python
@router.post("/send-email-celery")
async def send_email_celery_endpoint(email_data: EmailSchema):
    html_message = "<h1>Welcome to the app</h1>"
    send_email_task.delay(email_data.addresses, "Welcome", html_message)
    return {"message": "Email sent successfully."}
```

---

## API 文档

上一节我们介绍了后台任务，本节中我们来看看如何生成 API 文档。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_162.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_164.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_166.png)

FastAPI 自动生成 OpenAPI 文档，我们可以通过访问 `/docs` 或 `/redoc` 来查看。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_168.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_169.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_171.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_172.png)

我们还可以自定义文档的元数据。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_174.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_176.png)

```python
from fastapi import FastAPI

![](img/7d5adcabc5e6be579c6937eaaf16dbca_178.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_180.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_181.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_183.png)

app = FastAPI(
    title="Bookly API",
    description="A simple book review API.",
    version="1.0.0",
    contact={
        "name": "Your Name",
        "email": "your-email@example.com"
    },
    docs_url="/api/docs",
    redoc_url="/api/redoc"
)
```

![](img/7d5adcabc5e6be579c6937eaaf16dbca_185.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_186.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_188.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_190.png)

---

![](img/7d5adcabc5e6be579c6937eaaf16dbca_192.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_194.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_196.png)

## 部署

上一节我们介绍了 API 文档，本节中我们来看看如何部署应用。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_198.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_200.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_201.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_203.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_204.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_206.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_208.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_210.png)

我们将使用 Render 平台部署 FastAPI 应用、PostgreSQL 数据库、Redis 和 Celery 工作器。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_212.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_214.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_215.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_217.png)

### 部署步骤：
1. 创建 Web 服务，指向 FastAPI 应用。
2. 创建 PostgreSQL 数据库，并更新环境变量。
3. 创建 Redis 实例，用于任务队列。
4. 创建 Celery 工作器，处理后台任务。
5. 更新环境变量，确保所有服务正确连接。

![](img/7d5adcabc5e6be579c6937eaaf16dbca_219.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_221.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_223.png)

### 环境变量示例：
```
DATABASE_URL=postgresql+asyncpg://user:password@host:port/dbname
REDIS_URL=redis://host:port
MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=your-app-password
DOMAIN=https://your-app.onrender.com
```

![](img/7d5adcabc5e6be579c6937eaaf16dbca_225.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_226.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_227.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_229.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_231.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_233.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_235.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_237.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_239.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_240.png)

### 启动命令：
- Web 服务：`fastapi run source`
- Celery 工作器：`celery -A source.celery_tasks.celery_app worker --loglevel=info`

![](img/7d5adcabc5e6be579c6937eaaf16dbca_242.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_244.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_246.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_248.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_250.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_252.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_254.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_255.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_257.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_259.png)

---

![](img/7d5adcabc5e6be579c6937eaaf16dbca_261.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_263.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_265.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_267.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_268.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_270.png)

## 总结

![](img/7d5adcabc5e6be579c6937eaaf16dbca_272.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_273.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_275.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_277.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_279.png)

![](img/7d5adcabc5e6be579c6937eaaf16dbca_281.png)

在本节课中，我们一起学习了如何使用 FastAPI 构建一个完整的 API 应用。我们从获取当前登录用户开始，逐步实现了角色管理、数据库关系、错误处理、中间件、电子邮件发送、密码重置、后台任务、API 文档和部署。通过这些内容，你应该能够构建一个功能齐全的 FastAPI 应用，并将其部署到生产环境中。