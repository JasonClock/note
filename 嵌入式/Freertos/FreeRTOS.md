# FreeRTOS
完整的FreeRTOS包内部
```
FreeRTOSv202406.05-LTS/
└── FreeRTOS-LTS/
    ├── FreeRTOS/     ← 内核及连接类库
    ├── aws/          ← AWS IoT 服务类库
    ├── README.md、CHANGELOG.md、manifest.yml、LICENSE.md
```
~~tips:LTS 全称 Long-Term-Support~~
## freeRTOS-LTS结构
``` mermaid
graph LR
    LTS["FreeRTOS-LTS/"]
    LTS --> FILES["README.md · CHANGELOG.md · LICENSE.md<br/>manifest.yml · CODE_OF_CONDUCT.md · CONTRIBUTING.md"]
    LTS --> FRT["FreeRTOS/"]
    LTS --> AWS["aws/"]

    FRT --> KER["FreeRTOS-Kernel 11.1.0"]
    FRT --> TCP["FreeRTOS-Plus-TCP 4.2.6"]
    FRT --> CELL["FreeRTOS-Cellular-Interface 1.4.0"]
    FRT --> MQTT["coreMQTT 2.3.1"]
    FRT --> HTTP["coreHTTP 3.1.1"]
    FRT --> PKCS["corePKCS11 3.6.3"]
    FRT --> JSON["coreJSON 3.3.0"]
    FRT --> SNTP["coreSNTP 1.3.1"]
    FRT --> BACK["backoffAlgorithm 1.4.1"]

    AWS --> SIG["sigv4-for-aws-iot-embedded-sdk 1.3.0"]
    AWS --> SH["device-shadow-for-aws-iot-embedded-sdk 1.4.1"]
    AWS --> DF["device-defender-for-aws-iot-embedded-sdk 1.4.0"]
    AWS --> JB["jobs-for-aws-iot-embedded-sdk 1.5.1"]
    AWS --> FP["fleet-provisioning-for-aws-iot-embedded-sdk 1.2.1"]
    AWS --> MFS["aws-iot-core-mqtt-file-streams-embedded-c 1.1.0"]

    KER --> KSRC["内核源文件"]
    KER --> KFILE["README.md, History.txt, MISRA.md, LICENSE.md, CMakeLists.txt"]
    KER --> KINC["include/"]
    KER --> KEX["examples/"]
    KER --> KPORT["portable/ 移植层"]

    KSRC --> KSRC1["tasks.c, queue.c, list.c"]
    KSRC --> KSRC2["timers.c, event_groups.c"]
    KSRC --> KSRC3["stream_buffer.c, croutine.c"]

    KEX --> E1["cmake_example/"]
    KEX --> E2["template_configuration/"]
    KEX --> E3["coverity/"]

    KPORT --> PMEM["MemMang/"]
    PMEM --> PMEM1["heap_1.c 到 heap_5.c, 五种内存管理方案"]
    KPORT --> PCOM["Common/"]
    PCOM --> PCOM1["mpu_wrappers.c, mpu_wrappers_v2.c"]
    KPORT --> P3RD["ThirdParty/"]
    KPORT --> PTOOL["编译器工具链端口"]
    KPORT --> POTHER["template, BCC, WizC, oWatcom, Paradigm, ARMv8M"]

    PTOOL --> PGCC["GCC/"]
    PTOOL --> PIAR["IAR, Keil, RVDS, ARMClang"]
    PTOOL --> PX["MSVC-MingW, CCS, CodeWarrior, MPLAB, MikroC"]
    PTOOL --> PY["Renesas, Rowley, SDCC, Softune, Tasking"]

    PGCC --> PM1["STM32 常用 Cortex-M 端口"]
    PM1 --> PM1A["ARM_CM0, ARM_CM3, ARM_CM4F, ARM_CM7"]
    PM1 --> PM1B["ARM_CM23, ARM_CM33, ARM_CM55, ARM_CM85"]
    PM1 --> PM1C["ARM_CM3_MPU, ARM_CM4_MPU 及各 NTZ 变体"]
    PGCC --> PA1["其他 ARM 端口"]
    PA1 --> PA1A["ARM7_LPC2000 等 ARM7 系列, ARM_CA9"]
    PA1 --> PA1B["ARM_CA53_64_BIT, ARM_AARCH64"]
    PA1 --> PA1C["ARM_CR5, ARM_CRx_MPU, STR75x"]
    PGCC --> PO1["其他架构端口"]
    PO1 --> PO1A["RISC-V"]
    PO1 --> PO1B["RX100, RX200, RX600"]
    PO1 --> PO1C["MSP430F449"]
    PO1 --> PO1D["AVR_AVRDx, AVR_Mega0"]
    PO1 --> PO1E["RL78"]
    PO1 --> PO1F["TriCore_1782"]
    PO1 --> PO1G["MicroBlaze, NiosII"]
    PO1 --> PO1H["PPC405, PPC440, IA32_flat 等"]
```

