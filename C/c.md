# extern "C"  
函数在c和c++中编译后名字是不同的，在c++中调用c的函数会出问题。
比如add(int num1, int num2)：
- c：函数编译后的名字是_add
- c++： 名字是add_intint之类的，c++支持重载，所以名字不一样
 
c函数：
```c
//写法一
#ifdef __cplusplus
extern "C"{
#endif
	int add(int num1, int num2);
#ifdef __cplusplus
};
#endif

//写法二
//extern "C" int add(int num1, int num2);
```

c++调用  
```std::cout<<add(4, 5)<<std::endl;```  
加入"extern C"后，在c++中add被编译为_add

# JNI  
1. 编写带有native声明的方法的java类
2. 使用 javac 命令编译所编写的java类
3. 使用 “ javah -jni java类名”  生成扩展名为h的头文件
4. 使用C/C++实现本地方法
5. 将C/C++编写的文件生成动态连接库
