# 031：如何将你的神经网络导入上游MLIR方言

![](img/4721fb76f4294da820a699f88670ffb1_0.png)

![](img/4721fb76f4294da820a699f88670ffb1_0.png)

在本教程中，我们将学习如何将不同框架的神经网络模型导入到上游的MLIR方言中。我们将探讨TensorFlow Lite、ONNX、PyTorch和JAX这四种主流框架，并了解如何将它们分别转换为Linalg和TOSA这两种核心的MLIR方言。教程将提供具体的项目依赖、转换步骤以及实践经验，旨在帮助初学者克服模型导入的初始障碍。

## 问题定义与学习目标

首先，我们来明确本次教程要解决的问题以及你将学到什么。

许多MLIR初学者在尝试将神经网络模型导入上游MLIR方言时感到困难。MLIR本身不是一个编译器，但其张量编译器组件是上游MLIR的重要组成部分。新用户希望使用MLIR进行平铺、尝试不同编译过程等实验，但将模型导入上游方言的工具并不包含在MLIR核心库中，而是分散在不同的代码仓库和项目中。这种碎片化给新手带来了挑战。

此外，在使用MLIR的典型编译器流程图中，从PyTorch等框架到编译器的箭头常常被隐藏，显得很神秘。但实际上，这部分工作非常复杂，且经常导致依赖关系混乱。

因此，在本教程中，你将学习：
*   如何将模型导入到Linalg或TOSA方言。
*   针对不同框架需要使用的具体项目。
*   如何找到相关代码和示例。
*   一些实用的技巧和注意事项。

需要说明的是，本教程基于2025年4月的情况，未来相关工具链可能会发生变化。

## 目标方言：Linalg与TOSA

在深入各个框架之前，我们先简要了解两个目标上游方言：Linalg和TOSA。

Linalg方言是MLIR中主要的、也可能是最常用的张量级方言。它有两种形式：命名操作（Named Ops）和通用操作（Generic Ops）。命名操作是特定通用操作（如`linalg.matmul`）的语法糖，能保留更多信息。大多数框架的导入器会尝试先转换为命名操作变体。

一个简单的Linalg示例如下：
```mlir
%0 = linalg.matmul ins(%arg0, %arg1: tensor<128x256xf32>, tensor<256x64xf32>)
                   outs(%init: tensor<128x64xf32>) -> tensor<128x64xf32>
```

TOSA（Tensor Operator Set Architecture）是上游MLIR中唯一的ML图级别方言。它致力于定义一组最小且稳定的张量运算符，并关联到一个规范。TOSA既可作为输入方言，也可作为输出方言，因为一些后端编译器会使用TOSA字节码。

## 框架一：TensorFlow Lite (TFLite)

现在，我们来看第一个框架：TensorFlow Lite（现称LiteRT）。它是一个为移动和物联网设备优化的轻量级ML运行时，使用稳定的FlatBuffer文件格式（通常以`.tflite`结尾），支持量化和模型优化，非常适合全整型网络。它最初为CNN和RNN设计，但也能编译Transformer模型。

**以下是TFLite的导入流程：**

1.  **反序列化**：首先，你需要一个`.tflite`文件。在TensorFlow项目中，有一个反序列化步骤，可以将FlatBuffer文件几乎一对一地转换为`tflite`方言。
2.  **转换为TOSA**：同样在TensorFlow项目中，存在从`tflite`方言到TOSA方言的 lowering 过程。
3.  **工具链**：目前，你可能需要两个二进制工具：`flatbuffer_translate`（用于将FlatBuffer文件转换为MLIR方言）和`tf-opt`（用于将TFLite转换为TOSA）。之前存在Python API，但已被移除。

转换后的TOSA IR示例如下，它支持全整型（如i32）和一些动态形状：
```mlir
func.func @main(%arg0: tensor<1x224x224x3xi32>) -> tensor<1x1000xi32> {
  %0 = "tosa.conv2d"(%arg0, %cst0, %cst1) {...} : (...)
  // ... 更多操作
  return %result : tensor<1x1000xi32>
}
```

