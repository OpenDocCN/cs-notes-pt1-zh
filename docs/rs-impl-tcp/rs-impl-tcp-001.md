# 001：基础搭建与三次握手 👨‍💻

![](img/e709d71a082b45418e3b70befbd3ce3f_1.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_2.png)

## 概述

![](img/e709d71a082b45418e3b70befbd3ce3f_4.png)

在本节课中，我们将开始一个相对宏大的项目：使用Rust语言从头开始实现TCP协议。TCP是互联网的基础协议之一，它使得互联网上的两台主机能够以可靠的方式进行通信，确保数据不会丢失，并且接收方接收数据的顺序与发送方发送的顺序一致。互联网本身并不保证这些特性，因此TCP协议通过在网络中为数据排序、在数据包丢失时进行重传等机制来实现可靠传输。

![](img/e709d71a082b45418e3b70befbd3ce3f_6.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_7.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_9.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_11.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_13.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_15.png)

我们的目标是实现一个能与真实服务器通信的TCP实现。这意味着我们将实现TCP的核心功能，但不会涉及高级扩展或复杂的拥塞控制算法。我们将主要遵循RFC 793规范，并参考其他相关RFC文档。

---

![](img/e709d71a082b45418e3b70befbd3ce3f_17.png)

## 准备工作与环境搭建 🛠️

![](img/e709d71a082b45418e3b70befbd3ce3f_19.png)

在开始编码之前，我们需要搭建一个合适的网络环境。通常，如果你想自己实现TCP，会遇到一个问题：操作系统内核已经实现了TCP协议栈。为了让我们的用户空间程序能够处理网络数据包，我们需要“劫持”原本发送给内核的数据包。

![](img/e709d71a082b45418e3b70befbd3ce3f_21.png)

### 使用TUN/TAP设备

![](img/e709d71a082b45418e3b70befbd3ce3f_23.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_25.png)

为了解决上述问题，我们将使用Linux的TUN/TAP功能。TUN/TAP允许我们在用户空间创建一个虚拟网络接口。内核会将这个接口视为一个真实的网卡。

*   **工作原理**：当内核尝试向这个虚拟接口发送数据包时，数据包会被重定向到我们的用户空间程序。同样，当我们的程序向这个接口写入数据时，内核会认为这些数据是从网络接收到的。
*   **优势**：这使我们能够在一个受控的环境中实现自己的TCP/IP协议栈，而不会与内核的协议栈冲突。

![](img/e709d71a082b45418e3b70befbd3ce3f_27.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_29.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_30.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_31.png)

我们将使用Rust的 `tun-tap` crate来方便地创建和使用TUN接口。

![](img/e709d71a082b45418e3b70befbd3ce3f_33.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_35.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_37.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_39.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_41.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_43.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_45.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_47.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_49.png)

### 项目初始化

![](img/e709d71a082b45418e3b70befbd3ce3f_51.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_53.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_55.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_56.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_58.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_60.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_62.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_64.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_66.png)

首先，我们创建一个新的Rust二进制项目，并添加必要的依赖。

![](img/e709d71a082b45418e3b70befbd3ce3f_68.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_69.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_71.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_73.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_75.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_77.png)

```rust
// Cargo.toml
[dependencies]
tun-tap = "0.1"
etherparse = "0.8"
```

![](img/e709d71a082b45418e3b70befbd3ce3f_79.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_80.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_82.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_84.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_86.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_88.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_90.png)

在 `main.rs` 中，我们首先尝试创建一个TUN接口并接收数据包，以验证基础设置是否正常工作。

![](img/e709d71a082b45418e3b70befbd3ce3f_92.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_94.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_96.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_98.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_100.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_102.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_104.png)

```rust
use tun_tap::Iface;

![](img/e709d71a082b45418e3b70befbd3ce3f_106.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_108.png)

fn main() -> std::io::Result<()> {
    // 创建一个TUN接口，命名为 `tun0`
    let nic = Iface::without_packet_info("tun0", tun_tap::Mode::Tun)?;

    let mut buf = [0u8; 1500]; // 以太网MTU通常是1500字节
    loop {
        // 从接口读取数据包
        let nbytes = nic.recv(&mut buf[..])?;
        println!("read {} bytes: {:?}", nbytes, &buf[..nbytes]);
    }
}
```

