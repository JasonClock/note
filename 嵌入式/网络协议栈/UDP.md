![[Pasted image 20261112235823.jpg]]
# UDP是什么
**UDP**全称：**User Datagram Protocol**
类型：OSI(开放式系统互联)参考模型中一种**无连接的传输层协议**，**传输可靠性没有保证**。

# 报文
结构：UDP报文头和UDP数据区域两部分
报头组成源端口(source port)、目的端口(destination port)、报文长度(Length)、校验和(checksum)
16位source port:源主机的应用程序的使用端口号
16号destination port:目的主机的应用程序使用的端口号
16位UDP Length: 
