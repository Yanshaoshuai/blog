---
title: Linux C 基础(三) 工具和环境
description: Linux C 基础(三) 工具和环境
date: 2022-05-22
tags:
- "C/C++"
---
<!--more-->
### 安装编译工具

输入gcc -v和g++ -v查看有没有安装C/C++编译工具，没有的话按照提示安装一下。

### 安装CMake

到[cmake官网]()找到你的机器支持的包,下载压缩包,或者使用如下命令下载:

```sh
wget https://github.com/Kitware/CMake/releases/download/v3.22.4/cmake-3.22.4-linux-x86_64.tar.gz
tar -xzvf cmake-3.22.4-linux-x86_64.tar.gz
```

把cmake配置到环境变量:

```sh
vim /etc/profile
# 在最后加如下语句
export PATH=/opt/cmake-3.22.4-linux-x86_64/bin:$PATH
#保存退出后 source一下
source /etc/profile
```

输入cmake --version检查是否配置成功。

### 安装 Ninja

```sh
apt-get install -y ninja-build
```

### 安装GDB

输入gdb -v检查是否已经安装gdb，如果没有按照提示安装一下即可。

### 安装配置VSCode

到VSCode官网下载dep包，指向如下命令安装:

```sh
dpkg -i code_1.67.2-1652812855_amd64.deb
```

下载如下插件:

1.C/C++

2.CMake

其它不是必须的。

**项目运行配置**

`CMake项目为例`

> 注意VSCode启动项目的顺序是先找到launch.json下的配置项,然后根据其preLaunchTask字段值去tasks.json下面找要执行的task，所以要创建launch.json和tasks.json,然后把要运行的task的label字段复制到launch.json的对应配置项的preLaunchTask字段中。

> F5启动时顺序:
>
> =>根据launch.json中的preLaunchTask找到tasks中的task
>
> =>按照task中定义的dependsOrder执行dependsOn中依赖的task生成可执行文件
>
> =>启动GDB调试程序调试进行调试(Debug版本)

1. 在.vscode文件夹下建立tasks.json，内容如下:

```json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "options": {
        "cwd": "${workspaceFolder}/build"
    },
    "tasks": [
        {
            "type": "shell",
            "label": "delete-old-build",
            "command": "clear && rm -rf  ${workspaceFolder}/build/*",
            "args": []
        },
        {
            "type": "shell",
            "label": "cmake-make",
            "command": "cmake",
            "args": [
                ".."
            ]
        },
        {
            "label": "make",
            "args": [],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "command": "make"
        },
        {
            "type": "shell",
            "label": "cmake-ninja",
            "command": "cmake",
            "args": [
                "-G",
                "Ninja",
                ".."
            ]
        },
        {
            "label": "ninja",
            "args": [],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "command": "ninja"
        },
        {
            "label": "Build-make",
            "dependsOrder": "sequence", //按列出的顺序输出依赖项
            "dependsOn": [
                "delete-old-build",
                "cmake-make",
                "make"
            ]
        },
        {
            "label": "Build-ninja",
            "dependsOrder": "sequence", //按列出的顺序输出依赖项
            "dependsOn": [
                "delete-old-build",
                "cmake-ninja",
                "ninja"
            ]
        }
    ]
}
```



2. 在.vscode文件夹下建立launch.json，内容如下:

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "gdb debug",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/build/TEST_C_MAIN",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ],
            "targetArchitecture": "",
            "preLaunchTask" :"Build-make",//指定task名称
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```



我这里配置了两种install方式,make和ninja,需要修改intall方式只需修改preLaunchTask名字即可。

### 最终文件结构

```shell
root@yan-virtual-machine:/home/yan/code/c/CPP_DEMO# tree
.
├── build
├── c_cpp_mix
│   ├── run_c.c
│   ├── run_cpp.cpp
│   ├── test_mix_cpp.c
│   ├── test_mix_cpp.cpp
│   └── test_mix_cpp.h
├── CMakeLists.txt
├── README.md
├── run_app.c
└── test_c
    ├── c_base_no_ptr.c
    ├── c_base_no_ptr.h
    ├── extra_func.c
    ├── file
    │   ├── test_read.txt
    │   ├── test_write2.txt
    │   └── test_write.txt
    ├── keyword_test.c
    ├── test_base.c
    ├── test_dive.c
    ├── test_file.c
    └── test.h
```



最后贴上我的CMakeLists.txt:

```cmake
cmake_minimum_required(VERSION 3.21)
project(CPP_DEMO C CXX)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_C_STANDARD 11)

# 设置启动参数
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -Fno-toplevel-reorder")
# 设置编译类型
set(CMAKE_BUILD_TYPE    Debug)
# Release类型无法调试
# set(CMAKE_BUILD_TYPE    Release)
#忽略警告
add_definitions(-w)

# Add header file include directories
include_directories(./test_c ./c_cpp_mix)

# 设置编译包含的文件
file(GLOB_RECURSE TEST_C "test_c/*.c")
file(GLOB_RECURSE MIX_C_CALL_CPP_FILE  "c_cpp_mix/*.c" "c_cpp_mix/*.cpp" EXCEPT "c_cpp_mix/run_cpp.cpp")
file(GLOB_RECURSE MIX_CPP_CALL_C_FILE  "c_cpp_mix/*.c" "c_cpp_mix/*.cpp" EXCEPT "c_cpp_mix/run_c.c")

# 混合编程 排除c++ main() 所在文件
list(REMOVE_ITEM  MIX_C_CALL_CPP_FILE "${CMAKE_CURRENT_SOURCE_DIR}/c_cpp_mix/run_cpp.cpp")
message(******** MIX_C_CALL_CPP_FILE exclude ==> "${CMAKE_CURRENT_SOURCE_DIR}/c_cpp_mix/run_cpp.cpp")
# 排除c main() 所在文件
list(REMOVE_ITEM  MIX_CPP_CALL_C_FILE "${CMAKE_CURRENT_SOURCE_DIR}/c_cpp_mix/run_c.c")
message(******** MIX_CPP_CALL_C_FILE exclude ==> "${CMAKE_CURRENT_SOURCE_DIR}/c_cpp_mix/run_c.c")

message(******** MIX_C_CALL_CPP_FILE ${MIX_C_CALL_CPP_FILE})
message(******** MIX_CPP_CALL_C_FILE ${MIX_CPP_CALL_C_FILE})

add_executable(TEST_C_MAIN  run_app.c ${TEST_C})
add_executable(MIX_C_CALL_CPP   c_cpp_mix/run_c.c ${MIX_C_CALL_CPP_FILE})
add_executable(MIX_CPP_CALL_C   c_cpp_mix/run_cpp.cpp ${MIX_CPP_CALL_C_FILE})
```



[项目地址](https://github.com/Yanshaoshuai/CPP_DEMO)

