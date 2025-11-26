# E1
TP EC3 - VHDL 1：探索 VIVADO 2025.1 环境
简介

软件环境： VIVADO 2025.1 是一个 IDE（集成开发环境），用于开发、编译和执行特定语言的程序 。


硬件平台： 我们将使用基于 Artix 7 FPGA 构建的 NEXYS-A7 开发板 。

开发板介绍 (NEXYS-A7 Artix-7)
相关文档可在 Moodle 上找到 。 主要特性： 



FPGA Artix A7™ (XC7A100T-1CSG324C)

128 MB DDR2 内存

以太网 10/100 PHY (RJ45)

USB 供电或外部 4.5-5.5V 电源供电

100 MHz 振荡器

4 个 PMOD 扩展接口

1 个 XADC 扩展接口，12位 VGA 输出端口

USB 主机接口（用于键盘、鼠标或 U 盘）和 Micro SD 卡槽

8 个七段数码管，16 个 LED 灯

16 个拨动开关和 5 个按钮

温度传感器和加速度计

FPGA 编程设置： 可以通过 USB 或 Micro SD 卡进行编程 。


JP2 跳线 (SD, USB)： 选择配置模式（USB 或 Micro SD）。


JP1 跳线 (MODE)： 设置为 "Master Serial" 时从 Flash 自动加载；设置为 "JTAG" 时等待 PC 通过 USB 编程 。

操作步骤：

通过 USB 线将 NEXYS-A7 连接到电脑并上电（默认处于演示模式）。

将 JP3 (SELECT POWER) 跳线设置为 USB 。

将 JP1 (MODE) 跳线设置为 QSPI/JTAG 。

第 1 部分：教程 - 反相器 (Inverter)

目标： 熟悉 NEXYS A7 开发板的编程流程 。 任务： 打开 Moodle 上的《Tutorial VIVADO 2025.1》文档，并完整执行其中的 7 个步骤 。


第 2 部分：4 位计数器 (Counter 4 bits)

目标： 描述一个 4 位计数器，每一位连接到开发板的一个 LED 上。使用一个开关来控制计数器的增加或减少 。

操作步骤： 按照反相器教程中的步骤进行，但需注意以下事项 ：

创建文件： 命名为 counter_4bits。


定义输入/输出 (E/S)： 


CLOCK: in

DIRECTION: in

COUNT_OUT[3:0]: out


使用模板： 在 "Flow Navigator" -> "Project Manager" 中点击 "Language Templates"。选择：VHDL -> Synthesis Constructs -> Coding Examples -> Counters -> Binary -> Up-Down Counters -> Simple Counter 。


修改代码： 复制模板代码到 counter_4bits.vhd 并按如下方式修改 ：

添加库：use IEEE.NUMERIC_STD.ALL;

修改 Entity 和 Architecture 部分（参考文档第 2 页的代码截图），核心逻辑是检测时钟上升沿，根据 DIRECTION 信号决定 count_int 是 +1 还是 -1 。





仿真： 创建仿真文件 tb_counter_4bits 并进行功能仿真 。


I/O 引脚分配 (I/O Floorplaner)： 

电平标准：LVCMOS33

CLK: E3

DIRECTION: J15

COUNT_OUT(0): H17

COUNT_OUT(1): K15

COUNT_OUT(2): J13

COUNT_OUT(3): N14


最后步骤： 执行设计流程，生成比特流并对 FPGA 进行编程。观察板上现象并思考改进方案 。

第 3 部分：秒表研究 (Study of a Stopwatch)
目标：

在 LED 灯带上显示十分之一秒（跑马灯效果）。

在两个七段数码管上显示秒和十秒 。


硬件控制： 

一个复位按钮 (Reset)。

一个按钮用于开始计数、停止计数和恢复计数 (Start/Stop)。


stopwatch.VHD 实体接口： 


输入： Clk (外部时钟), Reset (主复位), Startstop (控制信号) 。


输出： Tenthsout [9:0] (1/10秒), Onesout [6:0] (秒), Tensout [6:0] (十秒) 。



主要任务： 修改现有项目，使其能够移植到 NEXYS-A7 开发板上，关键是管理数码管的多路复用 (Multiplexing) 。

实验步骤 (第 4 页)：
准备项目：

解压 TP_E1.zip (在 Moodle 上下载) 。

创建新项目 stopwatch.xpr 。

创建约束文件 stopwatch.xdc 。


添加源文件： 在 "Project Manager" 中选择 "Add Sources"，添加所有 .vhd 文件，务必勾选 "Copy sources into project" 。


状态机分析： 研究状态机代码，画出状态图，并识别它是 Moore 型还是 Mealy 型 。

编写多路复用器 (Multiplexing)：

编写 VHDL 代码实现多路复用功能，用于显示秒和十秒 。

修改主文件 stopwatch.VHD 以集成该多路复用器 。


逻辑更改： 多路复用器的输出将替代原有的 LSBLED (秒) 和 MSBLED (十秒) 模块的输出。我们将使用数码管 1 和 2，并将数码管 3 到 8 禁用（阳极 A3 到 A8 接高电平）。

引脚分配：

选择 "I/O Pin planning" 。

参考文档第 5 页的电路图进行引脚分配（也可以直接看开发板上的丝印），并保存约束文件 。



编程： 生成比特流 (Bitstream) 并对开发板进行编程 。

附录：电路图参考 (第 5 页)
此页提供了详细的 NEXYS-A7 引脚映射，你在编写 .xdc 约束文件时需要参考此图：

Buttons (按钮): BTNL, BTNR, BTNU, BTND, BTNC (对应引脚如 P17, M17 等)。

Switches (开关): SW0 - SW15 (对应引脚如 J15, L16 等)。

LEDs (发光二极管): LD0 - LD15 (对应引脚如 H17, K15 等)。

7-seg Display (七段数码管):

位选 (Anodes): AN0 - AN7 (对应 J17, J18 等)。注意： 实验要求只用其中两个，其余要禁用。

段选 (Cathodes): CA - CG, DP (对应 T10, R10 等)。
