# 098：创建HTTP服务发现第二部分 🔧

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_1.png)

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_3.png)

在本节课中，我们将继续完善上一节创建的HTTP服务发现应用。我们将通过分离配置文件、添加HTTPS加密和基础认证来提升应用的安全性和可维护性。

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_5.png)

---

## 概述 📋

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_7.png)

上一节我们创建了一个基础的Flask应用，用于为Prometheus提供动态服务发现。本节中，我们将对这个应用进行三项重要改进：
1.  将服务列表数据分离到独立的文件中。
2.  为Web服务添加HTTPS加密。
3.  为访问页面添加基础身份验证。

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_9.png)

这些改进将使我们的服务发现服务器更加安全、专业且易于管理。

---

## 重构Flask应用文件

首先，我们需要修改主Flask应用文件。主要目标是移除内嵌的服务列表，将其移至一个独立的文件中。

我们将导入必要的模块，并修改文件结构。以下是修改后的主文件核心部分：

```python
from flask import Flask, jsonify
from flask_basicauth import BasicAuth
import ssl
from services import services_list  # 从独立的services文件导入服务列表

app = Flask(__name__)

# 基础认证配置
app.config['BASIC_AUTH_USERNAME'] = 'username'
app.config['BASIC_AUTH_PASSWORD'] = 'password'
basic_auth = BasicAuth(app)

# HTTPS SSL配置
context = ssl.SSLContext(ssl.PROTOCOL_TLSv1_2)
context.load_cert_chain('public.crt', 'private.key')

@app.route('/services')
@basic_auth.required
def services():
    return jsonify(services_list)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=880, ssl_context=context)
```

**代码解释**：
*   `from services import services_list`： 从名为 `services.py` 的文件中导入 `services_list` 变量。
*   `BasicAuth`： 用于为路由添加用户名和密码验证。
*   `ssl.SSLContext`： 用于加载SSL证书和私钥，启用HTTPS。

---

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_11.png)

## 创建独立服务列表文件

现在，让我们创建独立的 `services.py` 文件来存放服务列表。这样做的好处是，更新服务信息时无需改动主应用代码。

在相同目录下创建 `services.py` 文件，内容如下：

```python
services_list = [
    {"targets": ["192.168.1.10:9100"], "labels": {"job": "windows", "os": "windows_2016"}},
    {"targets": ["192.168.1.20:9100"], "labels": {"job": "linux", "os": "ubuntu_22.04"}},
    {"targets": ["192.168.1.30:9100"], "labels": {"job": "linux", "os": "centos_8"}}
]
```

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_13.png)

这个列表包含了目标地址和对应的标签，Prometheus将根据这些信息进行抓取。

---

## 安装依赖与生成SSL证书

在运行应用之前，我们需要安装一个额外的Python包并生成自签名的SSL证书。

**1. 安装Flask-BasicAuth包**
打开终端，执行以下命令：
```bash
pip install Flask-BasicAuth
```

**2. 生成SSL证书**
在终端中，切换到Flask应用所在的目录，执行以下命令生成私钥和证书：
```bash
# 生成私钥
openssl genrsa -out private.key 2048
# 使用私钥生成自签名证书
openssl req -new -x509 -key private.key -out public.crt -days 365
```
执行第二条命令时，终端会提示输入国家、省份、城市等信息，可以按需填写或直接回车使用默认值。生成的 `public.crt`（证书）和 `private.key`（私钥）文件必须与Flask主文件放在同一目录下。

---

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_15.png)

## 配置Prometheus

我们的服务端已经升级为HTTPS并需要认证，因此需要更新Prometheus的抓取配置。

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_17.png)

打开Prometheus的配置文件 `prometheus.yml`，修改 `http_sd_configs` 部分如下：

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_19.png)

```yaml
scrape_configs:
  - job_name: 'http-sd-services'
    http_sd_configs:
      - url: 'https://your-server-ip:880/services'
        basic_auth:
          username: 'username'
          password: 'password'
        tls_config:
          insecure_skip_verify: true  # 因为是自签名证书，跳过验证
    metrics_path: /services
    relabel_configs:
      - source_labels: [__address__]
        target_label: instance
```

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_21.png)

**配置说明**：
*   `url`： 协议改为 `https`，并指定端口 `880` 和路径 `/services`。
*   `basic_auth`： 提供在Flask应用中设置的用户名和密码。
*   `tls_config.insecure_skip_verify: true`： 由于我们使用的是自签名证书，需要让Prometheus跳过SSL证书验证。在生产环境中应使用可信证书并移除此选项。
*   `metrics_path`： 指向我们提供服务列表的端点。

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_23.png)

保存配置文件后，重启Prometheus服务使配置生效：
```bash
sudo systemctl restart prometheus
```

---

## 测试与验证

所有配置完成后，让我们进行测试。

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_25.png)

**1. 启动Flask应用**
在应用目录下运行：
```bash
python your_flask_app.py
```

**2. 访问Web界面**
在浏览器中访问 `https://your-server-ip:880/services`。浏览器会提示安全风险（因使用自签名证书），选择继续访问。随后会弹出登录框，输入用户名 `username` 和密码 `password` 后，应能看到JSON格式的服务列表。

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_27.png)

**3. 检查Prometheus目标状态**
访问Prometheus的Web界面（通常为 `http://your-prometheus-ip:9090`），导航到 **Status -> Targets**。你应该能看到在 `services.py` 中定义的所有目标（如Windows和Linux主机）状态为 **UP**。

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_29.png)

**4. 动态更新测试（可选）**
你可以通过HTTP `POST` 请求（例如使用 `curl` 命令）来调用一个我们预设的编辑接口（视频中提及），动态修改 `services.py` 文件的内容。修改后，Prometheus会在下一个抓取周期自动发现新的目标。
```bash
curl -X POST -H "Content-Type: application/json" -d '{"new":"target"}' https://username:password@your-server-ip:880/update
```
*(注意：教程中的主文件未展示 `/update` 端点的具体实现，这是一个功能示意。)*

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_31.png)

---

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_33.png)

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_35.png)

## 总结 🎯

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_37.png)

本节课我们一起完成了HTTP服务发现服务器的增强工作。我们主要做了三件事：
1.  **分离关注点**：将服务列表数据移至独立的 `services.py` 文件，使配置管理更清晰。
2.  **提升安全性**：
    *   通过生成和配置SSL证书，将HTTP服务升级为HTTPS，对传输数据进行加密。
    *   使用 `Flask-BasicAuth` 为访问接口添加了基础身份验证，防止未授权访问。
3.  **更新客户端配置**：相应地修改了Prometheus的抓取配置，使其能够通过HTTPS和认证信息从我们的新端点获取数据。

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_39.png)

![](img/2e6e9ca66fdeeb91a2d1cd20bfb41911_41.png)

经过这些改进，我们的服务发现机制变得更加安全、可靠，并且易于维护。你可以安全地将它用于内部监控环境。记得在实际部署时，考虑使用可信的SSL证书并管理好认证凭证。