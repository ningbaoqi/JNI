### 结构体JNIEnv
+ `JNIEnv`是代表JNI环境的结构体；`JNIEnv`对象和线程是绑定在一起的；每个线程都有一个JNIEnv实例；JNIEnv实际上就是提供了一些JNI系统函数，通过这些函数可以做到：调用Java的函数，操作jobject对象等很多事情； 
