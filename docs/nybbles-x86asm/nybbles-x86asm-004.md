# 004：错误消息、存储库类型选择与存储库文件 📁

## 概述
在本节课中，我们将学习如何在x86汇编语言项目中处理错误消息、实现状态栈管理、创建对话框以及优化按钮渲染逻辑。我们将构建一个工具，用于编辑游戏数据存储库（bank），并逐步完善其用户界面和交互逻辑。

---

## 状态栈管理 🧱

上一节我们介绍了工具的基本状态管理。本节中，我们将看到单一“当前状态”变量的局限性，并引入状态栈的概念。

![](img/280578a6e4f2fc5320877f4de87185f8_1.png)

当用户在不同界面（如主界面、新建文件对话框、错误提示框）之间导航时，我们需要记住返回路径。一个简单的状态指针无法处理嵌套的状态转换（例如，从主界面进入新建状态，再弹出错误提示）。因此，我们需要一个栈来管理状态历史。

### 核心概念：状态栈实现
我们分配一块32字节（16个状态）的内存作为栈，并维护一个栈指针。栈从高地址向低地址增长。

**数据结构定义：**
```assembly
; 状态结构体
struc state_def
    .code   dw ?    ; 状态标识码
    .pad    db ?    ; 填充字节，保证结构体大小为偶数
    .callback dd ?  ; 该状态的回调函数指针
endstruc

![](img/280578a6e4f2fc5320877f4de87185f8_3.png)

; 状态栈指针变量
state_stack_ptr dw ?
```

**栈操作宏：**
```assembly
; 状态入栈宏
%macro state_push 1
    push bx
    mov bx, [state_stack_ptr]
    sub bx, 2
    mov [bx], word %1
    mov [state_stack_ptr], bx
    pop bx
%endmacro

; 状态出栈宏
%macro state_pop 0
    mov bx, [state_stack_ptr]
    add bx, 2
    mov [state_stack_ptr], bx
%endmacro

; 获取栈顶状态到BP寄存器
%macro state_top 0
    push bx
    mov bx, [state_stack_ptr]
    mov bp, [bx]
    pop bx
%endmacro
```

![](img/280578a6e4f2fc5320877f4de87185f8_5.png)

![](img/280578a6e4f2fc5320877f4de87185f8_7.png)

![](img/280578a6e4f2fc5320877f4de87185f8_9.png)

![](img/280578a6e4f2fc5320877f4de87185f8_11.png)

![](img/280578a6e4f2fc5320877f4de87185f8_13.png)

![](img/280578a6e4f2fc5320877f4de87185f8_15.png)

![](img/280578a6e4f2fc5320877f4de87185f8_17.png)

程序启动时，我们将初始状态（如“无文件”状态）压入栈中。当用户点击“新建”按钮时，我们将“新建文件”状态压栈。当用户按下ESC或回车时，我们执行`state_pop`返回到前一个状态。

![](img/280578a6e4f2fc5320877f4de87185f8_19.png)

![](img/280578a6e4f2fc5320877f4de87185f8_21.png)

![](img/280578a6e4f2fc5320877f4de87185f8_23.png)

![](img/280578a6e4f2fc5320877f4de87185f8_25.png)

![](img/280578a6e4f2fc5320877f4de87185f8_27.png)

![](img/280578a6e4f2fc5320877f4de87185f8_29.png)

---

![](img/280578a6e4f2fc5320877f4de87185f8_31.png)

![](img/280578a6e4f2fc5320877f4de87185f8_32.png)

![](img/280578a6e4f2fc5320877f4de87185f8_34.png)

![](img/280578a6e4f2fc5320877f4de87185f8_36.png)

![](img/280578a6e4f2fc5320877f4de87185f8_38.png)

![](img/280578a6e4f2fc5320877f4de87185f8_40.png)

![](img/280578a6e4f2fc5320877f4de87185f8_41.png)

![](img/280578a6e4f2fc5320877f4de87185f8_43.png)

## 文本字段与回调处理 ⌨️

![](img/280578a6e4f2fc5320877f4de87185f8_45.png)

上一节我们实现了状态栈。本节中，我们来看看如何让文本输入框与状态转换协同工作。

每个文本字段（如输入文件名的字段）都有一个关联的回调函数。当用户在字段中按下回车（接受输入）或ESC（取消）时，会触发此回调。

