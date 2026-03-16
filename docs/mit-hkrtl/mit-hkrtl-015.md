# 015：网页与浏览器 🕸️

![](img/5d77d4d592f2caabc93887dd456d74f7_0.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_2.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_3.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_5.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_6.png)

在本节课中，我们将学习关于网页浏览器的高级使用技巧。内容涵盖浏览器快捷键、高效搜索、隐私保护、自定义网页样式、使用用户脚本、Web API以及浏览器自动化。掌握这些工具和概念，能让你更高效、更安全地浏览网页，并解锁浏览器的强大自定义能力。

## 浏览器快捷键 ⌨️

![](img/5d77d4d592f2caabc93887dd456d74f7_8.png)

上一节我们介绍了课程概述，本节中我们来看看如何通过键盘快捷键更高效地使用浏览器。掌握快捷键可以显著提升你的浏览效率。

以下是几个核心的浏览器快捷键：

![](img/5d77d4d592f2caabc93887dd456d74f7_10.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_12.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_14.png)

*   **`Ctrl/Cmd + T`**：打开新标签页。
*   **`Ctrl/Cmd + Shift + T`**：重新打开最近关闭的标签页。
*   **`Ctrl/Cmd + L`**：将光标聚焦到地址栏，方便快速输入网址或搜索。
*   **`Ctrl/Cmd + F`**：在当前网页内查找文本。
*   **`Ctrl/Cmd + W`**：关闭当前标签页。
*   **鼠标中键点击标签页**：关闭该标签页。

## 高级搜索技巧 🔍

![](img/5d77d4d592f2caabc93887dd456d74f7_16.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_18.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_19.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_21.png)

我们已经了解了如何快速导航，接下来看看如何更精准地获取信息。搜索引擎（如Google）支持许多高级操作符，能帮你精确过滤结果。

以下是一些实用的搜索操作符示例：

*   **精确短语搜索**：使用引号 `" "`。例如，`"exact phrase"` 会搜索完全匹配这个短语的页面。
*   **站内搜索**：使用 `site:`。例如，`site:stackoverflow.com python` 只在 Stack Overflow 网站内搜索关于 Python 的内容。
*   **排除特定词**：使用减号 `-`。例如，`Cambridge -UK` 会排除包含 “UK” 的搜索结果。
*   **逻辑“或”搜索**：使用 `OR`。例如，`electronics OR circuits` 会搜索包含任一关键词的结果。
*   **指定文件类型**：使用 `filetype:`。例如，`lecture notes filetype:pdf` 只搜索 PDF 格式的讲义。

## 自定义搜索引擎 🔧

除了通用搜索，你还可以为特定网站设置快捷搜索。这允许你直接从地址栏跳转到目标网站的搜索结果页。

设置方法因浏览器而异，但核心思想是让浏览器识别特定关键词。例如，你可以设置：
*   输入 `am` 后按 `Tab` 键，直接在亚马逊（Amazon）搜索。
*   输入 `yt` 后按 `Tab` 键，直接在 YouTube 搜索。

## 隐私与安全扩展 🛡️

在享受便捷的同时，保护隐私和安全至关重要。安装合适的浏览器扩展是有效手段。

以下是三个推荐的基础扩展：

1.  **广告拦截器 (如 uBlock Origin)**：不仅能屏蔽广告，其内置的过滤器列表也能有效阻止访问已知的恶意软件站点。你可以针对特定网站临时禁用。
2.  **隐私保护工具 (如 Privacy Badger)**：阻止第三方跟踪器收集你的浏览数据，防止跨站指纹识别。
3.  **HTTPS 强制升级 (如 HTTPS Everywhere)**：自动将网站连接从明文的 HTTP 升级到加密的 HTTPS，保护数据传输安全。

![](img/5d77d4d592f2caabc93887dd456d74f7_23.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_25.png)

## 自定义网页外观 🎨

![](img/5d77d4d592f2caabc93887dd456d74f7_27.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_29.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_31.png)

