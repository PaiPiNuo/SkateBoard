## gcc & g++

[TOC]

### 1.预处理，编译，汇编，链接

预处理命令：cpp

编译命令：cc1,cc1plus

汇编命令：as

链接命令：ld

我们常用的gcc/g++命令只不过调用了上述命令而已。

### 2. gcc & g++

#### 2.1 gcc是什么？

[https://www.zhihu.com/question/20940822]()

gcc 最开始的时候是 GNU C Compiler, 如你所知，就是一个c编译器。但是后来因为这个项目里边集成了更多其他不同语言的编译器，GCC就代表 the GNU Compiler Collection，所以表示一堆编译器的合集。 g++则是GCC的c++编译器。

```c++
g++ main.cc -o main.o
gcc main.cc -o main.o -lstdc++  // gcc命令不能自动和C＋＋程序使用的库联接
```

#### 2.2 gcc和ｇ++如何指定包含头文件,库文件？

| 参数 | 作用                               | 备注                                                         |
| ---- | ---------------------------------- | ------------------------------------------------------------ |
| -l   | 指定需要链接的库的名字，如-lstdc++ | 库的名字和库文件的名字不是完全相同，比如：-ltest,真实的库文件名字是：libtest.so(这是linux系统下的默认规则) |
| -L   | 指定需要搜索的库的路径             | linux默认的共享库搜索路径是：/lib, /usr/lib, /etc/ld.so.conf配置文件指定的路径（一般包含 /usr/local/lib）<br />LD_LIBRARY_PATH,LD_PRELOAD环境变量指定的路径 |
| -I   | 指定头文件的搜索路径               | /usr/include 是默认搜索头文件的路径                          |

#### 2.3 gcc与g++区别

- 文件后缀为.cpp 两者都认为是c++程序
- 文件后缀为.c，gcc认为是c程序，g++认为是c++程序
- 在编译阶段，g++会调用gcc,两者等价。在链接阶段，gcc不能自动链接C++库，所以需要手动指定-lstdc++

### 3. libc & glibc & glib

#### 3.1 区别

| **libc** | Linux中ANSI C函数库（math.h, stdio.h, stdlib.h）             |
| -------- | ------------------------------------------------------------ |
| glibc    | GNU C函数库。现在由于GNU C函数库更好用，已经成为linux 系统中的C标准库 |
| glib     | 一个工具库，与glibc,libc没有关系                             |

#### 3.2 glibc是什么？

glibc是Linux系统中最底层的API，几乎其他任何的运行库都依赖glibc。glibc最主要的作用就是对系统调用的封装。除了封装，glibc也会提供一些上层应用函数必要的功能，如string,malloc,stdlib。

#### 3.3 libc++ & libstdc++

两者都是C++标准库

- libc++是针对clang编译器重写的c++标准库
- libstdc++是针对gcc的。libstdc++与gcc是捆绑在一起的，即安装gcc时libstdc++也会自动装上
- 为什么glibc没有与gcc捆绑呢？
  - 相比glibc, libstdc++并不与内核打交道。对于系统级别事件，libstdc++首先会与glibc交互，才能和内核通信。相比glibc,libstdc++没那么基础。

### 4. 共享库的简要介绍

#### 4.1 共享库的系统路径的说明

| 共享库的系统路径 | 作用                                                         |
| ---------------- | ------------------------------------------------------------ |
| /lib             | 存放系统最关键和基础的共享库，如：动态链接器，C语言运行库，数学库，还有系统启动时需要的库。（这些库主要是/bin, /sbin下程序所要用到的库） |
| /usr/lib         | 存放非系统运行时需要的库，主要是开发时可能会用到的库         |
| /usr/local/lib   | 主要存放第三方应用程序的库。如如果在系统中安装了python解释器，那么与他相关的共享库可能会被放到/usr/local/lib/python,他的可执行文件可能被放到/usr/local/bin下 |
| 总结             | /lib,/usr/lib 存放常用的成熟的，系统本身所需要的库<br />/usr/local/lib存放第三方程序的库 |

#### 4.2 共享库的查找过程

动态链接器默认会在下列路径中查找共享库：

- **LD_LIBRARY_PATH**指定的路径

- /lib

- /usr/lib,

- /etc/ld.so.conf配置文件指定的路径

```
ld.so.conf指定的配置内容可能如：
/usr/local/lib
/lib/i486-linux-gnu
/usr/lib/i486-linux-gnu
```

- 每次动态链接器都需要遍历目录的话会非常耗时，因此有命令**ldconfig**。

- **ldconfig**作用是为共享库目录下的各个共享库创建，删除或更新相应的SO-NAME，这样每个共享库的SO-NAME就能指向正确的共享库文件。并且，将这些SO-NAME收集起来，集中存放到 /etc/ld.so.cache文件里面，建立一个SO-NAME的缓存。当动态链接器需要查找共享库时，可以直接在ld.so.cache里面查找。

- 如果动态链接器在/etc/ld.so.cache中没有找到共享库，还会遍历/lib和/usr/lib这两个目录。

- 所以，理论上我们在系统指定的共享库目录下添加，删除，更新了任何一个共享库，或者更改了/etc/ld.so.conf的配置，都应该运行ldconfig这个程序，以便调整SO-NAME和/etc/ld.so.cache.很多软件包的安装程序在往系统路径里安装共享库后都会调用ldconfig。

- **LD_LIBRARY_PATH**的优先级较高，它的作用本质上同gcc -L参数是一样的。

- **LD_PRELOAD**

- **LD_DEBUG**

  





