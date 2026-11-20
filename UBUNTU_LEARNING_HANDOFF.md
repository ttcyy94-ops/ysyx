# Ubuntu / 一生一芯学习记录与续接说明

> 用途：下次开启聊天时，将本文发给助手或提供可读取的本文链接，快速恢复学习背景与实验进度。
> 整理时间：2026-11-20。此日期是文档整理日期，不代表各项实验的完成日期。
> 依据：本次对话中用户逐段提供的历史聊天、附件内容，以及最新粘贴的代码和终端输出。
> 本文是学习交接摘要，不是全部聊天逐字备份，也不是完整工程源码。未展示的文件内容与测试结果不作补全。

## 1. 给下一次助手的续接入口

用户正在 Ubuntu 虚拟机中学习一生一芯及数字逻辑实验，使用 Verilator、C++、Verilog、GTKWave、NVBoard。此前逐步做过组合逻辑、加法器、移位、LFSR、FSM、PS/2 键盘等内容。

**最新技术任务：开始实验八 VGA 接口控制器，实现静态图片显示。**

PS/2 键盘主要交互功能已由用户确认正常；VGA 目前刚开始检查既有代码，尚未完成本轮纯色显示、图片转换或图片显示验收。

**最新已经收到的文件内容：`vsrc/vga_ctrl.v`，完整代码保存在本文第 8 节。**

**最新待执行步骤：让用户提供 `cat csrc/main.cpp` 的完整输出，确认仿真时钟和复位的驱动方式，再决定 VGA 像素时钟如何连接。用户尚未提供这份最新输出。**

当前另一个任务是把学习经历整理成本文，保存到用户指定的 GitHub 仓库：

- 仓库：<https://github.com/ttcyy94-ops/ysyx>
- 用户要上传的是学习聊天的整理文档，方便下次读取；不是要求上传完整工程源码。
- 本文的生成不代表已经提交到 GitHub，提交状态应以实际仓库结果为准。

## 2. 后续指导方式

1. 使用中文，沿用现有工程，不从安装环境重新讲起。
2. 每次推进一个明确的小任务，说明在哪个终端或编辑器操作、涉及哪个文件、命令作用和预期结果。
3. 用户提供实际代码和输出后，再决定修改；不要假定助手能直接访问用户虚拟机的硬盘。
4. 讲清代码与硬件之间的关系：信号、位宽、时钟、复位、组合逻辑、时序逻辑、模块实例化。
5. 用户表示已理解原理时，直接进入编写，避免重复确认题。
6. 区分建议、用户已执行、静态检查通过、编译通过、功能测试通过、NVBoard 实际验证通过。
7. 不把助手给出的预期输出当成用户实际输出，不把助手自述“已完成”当成用户确认。
8. 保留以前实验成果，切换顶层前备份；先检查 Makefile、引脚约束和现有模块接口。
9. 报错时根据完整输出解释原因并修复，不盲目覆盖或尝试段码。
10. 用户还希望制作清晰、规范、能用于实验报告的真实仿真波形。

## 3. 环境与路径

| 项目 | 已知信息 |
| --- | --- |
| 系统 | Ubuntu 22.04 虚拟机，宿主机为 Windows，使用 VMware |
| 终端用户与主机 | `yansu@yansu-virtual-machine` |
| C 练习目录 | `~/c` |
| 一生一芯工作区 | `~/ysyx-workbench` |
| NPC | `~/ysyx-workbench/npc` |
| NVBoard | `~/ysyx-workbench/nvboard` |
| 主要实验目录 | `~/ysyx-workbench/nvboard/example` |
| Verilator 源码目录 | `/home/yansu/verilator`，曾切换到 `stable` 分支 |
| Verilator 实际报告 | `5.054 2026-11-01 rev v5.054-62-ge4c94116f` |
| GTKWave 实际版本输出 | `GTKWave Analyzer v3.3.104` |
| Valgrind | 历史记录中安装并运行过 `3.18.1` |
| NVBoard 环境变量 | 初始化脚本报告已将 `NVBOARD_HOME` 写入 `/home/yansu/.bashrc`；后续工程构建实际使用过该路径 |

