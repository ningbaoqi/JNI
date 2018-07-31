### JNI数据类型转换及参数签名
+ 参数签名由参数加返回值组成，参数必须用小括号括起来，没有参数时也要使用一对空括号；因为java支持函数重载，所以仅仅根据函数名是无法找到具体函数的，为了解决这个问题，JNI技术中就将参数类型和返回值类型的组合作为了一个函数的签名信息，有了签名信息和函数名，就能很顺利的找到java中的函数了；

|参数签名|java类型|JNI类型|
|-------|-------|------|
|`Z`|boolean|jboolean|
|`B`|byte|jbyte|
|`C`|char|jchar|
|`S`|short|jshort|
|`I`|int|jint|
|`J`|long|jlong|
|`F`|float|jfloat|
|`D`|double|jdouble|
|`[Z`|boolean[]|jbooleanArray|
|`[C`|char[]|jcharArray|
|`[B`|byte[]|jbyteArray|
|`[S`|short[]|jshortArray|
|`[I`|int[]|jintArray|
|`[J`|long[]|jlongArray|
|`[F`|float[]|jfloatArray|
|`[D`|double[]|jdoubleArray|
|`Ljava/lang/String;`|String|jstring|
|`[Ljava/lang/Object;`|Object[]|jobjectArray|
|`Ljava/lang/Throwable;`|java.lang.Throwable实例|jthrowable|
|`Ljava/lang/Class;`|java.lang.Class实例|jclass|
|`L全路径名称;`|All objects|jobject|
|`()Ljava/lang/String;`|String f()|
|`(ILjava/lang/Class;)J`|long f(int i , Class c)|

+ 复杂类型的参数签名格式是`L`加上`全限定类名`再加上`;`；除了`String`外，其余的Java复杂类型对应的JNI类型都是`jobject`；Java提供了一个叫`javap`的工具能够帮助生成函数或变量的签名信息如：`javap -s -p xxx`其中`xxx`为编译后的class文件，`s`表示输出内部数据类型的签名信息，`p`表示打印所有函数和成员的签名信息，默认只会打印`public`成员和函数的签名信息；

```
private native void processFile(String path, String mimeType, MediaScannerClient client);

{"processFile","(Ljava/lang/String;Ljava/lang/String;Landroid/media/MediaScannerClient;)V",(void *)android_media_MediaScanner_processFile}

//第二个参数jobject代表Java层的MediaScanner对象，它表示在哪个MediaScanner对象上调用的processFile方法，如果Java层是static函数，那么这个参数将是jclass，表示是在调用哪个Java Class的静态函数

static void android_media_MediaScanner_processFile(JNIEnv *env, jobject thiz, jstring path, jstring mimeType, jobject client);
```
