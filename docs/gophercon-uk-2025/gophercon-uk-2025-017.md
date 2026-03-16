# 017：Go、Otel、LGTM栈

![](img/c68ad20435b5d7d3cdcc2df142b9605b_0.png)

![](img/c68ad20435b5d7d3cdcc2df142b9605b_2.png)

## 概述

在本节课中，我们将学习如何为Go应用程序构建一个无痛的可观测性解决方案。我们将介绍可观测性的核心概念，包括日志、指标和追踪，并演示如何使用OpenTelemetry（Otel）和LGTM（Loki, Grafana, Tempo, Mimir）栈来统一收集、关联和可视化这些信号。课程内容将尽可能简单直白，适合初学者理解。

---

## 章节 1：什么是可观测性？

可观测性，顾名思义，是指了解应用程序内部正在发生什么的能力。特别是在微服务架构中，当客户无法登录或系统出现故障时，我们需要知道问题出在哪里。可观测性使我们能够主动发现问题，而不是被动地等待客户支持报告。

在Otel的世界里，我们主要关注三种可观测性信号（有时也称为支柱）：

*   **日志**：记录特定时间点发生的事件。例如，`08:05: 由于烤箱故障，订单#123的大号素食披萨烧焦了`。日志提供了事件发生的上下文和原因。
*   **追踪**：展示一个用户操作（或请求）的完整生命周期。例如，`订单#123 总共耗时30分钟：准备5分钟，烹饪20分钟，配送5分钟`。追踪提供了系统的整体视图。
*   **指标**：用于聚合数值数据，通常上下文较少。例如，`每小时售出50个披萨，平均准备时间为8分钟`。指标擅长分析趋势。

**核心公式**：可观测性 = 日志 + 追踪 + 指标

---

## 章节 2：为什么可观测性很重要？

上一节我们介绍了可观测性的基本概念，本节中我们来看看它为何如此重要。

想象一下，你在凌晨2点被生产环境的事故警报叫醒，却对问题原因一无所知。缺乏足够的工具和信息来调试问题，尤其是在睡眠不足的情况下，会让情况变得更糟。

可观测性在以下方面至关重要：

*   **提供问题上下文**：如果用户无法登录，是因为黑名单规则异常？用户输错密码？还是系统正遭受暴力登录攻击？
*   **发现系统瓶颈**：研究表明，超过50%的用户会在网站加载时间超过3秒时离开。在微服务架构中，一个服务的轻微延迟可能会在整个调用链中累积，显著影响用户体验。没有数据观测，你无法定位瓶颈。
*   **减轻值班负担**：清晰的可观测性意味着当系统故障时，值班人员能快速理解原因，而不是在黑暗中摸索。

---

## 章节 3：什么是OpenTelemetry (Otel)？

在了解了可观测性的重要性后，我们来看看实现它的一个关键工具：OpenTelemetry。

OpenTelemetry（常简称为Otel）是一个开放标准。它的诞生源于一个理念：你的数据应该属于你自己。

在过去，如果你想使用追踪功能，通常会为每个服务配置特定的代理（如Datadog Agent），将数据发送到特定的后端。但如果你想更换后端（例如从Datadog切换到Jaeger），就需要更新所有服务的代理配置，这在拥有数百个微服务时是巨大的负担。

Otel通过引入 **Otel Collector** 解决了这个问题。你的应用程序只需生成Otel格式的观测数据，并将其导出到Otel Collector。这个Collector就像一个代理服务，负责将数据转换并路由到任何你配置的后端（如Datadog, Jaeger, Prometheus等）。你只需修改Collector的配置，而无需改动应用程序代码。

**核心概念**：
```
应用程序 --(Otel格式数据)--> Otel Collector --(转换后数据)--> 后端A / 后端B / ...
```

这实现了应用程序与具体观测后端的解耦，使管理和迁移变得异常轻松。目前，Otel已支持追踪、指标，日志也进入了测试阶段。

---

## 章节 4：追踪详解与实战

我们花了较多时间介绍Otel，因为它是现代可观测性的基石。现在，让我们深入探讨其中一个强大的信号：分布式追踪。

追踪对于可视化跨服务调用链特别有用。想象一个披萨订购服务：用户点击下单 -> 订单服务 -> 用户服务 -> 数据库 -> 支付网关 -> 消息队列 -> 邮件服务。没有追踪，理解这个流程非常困难。

