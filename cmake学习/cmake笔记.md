# cmake基础
用于管理C/C++项目的编译过程

CMakeLists.txt ：Cmake核心配置文件

## 基本语法 
语法结构：命令名称(参数列表)

指定最低版本:
``` cmake
cmake_minmum_required(VERSION <xxx>)
```

定义项目名称和语言project(<项目名> <语言>)
``` cmake
project(MyProject C)
//调用 project() 后会自动设置 PROJECT_NAME 等变量
```


