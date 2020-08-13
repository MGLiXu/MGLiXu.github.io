# makefile  
 $@ 目标文件   
 $^ 所有的依赖文件    
 $< 第一个依赖文件   
 反斜杠（\）是换行符的意思  
## 1 例子  
[参考!](https://blog.csdn.net/weixin_38391755/article/details/80380786)  
```mk
edit : main.o kbd.o command.o display.o \
    insert.o search.o files.o utils.o
  cc -o edit main.o kbd.o command.o display.o \
    insert.o search.o files.o utils.o

main.o : main.c defs.h
  cc -c main.c
kbd.o : kbd.c defs.h command.h
  cc -c kbd.c
command.o : command.c defs.h command.h
  cc -c command.c
display.o : display.c defs.h buffer.h
  cc -c display.c
insert.o : insert.c defs.h buffer.h
  cc -c insert.c
search.o : search.c defs.h buffer.h
  cc -c search.c
files.o : files.c defs.h buffer.h command.h
  cc -c files.c
utils.o : utils.c defs.h
  cc -c utils.c
clean :
  rm edit main.o kbd.o command.o display.o \
          insert.o search.o files.o utils.o
```
在定义好依赖关系后，后续的那一行定义了如何生成目标文件的操作系统命令，___一定要以一个Tab键作为开头___。记住，make并不管命令是怎么工作的，他只管执行所定义的命令。
make会比较targets文件和prerequisites文件的修改日期，如果prerequisites文件的日期要比targets文件的日期要新，或者target不存在的话，那么，make就会执行后续
定义的命令。

## 2 变量的使用
为了新加入文件时候可以更加方便，声明一个变量，叫objects, OBJECTS, objs, OBJS, obj, 或是OBJ等等，如：  
```mk
objects = main.o kbd.o command.o display.o \
  insert.osearch.o files.o utils.o 
edit : $(objects)
  cc -o edit $(objects)
#.....
#.....
#一些其他的依赖
.PHONY : clean
clean :
  -rm edit $(objects)
```
这里的```objects```替换了定义的.o文件,```.PHONY```表示clean是个伪目标文件。```-```表示也许某些文件出现问题，但不要管，继续做后面的事。

## 3 CFLAGS与LDFLAGS  
[参考！](https://blog.csdn.net/zhaoyun_zzz/article/details/82466031)
### 3.1 GCC编译选项CFLAGS参数:
|  选项   | 说明  |
|  ----  | ----  |
| -c  | 用于把源码文件编译成 .o 对象文件,不进行链接过程 |
| -o  | 用于连接生成可执行文件，在其后可以指定输出文件的名称 |
| -Wall | 生成常见的所有告警信息，且停止编译 |
|  -Idir  | 用于把新目录添加到include路径上，可以使用相对和绝对路径，“-I.”、“-I./include”、“-I/opt/include”  |
|  ...  | ...  |

### 3.2 GCC链接选项LDFLAGS参数
|  选项   | 说明  |
|  ----  | ----  |
|  -llibrary   | 链接时在标准搜索目录中寻找库文件，搜索名为liblibrary.a 或 liblibrary.so  |
|  -Ldir  | 用于把新目录添加到库搜索路径上，可以使用相对和绝对路径，“-L.”、“-L./include”、“-L/opt/include”  |
|  -Wl,option   | 把选项 option 传递给连接器，如果 option 中含有逗号,就在逗号处分割成多个选项  |
|  -static  | 使用静态库链接生成目标文件，避免使用共享库，生成目标文件会比使用动态链接库大  |  

有时候LDFLAGS指定-L虽然能让链接器找到库进行链接，但是运行时链接器却找不到这个库，如果要让软件运行时库文件的路径也得到扩展，那么我们需要增加这两个库给”-Wl,R”，如```LDFLAGS = -L/var/xxx/lib -L/opt/mysql/lib -Wl,R/var/xxx/lib -Wl,R/opt/mysql/lib```

### 3.3 例子
```mk
CFLAGS = -g -Wall -I./include -I./include/tinyalsa -Wl,--whole-archive -lpthread -Wl,--no-whole-archive -lc
LDFLAGS = -L./lib
ALL:
	$(CC) $(CFLAGS) $(LDFLAGS) main.c gfifo.c queue.c usbmonitor.c socket_rcv_360_server.c ./lib/srs_librtmp.a ./lib/libcrypto.a ./lib/libssl.a ./lib/libtinyalsa.a -o media_record -static -ldl -lstdc++ -lm -lpthread
clean:
	rm media_record *.raw *.mp4 *.wav -rf
 
下面是解析
CFLAGS = -g -Wall -I./include -I./include/tinyalsa -Wl,--whole-archive -lpthread -Wl,--no-whole-archive -lc
-g：生成调试信息
-Wall：输出全部的告警信息
-I./include：添加头文件搜索目录./include
-I./include/tinyalsa：添加头文件搜索目录./include/tinyalsa
-Wl,--whole-archive -lpthread：将libpthread.so及以后的库中的所有符号都链接进可执行目标文件
-Wl,--no-whole-archive：关闭之前的-Wl,--whole-archive -lpthread 选项
LDFLAGS = -L./lib，将makefile当前目录下的、lib文件夹添加到库文件搜索目录

$(CC)：makefile隐含变量，默认选择gcc编译器
$(CFLAGS)：引用之前定义的CFLAGS变量，即编译选项参数
$(LDFLAGS)：引用之前定义的LDFLAGS变量，即链接参数

$(CC) $(CFLAGS) $(LDFLAGS) main.c gfifo.c queue.c usbmonitor.c socket_rcv_360_server.c ./lib/srs_librtmp.a ./lib/libcrypto.a ./lib/libssl.a ./lib/libtinyalsa.a -o media_record -static -ldl -lstdc++ -lm -lpthread
使用定的编译器、编译选项参数、链接选项参数，编译.c文件，并使用静态方式链接制定的库文件，以及编译器目录下的libdl.a、libstdc++.a、libm.a、libpthread.a库文件生成 media_record 可执行目标文件。
```
简单地说，LDFLAGS是告诉链接器从哪里寻找库文件，而LIBS是告诉链接器要链接哪些库文件。  
还可以看看[这个](https://blog.csdn.net/Qiuoooooo/article/details/79736954)简单的例子，关于.so的编译的  