![](img/e709d71a082b45418e3b70befbd3ce3f_110.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_112.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_114.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_116.png)

运行此程序需要 `CAP_NET_ADMIN` 权限。我们可以通过一个脚本来自动化构建、设置权限和运行的过程。

![](img/e709d71a082b45418e3b70befbd3ce3f_118.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_119.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_121.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_123.png)

---

![](img/e709d71a082b45418e3b70befbd3ce3f_125.png)

## 解析网络数据包 📦

![](img/e709d71a082b45418e3b70befbd3ce3f_127.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_128.png)

当我们的程序开始接收数据包后，我们需要解析它们。从TUN接口读取到的是原始的IP数据包（因为我们使用的是TUN模式，它处理的是网络层数据）。

### 解析IP头部

![](img/e709d71a082b45418e3b70befbd3ce3f_130.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_132.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_134.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_136.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_137.png)

IP数据包遵循RFC 791定义的格式。为了节省时间，我们使用 `etherparse` crate来解析IP头部。

![](img/e709d71a082b45418e3b70befbd3ce3f_139.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_141.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_142.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_144.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_146.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_148.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_150.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_152.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_153.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_155.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_156.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_158.png)

以下是处理接收到的数据包的主要逻辑：

![](img/e709d71a082b45418e3b70befbd3ce3f_160.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_162.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_164.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_166.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_168.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_170.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_172.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_174.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_176.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_178.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_180.png)

1.  检查以太网帧类型，过滤掉非IPv4的数据包。
2.  使用 `etherparse` 解析IPv4头部。
3.  根据IP头部的协议字段，进一步处理TCP数据包。

![](img/e709d71a082b45418e3b70befbd3ce3f_182.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_184.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_186.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_188.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_189.png)

```rust
use etherparse::{Ipv4HeaderSlice, TcpHeaderSlice};

![](img/e709d71a082b45418e3b70befbd3ce3f_191.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_193.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_195.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_197.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_199.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_200.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_202.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_204.png)

// 在循环中处理接收到的数据
let nbytes = nic.recv(&mut buf[..])?;

![](img/e709d71a082b45418e3b70befbd3ce3f_206.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_208.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_210.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_212.png)

// 解析以太网帧（TUN接口省略了链路层头部，直接是IP包）
// 这里我们假设前4个字节是TUN的元信息（如果使用with_packet_info），但我们使用了without_packet_info
let ip_packet = match Ipv4HeaderSlice::from_slice(&buf[..nbytes]) {
    Ok(p) => p,
    Err(_) => {
        eprintln!("Ignoring non-IPv4 packet");
        continue;
    }
};

![](img/e709d71a082b45418e3b70befbd3ce3f_214.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_216.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_218.png)

// 检查协议类型，只处理TCP（协议号6）
if ip_packet.protocol() != 0x06 {
    continue; // 忽略非TCP数据包
}

![](img/e709d71a082b45418e3b70befbd3ce3f_220.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_222.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_223.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_225.png)

// 解析TCP头部
let tcp_slice = &buf[ip_packet.slice().len()..nbytes];
let tcp_packet = match TcpHeaderSlice::from_slice(tcp_slice) {
    Ok(p) => p,
    Err(_) => {
        eprintln!("Failed to parse TCP header");
        continue;
    }
};

// 打印连接四元组信息：源IP、源端口、目标IP、目标端口
println!(
    "{}:{} -> {}:{}",
    ip_packet.source_addr(),
    tcp_packet.source_port(),
    ip_packet.destination_addr(),
    tcp_packet.destination_port()
);
```

![](img/e709d71a082b45418e3b70befbd3ce3f_227.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_229.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_231.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_233.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_235.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_237.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_239.png)

### 管理TCP连接状态