浏览器不仅是内容查看器，也是内容修改器。网页由 **HTML**（结构）、**CSS**（样式）和 **JavaScript**（行为）构成。你可以覆盖这些层来自定义体验。

![](img/5d77d4d592f2caabc93887dd456d74f7_33.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_35.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_37.png)

首先，使用浏览器的开发者工具（按 `F12`）可以临时编辑任何网页的 HTML 和 CSS。例如，你可以将一段文字的颜色改为红色：
```css
p { color: red; }
```

要使更改永久化，可以安装 **Stylus** 扩展。它允许你为特定网站编写并应用自定义 CSS 样式。更有趣的是，存在 **UserStyles.org** 等社区网站，分享针对各种网站（如 GitHub、Wikipedia）的现成样式主题，你可以直接安装使用。

## 使用用户脚本 🤖

如果修改样式还不够，你还可以通过用户脚本修改网页的行为。这需要更谨慎，因为脚本能力更强。

安装 **Tampermonkey** 或 **Violentmonkey** 等扩展来管理用户脚本。例如，你可以编写一个脚本，在课程网站上用 `J` 和 `K` 键实现快速滚动：
```javascript
// ==UserScript==
// @name         Class Site JK Scroll
// @match        https://example.com/*
// ==/UserScript==
document.addEventListener('keydown', (e) => {
  if (e.key === 'j') window.scrollBy(0, 100);
  if (e.key === 'k') window.scrollBy(0, -100);
});
```
**警告**：只运行你信任的来源的脚本，恶意脚本可能窃取你的信息。

## 利用 Web API 🌐

许多网站和服务提供 **Web API**（应用程序接口），允许你通过程序（而非浏览器）直接获取数据或触发操作。这通常通过发送 HTTP 请求并接收结构化数据（如 JSON）来完成。

例如，你可以用 `curl` 命令获取你的公网 IP 信息：
```bash
curl https://ipinfo.io/json
```
或者，利用 Google 的搜索建议 API：
```bash
curl "https://suggestqueries.google.com/complete/search?client=firefox&q=mit"
```
更强大的应用是结合不同服务的 API 实现自动化，例如：当你的位置显示到家时，自动通过智能家居 API 打开电灯。**IFTTT** 或 **Zapier** 这类平台提供了无需编程的 API 连接方式。

处理返回的 JSON 数据时，可以使用 `jq` 工具进行过滤和格式化：
```bash
curl ... | jq '.[1]'
```

## 浏览器自动化 🤖

当网站没有提供 API，或者你需要模拟复杂的用户交互（如点击、填写表单）时，可以使用浏览器自动化工具。

**Selenium** 是一个流行的框架，它通过 **WebDriver** 控制真实的浏览器实例。你可以用 Python 等语言编写脚本，指示浏览器执行一系列操作。

![](img/5d77d4d592f2caabc93887dd456d74f7_39.png)

例如，自动将网页保存到 Wayback Machine：
```python
from selenium import webdriver
driver = webdriver.Firefox()
driver.get("https://web.archive.org/save")
url_field = driver.find_element_by_id("web-save-url-input")
url_field.clear()
url_field.send_keys("https://example.com")
url_field.submit()
# ... 等待并处理结果
driver.quit()
```
这适用于自动化测试、数据抓取或任何重复性的网页操作任务。

![](img/5d77d4d592f2caabc93887dd456d74f7_41.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_43.png)

---

![](img/5d77d4d592f2caabc93887dd456d74f7_45.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_47.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_49.png)

![](img/5d77d4d592f2caabc93887dd456d74f7_50.png)

本节课中我们一起学习了网页浏览器的强大功能。从提升效率的快捷键和搜索技巧，到保护隐私的扩展；从深度自定义网页外观和行为，到利用 Web API 和自动化工具与网络服务交互。希望这些知识能帮助你更聪明、更安全、更高效地使用浏览器这个日常核心工具。