**实践经验：**
*   该流程相对稳定。
*   需要注意所使用的TensorFlow包与编译的MLIR版本保持同步，因为方言可能会变化。
*   移除Python API带来了一些不便，目前需要从源码编译TensorFlow才能使用此流程。
*   依赖整个TensorFlow框架有些繁琐，因为你只使用了其中很小一部分功能（反序列化和 lowering）。
*   对于想要尝试全整型网络的用户，这可能是最简单的方式，因为TFLite量化器会处理好函数参数和返回值的类型。

## 框架二：ONNX

接下来是ONNX（开放神经网络交换格式）。它使用协议缓冲区文件格式（通常以`.onnx`结尾），代表静态计算图，旨在实现框架间的互操作性。你也可以导出训练图，因此在用于推理时需要确保没有训练节点。

**ONNX有两种导入到上游MLIR的方式：**

**1. 通过 ONNX-MLIR：**
这是一个较老的MLIR项目，最初由IBM启动。它包含一个到TOSA的 lowering 过程，但目前不完整。此外，还有一条通过StableHLO到Linalg的路径，看起来更完整一些。使用此方法需要先反序列化ONNX文件。

ONNX-MLIR使用的中间表示（IR）几乎与ONNX节点一一对应：
```mlir
%0 = "onnx.Conv"(%arg0, %arg1) {auto_pad = "NOTSET", dilations = [1, 1], ...} : (...)
```

**2. 通过 Torch-MLIR（新方式）：**
这是较新的方法。有两个主要原因：一是ONNX和ATen（PyTorch核心）操作非常相似；二是人们希望减少对大型MLIR项目的依赖。
*   流程：首先将ONNX模型导入到一个使用Torch自定义操作（Custom Ops）表示的“ONNX方言”中（这并非真正的方言，而是一一映射）。
*   然后，将这个表示转换为`torch`方言。
*   最后，`torch`方言可以被转换为TOSA。这相当于先横向转换，再向下转换。

**实践经验：**
*   两种方法都不完美。ONNX-MLIR的TOSA lowering不完整；Torch-MLIR有时会遇到失败，但正在快速改进。
*   使用Torch-MLIR的好处是，通过依赖一个项目，你同时获得了ONNX和PyTorch的支持。
*   ONNX在Torch-MLIR中的表示（使用Custom Ops）有些非常规，但这是出于实用性的选择，避免为ONNX单独维护一个完整的C++方言定义。
*   Torch-MLIR中的 lowering 代码组织很有趣，它是按操作名字母顺序排列的多个文件，本质上是一个巨大的switch语句。

## 框架三：PyTorch

现在我们来讨论最复杂的框架：PyTorch。它是一个Python优先的框架，非常流行，但导出路径没有稳定的序列化格式（Torch Export目前是实验性的，变化频繁）。

PyTorch的生态现在分为两部分：一部分依赖于PyTorch基础设施，另一部分存在于Torch-MLIR项目中。

**PyTorch导出与导入流程：**
1.  **Torch Export**：这是默认的模型导出方式，是一种追踪导出格式。你提供模型和一些示例输入（fake arguments），模型会在这些伪张量上执行，从而追踪计算图。它不能处理张量元素级别的控制流（会导致“图中断”）。
2.  **Torch-MLIR导入**：Torch-MLIR中的`torchscript`导入器接收导出的图，并输出`torch`方言。它支持一些高级特性，如可变缓冲区（mutable buffers）。
3.  **转换**：得到`torch`方言后，你可以将其 lowering 到TOSA或Linalg。这涉及类型转换和确保函数参数/返回值为张量类型的Pass管道，并非单个Pass。

一个示例如下，展示了动态形状和约束：
```python
# Python端：定义模型和动态形状约束
class SimpleModel(torch.nn.Module):
    def forward(self, x):
        return torch.nn.functional.relu(x)

model = SimpleModel()
# 约束：batch维度在2到10之间，且是16的倍数；channel维度固定为3
dynamic_shapes = {“x”: {0: torch.export.Dim(“batch”, min=2, max=10, multiple_of=16), 1: 3, 2: 224, 3: 224}}
exported_program = torch.export.export(model, args=(torch.randn(2, 3, 224, 224),), dynamic_shapes=dynamic_shapes)
# 使用 torch-mlir 的 `torchscript_export_import` 函数导入到 MLIR
```
在生成的MLIR中，你会看到代表这些约束的符号形状（Symbolic Shapes）。

