# 003：实现数据传输与关闭

![](img/49347db1b6244c3ae42b589040f18ec5_1.png)

![](img/49347db1b6244c3ae42b589040f18ec5_3.png)

![](img/49347db1b6244c3ae42b589040f18ec5_5.png)

![](img/49347db1b6244c3ae42b589040f18ec5_7.png)

![](img/49347db1b6244c3ae42b589040f18ec5_9.png)

![](img/49347db1b6244c3ae42b589040f18ec5_11.png)

![](img/49347db1b6244c3ae42b589040f18ec5_13.png)

在本节课中，我们将继续使用Rust实现TCP协议。我们将重点实现TCP连接的数据读写功能，并处理连接的优雅关闭。我们将学习如何处理接收到的数据、如何发送数据，以及如何管理发送窗口和重传计时器。

## 课程回顾

![](img/49347db1b6244c3ae42b589040f18ec5_15.png)

![](img/49347db1b6244c3ae42b589040f18ec5_17.png)

![](img/49347db1b6244c3ae42b589040f18ec5_19.png)

![](img/49347db1b6244c3ae42b589040f18ec5_21.png)

上一节我们实现了TCP连接的基本建立与拆除。本节中，我们来看看如何让TCP连接能够传输实际的数据。

![](img/49347db1b6244c3ae42b589040f18ec5_23.png)

![](img/49347db1b6244c3ae42b589040f18ec5_25.png)

![](img/49347db1b6244c3ae42b589040f18ec5_27.png)

![](img/49347db1b6244c3ae42b589040f18ec5_29.png)

![](img/49347db1b6244c3ae42b589040f18ec5_31.png)

![](img/49347db1b6244c3ae42b589040f18ec5_33.png)

![](img/49347db1b6244c3ae42b589040f18ec5_34.png)

![](img/49347db1b6244c3ae42b589040f18ec5_36.png)

![](img/49347db1b6244c3ae42b589040f18ec5_38.png)

![](img/49347db1b6244c3ae42b589040f18ec5_40.png)

![](img/49347db1b6244c3ae42b589040f18ec5_42.png)

![](img/49347db1b6244c3ae42b589040f18ec5_44.png)

![](img/49347db1b6244c3ae42b589040f18ec5_46.png)

![](img/49347db1b6244c3ae42b589040f18ec5_48.png)

![](img/49347db1b6244c3ae42b589040f18ec5_50.png)

![](img/49347db1b6244c3ae42b589040f18ec5_52.png)

![](img/49347db1b6244c3ae42b589040f18ec5_54.png)

![](img/49347db1b6244c3ae42b589040f18ec5_56.png)

![](img/49347db1b6244c3ae42b589040f18ec5_58.png)

我们当前的代码库已经能够处理TCP的三次握手和四次挥手，但`read`和`write`方法还不能处理实际的数据。我们的目标是让`TCPStream`的`read`方法能够读取对端发送的数据，`write`方法能够向对端发送数据，并实现`shutdown`方法来优雅地关闭连接。

![](img/49347db1b6244c3ae42b589040f18ec5_60.png)

![](img/49347db1b6244c3ae42b589040f18ec5_62.png)

![](img/49347db1b6244c3ae42b589040f18ec5_64.png)

![](img/49347db1b6244c3ae42b589040f18ec5_66.png)

![](img/49347db1b6244c3ae42b589040f18ec5_68.png)

![](img/49347db1b6244c3ae42b589040f18ec5_70.png)

![](img/49347db1b6244c3ae42b589040f18ec5_72.png)

![](img/49347db1b6244c3ae42b589040f18ec5_74.png)

![](img/49347db1b6244c3ae42b589040f18ec5_76.png)

![](img/49347db1b6244c3ae42b589040f18ec5_78.png)

![](img/49347db1b6244c3ae42b589040f18ec5_80.png)

![](img/49347db1b6244c3ae42b589040f18ec5_82.png)

![](img/49347db1b6244c3ae42b589040f18ec5_84.png)

![](img/49347db1b6244c3ae42b589040f18ec5_85.png)

![](img/49347db1b6244c3ae42b589040f18ec5_87.png)

![](img/49347db1b6244c3ae42b589040f18ec5_89.png)

![](img/49347db1b6244c3ae42b589040f18ec5_91.png)

![](img/49347db1b6244c3ae42b589040f18ec5_93.png)