**回调函数处理逻辑：**
```assembly
bank_fname_callback:
    cmp al, 0          ; AL=0 表示按下ESC
    je .handle_escape
    ; 处理回车：验证文件名，尝试创建或加载文件
    ; ...
    jmp .done
.handle_escape:
    state_pop          ; 取消操作，弹出当前状态，返回上一状态
.done:
    ret
```

![](img/280578a6e4f2fc5320877f4de87185f8_47.png)

在“新建”按钮的回调中，我们不仅需要压入新状态，还要激活对应的文本字段并将光标置于末尾：
```assembly
bank_new_callback:
    state_push state_new_file ; 压入新状态
    ; ... 获取文本字段指针到BP ...
    mov byte [bp+text_field.flags], TEXT_FIELD_ENABLED ; 激活字段
    xor bx, bx
    mov bl, TXT_CH_IDX_BANK_FNAME ; 设置字段索引
    call text_field_end           ; 将光标移到字段末尾
    ret
```

---

![](img/280578a6e4f2fc5320877f4de87185f8_49.png)

![](img/280578a6e4f2fc5320877f4de87185f8_51.png)

![](img/280578a6e4f2fc5320877f4de87185f8_53.png)

![](img/280578a6e4f2fc5320877f4de87185f8_55.png)

![](img/280578a6e4f2fc5320877f4de87185f8_57.png)

## 对话框与消息框 🪟

![](img/280578a6e4f2fc5320877f4de87185f8_59.png)

当需要向用户显示错误或确认信息时，我们使用一个模态对话框（消息框）。本节中，我们来实现它的显示和基本交互。

消息框是一个独立的状态。当进入此状态时，我们需要：
1.  禁用主界面上的其他按钮。
2.  启用消息框专属的“确定”和“取消”按钮。
3.  在屏幕指定区域绘制一个框体并显示消息文本。

**消息框显示函数：**
```assembly
message_box_show:
    state_push state_message_box ; 进入消息框状态
    mov byte [message_box_enabled], 1 ; 启用绘制
    ; 启用“确定”和“取消”按钮
    button_set button_ok, F_BUTTON_ENABLED | F_BUTTON_VISIBLE
    button_set button_cancel, F_BUTTON_ENABLED | F_BUTTON_VISIBLE
    ret
```

**消息框状态回调：**
在此状态的回调函数中，我们主要检查按键。按下ESC键视为取消，并退出消息框状态。
```assembly
state_message_box.callback:
    call input_get_key
    cmp al, KEY_ESCAPE
    jne .done
    mov byte [message_box_enabled], 0 ; 禁用消息框
    button_set button_ok, 0           ; 禁用按钮
    button_set button_cancel, 0
    state_pop                         ; 弹出消息框状态，返回之前状态
    mov al, 1                         ; 告知主循环已处理按键
.done:
    ret
```

![](img/280578a6e4f2fc5320877f4de87185f8_61.png)

![](img/280578a6e4f2fc5320877f4de87185f8_63.png)

![](img/280578a6e4f2fc5320877f4de87185f8_65.png)

![](img/280578a6e4f2fc5320877f4de87185f8_67.png)

![](img/280578a6e4f2fc5320877f4de87185f8_69.png)

![](img/280578a6e4f2fc5320877f4de87185f8_71.png)

**消息文本存储：**
我们预留一块内存来存储消息框的多行文本。每行是一个Pascal风格字符串（长度前缀+内容）。
```assembly
message_box_lines:
    %rep 10  ; 10行
        string_reserve 32 ; 每行预留32字节空间，初始长度为0
    %endrep
    db 0FFh  ; 结束标记
```
`string_reserve`是一个新宏，它分配指定长度的空间并用空格填充，但将长度字节设为0，表示空字符串。

![](img/280578a6e4f2fc5320877f4de87185f8_73.png)

---

![](img/280578a6e4f2fc5320877f4de87185f8_75.png)

![](img/280578a6e4f2fc5320877f4de87185f8_77.png)

![](img/280578a6e4f2fc5320877f4de87185f8_79.png)

![](img/280578a6e4f2fc5320877f4de87185f8_81.png)

## 按钮渲染优化 🎨

![](img/280578a6e4f2fc5320877f4de87185f8_83.png)

为了在对话框弹出时清晰地显示哪些按钮可用，我们优化了按钮的渲染逻辑。本节中，我们为按钮添加了“可见”和“启用”两个独立标志。

按钮的标志位（`F_BUTTON_VISIBLE` 和 `F_BUTTON_ENABLED`）现在被分开处理：
*   **可见**：按钮是否被绘制在屏幕上。
*   **启用**：按钮是否可点击（并影响其文本颜色）。