**实践经验：**
*   Torch Export功能强大，能捕获符号形状、约束、混合精度等信息，这意味着PyTorch前端设计上就比TFLite前端更复杂。
*   “图中断”问题需要模型开发者来解决。
*   `torchscript`导入器设计良好，其钩子（hooks）可以像插件一样方便地用于自定义编译器或实现。
*   目前，从`torch`到Linalg的覆盖度比到TOSA更好，部分原因是TOSA受规范限制，允许的操作变体更少。
*   代码质量在某些部分存在问题，修复bug或添加新特性可能比较困难。
*   需要注意的是，如果目标方言是TOSA，由于卷积和池化操作的数据布局不同，你会在IR中看到大量转置操作，需要设法优化它们。

## 框架四：JAX

最后是JAX。它通过XLA进行加速，支持自动微分，在研究和大型Transformer模型中广泛使用，与MLIR有非常原生的集成。

**JAX的导入流程相对直接：**
1.  从JAX模型导出到StableHLO。
2.  依赖StableHLO项目（可能需要从源码构建）。
3.  从StableHLO可以 lowering 到TOSA或Linalg，其中到Linalg的路径相当完整。

一个简单的JAX导出示例代码如下：
```python
import jax
import jax.numpy as jnp
import numpy as np
from transformers import FlaxResNetModel

# 加载模型
model = FlaxResNetModel.from_pretrained(“microsoft/resnet-50”)
params = model.params

# 定义前向函数
def forward(inputs):
    return model(inputs, params=params)

# 将前向函数编译为StableHLO
compiled = jax.jit(forward).lower(jnp.ones((1, 3, 224, 224))).compile()
# compiled 中包含 StableHLO 表示
```

**实践经验：**
*   对于基本模型，流程顺畅，开箱即用。
*   对动态形状等高级特性体验较少。
*   整体印象良好，未遇到重大问题。

## 总结与对比

本节课我们一起学习了如何将四种主流机器学习框架的模型导入到上游MLIR方言。

![](img/4721fb76f4294da820a699f88670ffb1_2.png)

**总结如下：**
*   **选择正确的导入项目**至关重要：TFLite用TensorFlow项目；ONNX可选Torch-MLIR或ONNX-MLIR；PyTorch用Torch-MLIR；JAX用StableHLO。
*   **简单模型**通常在各框架下都能工作。
*   **复杂情况**，尤其是PyTorch赋予开发者的强大表达能力（动态形状、约束等），会带来更多细节上的挑战。
*   你通常可以在**TOSA和Linalg**之间选择。总体来看，Linalg的操作覆盖度更好，而TOSA因其精简、稳定的操作集也有其适用场景。
*   一个不便之处是，为了导入模型，你经常需要从源码构建多个基于MLIR的项目，这对初学者不够友好。Torch-MLIR提供了PyPI包，但TensorFlow移除Python API带来了不便。

希望本教程能帮助你更顺利地将神经网络模型带入MLIR的世界进行探索和实验。

![](img/4721fb76f4294da820a699f88670ffb1_2.png)

---
**Q&A环节摘要**

**Q1: PyTorch流程中，对于像Torch Compile或Triton这样的即时编译组件，导出器和导入器是否支持？**
A1: Torch Compile使用了许多相同的基础设施，但它能处理图中断，代价是可能有效能损耗。据我所知，Triton有自己的流程，如果遇到图中断，目前没有很好的自动处理方式，可能需要手动修复模型代码。

**Q2: 不同导入器如何处理权重？是作为常量还是函数参数？**
A2: 在ONNX-MLIR和TFLite导入器中，权重是常量操作（constant ops）。在Torch-MLIR中，权重可以被放在IR的资源部分，甚至可以保存到单独的文件中，它们不是函数的参数，而是通过类似全局加载的操作来获取。

**Q3: Torch Export 会展开所有NN模块，导致IR非常大。是否有计划支持循环结构？**
A3: 你可以指定Torch Export使用哪些分解规则。默认的分解集可能过于激进，导致循环被展开。这可能是一个系统性问题，PyTorch团队正在努力改进，例如ExecuTorch也依赖Torch Export并试图解决此类问题。

![](img/4721fb76f4294da820a699f88670ffb1_4.png)

![](img/4721fb76f4294da820a699f88670ffb1_4.png)