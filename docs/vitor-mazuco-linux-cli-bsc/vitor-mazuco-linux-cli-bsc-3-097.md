# 097：创建基于HTTP的服务发现

## 概述

在本节课中，我们将学习如何配置一个基于Web的服务发现机制，使Prometheus能够通过HTTP协议从Web服务器动态获取监控目标的信息。我们将使用Python的Flask框架创建一个简单的Web服务器，并配置Prometheus从该服务器获取目标列表。

---

## 基于Web的服务发现简介

![](img/c169be42078be7e3e5b27601c2c93c13_1.png)

上一节我们介绍了基于文件的服务发现。本节中，我们来看看如何通过HTTP协议实现服务发现。

基于Web的服务发现允许Prometheus从一个Web服务器端点获取监控目标列表。这种方法相比静态文件更灵活，可以集成身份验证、动态更新等高级功能。Prometheus会定期访问配置的URL，获取一个JSON格式的响应，其中包含目标列表。

以下是该JSON格式的基本结构，**`targets`** 和 **`labels`** 是必须保留的关键字：

```json
[
  {
    "targets": ["host1:9100", "host2:9100"],
    "labels": {
      "job": "node_exporter",
      "datacenter": "us-east-1"
    }
  }
]
```

![](img/c169be42078be7e3e5b27601c2c93c13_3.png)

---

![](img/c169be42078be7e3e5b27601c2c93c13_5.png)

## 搭建Flask Web服务器

我们将使用Flask，一个轻量级的Python Web框架，来创建我们的服务发现端点。

![](img/c169be42078be7e3e5b27601c2c93c13_7.png)

首先，确保你的系统已安装Python和pip。以下是在基于Red Hat的系统（如CentOS）上的安装命令，对于Ubuntu/Debian系统，请将 `dnf` 替换为 `apt`。

![](img/c169be42078be7e3e5b27601c2c93c13_9.png)

```bash
sudo dnf install python3 python3-pip
sudo pip3 install flask
```

安装完成后，创建一个Python脚本来运行我们的Flask应用。

以下是创建Flask应用的核心代码：

```python
#!/usr/bin/env python3
from flask import Flask, jsonify

app = Flask(__name__)

![](img/c169be42078be7e3e5b27601c2c93c13_11.png)

# 定义服务发现端点返回的数据
services = [
    {
        "targets": ["192.168.1.10:9100", "192.168.1.11:9100"],
        "labels": {
            "job": "node_exporter",
            "datacenter": "home-lab"
        }
    }
]

![](img/c169be42078be7e3e5b27601c2c93c13_13.png)

@app.route('/services', methods=['GET'])
def get_services():
    return jsonify(services)

![](img/c169be42078be7e3e5b27601c2c93c13_15.png)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
```

![](img/c169be42078be7e3e5b27601c2c93c13_17.png)

![](img/c169be42078be7e3e5b27601c2c93c13_18.png)

将上述代码保存为文件，例如 `prometheus_sd.py`。运行此脚本将启动一个监听8080端口的Web服务器。

---

## 配置系统服务（Systemd）

为了让Flask应用在系统启动时自动运行并在后台持续服务，我们将其配置为Systemd服务。

以下是创建Systemd服务单元的步骤：

![](img/c169be42078be7e3e5b27601c2c93c13_20.png)

1.  创建服务文件：`/etc/systemd/system/prometheus-sd.service`
2.  编辑该文件，添加以下内容（请根据你的实际路径修改 `WorkingDirectory` 和 `ExecStart`）：

![](img/c169be42078be7e3e5b27601c2c93c13_22.png)

```ini
[Unit]
Description=Prometheus HTTP Service Discovery
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/path/to/your/script/directory
ExecStart=/usr/bin/python3 /path/to/your/script/directory/prometheus_sd.py
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

3.  启用并启动服务：

![](img/c169be42078be7e3e5b27601c2c93c13_24.png)

![](img/c169be42078be7e3e5b27601c2c93c13_25.png)

```bash
sudo systemctl daemon-reload
sudo systemctl enable prometheus-sd.service
sudo systemctl start prometheus-sd.service
sudo systemctl status prometheus-sd.service
```

4.  确保防火墙开放8080端口：

![](img/c169be42078be7e3e5b27601c2c93c13_27.png)

![](img/c169be42078be7e3e5b27601c2c93c13_28.png)

```bash
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

现在，你可以通过浏览器访问 `http://<你的服务器IP>:8080/services` 来验证JSON数据是否正确返回。

---

![](img/c169be42078be7e3e5b27601c2c93c13_30.png)

![](img/c169be42078be7e3e5b27601c2c93c13_32.png)

## 配置Prometheus

最后一步是配置Prometheus，使其从我们刚创建的Web端点获取服务发现信息。

编辑Prometheus的主配置文件 `prometheus.yml`，在 `scrape_configs` 部分添加一个新的作业（job）。关键点在于指定 `metrics_path` 为我们Flask应用的端点 `/services`。

![](img/c169be42078be7e3e5b27601c2c93c13_34.png)

![](img/c169be42078be7e3e5b27601c2c93c13_36.png)

```yaml
scrape_configs:
  - job_name: 'http-sd'
    http_sd_configs:
      - url: 'http://localhost:8080/services'
    metrics_path: /services
    # 其他配置如 scrape_interval 可以保持全局或在此覆盖
```

配置完成后，重新加载或重启Prometheus服务：

![](img/c169be42078be7e3e5b27601c2c93c13_38.png)

![](img/c169be42078be7e3e5b27601c2c93c13_40.png)

```bash
sudo systemctl restart prometheus
```

![](img/c169be42078be7e3e5b27601c2c93c13_42.png)

在Prometheus的Web界面（通常为 `http://<prometheus-server>:9090`）中，导航到 **Status -> Service Discovery**，你应该能看到名为 `http-sd` 的作业，并且其目标列表与我们Flask服务器返回的JSON数据一致。

---

![](img/c169be42078be7e3e5b27601c2c93c13_44.png)

## 总结

本节课中我们一起学习了如何实现基于HTTP的服务发现。我们完成了以下步骤：
1.  理解了基于Web的服务发现原理及其JSON数据格式。
2.  使用Python Flask框架创建了一个提供目标列表的Web服务器。
3.  通过Systemd将Flask应用配置为常驻系统服务。
4.  修改Prometheus配置，使其从新的HTTP端点动态获取监控目标。

![](img/c169be42078be7e3e5b27601c2c93c13_46.png)

这种方法提供了比静态文件更高的灵活性和可集成性，为后续添加身份验证、动态更新等功能奠定了基础。