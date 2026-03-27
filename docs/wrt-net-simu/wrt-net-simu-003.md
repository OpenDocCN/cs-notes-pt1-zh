# 003：编写一个网络模拟器

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_1.png)

## 概述
在本节课中，我们将学习如何编写一个网络模拟器。这个模拟器能够分配大量IP地址，并在不同的TCP端口上创建虚拟服务。当连接到这些服务时，模拟器会返回一些数据。我们将使用Linux Shell脚本（Bash）来实现这个项目，因为它对于此类任务来说简单高效。

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_3.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_5.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_7.png)

---

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_9.png)

## 回顾与准备
上一节我们完成了网络扫描器的初始版本。为了继续完善扫描器，我们需要一个模拟的网络环境来进行端口扫描。因此，本节我们将专注于创建这个网络模拟器。

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_11.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_13.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_14.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_16.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_18.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_20.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_22.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_24.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_26.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_28.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_30.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_32.png)

但在开始之前，我们需要先为网络扫描器添加一个小功能。目前，扫描器在记录IP地址时，显示的是十六进制和网络字节序格式。这是因为我们有一个库函数 `inet_addr` 可以将点分十进制IP地址转换为数字，但没有一个函数能执行反向操作——将数字转换回点分十进制字符串。接下来，我们将创建这个函数。

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_34.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_36.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_38.png)

### 创建 `to_dotted` 函数
这个函数将接收一个网络字节序格式的IP地址（数字），并返回其点分十进制字符串表示。

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_40.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_42.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_44.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_46.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_48.png)

我们将把这个函数添加到我的可重用函数库 `bird_lib` 中。

**函数定义如下：**
```c
char *to_dotted(in_addr_t ip) {
    unsigned char a, b, c, d;
    static char buffer[16];

    // 使用位操作提取IP地址的四个部分
    a = (ip >> 24) & 0xFF;
    b = (ip >> 16) & 0xFF;
    c = (ip >> 8) & 0xFF;
    d = ip & 0xFF;

    // 将四个部分格式化为点分十进制字符串
    snprintf(buffer, sizeof(buffer), "%d.%d.%d.%d", a, b, c, d);
    return buffer;
}
```

**代码解释：**
1.  `a = (ip >> 24) & 0xFF;`：将IP地址右移24位，然后与 `0xFF` 进行按位与操作，提取最高8位（对应点分十进制中的第一部分）。
2.  类似地，`b`、`c`、`d` 分别提取接下来的三个8位部分。
3.  使用 `snprintf` 函数将这四个整数格式化为点分十进制字符串，并存储在静态缓冲区 `buffer` 中。
4.  函数返回这个缓冲区的指针。

现在，我们可以在网络扫描器中用 `to_dotted` 函数替换原来的日志输出，这样就能以熟悉的点分十进制格式显示IP地址了。

---

## 构建网络模拟器
现在，让我们开始构建网络模拟器。我们将使用Bash脚本编写，因为它能方便地调用系统命令来管理网络接口。

### 项目结构
脚本将支持三个主要命令：
1.  **alloc**：分配（创建）一系列虚拟IP地址。
2.  **free**：释放（删除）所有已创建的虚拟IP地址。
3.  **simulate**：根据配置文件，在随机IP地址上启动模拟的网络服务。

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_50.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_52.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_54.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_55.png)

### 1. 基础框架与辅助函数
首先，我们创建脚本的基础框架，包括错误处理和用法说明。

**以下是脚本开头部分：**
```bash
#!/bin/bash

# 失败处理函数
fail() {
    echo "$1" >&2
    killall netcat 2>/dev/null
    exit 1
}

# 用法说明函数
usage() {
    cat <<EOF >&2
Usage: $0 <command> [arguments]

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_57.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_59.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_60.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_62.png)

Commands:
    alloc <network/cidr> [first] [amount]  分配IP地址
        Example: $0 alloc 192.168.10.0/24 10 15

    free <network/cidr>                    释放所有分配的IP地址
        Example: $0 free 192.168.10.0/24

    simulate <network/cidr> <config_file>  启动网络服务模拟
        Example: $0 simulate 192.168.10.0/24 config.txt

    stop                                   停止所有模拟服务
EOF
    exit 1
}

# 检查是否以root权限运行
if [[ $EUID -ne 0 ]]; then
    fail "This script must be run as root."
fi
```