![](img/e709d71a082b45418e3b70befbd3ce3f_241.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_243.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_245.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_247.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_248.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_249.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_251.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_253.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_255.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_257.png)

TCP是面向连接的协议。每个连接由一个四元组唯一标识：`(源IP, 源端口, 目标IP, 目标端口)`。我们需要为每个活跃的连接维护状态。

![](img/e709d71a082b45418e3b70befbd3ce3f_259.png)

我们将定义一个 `Connection` 结构体来保存连接状态，并使用一个 `HashMap` 来管理所有活跃连接。

```rust
use std::collections::HashMap;
use std::net::Ipv4Addr;

#[derive(Hash, Eq, PartialEq, Debug, Clone, Copy)]
struct Quad {
    src: (Ipv4Addr, u16),
    dst: (Ipv4Addr, u16),
}

![](img/e709d71a082b45418e3b70befbd3ce3f_261.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_263.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_265.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_267.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_269.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_271.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_273.png)

struct Connection {
    // 连接状态机（例如：Listen, SynReceived, Established, FinWait1等）
    state: State,
    // 发送序列号空间状态
    send: SendSequenceSpace,
    // 接收序列号空间状态
    recv: RecvSequenceSpace,
    // 其他字段，如IP和TCP头部模板、重传队列等
}

![](img/e709d71a082b45418e3b70befbd3ce3f_275.png)

// 发送序列号空间 (RFC 793 Section 3.2, Figure 4)
struct SendSequenceSpace {
    una: u32, // 已发送但未确认的最老序列号
    nxt: u32, // 下一个要使用的序列号
    wnd: u16, // 发送窗口大小
    up: bool, // 紧急指针标志
    wl1: usize, // 用于窗口更新的段序列号
    wl2: usize, // 用于窗口更新的段确认号
    iss: u32,  // 初始发送序列号
}

![](img/e709d71a082b45418e3b70befbd3ce3f_277.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_279.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_281.png)

// 接收序列号空间 (RFC 793 Section 3.2, Figure 5)
struct RecvSequenceSpace {
    nxt: u32, // 期望接收的下一个序列号
    wnd: u16, // 接收窗口大小
    up: bool, // 紧急指针标志
    irs: u32, // 初始接收序列号
}

![](img/e709d71a082b45418e3b70befbd3ce3f_283.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_285.png)

enum State {
    Closed,
    Listen,
    SynReceived,
    Established,
    FinWait1,
    FinWait2,
    // ... 其他状态
}
```

![](img/e709d71a082b45418e3b70befbd3ce3f_287.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_289.png)

在 `main` 函数中，我们根据接收到的数据包的四元组，在 `HashMap` 中查找或创建对应的 `Connection`，并将数据包交给它处理。

![](img/e709d71a082b45418e3b70befbd3ce3f_291.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_293.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_295.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_296.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_298.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_300.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_302.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_304.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_306.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_308.png)

```rust
let mut connections: HashMap<Quad, Connection> = HashMap::new();

![](img/e709d71a082b45418e3b70befbd3ce3f_310.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_312.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_314.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_316.png)

// 在数据包处理循环中
let quad = Quad {
    src: (ip_packet.source_addr(), tcp_packet.source_port()),
    dst: (ip_packet.destination_addr(), tcp_packet.destination_port()),
};

![](img/e709d71a082b45418e3b70befbd3ce3f_317.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_319.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_321.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_323.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_324.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_326.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_327.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_329.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_331.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_333.png)

// 获取或创建连接，并处理数据包
let connection = connections.entry(quad).or_insert_with(|| {
    // 如果连接不存在，尝试接受新连接（例如，处理SYN包）
    Connection::accept(ip_packet, tcp_packet, &nic)
});

![](img/e709d71a082b45418e3b70befbd3ce3f_335.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_337.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_339.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_341.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_343.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_345.png)

// 如果连接存在，调用其 on_packet 方法处理数据包
if let Some(conn) = connection {
    conn.on_packet(ip_packet, tcp_packet, &data_payload, &nic)?;
}
```

![](img/e709d71a082b45418e3b70befbd3ce3f_347.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_349.png)

