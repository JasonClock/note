📚 第一部分：CMake 基础

CMake 是什么？

CMake 不是编译器，它是一个构建系统生成器。

你写 CMakeLists.txt → cmake 生成 Makefile → make 真正编译

🧱 核心概念：Target

CMake 万物皆 Target（构建目标），不是围绕文件写的。

|Target 类型|产物|命令|
|---|---|---|
|可执行文件|.elf / .exe|add_executable()|
|静态库|.a|add_library(xxx STATIC ...)|
|动态库|.so|add_library(xxx SHARED ...)|

📝 CMakeLists.txt 最小模板
```
# 1. 最低版本（必须第一行） cmake_minimum_required(VERSION 3.16) # 2. 项目名 + 语言 project(MyProject C ASM) # 3. 创建可执行 target add_executable(${PROJECT_NAME} Core/Src/main.c Core/Src/gpio.c ) # 4. 头文件路径 target_include_directories(${PROJECT_NAME} PRIVATE Core/Inc ) # 5. 编译选项 target_compile_options(${PROJECT_NAME} PRIVATE -mcpu=cortex-m4 -mthumb )
```

🔑 PRIVATE / PUBLIC / INTERFACE

控制属性（头文件路径、编译选项、链接库）的传递方向：

| |你的库自身| 链接你的库的 target|
| :---| :---- | :---|
|PRIVATE |✅ |❌|
|PUBLIC |✅ |✅|
|INTERFACE |❌ |✅|

  
说白了：  

- PRIVATE — 自己用，不传给别人

- PUBLIC — 自己用，也传给别人

- INTERFACE — 自己不直接用，但要求链接你的人用

⚙️ 常用命令速查

|命令|作用|
|---|---|
|add_executable(name src1 src2...)|添加可执行 target|
|add_library(name STATIC src...)|添加库 target|
|target_include_directories(tgt SCOPE dirs)|头文件搜索路径|
|target_compile_definitions(tgt SCOPE DEFS)|宏定义 -DXXX|
|target_compile_options(tgt SCOPE opts)|编译选项|
|target_link_libraries(tgt SCOPE libs)|链接其他 target|
|target_sources(tgt SCOPE srcs)|追加源文件|
|add_subdirectory(dir)|引入子目录|
|set(VAR value)|设变量|
|list(APPEND VAR val)|追加到列表|

💡 核心心法

CMake 是声明式的——你描述"这个 target 需要什么"，而不是"先做A再做B"。

面向 Target 写，不是面向文件写。
好，接着来！第二部分：STM32CubeMX 生成的 CMakeLists.txt 逐层拆解 ⚡

🗂️ CubeMX 生成的 CMake 项目结构
典型的 STM32CubeMX CMake 项目长这样：
project/
├── CMakeLists.txt ← 顶层构建文件
├── cmake/
│ ├── gcc-arm-none-eabi.cmake ← 工具链文件（核心！）
│ └── stm32cubemx/ ← CubeMX 自动生成的辅助脚本
├── Core/
│ ├── Inc/ ← 你的头文件
│ ├── Src/ ← 你的源文件（main.c, gpio.c...）
│ └── Startup/ ← 启动文件 startup_stm32xxx.s
├── Drivers/
│ ├── CMSIS/
│ └── STM32F4xx_HAL_Driver/
├── Middleware/ ← 如果有 FreeRTOS 等
└── build/ ← 构建输出目录（自己建）

🔧 首先看工具链文件 gcc-arm-none-eabi.cmake

这是我们之前说的"交叉编译"的秘诀——告诉 CMake 用 ARM 的 gcc，而不是你电脑的 x86 gcc：

1. 指定目标系统（告诉 CMake 这不是在编译本地程序）
set(CMAKE_SYSTEM_NAME Generic)

set(CMAKE_SYSTEM_PROCESSOR ARM)

2. 指定编译器位置（arm-none-eabi- 前缀）

set(CMAKE_C_COMPILER arm-none-eabi-gcc)

set(CMAKE_CXX_COMPILER arm-none-eabi-g++)

set(CMAKE_ASM_COMPILER arm-none-eabi-as)

set(CMAKE_OBJCOPY arm-none-eabi-objcopy)