早期 `gtkwave --version` 带有 `canberra-gtk-module` 加载提示，但能输出版本。不要因此让用户重复安装 GTKWave。

助手侧的临时工作区不是用户 Ubuntu 虚拟机。只能读取当前对话已提供的文件和代码；不要把聊天中出现的路径当成助手能够直接访问的文件。

## 4. 学习与实验进度总表

| 阶段 | 实际记录与状态 | 证据边界 |
| --- | --- | --- |
| C / Makefile / Valgrind | 在 `~/c` 练习 `ex4.c`，检查 printf 参数与未初始化变量；运行过 Valgrind 和 `--track-origins=yes` | 记录有 5 个错误上下文，未见修复后 0 errors 输出 |
| NEMU | 追踪分支包含 `compile NEMU`、`gdb NEMU` 提交 | 证明相关记录存在，不独立证明 NEMU 所有测试通过 |
| Verilator | 源码下载、切换 stable、autoconf、configure；后续实际 lint 和编译运行可用 | 不必继续纠结早期未贴出的安装日志；后续使用已经得到证明 |
| NPC 双控开关 | `top` 中 `assign f = a ^ b`；C++ 随机设置输入、eval、assert | 展示了代码和 sim RTL 追踪；本次摘录未展示 XOR 最终运行输出 |
| 波形基础 | 学习 FST 的编译开关和记录接口；GTKWave 已可执行 | 未见该阶段 `wave.fst` 实际生成与查看结果 |
| NVBoard 官方示例 | 初始化成功，观察流水灯；用户确认早期示例清理、构建、运行正常 | 用户明确纠正光点移动范围为 LED 8～15；低位开关映射不能仅凭助手描述断定 |
| 示例静态检查 | 将 vmem 从 top.v 拆出，最终 top lint 无警告错误 | 其他警告修复有助手总结，具体过程并非全部展示 |
| 2-4 译码器 | 提供过 decode24 设计和测试指导 | 后续助手称完成，本文可见记录缺少用户单独确认或原始通过日志 |
| 3-8 译码器 | WIDTHEXPAND 修复后 lint 通过；用户确认功能结果正确 | 给出的测试覆盖 8 输入 × 2 使能状态 |
| 普通 4-2 编码器 | 提供 encoder42.v；后续文件列表确认存在 | 未见其独立测试结果 |
| 4-2 优先编码器 | 用户确认提供的 6 组测试通过 | 测试覆盖无输入、单输入、多输入高位优先，不是全输入穷举 |
| 七段译码器 | 用户确认早期 seg7 静态检查通过；后续实际代码扩展到 0～F | 早期 tb_seg7 只有打印，没有自动判错；后续有显示调试 |
| 8-3 优先编码器 | 接入 top；处理 btn 未使用与 timescale 问题；整体编译链接成功 | 未见当阶段开关输入与显示验收的用户单独结果 |
| 一位全加器 | 全部 8 种输入组合 PASS，最后 `All 8 tests passed!` | 有用户实际运行输出 |
| 四位加法器 / ALU | 给出过 adder4 级联代码；后续目录存在 ripple_adder4、alu_addsub、alu | 文件存在不等于具体功能已验收，缺少这些阶段的完整记录 |
| 8 位移位寄存器 | lint 通过，有八种操作的实际输出，与给出的顺序测试一致 | Testbench 主要打印，不能称为全部输入空间验证 |
| 桶形移位器 | 给出组合逻辑设计，后续目录存在文件 | “四种测试通过”出自此前助手总结，当前未见原始结果 |
| LFSR | 接入按钮、LED、双数码管；修正段码拼接后用户确认正确 | “255 周期验证通过”只有此前助手总结，未见对应日志 |
| FSM | 实际 top 代码中连接 fsm_bin，使用开关、手动时钟和 LED | 未见独立测试与波形结果；已有实现文件和顶层连接 |
| PS/2 键盘 | 用户确认集成仿真通过；基本 FIFO 有实际日志；最终主要交互功能用户确认正常 | 连续 FIFO、异常帧和溢出专项测试并非全部有本次可见原始输出 |
| VGA 实验八 | 已阅读官方讲义并收到最新 vga_ctrl.v；待检查 C++ 驱动 | 尚未完成本轮纯色、图片、动态碰撞验收 |