**按钮绘制逻辑更新：**
在遍历按钮列表进行绘制的函数中，我们首先检查`F_BUTTON_VISIBLE`标志。如果不可见，则跳过。如果可见，则根据`F_BUTTON_ENABLED`标志决定文本颜色（启用时为亮色，禁用时为暗色）。
```assembly
draw_buttons:
    mov bp, button_array
.loop:
    mov ax, [bp+button.flags]
    test ax, F_BUTTON_VISIBLE
    jz .next_button      ; 不可见，跳过
    ; ... 计算位置和绘制背景 ...
    test ax, F_BUTTON_ENABLED
    jnz .text_enabled
    mov ch, COLOR_DARK_GRAY ; 禁用状态文本颜色
    jmp .draw_text
.text_enabled:
    mov ch, COLOR_LIGHT_GRAY ; 启用状态文本颜色
.draw_text:
    ; ... 绘制按钮文本 ...
.next_button:
    add bp, button_size
    jmp .loop
```

![](img/280578a6e4f2fc5320877f4de87185f8_85.png)

**按钮设置宏：**
为了方便地修改按钮状态，我们创建了一个`button_set`宏：
```assembly
%macro button_set 2 ; %1=按钮标签， %2=标志位值
    push bp
    mov bp, %1
    mov [bp+button.flags], word %2
    pop bp
%endmacro
```
这样，在弹出消息框时，我们可以轻松地禁用主界面按钮，并启用消息框的按钮。

![](img/280578a6e4f2fc5320877f4de87185f8_87.png)

![](img/280578a6e4f2fc5320877f4de87185f8_89.png)

---

## 字符串绘制优化 ✨

![](img/280578a6e4f2fc5320877f4de87185f8_91.png)

在绘制消息框文本时，我们进一步优化了字符串绘制函数。本节中，我们添加了两个简单的优化以提升效率。

![](img/280578a6e4f2fc5320877f4de87185f8_93.png)

![](img/280578a6e4f2fc5320877f4de87185f8_95.png)

![](img/280578a6e4f2fc5320877f4de87185f8_97.png)

![](img/280578a6e4f2fc5320877f4de87185f8_99.png)

**优化1：跳过空字符串**
如果传入的Pascal字符串长度为0，则直接返回，避免不必要的循环。
```assembly
draw_string:
    ; ... 设置SI为字符串指针 ...
    xor cx, cx
    mov cl, [si]        ; 获取长度字节
    jcxz .done          ; 如果长度为0，直接结束
    ; ... 正常绘制逻辑 ...
.done:
    ret
```

**优化2：跳过空格**
在绘制每个字符时，如果遇到空格（ASCII 0x20），我们可以直接跳到更新位置的计算，而无需进行颜色判断和像素绘制。
```assembly
.draw_char_loop:
    lodsb               ; 加载字符到AL
    cmp al, ' '
    je .skip_space      ; 是空格，跳过绘制步骤
    ; ... 正常处理非空格字符 ...
    jmp .next_char
.skip_space:
    ; ... 仅更新绘制位置X坐标 ...
.next_char:
    loop .draw_char_loop
```
这些优化虽然微小，但体现了在资源受限环境下（如DOS实模式）进行编程时的效率考量。

---

![](img/280578a6e4f2fc5320877f4de87185f8_101.png)

## 总结 🎯

![](img/280578a6e4f2fc5320877f4de87185f8_103.png)

![](img/280578a6e4f2fc5320877f4de87185f8_105.png)

![](img/280578a6e4f2fc5320877f4de87185f8_106.png)

本节课中我们一起学习了：
1.  **状态栈管理**：使用栈来处理嵌套的UI状态转换，实现了`state_push`、`state_pop`和`state_top`宏。
2.  **文本字段回调**：将文本输入框的完成事件（回车/ESC）与状态转换逻辑连接起来。
3.  **对话框实现**：创建了一个模态消息框状态，可以显示多行文本，并包含“确定”和“取消”按钮。
4.  **按钮状态优化**：区分了按钮的“可见性”与“启用”状态，并据此改变渲染效果。
5.  **绘制例程优化**：在字符串绘制中添加了对空字符串和空格的特殊处理，提升了渲染效率。

![](img/280578a6e4f2fc5320877f4de87185f8_108.png)

![](img/280578a6e4f2fc5320877f4de87185f8_110.png)

通过这些步骤，我们工具的基础UI框架变得更加健壮，能够处理更复杂的用户交互流程，为后续实现文件操作和存储库编辑功能打下了坚实基础。