set(CMAKE_SIZE arm-none-eabi-size)

3. 关键！禁止 CMake 做"链接测试"

因为交叉编译出来的 elf 在你电脑上跑不了 → 测试会失败

set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

📋 顶层 CMakeLists.txt 逐段拆解
```
# ─── 第 1 块：基本声明 ─── 
cmake_minimum_required(VERSION 3.22) 
# ^^^ 必须第一行，声明 CMake 最低版本 # 

─── 第 2 块：工具链 ─── 
set(CMAKE_TOOLCHAIN_FILE cmake/gcc-arm-none-eabi.cmake)
# ^^^ 在 project() 之前设置！ 
# 告诉 CMake："我要交叉编译到 ARM" # 

─── 第 3 块：项目信息 ─── 
project(STM32F407_Project C CXX ASM) 
#        ^^^^^^^^^^^^^^^^ ^^^^^^^^ 
# 项目名（也变成变量 启用的语言 # ${PROJECT_NAME}）

# ─── 第 4 块：硬件定义 ───
set(CMAKE_C_STANDARD 11) # C 标准 
set(CMAKE_CXX_STANDARD 17) # C++ 标准 

# 芯片型号 → 作为全局宏传递给编译器 
add_definitions(-DSTM32F407xx) 
# ^^^^^^^^^^^^^ 这个宏 HAL 库会用到！

# ─── 第 5 块：源文件收集 ─── 
# CubeMX 喜欢用 set() 把文件路径收集到变量里 
set(CORE_SOURCES 
Core/Src/main.c 
Core/Src/gpio.c 
Core/Src/usart.c 
Core/Src/stm32f4xx_it.c 
Core/Src/system_stm32f4xx.c )

set(STARTUP_SOURCES 
Core/Startup/startup_stm32f407xx.s ) 

set(HAL_SOURCES 
Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal.c 
Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_gpio.c 
Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc.c
# ... 更多 HAL 文件 )

# ─── 第 6 块：创建 Target ─── 
add_executable(
${PROJECT_NAME} 
${CORE_SOURCES} 
${STARTUP_SOURCES}
${HAL_SOURCES} 
) 

# ─── 第 7 块：头文件路径 ───
target_include_directories(${PROJECT_NAME} PRIVATE 
Core/Inc Drivers/STM32F4xx_HAL_Driver/Inc
Drivers/CMSIS/Include 
Drivers/CMSIS/Device/ST/STM32F4xx/Include )

# ─── 第 8 块：ARM 编译选项 ─── 
target_compile_options(${PROJECT_NAME} PRIVATE 
-mcpu=cortex-m4 # 目标 CPU 
-mthumb # Thumb # 指令集（ARM MCU 都用这个） 
-mfloat-abi=hard # 硬浮点ABI 
-mfpu=fpv4-sp-d16 # FPU 型号 
-Wall # 开启所有警告 
-fdata-sections # 每个数据变量独立 section 
-ffunction-sections # 每个函数独立 section 
) 
# ^^^ -fdata/function-sections 配合下面的 --gc-sections 
# 让链接器删掉没用的函数/变量，减小固件体积 # 

─── 第 9 块：链接选项 ─── 
target_link_options(${PROJECT_NAME} PRIVATE 
-T "${CMAKE_SOURCE_DIR}/STM32F407VGTx_FLASH.ld" # 链接脚本 
-mcpu=cortex-m4 
-mthumb 
-mfloat-abi=hard 
-mfpu=fpv4-sp-d16 
-specs=nano.specs # 用 newlib-nano（更小的 C 库） 
-specs=nosys.specs # 不提供系统调用 
-Wl,--gc-sections # 删掉未引用的 section 
-Wl,--print-memory-usage # 打印内存使用 ) 

# ─── 第 10 块：编译后生成 .hex / .bin ─── 
add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD 
    COMMAND ${CMAKE_OBJCOPY} -O ihex $<TARGET_FILE:${PROJECT_NAME}> ${CMAKE_BINARY_DIR}/${PROJECT_NAME}.hex 
    COMMAND ${CMAKE_OBJCOPY} -O binary $<TARGET_FILE:${PROJECT_NAME}> ${CMAKE_BINARY_DIR}/${PROJECT_NAME}.bin COMMENT "生成 .hex 和 .bin 文件" )
```
🧠 关键理解