## 5. 工程与 Git 管理

### 5.1 目录和编译方式

`nvboard/example` 曾展示的目录包含 `build`、`constr`、`csrc`、`Makefile`、`README.md`、`resource`、`vsrc`；后续增加 `testbench`。

Makefile 的关键配置是：

```make
TOPNAME = top
NXDC_FILES = constr/top.nxdc
VSRCS = $(shell find $(abspath ./vsrc) -name "*.v")
```

还会收集 csrc 下 C/C++ 文件，通过 `auto_pin_bind.py` 生成绑定，链接 NVBoard 和 SDL 相关库。

- `make`：构建默认目标。
- `make run`：构建需要更新的目标后运行。
- `make clean`：在展示过的 Makefile 中删除 `./build`。
- `.v.bak` 不被 `*.v` 规则收集。
- `vsrc` 外的顶层备份不被这条规则收集。
- 文件名与 module 名不同；同时参与编译的模块不能重复定义同名 `top`。
- 大量历史模块文件可以共存；指定 `--top-module top` 后，不应仅因有未实例化的其他模块就断言存在多个顶层错误。

曾使用过：`top_encoder.v`、`vsrc/top.v.bak`、`top_fsm_backup.v` 等备份名。它们的实际内容应在使用前核对，不要仅凭文件名覆盖当前成果。

### 5.2 一生一芯追踪分支

用户实际分支列表：`master`、当前 `pa0`、`tracer-ysyx`。

追踪日志展示过：

```text
76285f2 > sim RTL
bdc0881 > sim RTL
fafe9ff > sim RTL
387d847 > gdb NEMU
2515fc6 > compile NEMU
09acb4f > compile NEMU
```

NPC sim 规则曾要求保留：

```make
$(call git_commit, "sim RTL") # DO NOT REMOVE THIS LINE!!!
include ../Makefile
```

追踪提交在 tracer-ysyx，直接查看 pa0 的 git log 不一定能看到。追踪可能发生在编译运行之前，因此 sim RTL 提交不等于仿真通过。追踪分支包含更早的 NEMU 记录，不能断言它首次由 NPC make sim 创建。

公共配置中出现过模板学号 `ysyx_22040000` 和姓名 `张三`；它们是记录中的模板字段，不能作为用户真实身份。

## 6. 重要实验细节与纠错记录

### 6.1 译码、编码与数码管

- 3 位输入 x 与 integer i 能按数值比较，但 `x == i` 曾触发位宽扩展警告；在循环范围 0～7 内改成 `x == i[2:0]` 后检查通过。
- 高位优先编码器可以由低到高遍历，用后一次赋值覆盖前一次结果。
- valid 区分“没有输入”和“第 0 位有效”，因为两者编码都可能为 000。
- 普通编码器教程文字提到非法多位输入为高阻，但展示代码 default 实际赋值 00，应以代码行为为准。
- `reg` 不自动意味着寄存器；组合过程是否推断存储还要看赋值是否完整等条件。

已展示的适配方式采用低电平点亮，`seg7` 输出 7 位 `{A,B,C,D,E,F,G}`。约束记录为：

```text
seg0 (SEG0A, SEG0B, SEG0C, SEG0D, SEG0E, SEG0F, SEG0G, DEC0P)
```

因此八位输出用：

```verilog
assign seg = {seg_data, 1'b1};
```

