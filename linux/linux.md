 # 1 ldd  
 编译出xxx.so库后， 可以通过```ldd -r xxx.so```查看当前库的动态库依赖关系,提前查出有undefined symbol的项

 # 2 test
  test命令是shell环境中测试条件表达式的实用工具。
```
test File1 –ef File2    两个文件是否为同一个文件，可用于硬连接。主要判断两个文件是否指向同一个inode。  
test File1 –nt File2    判断文件1是否比文件2新  
test File1 –ot File2    判断文件1比是否文件2旧  
test –b file   #文件是否块设备文件  
test –c File   #文件并且是字符设备文件  
test –d File   #文件并且是目录  
test –r File   #文件是否可读  
test –s File   #文件是否是非空白文件  
...
```
可以判断是否有文件夹，然后新建一个```test -d bin/Release || mkdir -p bin/Release```-p可以递归的创建目录，即两层的目录，mkdir不行。  

# 3 || && ()
__command1 && command2__  
&&左边的command1执行成功(返回0表示成功)，&&右边的command2才能被执行。  

__command1 || command2__  
如果||左边的command1执行失败(返回1表示失败)，就执行||右边的command2。  

__(command1；command2；command3;...)__  
如果想执行几个命令，则需要用命令分隔符分号隔开每个命令，并使用圆括号()把所有命令组合起来。结合||和&&可以实现复杂的功能。  
