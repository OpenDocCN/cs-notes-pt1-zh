# 025：API实现 🚀

在本节课中，我们将学习如何使用Elasticsearch的API进行数据操作，包括创建、读取、更新和删除（CRUD）。我们将通过具体的示例来理解PUT、POST、GET、DELETE和HEAD等HTTP方法在Elasticsearch中的不同用法和区别。

## 概述

![](img/0fb28192d4ee7010da2e6a6effb7a861_1.png)

我们将创建一个名为“aviation1”的索引（类似于数据库中的表），并向其中添加文档（类似于表中的行）。通过对比PUT和POST请求，理解它们在指定文档ID时的不同行为。同时，我们也将学习如何使用GET、DELETE和HEAD API来查询、删除和检查数据。

![](img/0fb28192d4ee7010da2e6a6effb7a861_3.png)

---

## PUT与POST API的区别 🔄

![](img/0fb28192d4ee7010da2e6a6effb7a861_5.png)

PUT API和POST API都用于创建或更新文档，但它们在处理文档ID的方式上有所不同。

*   **PUT API**：**必须**在请求中指定一个文档ID。它将在该特定ID上存储文档。
*   **POST API**：**不要求**指定文档ID。Elasticsearch会自动生成一个ID来存储文档。

![](img/0fb28192d4ee7010da2e6a6effb7a861_7.png)

下面我们通过实际操作来看两者的实现。

### 创建索引与添加数据

首先，我们创建一个名为 `aviation1` 的索引。

```http
PUT /aviation1
```

接下来，我们使用PUT API向索引中添加第一个文档。我们需要指定一个文档ID，例如 `1`。

![](img/0fb28192d4ee7010da2e6a6effb7a861_9.png)

```http
PUT /aviation1/_doc/1
{
  “airline”: “Indigo”,
  “category”: “domestic”,
  “source”: “Mumbai”,
  “destination”: “Delhi”,
  “total_flights”: 7,
  “date”: “2023-10-26”,
  “average_rate”: 4500
}
```

![](img/0fb28192d4ee7010da2e6a6effb7a861_11.png)

执行成功后，系统会返回确认信息，表示文档已创建。

![](img/0fb28192d4ee7010da2e6a6effb7a861_13.png)

现在，让我们看看POST API的用法。如果我们尝试在POST请求中不提供ID，Elasticsearch会生成一个随机ID。

```http
POST /aviation1/_doc
{
  “airline”: “Air India”,
  “category”: “domestic”,
  “source”: “Mumbai”,
  “destination”: “Goa”,
  “total_flights”: 4,
  “date”: “2023-10-26”,
  “average_rate”: 3500
}
```

![](img/0fb28192d4ee7010da2e6a6effb7a861_15.png)

![](img/0fb28192d4ee7010da2e6a6effb7a861_16.png)

但是，如果我们尝试在PUT请求中不提供ID，则会收到错误提示，要求必须指定一个ID。

![](img/0fb28192d4ee7010da2e6a6effb7a861_18.png)

```http
PUT /aviation1/_doc
{
  “airline”: “Test Airline”,
  “category”: “test”
}
```
**错误响应**：`document missing required `[_id]` field`

![](img/0fb28192d4ee7010da2e6a6effb7a861_20.png)

因此，PUT和POST的区别已经明确。为了后续演示，我们再使用PUT API添加几条数据。

![](img/0fb28192d4ee7010da2e6a6effb7a861_21.png)

![](img/0fb28192d4ee7010da2e6a6effb7a861_23.png)

```http
PUT /aviation1/_doc/3
{
  “airline”: “SpiceJet”,
  “category”: “domestic”,
  “source”: “Delhi”,
  “destination”: “Bangalore”,
  “total_flights”: 5,
  “date”: “2023-10-27”,
  “average_rate”: 5200
}

PUT /aviation1/_doc/4
{
  “airline”: “Vistara”,
  “category”: “international”,
  “source”: “Delhi”,
  “destination”: “London”,
  “total_flights”: 2,
  “date”: “2023-10-28”,
  “average_rate”: 85000
}
```

![](img/0fb28192d4ee7010da2e6a6effb7a861_25.png)

---

![](img/0fb28192d4ee7010da2e6a6effb7a861_27.png)

![](img/0fb28192d4ee7010da2e6a6effb7a861_28.png)

## 使用GET API查询数据 🔍

上一节我们介绍了如何创建数据，本节中我们来看看如何检索数据。GET API主要用于查询操作。

以下是GET API的几种常见用法：

**1. 获取所有索引列表**
此请求用于查看当前Elasticsearch集群中存在哪些索引。

![](img/0fb28192d4ee7010da2e6a6effb7a861_30.png)

![](img/0fb28192d4ee7010da2e6a6effb7a861_32.png)