最低位为小数点，1 表示关闭。早期用 `{1'b1, seg_data}` 或常见 `8'hC0` 等另一种位序表，导致显示错位、出现 bbbb。之后改为 hex7seg 包装既有 seg7，用户确认初始 0000 正常。

数字 3 在 `{A,B,C,D,E,F,G}` 下为 `0000110`；讲义其他位序中的 `0110000` 不能原样混用。

**显示数据清零会显示数字 0，并不等于数码管熄灭。** 需要消隐时，应控制段码全灭，而不是只给译码器输入 0。

### 6.2 移位寄存器与 LFSR

移位测试实际输出：

```text
Load:       Q=10110010
Logical R:  Q=01011001
Logical L:  Q=10110010
Arithmetic: Q=11011001
Serial:     Q=11011001
Rotate R:   Q=01011001
Rotate L:   Q=01100101
Clear:      Q=00000000
```

逻辑左移是在逻辑右移后连续进行的，所以输出回到 10110010。若直接对原始 10110010 左移一位，则为 01100100。不能把所有行解释成从同一原始数据独立运算。

桶形移位器给出的接口为 din[7:0]、shamt[2:0]、LR、AL、dout[7:0]；LR=1 左移，LR=0 时由 AL 选择算术或逻辑右移。组合逻辑无需时钟。

历史 LFSR 解释中反馈为 `Q[4] ^ Q[3] ^ Q[2] ^ Q[0]`，状态更新为 `{feedback, Q[7:1]}`，复位种子 8'h01；手动按钮控制推进，LED 显示 Q，双数码管显示高低半字节。

### 6.3 常见工具问题

- `--lint-only -Wall` 用于静态检查；`--binary --timing` 可构建包含延时测试平台的仿真程序。
- `TIMESCALEMOD` 曾因新模块/测试平台有 timescale，而部分旧模块没有而阻止构建；按具体输出统一时间声明。
- `1 threads`、`allocated ... MB` 是该次工具运行统计，不表示虚拟机只有一个 CPU，也不是电路实际占用这么多内存。
- 曾把 `<CodeBlock language="verilog" editable>` 复制进源文件；实际只有首行多余，末尾是 endmodule，因此只删首行，不能机械删除首尾两行。
- 单独仿真常输出到 obj_dir；NVBoard 工程使用 build/obj_dir，应以实际命令中的 Mdir 为准。

## 7. PS/2 键盘最终阶段

### 7.1 系统链路

1. ps2_keyboard：接收帧并缓存到 FIFO。
2. ps2_keyboard_system：协调 FIFO 读取，向后级提供接收到的数据和有效脉冲。
3. ps2_key_state：识别 F0 松开前缀，输出按下/松开事件。
4. ps2_to_ascii：查表转换扫描码到字符编码。
5. top：保存显示值、计数，并连接 hex7seg 与 NVBoard。

早期 ps2_keyboard 使用 resetn、key_down、key_up 接口；后来 FIFO 版本使用 clrn、nextdata_n、overflow。**接口存在版本变化，接线前必须查看实际文件，不能混用。**

### 7.2 记录中的测试与实际结果

- A：扫描码 1C，对应本项目大写 ASCII 41；B：32 → 42；C：21 → 43。
- A 松开序列 F0 1C。
- 用户确认早期系统集成测试通过。
- 基本 FIFO 实际日志：先收到 1c、READY=1，随后收到 32、READY=1，Overflow=0。
- 后续提供过连续写入 1C、32、21 再依次读取的测试，但本文可见记录未展示其实际输出。
- 最初 NVBoard 显示 bbbb，修复段码后初始显示 0000。
- 后来接入系统版 top 和按键计数后，曾按 A 无响应；处理 SW0 后用户回复“有了”“对的结果是对的”。
- 在要求验证长按 A、松开后重按、B 键之后，用户回复“功能我都验证了是对的”。可记录主要交互功能用户确认通过。

帧错误注入、连续 FIFO、溢出边界等不能仅凭此前助手完成表认定已有独立证据。

### 7.3 必须保留的实现边界

