# CAN通讯
## 硬件连接
![[Pasted image 20260607234748.png]]
组成： microControl can控制器 can收发器
两根线：CANH CANL
用双绞线可以提高抗干扰能力

网络两端必须有120欧的终端电阻（120欧的原因，电缆的阻抗特性是120欧，为了模拟无限远的[[传输线]]）
## 电平信号
总线电平 分为隐形电平 显性电平

IOS11898标准

| 名称|性质 |逻辑 |CAN_H |CAN_L|压差|
|--- |---| --- |--- |---|---|---|
|recessive|隐形| 1 |2.5V|2.5V|0V|
|dominant|显性| 0 | 3.5v| 1.5v| 2v|

控制器发0时 两条线压差为2v 总线上称为隐形电平
控制器发1时 两条线压差为0v 总线上称为显形电平

多节点发送数据涉及[[总线仲裁]]
## ==CAN外设内部结构==
    - 邮箱（发送，接收）
    - 筛选器
--------
## CAN帧类型

| | 名称|用途 |
|---|---|---|
|1|数据帧|发送单元向接收单元传送数据|
|2|遥控帧|接收单元向具有相同 ID 的发送单元请求数据|
|3|错误帧|检测出错误时向其它单元通知错误的帧|
|4|过载帧|节点正忙于处理接收的信息,可以通知其它节点暂缓发送新报文|
|5|帧间隔|用于将数据帧及遥控帧与前面的帧分离开来的帧|
3，4，5 硬件自动完成

数据帧和遥控帧有两种格式
- 标准 ：11位
- 扩展：29位
![[Pasted image 20260608001358.png]]
![[Pasted image 20260608001406.png]]

| 名称	|描述|
|---|---|
|帧起始|帧开始 一个bit的显性电平。|
|仲裁段|帧的优先级， 由标识符（ID）和传送帧类型(RTR)组成。|
|控制端|数据的字节数 由6个bit构成|
|数据段|数据内容 可发送0～8 个字节的数据|
|CRC段|校验传输是否正确|
|ACK段|确认是否正常接收|
|帧结束|帧结束|

- 仲裁端
类似代表优先级
格式：11位 | 29位 -> 都是ID
29位格式时分为11 + 18 位 在中间由一个SRR和IDE
仲裁端结尾有一位RTR
CAN每一个节点都有一个[[过滤器]]
- 控制端
表示发送多少个字节数据
由IDE（1）+r0（1）+DLC（4）组成
r1、r0：保留位 发送的必须是1 接收不管电平 （感觉是用来停顿用的）
由DLC决定发多少数据
- 数据段
发数据的，最多8bits

## 波特率
bit/s
can的波特率计算
CAN波特率 = f~APB~/prescaler/（TBS1+TBS2+SJY）

TBS1: TIM Quanta in bit segement 1
TBS2: TIM Quanta in bit segement 2
SJY: ReSynchronization Jump width`


## 发送过程
设备1 在发送邮箱里面写邮件（id 长度 数据 校验）
然后发送到设备2 经过过滤器（过滤不可以发送的邮件）
最后到设备2的can的接收邮箱

## can配置
启动can的发送和接收外设（can_init)
配置筛选器
配置Message内容（发送邮件内容格式）
### 筛选器配置
CAN_FilterTypeDef结构体
```C
typedef struct  
{  
  uint32_t FilterIdHigh;         
  uint32_t FilterIdLow;        
  uint32_t FilterMaskIdHigh;       
  uint32_t FilterMaskIdLow;    
  uint32_t FilterFIFOAssignment;  
  uint32_t FilterBank;           
  uint32_t FilterMode;           
  uint32_t FilterScale;           
  uint32_t FilterActivation;    
  uint32_t SlaveStartFilterBank; 
} CAN_FilterTypeDef;

```