```http
GET /_cat/indices?v
```
响应会列出所有索引（如 `aviation1`, `.kibana_1` 等）及其状态信息。

**2. 获取特定文档的全部信息**
通过指定索引名和文档ID，可以获取该文档的完整元数据和内容。

![](img/0fb28192d4ee7010da2e6a6effb7a861_34.png)

```http
GET /aviation1/_doc/3
```
响应包含文档ID、版本号、索引名以及我们存储的所有字段数据。

![](img/0fb28192d4ee7010da2e6a6effb7a861_35.png)

![](img/0fb28192d4ee7010da2e6a6effb7a861_37.png)

**3. 仅获取特定文档的源数据**
如果只关心文档中存储的原始数据，而不需要元信息，可以使用此请求。

```http
GET /aviation1/_source/4
```
响应仅返回我们最初PUT或POST的JSON数据内容。

**4. 获取索引下的所有文档**
要查询一个索引（如 `aviation1`）中的所有文档，可以使用搜索API。

```http
GET /aviation1/_search?q=*&pretty
```
或者使用请求体进行查询：
```http
GET /aviation1/_search
{
  “query”: {
    “match_all”: {}
  }
}
```
响应会列出索引 `aviation1` 中所有文档的详细信息，包括我们通过PUT和POST创建的所有记录。

![](img/0fb28192d4ee7010da2e6a6effb7a861_39.png)

![](img/0fb28192d4ee7010da2e6a6effb7a861_41.png)

---

## 使用DELETE API删除数据 🗑️

![](img/0fb28192d4ee7010da2e6a6effb7a861_43.png)

![](img/0fb28192d4ee7010da2e6a6effb7a861_44.png)

我们已经学习了如何创建和查询数据，现在来了解如何删除数据。DELETE API用于删除文档或整个索引。

**1. 删除特定文档**
通过指定索引名和文档ID来删除单个文档。

![](img/0fb28192d4ee7010da2e6a6effb7a861_46.png)

```http
DELETE /aviation1/_doc/<document_id>
```
例如，删除之前通过POST API创建的那个文档（需要先获取其自动生成的ID）：
```http
DELETE /aviation1/_doc/ABCdEfGhIjKlMnOpQ1234
```
执行成功后，再次使用GET API查询所有文档，将看不到该条记录。

![](img/0fb28192d4ee7010da2e6a6effb7a861_48.png)

**2. 删除整个索引**
此操作将删除索引及其中的所有数据，请谨慎使用。

![](img/0fb28192d4ee7010da2e6a6effb7a861_50.png)

```http
DELETE /<index_name>
```
例如，尝试删除一个名为 `people1` 的索引：
```http
DELETE /people1
```
如果删除成功，响应中会包含 `“acknowledged”: true`。之后使用 `GET /_cat/indices` 命令查看，该索引将不再出现在列表中。

![](img/0fb28192d4ee7010da2e6a6effb7a861_52.png)

![](img/0fb28192d4ee7010da2e6a6effb7a861_54.png)

---

![](img/0fb28192d4ee7010da2e6a6effb7a861_56.png)

## 使用HEAD API检查存在性 ℹ️

![](img/0fb28192d4ee7010da2e6a6effb7a861_58.png)

最后，我们来了解一个简单的HEAD API。它不返回文档内容，只用于检查某个文档或索引是否存在。

![](img/0fb28192d4ee7010da2e6a6effb7a861_59.png)

**检查文档是否存在**
```http
HEAD /aviation1/_doc/1
```
如果文档存在，将返回 `200 OK` 状态码。如果文档不存在（例如查询一个不存在的ID），将返回 `404 Not Found` 状态码。

![](img/0fb28192d4ee7010da2e6a6effb7a861_61.png)

![](img/0fb28192d4ee7010da2e6a6effb7a861_63.png)

**检查索引是否存在**
```http
HEAD /aviation1
```
同理，用于检查索引 `aviation1` 是否存在。

![](img/0fb28192d4ee7010da2e6a6effb7a861_65.png)

---

## 总结

本节课中我们一起学习了Elasticsearch核心的REST API操作。

1.  **创建数据**：使用**PUT**（必须指定ID）或**POST**（可自动生成ID）API。
2.  **查询数据**：使用**GET** API，可以获取索引列表、特定文档、文档源数据或索引下的全部文档。
3.  **删除数据**：使用**DELETE** API，可以删除特定文档或整个索引。
4.  **检查存在**：使用**HEAD** API，快速检查文档或索引是否存在，而不获取具体内容。

![](img/0fb28192d4ee7010da2e6a6effb7a861_67.png)

![](img/0fb28192d4ee7010da2e6a6effb7a861_69.png)

通过实际操作这些API，你已经掌握了在Elasticsearch中进行基本数据管理的方法，这是构建更复杂搜索和分析功能的基础。