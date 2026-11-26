# 全称
EtherCAT = Ethernet Control Automation Technology (以太网控制自动化技术)
# 技术定位与底层特点
- 定位：超高速以太网现场总线，是一种修改以太网底层的实时以太网方案
- 物理层：沿用标准以太网物理层->硬件驱动性架构，对软件协议栈的性能依赖性最小(可定制化程度高)
- 链路层：抛弃标准的以太网的[[CSMA/CD机制]]，修改链路层以保障实时性
ethercat的物理层与以太网类似(EtherType)
IPV4 0x8000
IPV6 0x86DD
ErherCAT 0x88A4

# 硬件与传输参数
- 传输速率：100Mbit/s (100base-TX)
- 传输介质：超 5 类屏蔽网线、光纤
- 设备硬件要求：
    - 主站：普通网卡即可实现
    - 从站：必须使用专用从站控制芯片
    
# 起源与标准化

- 开发方：德国 Beckhoff（倍福）公司
- 推广组织：ETG（EtherCAT Technology Group）协会
- 时间线：2003 年推向市场，2007 年成为国际标准
# 以太网为什么不适合
## 带宽利用率 
![[Pasted image 20261120102203.png]]
以太网帧的长度最小要求：82bit
## 
# EtherCAT slave station
ESC通识
ECAT Procesing Unit内部包括：
- Moniting 
- PHY_Management
- FMMU
- SyncManager
- Distributed_Clocks(DC分布式时钟)
- EEPROM(

# SOES
ethercat精简版的从站**Simple EtherCAT Slave**
针对 EtherCat 协议实现的从站（Slave）代码库，名为SOES