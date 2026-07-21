解读openocd下载日志

Info : auto-selecting first available session transport "swd".//优先选择第一个可以获取的传输调试器`swd`
To override use ' transport select <transport>'. 
Info : Using CMSIS-DAPv2 interface with VID:PID=0xfaed:0x4870, serial=4866556863 11 //下载器连接正常读取到VID:PID
Info : CMSIS-DAP: SWD supported                     //支持swd
Info : CMSIS-DAP: JTAG supported                    //支持jtag
Info : CMSIS-DAP: Atomic commands supported                    
Info : CMSIS-DAP: UART via USB COM port supported                   //支持usb虚拟串口发送uart data
Info : CMSIS-DAP: FW Version = Horco v0.2                    //固件版本
Info : CMSIS-DAP: Interface Initialised (SWD)                    //SWD初始化完成
Info : SWCLK/TCK = 1 SWDIO/TMS = 1 TDI = 0 TDO = 0 nTRST = 0 nRESET = 1 
/*

|信号|状态|解读|
|:--|:--|:--|
|`SWCLK/TCK = 1`|高电平|时钟线空闲状态正常|
|`SWDIO/TMS = 1`|高电平|数据线空闲状态正常（SWD 模式下 TMS 复用为 SWDIO）|
|`TDI = 0`|低电平|JTAG 数据输入线空闲（SWD 模式下不使用）|
|`TDO = 0`|低电平|JTAG 数据输出线空闲（SWD 模式下不使用）|
|`nTRST = 0`|低电平|JTAG 复位线空闲（SWD 模式下不使用）|
|`nRESET = 1`|高电平|系统复位线为高（未复位），目标芯片处于正常运行或待机状态|
*/
Info : CMSIS-DAP: Interface ready                     //外设初始化ok
Info : clock speed 2000 kHz                     //时钟频率2000KHz
Info : SWD DPIDR 0x2ba01477                     //swd识别到mcu内核
Info : [stm32g4x.cpu] Cortex-M4 r0p1 processor detected                      //读取到芯片id寄存器，确认目标是M4内核，版本为r0p1
Info : [stm32g4x.cpu] target has 6 breakpoints, 4 watchpoints                      //可以放置四个硬件观察点和六个断点
下面是CPU 暂停状态与寄存器快照
- [ ] Info : gdb port disabled [stm32g4x.cpu] halted due to debug-request, current mode: Thread  //由
xPSR: 0x01000000
pc: 0x080033fc 
msp: 0x20020000 
** Programming Started ** 
Info : device idcode = 0x20036469 (STM32G47/G48xx - Rev 'unknown' : 0x2003) 
Info : RDP level 0 (0xAA) 
Info : flash size = 256 KB 
Info : flash mode : dual-bank 
Info : gap detected from 0x08020000 to 0x0803ffff
Warn : Adding extra erase range, 0x0800c270 .. 0x0800c7ff 
** Programming Finished ** 
shutdown command invoked    //关机命令调用--说明程序已复位或者跑起来了