![](img/49347db1b6244c3ae42b589040f18ec5_95.png)

![](img/49347db1b6244c3ae42b589040f18ec5_97.png)

![](img/49347db1b6244c3ae42b589040f18ec5_99.png)

![](img/49347db1b6244c3ae42b589040f18ec5_101.png)

![](img/49347db1b6244c3ae42b589040f18ec5_103.png)

![](img/49347db1b6244c3ae42b589040f18ec5_105.png)

![](img/49347db1b6244c3ae42b589040f18ec5_107.png)

![](img/49347db1b6244c3ae42b589040f18ec5_109.png)

![](img/49347db1b6244c3ae42b589040f18ec5_110.png)

## 实现数据读取

![](img/49347db1b6244c3ae42b589040f18ec5_112.png)

![](img/49347db1b6244c3ae42b589040f18ec5_114.png)

![](img/49347db1b6244c3ae42b589040f18ec5_116.png)

![](img/49347db1b6244c3ae42b589040f18ec5_117.png)

首先，我们需要修改`on_packet`方法中处理接收数据的部分。当前，当连接处于`ESTABLISHED`状态并收到数据时，我们只是简单地断言数据为空并关闭连接。现在，我们需要将这些数据存入缓冲区，并通知等待读取的线程。

![](img/49347db1b6244c3ae42b589040f18ec5_119.png)

![](img/49347db1b6244c3ae42b589040f18ec5_121.png)

以下是处理接收数据的关键步骤：

![](img/49347db1b6244c3ae42b589040f18ec5_123.png)

![](img/49347db1b6244c3ae42b589040f18ec5_125.png)

![](img/49347db1b6244c3ae42b589040f18ec5_127.png)

![](img/49347db1b6244c3ae42b589040f18ec5_129.png)

![](img/49347db1b6244c3ae42b589040f18ec5_131.png)

![](img/49347db1b6244c3ae42b589040f18ec5_133.png)

![](img/49347db1b6244c3ae42b589040f18ec5_135.png)

![](img/49347db1b6244c3ae42b589040f18ec5_137.png)

![](img/49347db1b6244c3ae42b589040f18ec5_139.png)

1.  **计算有效数据**：TCP数据包的序列号可能指向我们已经接收过的字节。我们需要根据`receive.next`（期望接收的下一个字节的序列号）和当前数据包的序列号，计算出本次数据包中我们真正需要接收的新数据部分。
    *   公式：`unread_data_at = max(0, (receive.next.wrapping_sub(seq)) as usize)`
    *   如果`unread_data_at >= data.len()`，说明这个数据包不包含新数据（可能是重传的FIN包），我们可以忽略其数据部分。
2.  **存储数据**：将计算出的新数据切片（`data[unread_data_at..]`）添加到`incoming`字节队列中。
3.  **更新接收状态**：更新`receive.next`，将其指向下一个期望接收的字节序列号。这需要加上我们实际接收的新数据长度，如果数据包包含FIN标志，还需要额外加1（因为FIN占用一个逻辑序列号）。
    *   代码：`self.receive.next = seq.wrapping_add((data.len() + fin_offset) as u32);`
4.  **唤醒读者**：我们的`availability`机制会自动检测`incoming`队列非空，并设置`READ`标志。事件循环会据此唤醒正在`read`调用中等待的线程。

![](img/49347db1b6244c3ae42b589040f18ec5_141.png)

![](img/49347db1b6244c3ae42b589040f18ec5_143.png)

![](img/49347db1b6244c3ae42b589040f18ec5_145.png)

![](img/49347db1b6244c3ae42b589040f18ec5_147.png)

![](img/49347db1b6244c3ae42b589040f18ec5_149.png)

完成这些修改后，我们的服务器就能够读取客户端发送的数据了。我们可以使用`netcat`或`curl`等工具进行测试。

## 实现数据写入与连接关闭

![](img/49347db1b6244c3ae42b589040f18ec5_151.png)

![](img/49347db1b6244c3ae42b589040f18ec5_153.png)

![](img/49347db1b6244c3ae42b589040f18ec5_155.png)

![](img/49347db1b6244c3ae42b589040f18ec5_157.png)

![](img/49347db1b6244c3ae42b589040f18ec5_159.png)

![](img/49347db1b6244c3ae42b589040f18ec5_160.png)

![](img/49347db1b6244c3ae42b589040f18ec5_162.png)

