# 1 extern "C"  
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

# 2 JNI  
[参考！](https://blog.csdn.net/hui12581/article/details/44832651)
## 2.1 大致流程
1. 编写带有native声明的方法的java类
2. 使用 javac 命令编译所编写的java类
3. 使用 “ javah -jni java类名”  生成扩展名为h的头文件
4. 使用C/C++实现本地方法
5. 将C/C++编写的文件生成动态连接库
## 2.2 一些例子
- 传递数组
```java
//java
public native int[] intMethod(); 
```

```c++
//c++
 JNIEXPORT jintArray JNICALL Java_Sample1_intMethod(JNIEnv *env, jobject obj)
 {
	int i = 1;
	jintArray  array;//定义数组对象
	array = (*env)-> NewIntArray(env, 10);
	for(; i<= 10; i++)
		(*env)->SetIntArrayRegion(env, array, i-1, 1, &i);

	/* 获取数组对象的元素个数 */
	int len = (*env)->GetArrayLength(env, array);

	/* 获取数组中的所有元素 */
	jint* elems = (*env)-> GetIntArrayElements(env, array, 0);

	for(i=0; i<len; i++)
		printf("ELEMENT %d IS %d\n", i, elems[i]); 

	return array;
}
```
- 传递字符串数组  
	第5、6行是我们需要特别关注的地方：JNI框架并 没有定义专门的字符串数组，而是使用jobjectArray——对象数组，对象数组的基类是jclass，jclass是JNI框架内特有的类型，相当 于Java语言中的Class类型。在本例程中，通过FindClass()函数在JNI上下文中获取到java.lang.String的类型 （Class），并将其赋予jclass变量。  
在例程中我们定义了一个长度为5的对象数组texts，并在程序的第13行向其中循环放入预先定义好的sa数组中的字符串，当然前置条件是使用NewStringUTF()函数将C语言的字符串转换为jstring类型。
本例程的另一个关注点是C程序向Java程序传递的中文字符，在Java程序中能否正常显 示的问题。
```java
//java
public native String[] stringMethod(String text);
```
```c++
//c++
JNIEXPORT jobjectArray JNICALL Java_Sample2_stringMethod
(JNIEnv *env, jobject obj, jstring string)
{   
	jclass objClass = (*env)->FindClass(env, "java/lang/String");
	jobjectArray texts= (*env)->NewObjectArray(env, (jsize)ARRAY_LENGTH, objClass, 0);
	jstring jstr; 
	char* sa[] = { "Hello,", "world!", "JNI", "哈", "哈" };
	int i=0;
	for(;i<ARRAY_LENGTH;i++)
	{
		jstr = (*env)->NewStringUTF( env, sa[i] );
		(*env)->SetObjectArrayElement(env, texts, i, jstr);//必须放入jstring
	} 
	return texts;
}
```
- 传递对象数组
```java
//java
public native MailInfo[] objectMethod(String text);
```
```c++
//c++
JNIEXPORT jobjectArray JNICALL Java_Sample3_objectMethod(
JNIEnv *env, jobject obj, jstring string)
{  
	jclass objClass = (*env)->FindClass(env, "java/lang/Object");
	jobjectArray mails= (*env)->NewObjectArray(env, 
	(jsize)ARRAY_LENGTH, objClass, 0);
	jclass  objectClass = (*env)->FindClass(env, "MailInfo");
	jfieldID topicFieldId = (*env)->GetFieldID(env, objectClass,"topic", "Ljava/lang/String;");
	int i=0;
	for(;i<ARRAY_LENGTH;i++)
	{
		(*env)->SetObjectField(env, obj, topicFieldId, string);
		(*env)->SetObjectArrayElement(env, mails, i, obj);
	}
	return mails;
}
```

# 3 GCC Makefile Make CMakelists CMake关系
[参考！](https://www.cnblogs.com/cv-pr/p/6206921.html)














