# 005：接收原始IP数据包 📦

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_1.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_2.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_4.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_6.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_8.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_10.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_12.png)

在本节课中，我们将要学习如何接收并解析原始IP数据包，这是完成我们“从零实现PING”项目的最后一步。上一节我们成功修复了校验和错误，并能够发送正确的ICMP回显请求包。本节中，我们将实现接收函数，处理来自网络的回复，并最终完成一个功能完整的PING工具。

---

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_14.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_16.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_18.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_20.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_22.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_24.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_26.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_28.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_29.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_31.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_33.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_35.png)

## 概述与问题回顾

我们之前已经实现了发送ICMP回显请求（Ping）的功能。但在测试时发现，虽然能发送数据包，却收不到预期的回复。经过排查，问题出在校验和计算函数的一个小错误上：我们在计算后错误地进行了字节序反转。

以下是修复后的校验和函数核心逻辑：
```c
uint16_t checksum(uint16_t *addr, int len) {
    // ... 计算过程 ...
    // 注意：返回计算结果本身，不再进行反转
    return sum;
}
```
修复后，我们的Ping请求能够成功收到回复。接下来的任务就是编写代码来接收并解析这些回复包。

---

## 接收原始IP数据包

我们需要实现一个函数，从原始套接字中读取数据，验证其校验和，并将其解析为我们定义的结构化格式（如IP包、ICMP包）。

以下是接收函数的基本框架：
```c
struct ip_packet* ip_receive(int socket_fd) {
    char buffer[1600]; // 缓冲区，足够容纳最大传输单元
    memset(buffer, 0, sizeof(buffer));

    // 使用 recvfrom 接收数据
    ssize_t bytes_received = recvfrom(socket_fd, buffer, sizeof(buffer) - 1, 0, NULL, NULL);
    if (bytes_received <= 0) {
        return NULL; // 接收失败或超时
    }
    // ... 后续解析和验证代码
}
```

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_37.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_39.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_41.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_42.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_44.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_46.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_48.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_50.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_52.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_54.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_56.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_57.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_59.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_61.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_63.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_64.png)

---

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_66.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_68.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_70.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_72.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_74.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_76.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_77.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_79.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_81.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_83.png)

## 解析IP包头

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_85.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_87.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_89.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_91.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_93.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_95.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_97.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_99.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_101.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_103.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_105.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_106.png)

接收到原始数据后，我们首先将其强制转换为IP包头结构，并提取关键信息，如源地址、目标地址和协议类型。

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_108.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_110.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_112.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_114.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_116.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_118.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_120.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_122.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_124.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_126.png)

以下是解析IP包头并验证校验和的关键步骤：
1.  **获取IP头指针**：`struct ip_header *ip_hdr = (struct ip_header*)buffer;`
2.  **计算校验和**：调用 `checksum` 函数计算接收数据的校验和。
3.  **验证校验和**：正确的IP包，其校验和计算结果应为 `0xFFFF`。验证公式为：
    **`calculated_checksum == 0xFFFF`**
    如果校验失败，则丢弃该数据包。
4.  **提取信息**：从 `ip_hdr` 中读取源地址、目标地址、标识符（ID）和协议字段。

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_128.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_130.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_132.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_134.png)

---

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_136.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_138.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_140.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_142.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_144.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_146.png)

## 处理ICMP负载

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_148.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_150.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_152.png)

如果IP包头中的协议字段表明这是一个ICMP包（协议号=1），我们需要进一步解析ICMP头部及其负载（即Ping回复数据）。

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_154.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_156.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_158.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_160.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_162.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_164.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_165.png)

以下是处理ICMP包的核心步骤：
1.  **定位ICMP头**：ICMP头紧接在IP头之后。计算指针偏移：
    ```c
    struct icmp_header *icmp_hdr = (struct icmp_header*)(buffer + sizeof(struct ip_header));
    ```
2.  **验证ICMP校验和**：同样计算ICMP部分的校验和并进行验证。
3.  **提取Ping回复数据**：如果ICMP类型是“回显回复”（Type 0, Code 0），则其负载部分包含我们发送的Ping包数据（ID和序列号）。
4.  **构建结构化数据**：使用提取的信息，填充我们自定义的 `ip_packet` 和 `icmp_packet` 结构，以便于程序后续处理和显示。

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_167.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_169.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_171.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_173.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_175.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_177.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_179.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_181.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_183.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_185.png)

---

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_187.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_189.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_191.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_193.png)

## 整合到主程序并设置超时

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_194.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_196.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_198.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_199.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_201.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_203.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_205.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_207.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_209.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_211.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_213.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_215.png)

我们将接收逻辑封装成一个独立的函数，并在主程序中循环发送Ping请求并等待回复。

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_217.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_219.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_221.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_223.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_225.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_227.png)

为了让程序在目标主机不响应时不会无限期等待，我们需要为套接字设置接收超时。

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_229.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_231.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_233.png)

以下是设置套接字超时的代码：
```c
struct timeval tv;
tv.tv_sec = TIMEOUT_SECONDS;  // 超时秒数，例如2秒
tv.tv_usec = 0;
setsockopt(socket_fd, SOL_SOCKET, SO_RCVTIMEO, (const char*)&tv, sizeof(tv));
```
设置后，`recvfrom` 调用会在指定时间后返回，允许我们处理超时情况，并继续发送下一个Ping请求或结束程序。

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_235.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_237.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_239.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_240.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_241.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_243.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_245.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_246.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_248.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_250.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_252.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_254.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_255.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_257.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_259.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_261.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_263.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_265.png)

---

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_267.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_269.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_271.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_273.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_275.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_277.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_279.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_281.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_282.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_284.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_285.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_287.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_289.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_291.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_293.png)

## 最终测试与总结

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_295.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_297.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_299.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_301.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_303.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_305.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_306.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_308.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_309.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_311.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_313.png)

完成所有代码后，我们进行了最终测试：
*   **测试1**：Ping一个可达的主机（如 `10.0.0.6`）。程序成功发送请求并接收回复，显示“!”，并最终计算成功率。
*   **测试2**：Ping一个不可达的地址（如 `10.0.0.99`）。程序在超时后显示“.”，表示请求超时，最终成功率为0%。

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_315.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_317.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_319.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_321.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_323.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_325.png)

![](img/a43ce7f9cab56ff018c7ab10c2b04ae1_326.png)

本节课中我们一起学习了如何接收和解析原始IP数据包，实现了Ping工具的完整双向通信。我们修复了关键的校验和错误，构建了数据包解析流程，并增加了超时处理机制，最终完成了一个从零开始、功能完备的PING程序。通过这个项目，我们深入理解了网络协议栈底层、原始套接字编程以及数据包的结构与验证。