实现数据写入（`write`）和连接关闭（`shutdown`）更为复杂，因为它们涉及到发送窗口管理、数据重传和计时器。

![](img/49347db1b6244c3ae42b589040f18ec5_164.png)

![](img/49347db1b6244c3ae42b589040f18ec5_166.png)

![](img/49347db1b6244c3ae42b589040f18ec5_168.png)

![](img/49347db1b6244c3ae42b589040f18ec5_170.png)

![](img/49347db1b6244c3ae42b589040f18ec5_172.png)

![](img/49347db1b6244c3ae42b589040f18ec5_174.png)

![](img/49347db1b6244c3ae42b589040f18ec5_175.png)

![](img/49347db1b6244c3ae42b589040f18ec5_177.png)

![](img/49347db1b6244c3ae42b589040f18ec5_178.png)

![](img/49347db1b6244c3ae42b589040f18ec5_180.png)

### 1. 发送窗口与未确认数据

![](img/49347db1b6244c3ae42b589040f18ec5_182.png)

![](img/49347db1b6244c3ae42b589040f18ec5_184.png)

![](img/49347db1b6244c3ae42b589040f18ec5_186.png)

![](img/49347db1b6244c3ae42b589040f18ec5_188.png)

![](img/49347db1b6244c3ae42b589040f18ec5_189.png)

![](img/49347db1b6244c3ae42b589040f18ec5_191.png)

![](img/49347db1b6244c3ae42b589040f18ec5_193.png)

![](img/49347db1b6244c3ae42b589040f18ec5_195.png)

![](img/49347db1b6244c3ae42b589040f18ec5_196.png)

![](img/49347db1b6244c3ae42b589040f18ec5_198.png)

TCP使用滑动窗口机制进行流量控制。发送方需要维护：
*   `send.unacknowledged`: 已发送但尚未被确认的第一个字节的序列号。
*   `send.next`: 下一个将要发送的字节的序列号。
*   `send.window`: 接收方通告的窗口大小，即允许发送方在未收到确认前可以发送的最大数据量。
*   `unacked`: 一个字节队列，存储已发送但未被确认的数据，用于可能的重新传输。

![](img/49347db1b6244c3ae42b589040f18ec5_200.png)

![](img/49347db1b6244c3ae42b589040f18ec5_202.png)

![](img/49347db1b6244c3ae42b589040f18ec5_204.png)

![](img/49347db1b6244c3ae42b589040f18ec5_205.png)

![](img/49347db1b6244c3ae42b589040f18ec5_207.png)

![](img/49347db1b6244c3ae42b589040f18ec5_209.png)

![](img/49347db1b6244c3ae42b589040f18ec5_211.png)

![](img/49347db1b6244c3ae42b589040f18ec5_213.png)

当我们调用`stream.write()`时，数据被添加到`unacked`队列的尾部。真正的发送逻辑由`on_tick`方法（计时器触发）或当有新数据且窗口允许时驱动。

![](img/49347db1b6244c3ae42b589040f18ec5_215.png)

![](img/49347db1b6244c3ae42b589040f18ec5_217.png)

![](img/49347db1b6244c3ae42b589040f18ec5_219.png)

### 2. 计时器与重传

![](img/49347db1b6244c3ae42b589040f18ec5_221.png)

![](img/49347db1b6244c3ae42b589040f18ec5_223.png)

![](img/49347db1b6244c3ae42b589040f18ec5_225.png)

![](img/49347db1b6244c3ae42b589040f18ec5_227.png)

![](img/49347db1b6244c3ae42b589040f18ec5_229.png)

![](img/49347db1b6244c3ae42b589040f18ec5_231.png)

为了实现可靠传输，我们需要一个计时器来触发数据重传。我们在事件循环中使用`poll`系统调用，设置一个短暂的超时（例如1毫秒），定期检查所有连接。

![](img/49347db1b6244c3ae42b589040f18ec5_233.png)

![](img/49347db1b6244c3ae42b589040f18ec5_235.png)

![](img/49347db1b6244c3ae42b589040f18ec5_237.png)

在每个连接的`on_tick`方法中，我们需要：
1.  **检查是否需要重传**：查找`unacked`队列中最早发送但未被确认的数据包，计算其等待时间。如果等待时间超过了动态计算的重传超时（RTO），则触发重传。
    *   RTO基于平滑的往返时间（SRTT）估算，公式可简化为：`RTO = max(1秒, SRTT * 1.5)`。
