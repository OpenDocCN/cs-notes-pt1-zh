# 008：数据包套接字 - 实现以太网库

![](img/c2659e9ea806b288914cf08aedf7a4e0_0.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_0.png)

## 概述

![](img/c2659e9ea806b288914cf08aedf7a4e0_2.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_4.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_5.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_7.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_9.png)

在本节课中，我们将继续构建名为“Esther”的以太网库项目。上一节我们设置了项目基础并添加了一些实用函数。本节我们将实现核心的“评估”函数，将数据结构转换为可发送的字节流，并最终通过数据包套接字发送以太网帧。

![](img/c2659e9ea806b288914cf08aedf7a4e0_10.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_12.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_13.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_15.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_17.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_19.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_21.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_23.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_24.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_26.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_28.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_30.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_32.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_33.png)

## 创建字节串数据结构

![](img/c2659e9ea806b288914cf08aedf7a4e0_35.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_37.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_39.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_41.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_43.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_45.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_47.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_49.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_51.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_53.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_55.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_57.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_59.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_61.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_62.png)

为了简化操作，我们首先创建一个新的数据结构来同时管理数据及其大小。

![](img/c2659e9ea806b288914cf08aedf7a4e0_64.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_65.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_67.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_68.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_70.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_71.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_72.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_74.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_75.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_77.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_79.png)

上一节我们介绍了数据结构的定义，本节中我们来看看如何创建一个更易用的容器。

以下是`ByteString`数据结构的定义：

![](img/c2659e9ea806b288914cf08aedf7a4e0_81.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_83.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_85.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_87.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_89.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_91.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_93.png)

```c
typedef struct ByteString {
    uint16_t size;
    uint8_t data[];
} ByteString;
```

![](img/c2659e9ea806b288914cf08aedf7a4e0_95.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_97.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_98.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_100.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_102.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_104.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_106.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_107.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_109.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_111.png)

我们还需要一个构造函数来创建`ByteString`：

![](img/c2659e9ea806b288914cf08aedf7a4e0_113.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_115.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_116.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_118.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_120.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_122.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_124.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_125.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_127.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_129.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_131.png)

```c
ByteString* make_bytestring(const uint8_t* data, uint16_t size) {
    if (!data || !size) {
        return NULL;
    }
    ByteString* bs = malloc(sizeof(ByteString) + size);
    assert(bs);
    memset(bs, 0, sizeof(ByteString) + size);
    bs->size = size;
    memcpy(bs->data, data, size);
    return bs;
}
```

![](img/c2659e9ea806b288914cf08aedf7a4e0_133.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_135.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_137.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_138.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_140.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_142.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_144.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_146.png)

## 更新评估函数

接下来，我们需要更新现有的评估函数（如`eval_icmp`, `eval_ip`），让它们返回`ByteString`指针，而不是原始的`uint8_t`指针。这能让我们更方便地处理数据大小。

![](img/c2659e9ea806b288914cf08aedf7a4e0_148.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_150.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_152.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_154.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_156.png)

以下是更新`eval_ip`函数的示例：

![](img/c2659e9ea806b288914cf08aedf7a4e0_158.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_160.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_162.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_164.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_165.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_167.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_169.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_171.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_173.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_175.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_177.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_179.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_181.png)

```c
ByteString* eval_ip(const IP* ip) {
    if (!ip) return NULL;
    // ... 原有的头部评估逻辑 ...
    // 假设 `p` 是指向已评估头部数据的指针，`length` 是总长度
    return make_bytestring(p, length);
}
```

![](img/c2659e9ea806b288914cf08aedf7a4e0_183.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_185.png)

## 实现字节串合并功能

为了将多个协议层的数据（如以太网头、IP头、载荷）合并成一个完整的数据包，我们需要一个合并函数。

以下是合并两个`ByteString`的函数：

![](img/c2659e9ea806b288914cf08aedf7a4e0_187.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_189.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_191.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_193.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_195.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_197.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_199.png)

```c
ByteString* bs_merge(ByteString* a, ByteString* b) {
    if (!a && !b) return NULL;
    if (!a) return b;
    if (!b) return a;

    uint16_t new_size = a->size + b->size;
    ByteString* merged = make_bytestring(NULL, new_size);
    if (!merged) return NULL;

    uint8_t* p = merged->data;
    memcpy(p, a->data, a->size);
    p += a->size;
    memcpy(p, b->data, b->size);

    free(a);
    free(b);
    return merged;
}
```

![](img/c2659e9ea806b288914cf08aedf7a4e0_201.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_203.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_205.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_207.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_209.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_211.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_213.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_215.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_216.png)

## 实现以太网评估函数

![](img/c2659e9ea806b288914cf08aedf7a4e0_218.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_220.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_222.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_224.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_226.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_227.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_229.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_231.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_233.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_234.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_236.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_238.png)

现在，我们可以创建一个顶层的`eval_ether`函数。它会评估以太网头部，然后递归地评估其载荷（如IP包），最后将所有部分合并。

![](img/c2659e9ea806b288914cf08aedf7a4e0_240.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_242.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_244.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_246.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_248.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_249.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_251.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_253.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_255.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_257.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_259.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_261.png)

以下是`eval_ether`函数的框架：

![](img/c2659e9ea806b288914cf08aedf7a4e0_263.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_265.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_267.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_269.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_271.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_273.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_275.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_277.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_279.png)

