# 实训三：RISC-V交叉编译

## 在非RISC-V平台上编译RISC-V程序

推荐使用 Ubuntu 20.04或Ubunt22.04，其中Ubuntu 22.04 是 Ubuntu 长期稳定发行版，在这两个环境下安装运行环境更简单。

目前在 Ubuntu 20.04 环境下我们可以直接使用官方提供的 GNU工具链和 QEMU 模拟器，执行如下命令在线安装即可开始试验：

```shell
$ sudo apt update
$ sudo apt install build-essential gcc make perl dkms git gcc-riscv64-linux-gnu gdb-multiarch qemu-user
```

1. cd到代码目录，此处以用户“~/code”进行举例。

   ```shell
   $ cd ~/code
   ```

2. 创建1个函数文件hello.c。

   ```shell
   $ vi hello.c
   ```

   hello.c代码内容示例：

   ```c
   #include <stdio.h>
   int main()
   {
           printf("Hello, world!\n");
           return 0;
   }
   ```

3. 交叉编译出RISC-V平台可运行的二进制文件

   ```shell
   $ riscv64-linux-gnu-gcc  hello.c -o hello.out
   ```

4. 查看hello.out文件的类型

   ```shell
   $ file hello.out
   ```

5. 使用QEMU用户模式执行hello.out

   ```shell
   $ qemu-riscv64 -L /usr/riscv64-linux-gnu/ hello.out
   Hello, world!
   ```

## 使用Makefile实现编译的示例

1. cd到代码目录，此处以用户“~/code”进行举例。

   ```shell
   $ cd ~/code
   ```

2. 创建1个头文件hello.h和2个函数hello.c、main.c。

   ```shell
   $ vi hello.h
   $ vi hello.c
   $ vi main.c
   ```

   hello.h代码内容示例：

   ```c
   #pragma once
   #include <stdio.h>
   void hello();
   ```

   hello.c代码内容示例：

   ```c
   #include "hello.h"
   void hello()
   {
           int i=1;
           while(i<5)
           {
                   printf("The %dth say hello.\n", i);
                   i++;
           }
   }
   ```

   main.c代码内容示例：

   ```c
   #include "hello.h"
   #include <stdio.h>
   int main()
   {
           hello();
           return 0;
   }
   ```

3. 创建Makefile文件。

   ```shell
   $ vi Makefile
   ```

   Makefile文件内容示例：

   ```shell
   main:main.o hello.o
   	gcc -o main main.o hello.o
   main.o:main.c
   	gcc -c main.c
   hello.o:hello.c
   	gcc -c hello.c
   clean:
   	rm -f hello.o main.o main
   ```

4. 执行make命令。

   ```shell
   $ make
   ```

   命令执行后，会打印Makefile中执行的命令。如果不需要打印该信息，可以在执行make命令是加上参数-s。

   ```shell
   gcc -c main.c
   gcc -c hello.c
   gcc -o main main.o hello.o
   ```

5. 执行./main目标。

   ```shell
   $ ./main
   ```

   命令执行后，打印如下信息：

   ```shell
   The 1th say hello.
   The 2th say hello.
   The 3th say hello.
   The 4th say hello.
   ```