### 2. 分配IP地址（alloc命令）
`alloc` 命令的核心是使用 `ifconfig` 命令为物理网络接口创建多个虚拟子接口，并为每个子接口分配一个指定的IP地址。

**实现步骤：**
1.  从命令行参数中获取网络地址、起始IP和分配数量。
2.  使用 `ip route show` 命令获取指定网络对应的物理接口名称。
3.  计算要分配的所有IP地址（需跳过本机IP和默认网关IP，避免冲突）。
4.  循环调用 `ifconfig <interface>:<index> <ip> up` 命令创建子接口并分配IP。

**关键代码片段：**
```bash
alloc_ips() {
    local network=$1
    local first=${2:-1}
    local amount=${3:-10}
    local last=$((first + amount - 1))

    # 获取网络接口
    local iface=$(grab_interface "$network")
    [[ -z "$iface" ]] && fail "Could not determine interface for network $network"

    # 获取本机IP和网关IP，以便跳过
    local own_ip=$(grab_own_ip "$iface")
    local gateway_ip=$(grab_gateway_ip)

    local count=0
    local sub_if_index=2 # 虚拟子接口从 :2 开始编号

    for i in $(seq $first $last); do
        local ip="${network%.*}.$i" # 构造完整IP
        # 跳过本机IP和网关IP
        if [[ "$ip" == "$own_ip" || "$ip" == "$gateway_ip" ]]; then
            continue
        fi
        local sub_if="${iface}:${sub_if_index}"
        # 创建虚拟接口并分配IP
        ifconfig $sub_if $ip up 2>/dev/null || fail "Failed to create interface $sub_if"
        echo "Allocated IP: $ip on $sub_if"
        ((count++))
        ((sub_if_index++))
    done
    echo "Successfully created $count IP addresses."
}
```

### 3. 释放IP地址（free命令）
`free` 命令相对简单，它需要找到之前创建的所有虚拟子接口，并将其关闭。

**实现步骤：**
1.  获取网络对应的物理接口。
2.  使用 `ifconfig | grep` 命令找出所有该接口的虚拟子接口（格式为 `eth0:2`、`eth0:3` 等）。
3.  循环调用 `ifconfig <sub_if> down` 命令关闭每个子接口。

**关键代码片段：**
```bash
free_ips() {
    local network=$1
    local iface=$(grab_interface "$network")
    [[ -z "$iface" ]] && fail "Could not determine interface for network $network"

    # 查找所有虚拟子接口
    local sub_ifs=$(ifconfig | grep -o "^${iface}:[0-9]*" | uniq)
    for sub_if in $sub_ifs; do
        ifconfig $sub_if down 2>/dev/null && echo "Freed interface: $sub_if"
    done
    # 也尝试关闭 :0 接口（如果存在）
    ifconfig ${iface}:0 down 2>/dev/null
    echo "All IP addresses freed for network $network."
}
```

### 4. 模拟网络服务（simulate命令）
这是最有趣的部分。`simulate` 命令会读取一个配置文件，配置文件每一行定义了一个服务（端口号和返回的标语）。脚本会为每个已分配的虚拟IP地址随机选择一个服务，并使用 `netcat` 在该IP和端口上启动一个监听服务。

**配置文件格式示例（config.txt）：**
```
22 SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.3
80 HTTP/1.1 200 OK
```

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_64.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_66.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_68.png)

**实现步骤：**
1.  读取配置文件，忽略空行和注释（以`#`开头的行）。
2.  获取当前网络中已分配的所有虚拟IP地址。
3.  对于每个IP地址，从配置文件中随机选择一行。
4.  解析出端口号和标语。
5.  在后台运行 `netcat` 命令，绑定到该IP和端口，并在客户端连接时发送对应的标语。