---

![](img/e709d71a082b45418e3b70befbd3ce3f_351.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_353.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_355.png)

## 实现TCP三次握手 🤝

![](img/e709d71a082b45418e3b70befbd3ce3f_357.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_359.png)

TCP使用三次握手来建立连接。状态机是理解这个过程的关键。我们将根据RFC 793中定义的状态转换图来实现。

![](img/e709d71a082b45418e3b70befbd3ce3f_361.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_363.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_364.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_365.png)

### 状态转换概述

![](img/e709d71a082b45418e3b70befbd3ce3f_367.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_368.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_369.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_371.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_372.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_374.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_376.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_378.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_380.png)

1.  **LISTEN (服务器)**：服务器在某个端口上监听连接请求。
2.  **SYN-RECEIVED**：当服务器收到客户端的SYN包后，进入此状态。它必须回复一个SYN-ACK包。
3.  **ESTABLISHED**：当服务器收到客户端对SYN-ACK的ACK回复后，连接建立，进入此状态。

![](img/e709d71a082b45418e3b70befbd3ce3f_382.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_384.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_386.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_388.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_390.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_392.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_394.png)

### 处理SYN包（接受连接）

![](img/e709d71a082b45418e3b70befbd3ce3f_396.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_398.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_400.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_402.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_404.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_406.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_407.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_409.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_411.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_413.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_415.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_417.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_419.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_421.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_423.png)

当我们的程序（作为服务器）在 `LISTEN` 状态（或初始状态）收到一个SYN包时，需要执行以下操作：

![](img/e709d71a082b45418e3b70befbd3ce3f_425.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_427.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_429.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_431.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_433.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_435.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_437.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_439.png)

1.  解析客户端SYN包中的初始序列号（IRS）和窗口大小。
2.  为我们这一端选择一个初始序列号（ISS）。
3.  构造一个SYN-ACK包（设置SYN和ACK标志）发送给客户端。
    *   确认号（ACK）设置为 `IRS + 1`，表示我们已收到客户端的SYN。
    *   序列号（SEQ）设置为我们的 `ISS`。
4.  更新连接状态为 `SYN-RECEIVED`，并初始化发送和接收序列号空间。

![](img/e709d71a082b45418e3b70befbd3ce3f_441.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_443.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_445.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_447.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_449.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_451.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_453.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_455.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_457.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_459.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_460.png)

```rust
impl Connection {
    /// 尝试接受一个新连接（处理SYN包）
    fn accept<'a>(
        ip_header: Ipv4HeaderSlice<'a>,
        tcp_header: TcpHeaderSlice<'a>,
        nic: &Iface,
    ) -> Option<Connection> {
        // 只处理SYN包（且不能是ACK包）
        if !tcp_header.syn() || tcp_header.ack() {
            return None;
        }

        let mut conn = Connection {
            state: State::SynReceived,
            send: SendSequenceSpace {
                iss: 0, // 简化：实际应随机生成
                una: 0,
                nxt: 1, // ISS + 1，因为SYN消耗一个序列号
                wnd: 10, // 初始窗口大小
                up: false,
                wl1: 0,
                wl2: 0,
            },
            recv: RecvSequenceSpace {
                irs: tcp_header.sequence_number(),
                nxt: tcp_header.sequence_number().wrapping_add(1), // IRS + 1
                wnd: tcp_header.window_size(),
                up: false,
            },
            // ... 初始化其他字段
        };

        // 发送 SYN-ACK 响应
        conn.send_synack(ip_header, tcp_header, nic).ok()?;

        Some(conn)
    }

    fn send_synack(
        &mut self,
        ip_header: Ipv4HeaderSlice,
        tcp_header: TcpHeaderSlice,
        nic: &Iface,
    ) -> std::io::Result<usize> {
        // 构建TCP头部
        let mut synack = etherparse::TcpHeader::new(
            tcp_header.destination_port(), // 我们的源端口
            tcp_header.source_port(),      // 目标端口
            self.send.iss,                 // 我们的序列号
            self.recv.nxt,                 // 确认号 (IRS + 1)
        );
        synack.syn = true;
        synack.ack = true;
        synack.window_size = self.send.wnd;

        // 构建IP头部（交换源和目标地址）
        let mut ip = etherparse::Ipv4Header::new(
            synack.header_len() as u16, // 负载长度（只有TCP头，无数据）
            64, // TTL
            etherparse::IpNumber::TCP,
            ip_header.destination_addr(), // 源地址（我们的地址）
            ip_header.source_addr(),      // 目标地址
        );

        // 将IP和TCP头部写入缓冲区并发送
        self.write_to_nic(&ip, &synack, &[], nic)
    }

    /// 通用函数：将IP包写入NIC
    fn write_to_nic(
        &mut self,
        ip_header: &etherparse::Ipv4Header,
        tcp_header: &etherparse::TcpHeader,
        data: &[u8],
        nic: &Iface,
    ) -> std::io::Result<usize> {
        let mut buf = [0u8; 1500];
        let mut unwritten = &mut buf[..];

        // 写入IP头部
        ip_header.write(&mut unwritten);
        // 写入TCP头部
        tcp_header.write(&mut unwritten);
        // 写入数据
        unwritten.write_all(data)?;

        // 计算实际写入的字节数
        let written = buf.len() - unwritten.len();
        nic.send(&buf[..written])?;
        Ok(written)
    }
}
```

