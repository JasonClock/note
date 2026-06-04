# OLED
- 0.96寸驱动芯片：SSD1306或SSD1315
- 1.3寸：SH1106

## SSD1306芯片
点阵显示屏控制器
### 功能：
负责接收数据，显示存储，扫描刷新等任务
通讯支持：8位6800或8080并行接口 I2c通讯传输 3/4spi
引脚:128个seg引脚和64个com引脚对应128 * 64像素点阵显示屏
内置显示存储器GDDRAM：128 * 64bits SRAM
片上振荡器
可编程帧率
复用占比？
RAM读写同步信号
长和宽的重映射
矩阵显示 驱动OLED最高15volt 256步的亮度电流控制


供电：
- V~DD~=1.65~3.3（）
- V~CC~=7 to 15v(点亮显示屏，OLED模块内部有升压电路)



### 内部结构：
![](.\屏幕截图 2026-01-03 211826.png)

### 引脚功能
NC：not connect
C1P/C1N  C2P/C2N V~BREF~ BGGND ：they should be keep NC
V~ss~ : this is ground pin
V~lss~: this is analog ground pin,it should be connected to V~ss~ externally

V~comh~ : the pin for com signal deselected voltage level.A capacitor shouled be connected beteen the pin and V~ss~
V~bat~: reserved pin.it should be connected to V~DD~

BS[2 : 0 ]:MCN bus interface selection pins 
I~REF~：this is segment output current reference pin 

FR : the pin outputs RAM write synchronization singal.输出ram写入同步值的信号 proper timing between MCU data writing and frame display timing can be acchieved to prevent tearing effect .It should be kept NC if it is not used

CL : external clock input pin(如果不使用要和V~ss~ connect)
CLS : internal clock enable pin(高电平时外部时钟使能)

RES# 
I 
This pin is reset signal input.  When the pin is pulled LOW, initialization of the chip is 
executed. Keep this pin HIGH (i.e. connect to VDD) during normal operation. //低电平时复位初始化，高电平时保持正常工作

CS# I This pin is the chip select input. (active LOW). 

 

Solomon Systech  Apr 2008 P 14/59 Rev 1.1 SSD1306  

Pin Name Type Description 
D/C# I This is Data/Command control pin. When it is pulled HIGH (i.e. connect to VDD), the data 
at D[7:0] is treated as data. When it is pulled LOW, the data at D[7:0] will be transferred 
to the command register.  
In I2C mode, this pin acts as SA0 for slave address selection. 
When 3-wire serial interface is selected, this pin must be connected to VSS. 

For detail relationship to MCU interface signals, please refer to the Timing Characteristics 
Diagrams: Figure 13-1 to  Figure 13-5. 

E (RD#) I When interfacing to a 6800-series microprocessor, this pin will be used as the Enable (E) 
signal. Read/write operation is initiated when this pin is pulled HIGH (i.e. connect to VDD) 
and the chip is selected. 
When connecting to an 8080-series microprocessor, this pin receives the Read (RD#) 
signal. Read operation is initiated when this pin is pulled LOW and the chip is selected.  
When serial or I2C interface is selected, this pin must be connected to VSS. 

R/W#(WR#) I This is read / write control input pin connecting to the MCU interface.  
When interfacing to a 6800-series microprocessor, this pin will be used as Read/Write 
(R/W#) selection input. Read mode will be carried out when this pin is pulled HIGH (i.e. 
connect to VDD) and write mode when LOW.  
When 8080 interface mode is selected, this pin will be the Write (WR#) input. Data write 
operation is initiated when this pin is pulled LOW and the chip is selected. 
When serial or I2C interface is selected, this pin must be connected to VSS. 

D[7:0] IO These are 8-bit bi-directional data bus to be connected to the microprocessor’s data bus. 
When serial interface mode is selected, D0 will be the serial clock input: SCLK; D1 will 
be the serial data input: SDIN and D2 should be kept NC. 
When I2C mode is selected, D2, D1 should be tied together and serve as SDAout, SDAin in 
application and D0 is the serial clock input, SCL.

TR0-TR6 - Testing reserved pins. It should be kept NC. 

SEG0 ~ SEG127 
O These pins provide Segment switch signals to OLED panel. These pins are VSS state when 
display is OFF. 

COM0 ~ COM63 
O These pins provide Common switch signals to OLED panel. They are in high impedance 
state when display is OFF.  

I2c内部
该通讯由从机地址和通讯总线的数据信号SDA
所有时钟和数据信号必须和上拉电阻相连 

### 命令译码器
确定输入数据是否被解释为数据或者命令，基于D/C# pin 输入进行数据翻译
如果为高电平，D[7 : 0]被翻译为命令，输入数据会被解码和写入寄存器

### 振荡器电路和显示时间发生器
模块是低功耗RC震荡器电路。时钟树


## I2C通讯协议
- 简单双向两线串行总线 允许多从设备由一个或者多个主设备控制
通讯中每一个连接到总线上的设备都由一个独特的地址，主设备通过这些地址选择特定的从设备进行通讯
- 三种通讯模式：标准模式100kbps，快速模式400kbps，高速模式3.4Mbps
- 通讯过程：主设备发送起始信号，接着发设备地址加读写位。如果从设备接收到匹配的地址，发出应答信号。之后就可以正常传输了



## 设置注意点
- I2C初始化，配置其参数，时钟速率和地址模式等
- 通过I2c函数库控制起始信号，应答位，数据传输及其停止信号
- 清除缓存区

## 工作过程
- 一开始SCL时钟线会一直保持高电位，然后SDA数据线会先被拉低，此时开始准备发送
- 接着发送地址和读写位，如果从机接收到数据就会发一个回应，然后就可以读取和接受数据（注意:写时数据线的主导权是主机，读时数据线的主导权为从机），被接收方每接收一次数据就会发送一次aks
- 聊天结束后主机会在时钟线电平处于高电平，把数据线电平拉高，聊天结束