1. $<TARGET_FILE:${PROJECT_NAME}> 是 Generator Expression（生成器表达式），在生成 Makefile 时才展开成实际的 elf 路径
    
2. -specs=nano.specs — MCU 的 flash/RAM 都很小，用 nano 版 C 库省空间

Agent: main | Model: deepseek-v4-pro | Provider: deepseek

3. 链接脚本 .ld 文件 — 定义了 flash 和 RAM 的起始地址、大小，这个 CubeMX 会根据你选的芯片自动生成
    
4. add_custom_command(TARGET ... POST_BUILD ...) — 编译完后自动跑的命令，用来生成烧录用的 hex/bin
    


继续！第三部分：摆脱 CubeMX，自己搭 CMake 工具链 ⚡

🎯 为什么要自己写？

CubeMX 生成的 CMakeLists.txt 虽然能用，但有几个痛点：  

- 每次在 CubeMX 里改外设配置 → 覆盖你的改动
    

- 所有文件写在一个大文件里，项目大了很难维护
    

- 加个新文件得手动改 CMakeLists.txt
    

🏗️ 自己搭的目标结构

my-stm32-project/
├── CMakeLists.txt ← 顶层：只管 project() + subdirectory
├── cmake/
│ └── arm-toolchain.cmake ← 工具链文件
├── src/
│ ├── CMakeLists.txt ← 收集源文件，创建 target
│ ├── main.c
│ ├── gpio.c
│ └── ...
├── lib/
│ ├── hal/
│ │ └── CMakeLists.txt ← HAL 库单独做成一个 library target
│ └── cmsis/
│ └── CMakeLists.txt ← CMSIS 也做成一个 library target
├── ld/
│ └── STM32F407VGTx_FLASH.ld ← 链接脚本
└── build/
└── ... ← 构建输出

📝 逐文件写给你看

1️⃣ 顶层 CMakeLists.txt

cmake_minimum_required(VERSION 3.22) # 项目名，一会用 ${PROJECT_NAME} 引用 project(f407_project C ASM) # 芯片型号 → 全局宏 add_compile_definitions(STM32F407xx USE_HAL_DRIVER) # 标准 set(CMAKE_C_STANDARD 11) # 引入子目录（每个子目录有自己的 CMakeLists.txt） add_subdirectory(lib/cmsis) add_subdirectory(lib/hal) add_subdirectory(src)

2️⃣ cmake/arm-toolchain.cmake

set(CMAKE_SYSTEM_NAME Generic)

set(CMAKE_SYSTEM_PROCESSOR ARM)

# 如果 arm-none-eabi-gcc 不在 PATH 里，写完整路径

set(CMAKE_C_COMPILER arm-none-eabi-gcc)

set(CMAKE_CXX_COMPILER arm-none-eabi-g++)

set(CMAKE_ASM_COMPILER arm-none-eabi-gcc)

set(CMAKE_OBJCOPY arm-none-eabi-objcopy)

set(CMAKE_SIZE arm-none-eabi-size)

set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

# 这里不加编译选项！选项放 target 级别，别放全局

⚠️ 为什么编译选项不放工具链文件？ 因为工具链文件是全局的。如果你以后一个项目里要编 bootloader 和 app 两个 target，它们可能用不同的 -mcpu（比如 M0 bootloader + M4 app），全局设了反而麻烦。

3️⃣ lib/cmsis/CMakeLists.txt — CMSIS 做成库

# INTERFACE 库：只有头文件，不需要编译 .c

# 但别人 include 它的头文件时需要知道路径

add_library(cmsis INTERFACE)

target_include_directories(cmsis INTERFACE

Drivers/CMSIS/Include

Drivers/CMSIS/Device/ST/STM32F4xx/Include

)

# 把 system_stm32f4xx.c 也放这里（静态库更合适时用 STATIC）

# add_library(cmsis STATIC

# Drivers/CMSIS/Device/ST/STM32F4xx/Source/Templates/system_stm32f4xx.c