### 处理ACK包（完成握手）

![](img/e709d71a082b45418e3b70befbd3ce3f_462.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_464.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_466.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_468.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_470.png)

当处于 `SYN-RECEIVED` 状态的连接收到一个ACK包时，需要验证这个ACK是否确认了我们发送的SYN。

![](img/e709d71a082b45418e3b70befbd3ce3f_472.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_474.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_476.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_478.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_480.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_482.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_484.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_486.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_488.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_490.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_492.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_494.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_496.png)

1.  进行“可接受的ACK”检查，确保ACK号在预期的范围内。
2.  如果ACK有效，更新发送序列号空间中的未确认指针（`una`）。
3.  将连接状态转换为 `ESTABLISHED`。

![](img/e709d71a082b45418e3b70befbd3ce3f_498.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_500.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_502.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_504.png)

```rust
impl Connection {
    fn on_packet<'a>(
        &mut self,
        ip_header: Ipv4HeaderSlice<'a>,
        tcp_header: TcpHeaderSlice<'a>,
        data: &[u8],
        nic: &Iface,
    ) -> std::io::Result<()> {
        // 首先进行序列号有效性检查（RFC 793 Section 3.3）
        if !self.segment_acceptable(tcp_header, data) {
            // 如果段不可接受，可能需要发送ACK（TODO）
            return Ok(());
        }

        match self.state {
            State::SynReceived => {
                // 在SYN-RECEIVED状态，我们期望一个ACK来确认我们的SYN
                if !tcp_header.ack() {
                    return Ok(());
                }
                // 检查ACK是否确认了我们的SYN (self.send.iss)
                let ack_num = tcp_header.acknowledgment_number();
                if self.acceptable_ack_number(ack_num) {
                    // 更新未确认的序列号
                    self.send.una = ack_num;
                    // 连接建立！
                    self.state = State::Established;
                    println!("Connection established!");
                    // 这里我们可以选择立即关闭连接作为测试
                    self.initiate_close(nic)?;
                } else {
                    // ACK无效，可能发送RST（TODO）
                }
            }
            State::Established => {
                // 处理已建立连接的数据包...
                // 例如：处理数据、FIN包等
                self.handle_established(ip_header, tcp_header, data, nic)?;
            }
            // ... 处理其他状态
            _ => {}
        }
        Ok(())
    }

    /// 检查ACK号是否可接受（简化版）
    fn acceptable_ack_number(&self, ack_num: u32) -> bool {
        // ACK号必须大于已发送未确认的序列号（self.send.una）
        // 并且小于等于下一个要发送的序列号（self.send.nxt）
        // 注意：需要处理32位环绕运算
        wrapping_lt(self.send.una, ack_num) && wrapping_le(ack_num, self.send.nxt)
    }
}

![](img/e709d71a082b45418e3b70befbd3ce3f_506.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_508.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_510.png)

/// 辅助函数：处理32位无符号整数的环绕比较
fn wrapping_lt(a: u32, b: u32) -> bool {
    (a.wrapping_sub(b) as i32) < 0
}
fn wrapping_le(a: u32, b: u32) -> bool {
    (a.wrapping_sub(b) as i32) <= 0
}
```

