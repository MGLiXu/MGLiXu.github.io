# makefile
[参考!](https://blog.csdn.net/weixin_38391755/article/details/80380786)  
 $@ 目标文件  
 $^ 所有的依赖文件  
 $< 第一个依赖文件  
 反斜杠（\）是换行符的意思  
## 1 例子
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









