### Java层的MediaScanner分析

image1

#### 一、加载Jni库
+ 如果Java要调用native函数，就必须通过一个位于Jni层的动态库来实现，动态库就是运行时加载的库，原则上是，在调用native函数前，任何时候、任何地方加载都可以，通行的做法在类的static语句中加载，调用`System.loadLibrary()`方法；
#### 二、Java的native函数和总结
+ native函数都会用关键字`native`修饰，表示该函数将由Jni层实现；Jni技术要求Java程序员只做下面的两个工作；一、加载对应的Jni库；二、声明由关键字native修饰的函数；