# )

# target_include_directories(cmsis PUBLIC

# Drivers/CMSIS/Include

# Drivers/CMSIS/Device/ST/STM32F4xx/Include

# )

4️⃣ lib/hal/CMakeLists.txt — HAL 做成静态库

add_library(hal STATIC

Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal.c

Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_cortex.c

Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_gpio.c

Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_rcc.c

Drivers/STM32F4xx_HAL_Driver/Src/stm32f4xx_hal_uart.c

# 用到什么加什么，别全加上

)

target_link_libraries(hal PUBLIC cmsis)

# ^^^ PUBLIC：谁链接 hal，就自动也有了 cmsis 的头文件路径

target_include_directories(hal PUBLIC

Drivers/STM32F4xx_HAL_Driver/Inc

)

# HAL 库需要知道芯片型号宏

target_compile_definitions(hal PUBLIC STM32F407xx USE_HAL_DRIVER)

5️⃣ src/CMakeLists.txt — 你的应用代码

Agent: main | Model: deepseek-v4-pro | Provider: deepseek

# ★ 技巧：用 GLOB 自动收集源文件，不用一个一个写！ file(GLOB_RECURSE APP_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.c ${CMAKE_CURRENT_SOURCE_DIR}/*.s ) # ⚠️ GLOB_RECURSE 的坑：新增文件后需要重新 cmake # 开发阶段很方便，发布前建议显式列出 add_executable(${PROJECT_NAME} ${APP_SOURCES} ) # ─── 链接 HAL + CMSIS ─── target_link_libraries(${PROJECT_NAME} PRIVATE hal cmsis ) # ─── 头文件 ─── target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR} ) # ─── ARM 编译选项（只给这个 target） ─── target_compile_options(${PROJECT_NAME} PRIVATE -mcpu=cortex-m4 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -Wall -Wextra -fdata-sections -ffunction-sections -Os # 优化体积 ) # ─── 链接选项 ─── target_link_options(${PROJECT_NAME} PRIVATE -T ${CMAKE_SOURCE_DIR}/ld/STM32F407VGTx_FLASH.ld -mcpu=cortex-m4 -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -specs=nano.specs -specs=nosys.specs -Wl,--gc-sections -Wl,--print-memory-usage ) # ─── 生成 hex/bin ─── add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD COMMAND ${CMAKE_OBJCOPY} -O ihex $<TARGET_FILE:${PROJECT_NAME}> ${CMAKE_BINARY_DIR}/${PROJECT_NAME}.hex COMMAND ${CMAKE_OBJCOPY} -O binary $<TARGET_FILE:${PROJECT_NAME}> ${CMAKE_BINARY_DIR}/${PROJECT_NAME}.bin COMMENT "生成烧录文件" )

6️⃣ 构建命令

_# 在项目根目录：_

mkdir build && cd build

cmake .. -DCMAKE_TOOLCHAIN_FILE=../cmake/arm-toolchain.cmake

make -j$(nproc)

_# 或者用 CMakePresets.json 一键：_

cmake --preset arm-debug

cmake --build build

🧠 设计要点总结

|原则|说明|
|---|---|
|一个 subsystem 一个 target|HAL、CMSIS、FreeRTOS 各成一个 library target|
|用 PUBLIC 传递依赖|hal 依赖 cmsis → target_link_libraries(hal PUBLIC cmsis)，app 只需链接 hal 就自动有了 cmsis|
|编译选项放 target，不放全局|灵活、不出意外|
|src 层用 GLOB_RECURSE|开发方便。或者在源文件目录里显式 file(GLOB ...)|
|工具链文件只管"在哪里编译"|不管"怎么编译"|

🆚 对比 CubeMX 版本

|--|CubeMX 生成|自己搭|
|---|---|---|
|文件组织|一个巨大的 CMakeLists.txt|分层，每个目录有 CMakeLists.txt|
|HAL 管理|所有 HAL 源文件列在一处|独立 library target，用到才加|
|修改后|CubeMX 重新生成会覆盖|完全自由|
|多 target|麻烦|天然支持（bootloader + app）|
|新增源文件|手动改 CMakeLists.txt|GLOB_RECURSE 自动收录|