2.  **检查是否可以发送新数据**：如果不需要重传，则检查发送窗口是否有剩余空间（`send.window - (send.next - send.unacknowledged)`）。如果有空间且`unacked`队列中有未发送的数据，则发送尽可能多的新数据。
3.  **处理FIN**：如果连接已调用`shutdown`（`self.closed`为`true`），且FIN尚未发送，并且发送窗口允许（即FIN的逻辑序列号在窗口内），则在数据包中设置FIN标志。发送FIN后，连接状态应转移到`FIN_WAIT_1`。

![](img/49347db1b6244c3ae42b589040f18ec5_239.png)

![](img/49347db1b6244c3ae42b589040f18ec5_241.png)

![](img/49347db1b6244c3ae42b589040f18ec5_243.png)

![](img/49347db1b6244c3ae42b589040f18ec5_245.png)

![](img/49347db1b6244c3ae42b589040f18ec5_247.png)

![](img/49347db1b6244c3ae42b589040f18ec5_249.png)

![](img/49347db1b6244c3ae42b589040f18ec5_250.png)

![](img/49347db1b6244c3ae42b589040f18ec5_252.png)

![](img/49347db1b6244c3ae42b589040f18ec5_254.png)

### 3. 实现`shutdown`方法

![](img/49347db1b6244c3ae42b589040f18ec5_256.png)

![](img/49347db1b6244c3ae42b589040f18ec5_258.png)

![](img/49347db1b6244c3ae42b589040f18ec5_260.png)

![](img/49347db1b6244c3ae42b589040f18ec5_262.png)

![](img/49347db1b6244c3ae42b589040f18ec5_264.png)

![](img/49347db1b6244c3ae42b589040f18ec5_266.png)

`shutdown`方法相对简单。它应当：
1.  获取连接锁。
2.  检查当前状态。如果处于`ESTABLISHED`或`SYN_RCVD`状态，则将`self.closed`设置为`true`，并将连接状态改为`FIN_WAIT_1`。
3.  释放锁并返回。

实际的FIN发送将由后续的`on_tick`调用处理。

![](img/49347db1b6244c3ae42b589040f18ec5_268.png)

![](img/49347db1b6244c3ae42b589040f18ec5_270.png)

![](img/49347db1b6244c3ae42b589040f18ec5_272.png)

![](img/49347db1b6244c3ae42b589040f18ec5_273.png)

![](img/49347db1b6244c3ae42b589040f18ec5_275.png)

![](img/49347db1b6244c3ae42b589040f18ec5_276.png)

![](img/49347db1b6244c3ae42b589040f18ec5_278.png)

![](img/49347db1b6244c3ae42b589040f18ec5_280.png)

![](img/49347db1b6244c3ae42b589040f18ec5_282.png)

![](img/49347db1b6244c3ae42b589040f18ec5_284.png)

![](img/49347db1b6244c3ae42b589040f18ec5_286.png)

![](img/49347db1b6244c3ae42b589040f18ec5_288.png)

### 4. 处理确认（ACK）

![](img/49347db1b6244c3ae42b589040f18ec5_290.png)

![](img/49347db1b6244c3ae42b589040f18ec5_292.png)

![](img/49347db1b6244c3ae42b589040f18ec5_294.png)

![](img/49347db1b6244c3ae42b589040f18ec5_296.png)

![](img/49347db1b6244c3ae42b589040f18ec5_298.png)

![](img/49347db1b6244c3ae42b589040f18ec5_300.png)

![](img/49347db1b6244c3ae42b589040f18ec5_302.png)

![](img/49347db1b6244c3ae42b589040f18ec5_304.png)

![](img/49347db1b6244c3ae42b589040f18ec5_306.png)

![](img/49347db1b6244c3ae42b589040f18ec5_308.png)

当收到对端发来的ACK时，我们需要：
1.  **清理已确认数据**：从`unacked`队列头部删除所有已被确认的字节。
2.  **更新发送状态**：将`send.unacknowledged`更新为ACK号。
3.  **更新RTT估计**：如果这个ACK确认了我们记录的某个发送时间点，我们可以用当前时间减去发送时间来估算RTT，并更新SRTT。
4.  **唤醒写者/刷新者**：如果`unacked`队列变空，意味着所有数据都已确认，可以唤醒等待`flush`完成的线程。

![](img/49347db1b6244c3ae42b589040f18ec5_310.png)