![](img/e709d71a082b45418e3b70befbd3ce3f_512.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_514.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_516.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_518.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_519.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_521.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_523.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_524.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_526.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_527.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_529.png)

---

## 实现连接终止 🚪

![](img/e709d71a082b45418e3b70befbd3ce3f_531.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_533.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_535.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_537.png)

TCP使用四次挥手来优雅地关闭连接。为了简化，在我们的测试中，服务器在建立连接后立即发起关闭。

![](img/e709d71a082b45418e3b70befbd3ce3f_539.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_541.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_543.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_545.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_547.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_549.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_551.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_553.png)

### 发送FIN包

![](img/e709d71a082b45418e3b70befbd3ce3f_555.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_556.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_558.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_560.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_562.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_564.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_566.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_568.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_570.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_571.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_573.png)

在 `ESTABLISHED` 状态，我们可以通过发送一个FIN包来发起关闭。

![](img/e709d71a082b45418e3b70befbd3ce3f_575.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_577.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_579.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_581.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_583.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_585.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_587.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_589.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_590.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_592.png)

1.  设置TCP头部的FIN标志。
2.  发送数据包。
3.  进入 `FIN-WAIT-1` 状态，等待对方对FIN的ACK。

![](img/e709d71a082b45418e3b70befbd3ce3f_594.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_596.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_598.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_600.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_602.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_604.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_605.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_607.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_609.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_610.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_612.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_614.png)

```rust
impl Connection {
    fn initiate_close(&mut self, nic: &Iface) -> std::io::Result<()> {
        // 构建一个FIN包
        let mut fin = etherparse::TcpHeader::new(
            // ... 端口号使用连接中存储的
            self.tcp.src_port,
            self.tcp.dst_port,
            self.send.nxt, // 当前序列号
            self.recv.nxt, // 当前期望接收的序列号
        );
        fin.fin = true;
        fin.ack = true;

        // 发送FIN包
        self.write_to_nic(&self.ip, &fin, &[], nic)?;

        // 更新下一个序列号（FIN消耗一个序列号）
        self.send.nxt = self.send.nxt.wrapping_add(1);
        self.state = State::FinWait1;
        Ok(())
    }
}
```

![](img/e709d71a082b45418e3b70befbd3ce3f_616.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_618.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_620.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_622.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_624.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_626.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_628.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_630.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_632.png)

### 处理对FIN的ACK

![](img/e709d71a082b45418e3b70befbd3ce3f_634.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_635.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_636.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_638.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_640.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_642.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_643.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_645.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_647.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_648.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_650.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_651.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_653.png)

在 `FIN-WAIT-1` 状态，我们等待对方确认我们的FIN。

![](img/e709d71a082b45418e3b70befbd3ce3f_655.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_657.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_659.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_661.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_663.png)

1.  收到ACK包后，检查它是否确认了我们的FIN。
2.  如果确认，进入 `FIN-WAIT-2` 状态，等待对方的FIN。

![](img/e709d71a082b45418e3b70befbd3ce3f_665.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_667.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_669.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_671.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_673.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_675.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_677.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_679.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_681.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_683.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_685.png)

```rust
// 在 on_packet 的 match 语句中
State::FinWait1 => {
    if tcp_header.ack() && self.acceptable_ack_number(tcp_header.acknowledgment_number()) {
        // 对方确认了我们的FIN
        self.send.una = tcp_header.acknowledgment_number();
        // 检查是否所有已发送数据（包括FIN）都被确认了
        if self.send.una == self.send.nxt {
            self.state = State::FinWait2;
        }
    }
}
```

