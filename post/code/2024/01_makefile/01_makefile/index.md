# vscode调试C/C++代码的方法。这里是使用Makefile的多文件方式【转】


### 前言

[vscode调试](https://so.csdn.net/so/search?q=vscode%E8%B0%83%E8%AF%95&spm=1001.2101.3001.7020)C/C++教程很多，操作麻烦，这里试图找到一个最简单的使用vscode调试C/C++代码的方法。这里是使用Makefile的多文件方式。

### 测试文件

```bash
tree 
.
├── func.c
├── func.h
├── main.c
└── Makefile
```

**fun.c**

```C
#include <stdio.h>
#include "func.h"

int foo1(int a)
{
    int b = ++a;
    printf("This is foo1 %d\n",b);
}
```

**fun.h**

```C
int foo1(int a);
```

**main.c**

```C
#include <stdio.h>
#include "func.h"

int main()
{
    int a = 1;
    printf("Hello, I am coming %d\n", a);

    foo1(a);
    return 0;
}
```

**Makefile**

```makefile
CC = gcc
CFLAGS = -g
LDFLAGS = 

TARGET = test
SRCS = $(wildcard *.c)

OBJS = $(SRCS:.c=.o)

all: $(TARGET)

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) $(OBJS) -o $(TARGET) $(LDFLAGS)

%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -f $(OBJS) $(TARGET)
```

### 关键配置文件

在.vscode路径下

**lauch.json**

```json
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) 启动",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/test",  //编译后可执行文件路径
            "args": [],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                },
                {
                    "description": "将反汇编风格设置为 Intel",
                    "text": "-gdb-set disassembly-flavor intel",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask" :"C/C++: gcc 生成活动文件", // 与task中label一致
            "miDebuggerPath": "/usr/bin/gdb"
        }
    ]
}
```

**tasks.json**

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: gcc 生成活动文件",
            "command": "make",               // 使用Mafile编译
            "args": [
                //"-fdiagnostics-color=always",
                //"-g",
                //"${file}",
                //"-o",
                //"${fileDirname}/${fileBasenameNoExtension}"
            ],
            "options": {
                "cwd": "${workspaceFolder}"  //项目所在目录
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "调试器生成的任务。"
        }
    ],
    "version": "2.0.0"
}
```

最终调试时文件

```bash
/ws/example$ tree -a
.
├── func.c
├── func.h
├── func.o
├── main.c
├── main.o
├── Makefile
├── test
└── .vscode
    ├── launch.json
    ├── settings.json
    └── tasks.json

```

### 成功调试

![在这里插入图片描述](./assets/ada05d3f6c324b688a51972fbd2f118b.png#pic_center)