### 4.1 核心概念：Span（跨度）

追踪由 **Span** 构成。Span是工作的基本单位，是追踪的构建块。你永远不会直接“创建”一个追踪，而是创建Span。如果存在一个追踪上下文（Trace Context），新的Span会延续这个追踪。

一个Span包含：
*   **名称**
*   **开始和结束时间戳**（用于计算耗时）
*   **上下文**（包含Trace ID和Span ID）
*   **属性**（键值对，用于提供额外上下文，如 `db.query.text: "SELECT * FROM users"`）
*   **事件**（记录时间点）
*   **状态**（如错误状态）

**追踪上下文** 通过HTTP头（如 `traceparent`）在服务间传递，确保所有相关Span属于同一个追踪。`traceparent` 头格式类似：`00-<trace-id>-<span-id>-<flags>`，其中flags的`01`表示应采样此追踪。

### 4.2 代码示例：为Go服务添加追踪

以下是使用Otel为Go HTTP服务添加基础追踪的步骤概览：

1.  **设置环境变量**：指定Otel Collector的地址。
    ```bash
    export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317
    ```

2.  **创建追踪提供者**：配置导出器和批处理。
    ```go
    func newTraceProvider() (*sdktrace.TracerProvider, error) {
        exp, err := otlptrace.New(context.Background(), otlptracehttp.NewClient())
        if err != nil { return nil, err }

        tp := sdktrace.NewTracerProvider(
            sdktrace.WithBatcher(exp),
            sdktrace.WithResource(resource.NewSchemaless(semconv.ServiceNameKey.String("my-service"))),
        )
        otel.SetTracerProvider(tp)
        otel.SetTextMapPropagator(propagation.TraceContext{}) // 设置传播器
        return tp, nil
    }
    ```

3.  **使用HTTP中间件自动追踪**：Otel库提供了中间件，可以自动为所有端点创建或延续追踪。
    ```go
    http.Handle("/", otelhttp.NewHandler(http.HandlerFunc(handler), "请求处理", otelhttp.WithPublicEndpoint()))
    ```

4.  **传递上下文**：确保在函数调用间传递包含追踪上下文的 `context.Context` 对象。
    ```go
    func handler(ctx context.Context) {
        // 从ctx中获取当前span，并添加属性或事件
        span := trace.SpanFromContext(ctx)
        span.SetAttributes(attribute.String("user.id", "123"))
        // ... 业务逻辑
    }
    ```

5.  **自动检测常见库**：许多流行库（如数据库驱动、Redis客户端、HTTP客户端、Kafka）都有Otel插件，可以自动生成子Span。
    ```go
    // 例如，为HTTP客户端添加追踪
    client := &http.Client{
        Transport: otelhttp.NewTransport(http.DefaultTransport),
    }
    // 使用此client发起的请求会自动注入追踪头
    ```

### 4.3 采样

由于追踪数据量可能很大，通常需要进行采样。主要有两种方式：
*   **头部采样**：在追踪开始时决定是否采样（例如，5%的概率）。这是概率性的。
*   **尾部采样**：在Collector端根据规则决定（例如，采样所有出错的追踪）。这更强大但需要状态管理。

---

## 章节 5：指标与日志

在深入探讨了追踪之后，让我们来看看另外两个可观测性信号：指标和日志。

### 5.1 指标

指标用于存储和聚合数值型时间序列数据，查询速度快，存储成本相对较低。在Otel中，我们生成OTLP格式的指标，由Collector转换为Prometheus格式供Grafana查询。

**主要指标类型**：
*   **计数器**：只增不减的值，如总请求数。
*   **仪表盘**：可上下浮动的值，如CPU使用率。
*   **直方图**：观察值的分布，如请求延迟的百分位数（P95, P99）。
*   **升降计数器**：跟踪增减变化的值，如队列大小。

**重要提示：避免高基数标签**！例如，将`user_id`作为标签（属性）会导致为每个用户创建一条独立的时间序列，可能造成数据爆炸。指标应提供有限的、可聚合的上下文。

**代码示例：创建计数器**
```go
meter := otel.Meter("my-service-meter")
requestCounter, _ := meter.Int64Counter("http.requests.total",
    instrument.WithDescription("Total HTTP requests"))
// 在请求处理中增加计数
requestCounter.Add(ctx, 1, metric.WithAttributes(
    attribute.String("method", "GET"),
    attribute.String("route", "/api/users"),
    attribute.Int("status", 200),
))
```

