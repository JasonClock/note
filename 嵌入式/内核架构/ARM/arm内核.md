是arm公式规定的设计架构
arm系列有
cortex-A（application  processors应用处理器）
cortex-M（microcontroller processors微控制器处理器） cortex-R（Real-time processors实时处理器）

A系列:面向移动计算等场合，频率一般高于1Ghz



常见内核:
cortex-M3:
- NVIC(Nested vectored interrupt controller)嵌套限量中断控制器
- WIC(Wake-up interrupt control)唤醒中断器
- CPU处理器Armv7-M
- Memory protection unit(内存保护单元)
- AHB-Lite x3协议总线
- ITM trace (instrumentation trace macrocell)指令跟踪宏单元
- ETM trace (embeded trace macrocell)嵌入式跟踪宏单元
- Data watchpoint (DWT)数据监测点和跟踪
- breakpoint unit (断点单元)
- sw serial wire 串行调试
- jtag （joint test action group）联合测试工作组
![[Pasted image 20261102114032.png]]

cortex-A7:
集成L2缓存
Armv7-A架构拓展，实现40位物理寻址和增强的硬件虚拟化（没看懂没概念）