## 当前进展与挑战

![](img/49347db1b6244c3ae42b589040f18ec5_312.png)

![](img/49347db1b6244c3ae42b589040f18ec5_314.png)

![](img/49347db1b6244c3ae42b589040f18ec5_316.png)

![](img/49347db1b6244c3ae42b589040f18ec5_317.png)

![](img/49347db1b6244c3ae42b589040f18ec5_319.png)

![](img/49347db1b6244c3ae42b589040f18ec5_321.png)

![](img/49347db1b6244c3ae42b589040f18ec5_322.png)

![](img/49347db1b6244c3ae42b589040f18ec5_323.png)

![](img/49347db1b6244c3ae42b589040f18ec5_325.png)

按照上述思路实现后，我们的TCP栈在读取数据方面工作正常。服务器可以成功接收来自`netcat`或`curl`客户端发送的数据。

然而，在写入数据时遇到了问题。虽然代码能够生成看起来正确的TCP数据包（包含数据和FIN标志），并且Wireshark抓包显示协议交互符合预期，但Linux内核似乎没有确认（ACK）我们发送的数据包。这导致我们的重传计时器不断触发，而客户端收不到回复。

可能的原因包括：
*   我们的实现可能缺少某些TCP扩展（如窗口缩放、时间戳）。
*   内核可能因为某些校验（如校验和）问题而静默丢弃了我们的包。
*   将数据与FIN标志放在同一个包中发送可能不符合某些严格实现的要求。

![](img/49347db1b6244c3ae42b589040f18ec5_327.png)

![](img/49347db1b6244c3ae42b589040f18ec5_329.png)

![](img/49347db1b6244c3ae42b589040f18ec5_331.png)

![](img/49347db1b6244c3ae42b589040f18ec5_333.png)

![](img/49347db1b6244c3ae42b589040f18ec5_335.png)

![](img/49347db1b6244c3ae42b589040f18ec5_336.png)

![](img/49347db1b6244c3ae42b589040f18ec5_338.png)

![](img/49347db1b6244c3ae42b589040f18ec5_340.png)

尽管写入功能未能完全与标准TCP栈互操作，但我们已经实现了TCP数据传输的所有核心逻辑组件：滑动窗口、重传计时器、ACK处理、以及优雅关闭。代码结构已经为构建一个功能完整的TCP实现奠定了基础。

![](img/49347db1b6244c3ae42b589040f18ec5_342.png)

![](img/49347db1b6244c3ae42b589040f18ec5_343.png)

![](img/49347db1b6244c3ae42b589040f18ec5_345.png)

![](img/49347db1b6244c3ae42b589040f18ec5_347.png)

![](img/49347db1b6244c3ae42b589040f18ec5_349.png)

![](img/49347db1b6244c3ae42b589040f18ec5_351.png)

## 总结

![](img/49347db1b6244c3ae42b589040f18ec5_353.png)

![](img/49347db1b6244c3ae42b589040f18ec5_354.png)

![](img/49347db1b6244c3ae42b589040f18ec5_356.png)

![](img/49347db1b6244c3ae42b589040f18ec5_358.png)

![](img/49347db1b6244c3ae42b589040f18ec5_360.png)

![](img/49347db1b6244c3ae42b589040f18ec5_362.png)

本节课中我们一起学习了如何为TCP连接添加数据传输能力。我们实现了：
*   **数据读取**：正确解析接收到的数据段，处理序列号，将数据存入缓冲区并通知读取者。
*   **数据写入与重传框架**：建立了基于滑动窗口的发送逻辑、未确认数据缓冲区（`unacked`）以及由计时器驱动的重传机制。
*   **连接关闭**：实现了`shutdown`方法，用于启动连接关闭流程，并处理FIN标志的发送。
*   **ACK处理**：更新发送状态，清理已确认数据，并估算RTT。

![](img/49347db1b6244c3ae42b589040f18ec5_364.png)

![](img/49347db1b6244c3ae42b589040f18ec5_366.png)

![](img/49347db1b6244c3ae42b589040f18ec5_368.png)

![](img/49347db1b6244c3ae42b589040f18ec5_370.png)

虽然在与标准内核TCP栈的互操作性上遇到了挑战，但我们已经深入探讨了实现一个用户态TCP协议栈所需面对的主要设计问题和复杂性。希望本系列课程能帮助你更好地理解TCP协议和Rust系统编程。