```c
ByteString* eval_ether(const Ether* ether) {
    if (!ether) return NULL;

    // 1. 评估以太网头部，得到 ByteString* bs_header
    ByteString* bs_header = ...;

    // 2. 如果存在载荷，则评估载荷（例如IP包）
    if (!ether->payload) {
        return bs_header;
    }
    ByteString* bs_payload = eval(ether->payload); // 使用宏或函数分派
    if (!bs_payload) {
        free(bs_header);
        return NULL;
    }

    // 3. 合并头部和载荷
    return bs_merge(bs_header, bs_payload);
}
```

![](img/c2659e9ea806b288914cf08aedf7a4e0_281.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_283.png)

## 配置数据包套接字

![](img/c2659e9ea806b288914cf08aedf7a4e0_285.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_287.png)

为了发送原始的以太网帧，我们需要使用`AF_PACKET`套接字，而不是之前使用的原始IP套接字。

![](img/c2659e9ea806b288914cf08aedf7a4e0_289.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_291.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_293.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_295.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_297.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_299.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_301.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_303.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_305.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_307.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_309.png)

以下是更新后的套接字设置函数：

![](img/c2659e9ea806b288914cf08aedf7a4e0_311.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_313.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_315.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_317.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_319.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_321.png)

```c
int setup_socket(const char* interface_name, const uint8_t* source_mac) {
    int fd = socket(AF_PACKET, SOCK_RAW, htons(ETH_P_ALL)); // 捕获所有协议
    if (fd < 0) {
        perror("socket");
        return -1;
    }

    // 绑定到特定网络接口
    struct sockaddr_ll sll;
    memset(&sll, 0, sizeof(sll));
    sll.sll_family = AF_PACKET;
    sll.sll_protocol = htons(ETH_P_ALL);
    sll.sll_ifindex = if_nametoindex(interface_name); // 获取接口索引
    if (sll.sll_ifindex == 0) {
        perror("if_nametoindex");
        close(fd);
        return -1;
    }
    memcpy(sll.sll_addr, source_mac, 6);
    sll.sll_halen = 6;

    if (bind(fd, (struct sockaddr*)&sll, sizeof(sll)) < 0) {
        perror("bind");
        close(fd);
        return -1;
    }
    return fd;
}
```

![](img/c2659e9ea806b288914cf08aedf7a4e0_323.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_325.png)

## 创建发送帧的函数

![](img/c2659e9ea806b288914cf08aedf7a4e0_327.png)

有了评估函数和配置好的套接字，我们现在可以创建发送以太网帧的函数。

![](img/c2659e9ea806b288914cf08aedf7a4e0_329.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_331.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_333.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_335.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_336.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_338.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_340.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_342.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_344.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_346.png)

以下是`send_frame`函数：

![](img/c2659e9ea806b288914cf08aedf7a4e0_347.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_349.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_351.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_353.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_355.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_357.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_359.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_361.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_363.png)

```c
bool send_frame(int fd, const Ether* ether) {
    if (fd < 0 || !ether) return false;

    ByteString* bs = eval_ether(ether);
    if (!bs) return false;

    ssize_t bytes_sent = send(fd, bs->data, bs->size, 0);
    free(bs);

    if (bytes_sent > 0) {
        return true;
    } else {
        perror("send");
        return false;
    }
}
```

![](img/c2659e9ea806b288914cf08aedf7a4e0_365.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_367.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_369.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_371.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_372.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_373.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_374.png)

## 整合测试：发送以太网帧

![](img/c2659e9ea806b288914cf08aedf7a4e0_376.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_378.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_379.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_381.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_383.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_385.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_387.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_389.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_391.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_393.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_394.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_396.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_397.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_398.png)

最后，我们创建一个主函数来整合所有部分，从命令行参数读取信息，构造数据包，并通过套接字发送。

![](img/c2659e9ea806b288914cf08aedf7a4e0_400.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_402.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_404.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_406.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_408.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_410.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_412.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_414.png)

以下是主函数的简化流程：

![](img/c2659e9ea806b288914cf08aedf7a4e0_416.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_418.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_420.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_422.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_424.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_426.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_428.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_430.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_431.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_432.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_434.png)

```c
int main(int argc, char* argv[]) {
    // 1. 解析命令行参数（源/目标MAC, 源/目标IP, 消息，接口名）
    // 2. 创建 MAC 地址结构
    // 3. 创建 IP 包结构（类型设为 L4_RAW，载荷为消息字符串）
    // 4. 创建以太网包结构，将IP包作为其载荷
    // 5. 调用 setup_socket 创建并绑定套接字
    // 6. 调用 send_frame 发送数据包
    // 7. 清理资源
    return 0;
}
```

![](img/c2659e9ea806b288914cf08aedf7a4e0_436.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_438.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_440.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_442.png)

## 总结

![](img/c2659e9ea806b288914cf08aedf7a4e0_443.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_445.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_447.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_448.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_450.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_452.png)

![](img/c2659e9ea806b288914cf08aedf7a4e0_453.png)

本节课中我们一起学习了如何完成以太网库的核心功能。我们创建了`ByteString`数据结构来简化数据管理，实现了将多层网络协议数据结构评估并合并成字节流的功能，配置了`AF_PACKET`类型的数据包套接字以发送原始以太网帧，并最终整合所有模块，成功发送了一个自定义的以太网数据包。这为我们后续进行网络监控、数据包欺骗等高级操作奠定了坚实的基础。