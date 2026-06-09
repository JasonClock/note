# Linux学习
## 基本
### cat 将 文件内的内容打印在bash上
### mkdir 创建文件夹
-p 表示parent 父目录 作用:允许跨级连环建和静音防炸 
第一个作用的意思就是，eg mkdir -p ~/parent/hello.c 如果发现没有parent时会自己建，如果没有-p就直接报错
第二个作用的意思就是，如果存在同名文件夹 如果没有-p就会报错，有的会就直接跳过这一句话完成剩下代码
### touch 创建文件(没有这个文件的时候)和编辑文件(有这个文件)
## vim编辑
### nano编辑
### apt 
＋clean清楚下载源缓存 +update获取下载包 +install xxx下载(加上-y 表示所有询问都选择yes)

### pipe 管道 && redirect 重定向
linux中命令有默认三个接口
- standard input (stdin 引脚编号 0 )一般连着键盘
- standard output(stdout 引脚编号 1)一般连着终端屏幕
- standard error (stderr 引脚编号 2 )吐error information的 也连着终端屏幕
 #### `>``>>` (输出重定向)
  把要输出在屏幕的内容用一个导线接到一个文件
  eg. 
 ```bash
 ./my_hello > output.txt
 cat output.txt
 ```
会发现output.txt里面有"HEllo embedded Linux world!"
but 又有个问题了,多次使用也不会多几个"HEllo embedded Linux world!",这是因为
- `>`:是全擦后再写
- `>>`:是直接再末尾追加

#### `|` 管道(串联电路)
作用:把前一个命令的输出引脚和后一个命令的输入引脚接在一起
```bsah
ls -la \ //把根目录下的所有文件列出来
```
grep过滤命令
```bash
ls - la \ | grep "etc"
```
把输出的所有东西输出到grep命令 grep过滤出含有etc的文件(还会标红)

小练习:
温度检测日志
每一秒(watch -n 1)将时间戳和检测到的温度写在一个temp_log.txt的日志里面(用vcgencmd measure_temp)(时间用date读取 一起追加所以需要用到&&前面正确就会接着执行后面的内容)

### ps
```
ps -ef
```
查看所有的进程(静态显示)
UID : who启动的进程
PID : 这个进程的身份证号
CMD : 这个进程在执行什么命令或程序

### htop(动态显示) 终端的进程查看小妙招

### kill(杀死进程)
## 系统管理
### whoami 查看自己的身份
用户名 平民 不可以动系统核心配置和底层的硬件引脚，只能写写文件
root 可以删除系统的任何东西 直接操控硬件
`sudo`:借用超级管理权限、
### 查看文件的rwx
``` bash
ls -l
```
出现
```bash
-rw-r--r-- 1 pi pi 56 May 27 00:00 hello.c
```
解读：
- 第一个 - 表示普通文件 d表示文件夹
- 第2~4 拥有者的权限 r=read w=write  x= execute（执行权） -=无执行权 
- 第5~7个 同组（group）的权限，只读不能改
- 第8~10个 其他组（other）的权限  只能看（全部人看的）

### chmod(改变权限的秘籍)
```bash
eg. chmod +x xxxx
```
使其可以执行。

### shell脚本
shell脚本（.sh） 把Linux命令写进一个文件里，让它按顺序自动执行 差不多等效于 单片机的批量处理 
#### 写一个自动化运维脚本
- 功能：自动创建一个带当天日期的脚本的备份文件夹
``` bash
#!/bin/bash
# 告诉它要用bash运行
echo "xxx"
#获取时间信息
TODAY=$(date +%y-%m-%d) #+%y-%m-%d是它的格式，要求拿出date的年月日给TODAY变量

mkdir -p ~/embedded_study/backup_$TODAY
cp ~/embedded_study/*.c ~/embedded_study/backup_$TODAY

echo "xxxxx"
```
### mv 移动/重命名
eg.
``` bash
mv hello.txt readme.txt
```
mv [旧文件]  [新文件]
==安全隐患==：
如果已经出现了新的命名和文件夹中的文件重名就会覆盖！！
加上-i interactive 交互式提示
如果有重名会报警告:warning:

### cron & crontab (linux的硬件定时器)
cron 是系统守护进程 每分钟查看一次有没有预定要执行的任务
crontab文件 每个用户自己的任务计划表 存放着执行的命令和时间规则 cron会根据这个表自动运行
和单片机的定时中断类似，这里它有一个定时器寄存表 写一个表达式就会在时间到时自动运行
crontab -e 配置定时器 自动在固定时间运行脚本
进入配置界面 
```
# Edit this file to introduce tasks to be run by cron.
#
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
#
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
#
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
#
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
#
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
#
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
```
在末尾写`* * * * * 脚本绝对路径(实际上是命令)（/home/jason/embedded_study/backup.sh）`
`* * * * *`:表示
minute（0-59） 
hour （0-23）
day （1-31）
month （1-12）
week(周几)（0~7 0和7都是星期天）

日和周几是 | 的关系 一般不要同时设置具体值

命令最好是绝对路径 | 在crontab文件用path= 提前声明自己在哪里

#### 时间段的特殊字符


crontab -l 可以查看刚刚写下去的列表
  
| 符号	| 含义	| 示例	|
| --- | --- | --- |
| `*`  | 表示所有可能的值 | `* * * * *` 表示每分钟|
| ,  | 列举多个值 | `0 8,12 * * *` 表示在早上8点和中午12点 |
| -  | 表示一个范围 | `0 8-12 * * *` 表示8点到12点的每一个整点 |
|  /  | 步长每隔多少 | `*/10 * * * *` 在每隔10min |

不用时可以再次进入crontab计划表去注释掉
或者 用crontab -r 清空当前用户的crontab的所有任务(加上-i 删去前访问)

## 驱动外部硬件

sudo 命令切换
```
sudo -i
```

在sys文件下有class其中有许多外设
eg gpio
gpio内部还有export unexport等文件
### gpio相关命令
#### gpiodetect
- `pinout` 查找gpio引脚表
查看有什么引脚可以使用 cat /sys/kernel/debug/gpio
- 'gpiodetect' 查看gpio芯片状态
- `gpioinfo`查看引脚分配在哪条线
-c gpio芯片名称  查看特定芯片管哪些gpio
- `gpioset` 设置gpio
gpioset GPIOx=1/0 
-t 时间 设置时间
### bash的语法
```bash
while true
do


done
```
```bash
if 


fi
```
注释用#
## C语言
头文件 gpiod.h

gpiod_chip_open 打开内核 告诉地址

gpiod_line_settings/config_new 声明一块地方，赋给settings和config
然后写gpiod_line_setting_set_direction和gpiod_line_config_add_line_setting
把设置的模式和settings联系在一起，在把那一条线和config和settings联系起来
request把这个chip的这个line交给这个request

使用的时候用gpiod_line_request_set_valueji