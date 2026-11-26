# 包含的协议类型
协议：
- [[dshot]]
- [[sbus]]
- [[can]]
- [[i2c]]
- [[uart]]
- [[spi]]
- [[工业以太网]]

# 芯片
- [[AX58400芯片解读]]


# 代码
## __attribute__((packed)) 
__attribute__((packed)) ，GCC特有写法，由于取消结构体，联合体内或者变量的编译器优化对齐
eg.
``` C
struct my {
    char ch;
    int a;
};
```
在sizeof下默认可能是8字节
如果写成
``` C
struct my {
    char ch;
    int a;
}__attribute__((packed));
```
sizeof出来的结果会是5字节
### 为什么会出现数据对齐
在现代计算机中通常要求按特定边界对齐以提高访问效率，默认情况下会在结构体之间插入填充字节（padding）实现对齐
### 用途:
- **取消填充字节**
- **按最小对齐方式排列**（通常为1字节对齐）
- **可减少结构体或联合体的内存占用**
- 与位域联合使用
``` 
在处理特殊协议或低层数据时常常会把packed和位域联合使用
eg.
unsigned int channel1: 11;
unsigned int channel2: 11;
unsigned int channel3: 11;
加上__attribute__((packed))
```
## `__attribute__((aligned(8)))` 
让数组起始地址按 **8 字节对齐**，即地址是 `8` 的倍数

## utype.h
声明
```
typedef struct {
  /* Identity */

  uint32_t serial;//序列号

  /* Inputs */

  uint16_t can0_motor_positions[8];//can0电机位置
  uint16_t can1_motor_positions[8];//can1电机位置
  int16_t can0_motor_velocities[8];//can0电机速度
  int16_t can1_motor_velocities[8];//can1电机速度
  int16_t can0_motor_currents[8];//can0电机电流
  int16_t can1_motor_currents[8];//can1电机电流
  uint8_t can0_motor_temperatures[8];//can0电机温度
  uint8_t can1_motor_temperatures[8];//can1电机温度
  int16_t can0_imu_linear_acceleration[3];//can0 imu线性加速度
  int16_t can1_imu_linear_acceleration[3];//can1 imu线性加速度
  int16_t can0_imu_angular_velocity[3];//can0 imu角速度
  int16_t can1_imu_angular_velocity[3];//can1 imu角速度
  uint8_t digital_inputs;//？
  int16_t dbus_data1[8];//dbus输入1
  int16_t dbus_data2[8];//dbus输入2
  uint32_t statusword;//状态字

  /* Outputs */

  uint32_t controlword;//控制字
  int16_t can0_motor_commnads[8];//can0电机命令
  int16_t can1_motor_commnads[8];//can1电机命令
  uint8_t digital_outputs;//？

  /* Parameters */

  int16_t max_temperature;//最大温度
  uint8_t can0_imu_trigger;//can0 imu触发
  uint8_t can1_imu_trigger;//can1 imu触发
  uint8_t gpio_modes;//gpio模式
} _Objects;

extern _Objects Obj;
```