![](img/e709d71a082b45418e3b70befbd3ce3f_687.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_689.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_691.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_693.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_695.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_697.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_699.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_701.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_703.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_704.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_706.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_708.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_710.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_712.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_714.png)

### 处理对方的FIN并最终关闭

![](img/e709d71a082b45418e3b70befbd3ce3f_716.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_718.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_720.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_722.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_724.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_726.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_728.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_730.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_732.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_734.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_736.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_738.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_740.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_742.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_744.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_746.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_748.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_750.png)

在 `FIN-WAIT-2` 状态，我们等待对方也发送FIN来关闭它的数据流。

![](img/e709d71a082b45418e3b70befbd3ce3f_752.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_754.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_756.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_758.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_760.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_762.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_763.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_765.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_767.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_769.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_771.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_773.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_774.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_776.png)

1.  收到FIN包后，发送ACK进行确认。
2.  进入 `TIME-WAIT` 状态（在我们的简单实现中可能直接关闭连接）。

![](img/e709d71a082b45418e3b70befbd3ce3f_778.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_780.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_781.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_783.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_785.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_787.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_789.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_791.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_793.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_795.png)

```rust
State::FinWait2 => {
    if tcp_header.fin() {
        // 收到对方的FIN，发送ACK
        let mut ack = etherparse::TcpHeader::new(
            self.tcp.src_port,
            self.tcp.dst_port,
            self.send.nxt,
            self.recv.nxt.wrapping_add(1), // 确认对方的FIN (IRS + data + FIN)
        );
        ack.ack = true;
        self.write_to_nic(&self.ip, &ack, &[], nic)?;

        // 更新接收序列号（FIN消耗一个序列号）
        self.recv.nxt = self.recv.nxt.wrapping_add(1);

        // 连接可以关闭了（简化：实际应等待2MSL的TIME-WAIT状态）
        println!("Connection closing.");
        // 从连接映射中移除这个连接
        return Ok(()); // 通知调用者删除此连接
    }
}
```

![](img/e709d71a082b45418e3b70befbd3ce3f_797.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_799.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_801.png)

---

![](img/e709d71a082b45418e3b70befbd3ce3f_803.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_804.png)

## 总结与展望 🎯

![](img/e709d71a082b45418e3b70befbd3ce3f_806.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_808.png)

在本节课中，我们一起学习了：

1.  **TCP协议基础**：了解了TCP在可靠数据传输中的作用。
2.  **环境搭建**：使用Linux的TUN/TAP设备在用户空间创建虚拟网络接口，为实现协议栈打下基础。
3.  **数据包解析**：利用 `etherparse` crate解析IPv4和TCP头部，提取关键信息。
4.  **连接状态管理**：设计了 `Connection` 结构体和状态枚举，使用 `HashMap` 管理多个TCP连接。
5.  **三次握手实现**：成功实现了服务器端对SYN包的响应（SYN-ACK），以及对ACK包的处理，完成了连接的建立。
6.  **连接终止**：实现了服务器主动发起关闭（发送FIN）和响应对方FIN的过程，完成了连接的四次挥手。

我们目前实现了一个非常基础的、能够完成一次完整TCP连接建立和关闭流程的服务器。它还没有处理实际的数据传输、重传机制、流量控制、拥塞控制等复杂功能。

在接下来的课程中，我们将在此基础上，逐步实现：
*   数据的发送与接收。
*   重传队列与超时重传机制。
*   滑动窗口与流量控制。
*   基本的拥塞控制。
*   最终，使我们的TCP实现能够与真实的互联网服务器（如Web服务器）进行通信。

![](img/e709d71a082b45418e3b70befbd3ce3f_810.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_812.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_814.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_815.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_817.png)

![](img/e709d71a082b45418e3b70befbd3ce3f_819.png)

本节课的代码已经能够与像 `netcat` 这样的TCP客户端进行基本的握手和关闭交互，这是一个重要的里程碑。请继续关注后续课程，我们将一起完善这个TCP实现。