### 5.2 日志

日志是开发者最熟悉的调试工具。Otel为日志带来的好处包括：
1.  **自动关联**：将Trace ID和Span ID自动注入日志。
2.  **统一模式**：与追踪和指标共享属性定义。
3.  **减少开销**：通过Otel Collector统一导出。

Otel通过 **Bridge** 将Go标准库的 `slog` 日志记录转换为Otel格式。

**代码示例：配置Otel日志**
```go
func newLogProvider() (*slog.Logger, error) {
    logExporter, _ := otlploghttp.NewExporter(context.Background())
    loggerProvider := sdklogn.NewLoggerProvider(
        sdklogn.WithProcessor(sdklogn.NewBatchProcessor(logExporter)),
    )
    otellogn.SetLoggerProvider(loggerProvider)

    // 创建桥接处理器：同时输出到控制台和Otel
    otelHandler := otellogn.NewHandler(loggerProvider, &otellogn.HandlerOptions{})
    consoleHandler := slog.NewTextHandler(os.Stdout, nil)
    multiHandler := slogmulti.Fanout(consoleHandler, otelHandler)

    return slog.New(multiHandler), nil
}
```

---

## 章节 6：LGTM栈实战

现在，我们已经了解了三种信号如何通过Otel生成，本节中我们来看看如何用一个流行的开源栈来接收、存储和关联这些数据。

LGTM代表：
*   **Loki**：日志聚合系统。
*   **Grafana**：统一的可视化平台。
*   **Tempo**：分布式追踪后端。
*   **Mimir**：长期指标存储（兼容Prometheus）。

这个栈的魅力在于其高度的集成性。你可以通过一个 **Docker Compose** 文件轻松启动所有组件。

**数据流**：
```
你的Go应用 --(OTLP)--> Otel Collector --(转换)--> Loki (日志)
                                              --> Tempo (追踪)
                                              --> Mimir/Prometheus (指标)
```
然后，在Grafana中配置这三个数据源，并建立它们之间的关联：
*   **日志 -> 追踪**：在Loki中，将`trace_id`字段配置为“派生字段”，点击可直接跳转到Tempo查看对应追踪。
*   **追踪 -> 日志**：在Tempo的追踪视图中，可以查看该Trace ID关联的所有日志。
*   **指标 -> 追踪**：在Grafana的指标图表中启用 **Exemplars**。Exemplars是一种特殊的指标数据点，它附带了Trace ID，点击后可以跳转到导致该指标的具体追踪。

这种紧密的关联使得调试变得非常高效：你可以从异常的指标（如错误率飙升）下钻到具体的追踪，再查看该追踪相关的详细日志，快速定位根本原因。

---

## 章节 7：总结与最佳实践

本节课中我们一起学习了如何利用Go、OpenTelemetry和LGTM栈构建一个强大且易于管理的可观测性系统。

让我们回顾一下核心要点：

1.  **三大信号**：**日志**用于记录事件详情，**追踪**用于理解请求生命周期，**指标**用于监控趋势和聚合数据。
2.  **OpenTelemetry**：作为开放标准，它通过Otel Collector实现了应用程序与观测后端的解耦，使数据导出和迁移变得灵活。
3.  **分布式追踪**：通过Span和Trace Context将跨服务的调用串联起来，是理解复杂系统的利器。
4.  **LGTM栈**：Loki、Grafana、Tempo和Mimir提供了一个高度集成、关联性强的开源观测平台。
5.  **关联性**：利用Trace ID将日志、指标和追踪关联起来，是快速诊断问题的关键。

**一些最佳实践与反模式**：
*   **避免**：为每个HTTP请求记录日志（用指标代替），在指标中使用高基数标签（如`user_id`）。
*   **务必**：在日志中包含Trace ID，尤其是在微服务环境中。
*   **思考**：在添加观测代码时，考虑其未来的查询价值，避免产生噪音。

![](img/c68ad20435b5d7d3cdcc2df142b9605b_4.png)

![](img/c68ad20435b5d7d3cdcc2df142b9605b_5.png)

通过本教程介绍的方法，你可以将你的“披萨店服务”（或任何应用）从难以洞察的状态，转变为完全可观测、易于维护的系统。现在，当深夜警报响起时，你将拥有快速定位和解决问题的强大工具。