- SW0 对应键盘低有效复位：SW0=1 才解除复位。
- ready/key_down/key_up 若为单周期脉冲，LED 肉眼不闪不能证明没有事件。
- FIFO 的 ready（非空标志）与系统级 ready（事件有效脉冲）语义不同，阅读代码时要区分。
- 讲义式环形 FIFO 有 8 个物理单元且预留一槽时，有效容量为 7；实际容量和 overflow 行为仍以当前实现为准。
- 按住去重解释使用一个全局 key_held 标志，适合当前单键流程，不能据此宣称已支持完整多键同时按下与独立计数。
- 显示按十六进制拆分：seg0 扫描码低位、seg1 扫描码高位；seg2 ASCII 低位、seg3 ASCII 高位；seg4 次数低位、seg5 次数高位。
- A 第一次按下，从 seg0 到 seg5 应为 `C 1 1 4 1 0`；松开清零后为 `0 0 0 0 1 0`；不是低四位完全熄灭。
- 某段旧解释将次数 01 的 seg4/seg5 写成 0/1，是顺序错误。
- 曾给出的集成 Testbench 仅在 ready 时打印 PASS/ERROR，没有强制检查事件必须发生；正常 finish 本身不充分证明成功。

## 8. 当前 VGA 实验：最新检查点

### 8.1 官方讲义与范围

已阅读：

<https://nju-projectn.github.io/dlco-lecture-note/exp/08.html>

讲义包括 VGA 原理、分频器、控制器参考代码、静态图验收、低比特显存方案、同步显存要求，以及小图移动和边缘弹性反弹任务。

当前用户先做静态图片：检查与备份 → 纯色和彩条 → RGB444 数据转换 → 显存读取 → 图片显示。纯色和彩条是联调步骤，不代替静态图片验收；动态反弹是后续内容，尚未完成。

关键参数：

| 项目 | 数值或规则 |
| --- | --- |
| 有效分辨率 | 640×480 |
| 水平时序 | 96 同步 + 48 后沿 + 640 有效 + 16 前沿 = 800 |
| 垂直时序 | 2 同步 + 33 后沿 + 480 有效 + 10 前沿 = 525 |
| 每帧总周期 | 420000 个像素时钟 |
| 讲义像素时钟 | 25 MHz，由实体板 50 MHz 分频；对应约 59.52 Hz |
| 颜色 | RGB444，每像素 12 位，各分量输出到 8 位的高四位，低四位补 0 |
| 显存 | 640×512 = 327680 个 12 位单元 |
| 地址排列 | 按列，`{h_addr, v_addr[8:0]}`，即 h_addr×512+v_addr |
| 填充 | 每列 512 项，图像只显示前 480 行，额外行按讲义可填白色 |
| 图片转换 | 自行生成 MIF；若仿真用 readmemh，另生成一致布局的 HEX |
| 块 RAM | 讲义要求时钟沿驱动的显存；同步读取后必须考虑像素与同步/有效信号的延迟对齐 |

不要混用按列地址与行优先数据。早期助手建议过行优先布局，那是另一种组织方式，不是本讲义默认方案。

### 8.2 用户最新实际操作

```bash
cd ~/ysyx-workbench/nvboard/example
cp -n vsrc/top.v top_ps2_backup.v
cat vsrc/vga_ctrl.v
```

备份命令已执行且没有显示错误；`-n` 在已有备份时不覆盖，所以不能仅凭无输出断言该文件一定是最新键盘版本，恢复前应核对内容。

用户实际贴出的当前控制器完整代码：

