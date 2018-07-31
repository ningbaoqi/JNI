### Jni-MediaScanner分析
+ MediaScanner的Jni层代码在`android_media_MediaScanner.cpp`中；`native_init`函数位于`android.media`这个包中，它的全路径名应该是`android.media.MediaScanner.native_init`而Jni层函数的名字是`android_media_MediaScanner_native_init`因为在Native语言中，符号`.`有着特殊的含义，所以Jni层需要把Java函数名称中的`.`转换成`_`；