### FreeRTOS软件包
| 软件包 | 用途 |
| --- | --- |
| ==FreeRTOS-knrnel== | ==内核本体== |
| FreeRTOS-Plus-TCP | [[嵌入式/网络协议栈/TCP和IP网络协议栈]]|
|FreeRTOS-Cellular-Interface| 蜂窝网络模组抽象层|
| coreMQTT | [[嵌入式/网络协议栈/MQTT 客户端协议]]库 |
| coreHTTP | [[嵌入式/网络协议栈/HTTP客户端协议]]库|
|coreJSON|JSON解析器|
|corePKCS11|加密令牌接口（PKCS11）|
|coreSNTP|SNTP 时间同步|
|backoffAlgorithm|AWS 服务的重试/退避算法|

## FreeRTOS-kernel
| 文件 | 规模 | 内核角色 | 一句话概括 | 必要性|
|---|---|---|---| --- |
| `tasks.c` | ~8700 行 | 心脏 | 任务创建、调度器、延时、优先级、任务通知 | 必须|
| `queue.c` | ~3400 行 | 通信核心 | 队列 + 信号量/互斥量的统一实现 | |
| `list.c` | ~250 行 | 基础设施 | 双向循环链表，所有等待队列的底层 |必须 |
| `timers.c` | ~1340 行 | 定时服务 | 软件定时器 + 守护任务 | |
| `event_groups.c` | ~880 行 | 同步原语 | 事件标志组 | |
| `stream_buffer.c` | ~1700 行 | 数据流 | 流/消息缓冲区（基于任务通知实现） | |
| `croutine.c` | ~400 行 | 遗留特性 | 协程（默认关闭，可忽略） | |

### 解读list文件

数据类型
``` C
struct xLIST;  //结构体前向声明

struct xLIST_ITEM  
{  
    listFIRST_LIST_ITEM_INTEGRITY_CHECK_VALUE           /**< Set to a known value if configUSE_LIST_DATA_INTEGRITY_CHECK_BYTES is set to 1. */  
    configLIST_VOLATILE TickType_t xItemValue;          /**< The value being listed.  In most cases this is used to sort the list in ascending order. */  
    struct xLIST_ITEM * configLIST_VOLATILE pxNext;     /**< Pointer to the next ListItem_t in the list. */  
    struct xLIST_ITEM * configLIST_VOLATILE pxPrevious; /**< Pointer to the previous ListItem_t in the list. */  
    void * pvOwner;                                     /**< Pointer to the object (normally a TCB) that contains the list item.  There is therefore a two way link between the object containing the list item and the list item itself. */  
    struct xLIST * configLIST_VOLATILE pxContainer;     /**< Pointer to the list in which this list item is placed (if any).指向“这个节点当前所在的链表” */  
    listSECOND_LIST_ITEM_INTEGRITY_CHECK_VALUE          /**< Set to a known value if configUSE_LIST_DATA_INTEGRITY_CHECK_BYTES is set to 1. */  
};  
typedef struct xLIST_ITEM ListItem_t; //FreeRTOS 链表节点，双向链表的元素
```
#### xItemValue：
item节点的排序值，数据类型 ：TickType_t

