---
layout:            post
title:             "如何在JNI添加共享库"
menutitle:         "如何在JNI添加共享库"
category:          Android
author:            dean
tags:              android jni library
language:          ZH
---
在Android的JNI开发中，我们经常会需要链接一些第三方或自己编写的动态库，这些动态库往往需要同时打包到发布的APK中，我这里是以Android Studio 3.2.1为环境，它添加Native库的方式是通过Cmake加入的。下面介绍具体步骤
### 步骤
#### 1.共享库路径
  在一个完整的项目中，源码和库等文件的路径往往是固定的，在Android Studio中，一个默认项目的建立，往往设定了固定的路径。我们应该把需要添加的第共享库放置在app下的libs目录里面，我这里以要添加的库为libutil为名称，我们在libs下建立libutil目录，这个目录下包含include和lib目录，lib下通常包含了arm64-v8a,armeabi-v7a等不同平台编译的目录，这些目录下分别为libutil.so。
        
#### 2.编辑CMakeList
  CmakeList包含了你编写的Native代码编译规则，当然，所要调用的共享库也是我们的c++代码需要调用。我们需要添加如下内容
```makefile
include_directories(libs/libutil/include)
add_library(libutil SHARED IMPORTED)
set_target_properties(libutil PROPERTIES IMPORTED_LOCATION ${PROJECT_SOURCE_DIR}/libs/libutil/lib/${ANDROID_ABI}/libutil.so)
...
target_link_libraries( ...
                       jpeg-turbo1500
                        ...)
```
#### 3.编辑Gradle文件
  为了在打包apk的时候，能把共享库一块加入，我们需要在Module级gradle文件的Android节点下加入如下内容
```bash
    sourceSets {
        main {
            // let gradle pack the shared library into apk
            jniLibs.srcDirs = ['libs/libutil/lib']
        }
    }
```