**关键代码片段：**
```bash
simulate_services() {
    local network=$1
    local config_file=$2
    [[ -f "$config_file" ]] || fail "Config file not found: $config_file"

    local iface=$(grab_interface "$network")
    # 获取所有已分配的虚拟IP地址（需要从接口信息中提取）
    local ips=$(get_allocated_ips "$iface")

    for ip in $ips; do
        # 从配置文件中随机选取一行
        local line=$(grab_random_line "$config_file")
        local port=$(echo $line | awk '{print $1}')
        local banner=$(echo $line | cut -d' ' -f2-)
        # 启动 netcat 服务
        run_netcat "$ip" "$port" "$banner" &
        echo "Launched service at $ip:$port -> $banner"
    done
    echo "Simulation started. Run '$0 stop' to terminate all services."
}

# 随机获取配置文件一行
grab_random_line() {
    local file=$1
    # 过滤注释和空行
    local lines=$(grep -v -E '^\s*(#|$)' "$file")
    local line_count=$(echo "$lines" | wc -l)
    [[ $line_count -eq 0 ]] && return
    local random_num=$((RANDOM % line_count + 1))
    echo "$lines" | sed -n "${random_num}p"
}

# 运行 netcat 的后台服务
run_netcat() {
    local ip=$1 port=$2 banner=$3
    # 循环运行，确保服务在连接断开后能重启
    while true; do
        echo "$banner" | nc -l -s "$ip" -p "$port" -q 1 2>/dev/null
        sleep 1
    done &
}
```

### 5. 停止服务（stop命令）
`stop` 命令用于终止所有由 `simulate` 命令启动的后台 `netcat` 进程。

**实现很简单：**
```bash
stop_services() {
    killall netcat 2>/dev/null
    echo "All simulation services stopped."
}
```

### 6. 主命令调度
最后，我们需要一个主逻辑来解析用户输入的命令并调用对应的函数。

```bash
case "${1:-}" in
    alloc)
        alloc_ips "$2" "$3" "$4"
        ;;
    free)
        free_ips "$2"
        ;;
    simulate)
        simulate_services "$2" "$3"
        ;;
    stop)
        stop_services
        ;;
    *)
        usage
        ;;
esac
```

---

## 使用示例
1.  **分配IP地址**：
    ```bash
    sudo ./net_sim.sh alloc 192.168.10.0/24 10 5
    ```
    这将在 `192.168.10.0/24` 网络中，从 `.10` 开始创建5个IP地址（`.10` 到 `.14`）。

2.  **启动服务模拟**：
    ```bash
    sudo ./net_sim.sh simulate 192.168.10.0/24 services.config
    ```

3.  **使用扫描器测试**：
    现在，你可以使用之前编写的网络扫描器来扫描这个网段（例如 `192.168.10.0/24`），扫描器会发现并报告在指定端口上开放的模拟服务。

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_70.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_71.png)

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_73.png)

4.  **停止模拟并清理**：
    ```bash
    sudo ./net_sim.sh stop
    sudo ./net_sim.sh free 192.168.10.0/24
    ```

---

## 总结
在本节课中，我们一起完成了一个网络模拟器的构建。我们首先为扫描器添加了 `to_dotted` 函数，以改善IP地址的显示格式。然后，我们使用Bash脚本创建了一个功能完整的网络模拟器，它能够：
*   批量分配和释放虚拟IP地址。
*   根据配置文件，在随机IP和端口上启动模拟服务（如SSH、HTTP）。
*   为测试网络扫描工具提供了一个可控、安全的模拟环境。

![](img/0aa4836ec0290e8bfae03ac6578d7ed7_75.png)

这个项目展示了如何利用Shell脚本快速组合系统命令来实现有用的系统管理功能。在下一节课中，我们将利用这个模拟环境来测试和优化我们的网络端口扫描器。