```verilog
module vga_ctrl (
    input pclk,
    input reset,
    input [23:0] vga_data,
    output [9:0] h_addr,
    output [9:0] v_addr,
    output hsync,
    output vsync,
    output valid,
    output [7:0] vga_r,
    output [7:0] vga_g,
    output [7:0] vga_b
);

parameter h_frontporch = 96;
parameter h_active = 144;
parameter h_backporch = 784;
parameter h_total = 800;

parameter v_frontporch = 2;
parameter v_active = 35;
parameter v_backporch = 515;
parameter v_total = 525;

reg [9:0] x_cnt;
reg [9:0] y_cnt;
wire h_valid;
wire v_valid;

always @(posedge pclk) begin
    if(reset == 1'b1) begin
        x_cnt <= 1;
        y_cnt <= 1;
    end
    else begin
        if(x_cnt == h_total)begin
            x_cnt <= 1;
            if(y_cnt == v_total) y_cnt <= 1;
            else y_cnt <= y_cnt + 1;
        end
        else x_cnt <= x_cnt + 1;
    end
end

//生成同步信号
assign hsync = (x_cnt > h_frontporch);
assign vsync = (y_cnt > v_frontporch);
//生成消隐信号
assign h_valid = (x_cnt > h_active) & (x_cnt <= h_backporch);
assign v_valid = (y_cnt > v_active) & (y_cnt <= v_backporch);
assign valid = h_valid & v_valid;
//计算当前有效像素坐标
assign h_addr = h_valid ? (x_cnt - 10'd145) : 10'd0;
assign v_addr = v_valid ? (y_cnt - 10'd36) : 10'd0;
//设置输出的颜色值
assign {vga_r, vga_g, vga_b} = vga_data;

endmodule
```

对这份代码已说明：

- 可以作为当前实验基础，尚未要求用户修改。
- reset 是高有效同步复位，必须在 pclk 上升沿保持有效才复位。
- x_cnt 为 1～800，y_cnt 为 1～525；y_cnt 在行末增加，其余时间保持。
- 有效像素计数区间为水平 145～784、垂直 36～515。
- 对应像素坐标水平 0～639、垂直 0～479。
- RGB 直接传递 vga_data，没有内部 RGB 消隐门控；valid 应接到 VGA_BLANK_N，后续根据实际输出目标检查消隐处理。
- 参数名 h_frontporch 等不完全符合常见时序段命名，要以数值边界和表达式为准。
- 讲义实体板的 50MHz 不能直接当作当前 C++ 仿真的实际频率；先看驱动再决定是否分频。

### 8.3 下一次应从这里继续

用户尚未提供最新 main.cpp。先执行：

```bash
cd ~/ysyx-workbench/nvboard/example
cat csrc/main.cpp
```

根据实际内容检查：系统时钟翻转、eval 调用顺序、启动复位、NVBoard 更新调用。然后分步查看当前 top.v 和 constr/top.nxdc，将 vga_ctrl 接入并以固定像素颜色验证。

再检查当前 vmem.v，不要直接复用历史接口假设。早期示例 vmem 是 24 位存储、readmemh(resource/picture.hex)、组合读地址 `{h_addr, v_addr}`；它与讲义推荐的 12 位同步 RAM 并非同一实现。

## 9. 关于本次文档上传任务

用户曾因表达“全部项目”被误解为需要上传所有源码，助手指导其打包了 `~/ysyx-workbench` 并尝试从 VMware 传出。用户随后明确纠正：**只需要把当前学习聊天整理为文档上传 GitHub，方便下次读取。**

因此：

- 无需继续要求用户上传整个工程压缩包。
- 无需解决 VMware 文件拖放来完成本文内容整理。
- 不把文章写成“全部实验已经完成”的成果宣传。
- 不将教程模板姓名、学号认作用户真实资料。
- 不把历史链接中的其他聊天当成当前助手已经读取的全部对话。

## 10. 下次可以直接发给助手的话

> 请先完整读取这份 Ubuntu / 一生一芯学习交接文档，再按其中的环境、进度和指导方式继续。当前正在做实验八 VGA 静态图片显示，已提供 vga_ctrl.v，下一步是检查 csrc/main.cpp 的时钟与复位驱动。请一次带我完成一个明确步骤，结合实际代码和输出，不重复安装已经可用的工具，也不要把未验证的内容认定为通过。

