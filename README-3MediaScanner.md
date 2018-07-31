### Jni-MediaScanner分析
+ MediaScanner的Jni层代码在`android_media_MediaScanner.cpp`中；`native_init`函数位于`android.media`这个包中，它的全路径名应该是`android.media.MediaScanner.native_init`而Jni层函数的名字是`android_media_MediaScanner_native_init`因为在Native语言中，符号`.`有着特殊的含义，所以Jni层需要把Java函数名称中的`.`转换成`_`；
#### 注册Jni函数
+ Jni函数的注册方法实际上有两种：一、静态注册；二、动态注册；
##### 一、静态注册

|静态注册的整个流程|
|------|
|先编写Java代码，然后编译生成.class文件|
|使用Java的工具程序，`javah`如`javah -o output packagename.classname`这样它会生成一个叫`output.h`的Jni层的头文件，其中packagename.classname是Java代码编译后的class文件，而在生成的output.h文件里，声明了对应的Jni函数，只要实现里面的函数即可，这个头文件的名字一般都会使用packagename_class.h的样式，如MediaScanner对应的Jni层头文件就是android_media_MediaScanner.h|

+ 使用静态注册的方式生成的头文件的样式文件android_media_MediaScanner.h；

```
#include<jni.h>//必须包含这个头文件，否则编译通不过
#ifndef _Include_android_media_MediaScanner
#define _Include_android_media_MediaScanner
#ifdef _cplusplus
extern "C"{
#endif
       //略去一部分内容
       //native_init对应的Jni函数
       JNIEXPORT void JNICALL java_android_media_MediaScanner_native_1init(JNIEnv* , jclass);//java层函数名中如果有一个“_”转换成jni后就变成了“_1”
#ifdef _cplusplus
}
#endif
#endif
```

+ 使用静态注册的方式：当Java层调用native_init函数时，它会从对应的Jni库中寻找java_android_media_MediaScanner_native_1init函数，如果没有，就会报错，如果找到，则会为这个native_init和java_android_media_MediaScanner_native_1init建立一个关联关系，其实就是保存Jni层函数的函数指针，以后再调用native_init函数时，直接使用这个函数指针就可以了，这项工作是由虚拟机完成的；静态注册就是根据函数名来建立java函数和Jni函数之间的关联关系，而且要求Jni层函数的名字必须遵循特定的格式；

|静态注册的弊端|
|------|
|需要编译所有声明了native函数的java类，每个所生成的.class文件都得用javah生成一个头文件|
|javah生成的Jni函数名特别长，书写起来很不方便|
|初次调用native函数时要根据函数名搜索对应的Jni层函数来建立关联关系，这样会影响运行效率|

##### 二、动态注册
+ 在Jni技术中，用来记录这种一一对应关系的是一个叫`JNINativeMethod`的结构；
```
typedef struct{
       const char* name;//java中native函数的名字，不用携带包的路径，如native_init
       const char* signature;//java函数的签名信息，用字符串表示，是参数类型和返回值类型的组合
       void* fnPtr;//Jni层对应函数的函数指针，注意它是一个void*类型
}JNINativeMethod;
```

image1

image2

image3

image4

+ 其中jniRegisterNativeMethods是Android平台中为了方便Jni使用而提供的一个帮助函数；

image5

+ 所以在自己的Jni层代码中使用这种方法，就可以完成动态注册了；当Java层通过`System.loadLibrary加载完Jni动态库后，紧接着会查找该库中一个叫做JNIOnLoad的函数`，如果有它就调用它，而动态注册的工作就是在这里完成的；所以想使用动态注册方法，就必须实现JNI_OnLoad函数，只有在这个函数中才有机会完成动态注册的工作；静态注册则没有这个要求；但是建议静态注册也实现该函数；因为有一些初始化工作是可以在这里做的；

image6

+ Jni层代码中一般要包含jni.h这个头文件，Android源代码中提供了一个帮助头文件JNIHelp.h它的内部就包含了jni.h，所以在自己的代码中直接包含这个JNIHelp.h即可；