当多个Listitem时
A.xItemValue = 10
B.xItemValue = 30
C.xItemValue = 20
会按照10->20->30的顺序排列
被放入延时链表中
```
        xItemValue

        1050
          ↓
        ┌─────┐
        │任务A│
        └─────┘
           ↓
        ┌─────┐
        │任务B│
        │1100 │
        └─────┘
           ↓
        ┌─────┐
        │任务C│
        │1200 │
        └─────┘
```
但有B任务出现xTaskDelay(xxx);
如果此时tick时钟计到1000，那么在1100时刻B会再次启动抢占（当然此时得没有优先级比它高的
#### 方向指针
pxNext下一个节点
pxPrevious上一个节点
#### pvOwner
pvOwner作为函数指针类型，一般指向一个任务控制块
item真正想要的是让mcu做其中的动作，即TCB
通过pvOwener可以找到==TCB==并完成内部的东西

#### pxContainer
pxContainer:表明自己在哪个链表
节点处于哪一个链表，每一个链表都有自己的特殊状态
eg.下图列表（当节点处于不同状态时就会被放在不同的链表内
```
                    ┌──────────────┐
                    │   Ready List │
                    │  可以运行     │
                    └──────┬───────┘
                           ↑
                    被唤醒 │
                           │
              ┌────────────┴────────────┐
              │                         │
              │                         │
       ┌──────┴──────┐          ┌───────┴────────┐
       │ Delayed List│          │   Event List   │
       │ 等时间       │          │ 等事件         │
       └──────┬──────┘          └───────┬────────┘
              │                         │
         时间到了                    事件发生
              │                         │
              └──────────→ Ready ←──────┘


                    ┌──────────────┐
                    │Suspended List│
                    │   被挂起      │
                    └──────────────┘
                         │
                    resume后
                         ↓
                       Ready
```

configLIST_VOLATILE在这里是定义为volatile
pxContainer数据类型是struct xLIST * （下图）
``` C
typedef struct xLIST  
{  
    listFIRST_LIST_INTEGRITY_CHECK_VALUE      /**< Set to a known value if configUSE_LIST_DATA_INTEGRITY_CHECK_BYTES is set to 1. */  
    configLIST_VOLATILE UBaseType_t uxNumberOfItems;  //UBaseType_t等价unsigned long 说明现在有几个节点
    ListItem_t * configLIST_VOLATILE pxIndex; /**< Used to walk through the list.  Points to the last item returned by a call to listGET_OWNER_OF_NEXT_ENTRY ().目前指向哪一个节点 */  
    MiniListItem_t xListEnd;                  /**< List item that contains the maximum possible item value meaning it is always at the end of the list and is therefore used as a marker.链表最后一个节点是哪一个 */  
    listSECOND_LIST_INTEGRITY_CHECK_VALUE     /**< Set to a known value if configUSE_LIST_DATA_INTEGRITY_CHECK_BYTES is set to 1. */  
} List_t;
```
#### 完整性检查
**listFIRST_LIST_ITEM_INTEGRITY_CHECK_VALUE**和**listSECOND_LIST_ITEM_INTEGzRITY_CHECK_VALUE**
用于链表完整性检查机制
通过检查这些值来检查内存是否越界，越界会导致ListItem被覆盖，完整性检查失败然后发现被破坏了

#### 动作
链表初始化：void vListInitialise( List_t * const pxList )；
链表元素初始化：void vListInitialiseItem(ListItem_t * const pxItem )；
void vListInsertEnd( List_t * const pxList,   ListItem_t * const pxNewListItem )；
void vListInsert( List_t * const pxList,  ListItem_t * const pxNewListItem )；
UBaseType_t uxListRemove( ListItem_t * const pxItemToRemove )；