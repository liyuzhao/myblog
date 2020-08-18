---
title: 知道Android 中Gradle 的这些技巧，提升编译构建速度
date: 2016-06-07 14:58:45
tags: [android]
categories: android
---

现在基本上都使用Android Studio 来开发，使用Gradle 构建项目，Gradle 的种种优点，多渠道打包方便，依赖类库统一管理，基于Groovy 的DSL 语言简洁方便。这些好处在我们开发省了我们不少事，但是唯一的问题就是编译构建运行的时候特别慢，甚至是致命的，最后发现加班的时间都浪费在编译构建上（说多了都是泪）。

下面有几个技巧，让你你的Android Gradle 运行，编译更加快速。

>原文链接：[http://tikitoo.github.io/2016/05/26/android-studio-gradle-build-run-faster/](http://tikitoo.github.io/2016/05/26/android-studio-gradle-build-run-faster/)


<!-- more -->

## 设置代理

因为依赖要从Jcenter/Maven 仓库上下载，但是网络还是时不时地抽风。如果你使用VPN 或者全局代码可以从本地网络配置，就不需要在Android Studio 配置了（好像并没有什么用？）。给Gradle 设置代码需要在根目录的gradle.properties 配置。

```
// 举例ShadowSocket
systemProp.http.proxyHost=127.0.0.1
systemProp.http.proxyPort=1080
systemProp.https.proxyHost=127.0.0.1
systemProp.https.proxyPort=1080

```
如果你用ShadowSocket，记得在本地开启。如果你使用其他代理软件，也类似，会有主机和端口。

## 更新Gradle,设置离线状态

Android Studio 的版本一般都跟随着Gradle 的版本，在安装目录的根目录有个gradle 目录，就是最新版，我们可以在设置中打开Gradle，选择Use local gradle distribution，然后选择Gradle 的目录即可，这样就不会每次根据项目中./gradle/xxx 中的gradle 版本每次再下载。理论上Gradle 的版本越新构建运行越快，也只是理论上，所以推荐稳定版的版本。

![image](http://ww3.sinaimg.cn/large/68622377gw1f480c56owej20z50dedj1.jpg)

如果需要使用命令行编译，可以配置—daemon —parallel —offline。

![image](http://ww3.sinaimg.cn/large/68622377gw1f480znt81ij20z50dutd3.jpg)

## 守护进程，并行编译

在项目的根目录中有一个gradle.properties 文件，
![image](http://ww3.sinaimg.cn/large/68622377gw1f480bomjc0j20ry0am42c.jpg)

org.gradle.daemon=true 就是让你让你编译时使用守护进程。

org.gradle.parallel=true 使用并行编译

org.gradle.jvmargs=-Xmx2048m JVM最大允许分配的堆内存，按需分配

-XX:MaxPermSize=512m JVM最大允许分配的非堆内存，按需分配

当然你也可以在下面的目录下面创建`gradle.properties`,来配置全局的属性，在所有的项目中起作用。
    `/home//.gradle/`(Linux)
    `/Users//.gradle/`(Mac)
    `C:\Users\\.gradle`(Windows)
当然也可以修改xxx\android studio 安装目录\bin\studio64.exe.vmoptions 文件来配置JVM 的相关的参数。

开发使用SDK=21

```
android {
	...

    productFlavors {
        dev {
            minSdkVersion 21
        }

        release {
            minSdkVersion 14
        }
    }
}

```
我们都知道当API 不小于21，使用 ART，在 Build 时只做 class to dex，不做 mergeing dex，会省下大量的时间。

## 使用aar依赖
我们都知道我们或多或少使用第三方的开源库或者工具，还有自己封装的类库，但是一次编译的时候在Library Module 目录下， 打开build/outputs/aar，就有生成的aar 文件。把他放在libs 目录下，在build.gradle 添加。

```
allprojects {
   repositories {
      jcenter()
      flatDir {
        dirs 'libs'
      }
   }
}

dependencies {
    compile(name:'cards', ext:'aar')
}
```

**当然也可以这样添加**

我们可以新建一个jar/aar Module，选择aar 文件，然后新建的Module 目录下，就会多了个build.gradle 和xxx.aar。

```
configurations.maybeCreate("default")
artifacts.add("default", file('mylibrary-debug.aar'))
```

然后在我们的Module 中这样引用即可。

```
compile project(':mylibrary-debug')
```
## dexOptions

```
classpath 'com.android.tools.build:gradle:2.0.0-alpha9'
```
```
# Default value: -Xmx10248m -XX:MaxPermSize=256m
org.gradle.jvmargs=-Xmx4g -XX:MaxPermSize=512m
```

```
dexOptions {
    preDexLibraries true
    javaMaxHeapSize "3g"
    incremental true
    dexInProcess = true
}
```

这里是参考stackoverflow 上的，好像有Bug，作者本人试了Android gradle 的版本至少是2.0.0-alpha9，然后配置一些参数。这里为JVM 设置4G，给Dex 设置3G。


## 参考
[android - To run dex in process, the Gradle daemon needs a larger heap. It currently has 910 MB | Stack Overflow](http://stackoverflow.com/questions/35224255/to-run-dex-in-process-the-gradle-daemon-needs-a-larger-heap-it-currently-has-9)

[How to manually include external aar package using new Gradle Android Build System | Stack Overflow](http://stackoverflow.com/questions/16682847/how-to-manually-include-external-aar-package-using-new-gradle-android-build-syst)

[优化android studio编译效率的方法 | 开发技术前线](http://www.devtf.cn/?p=585)

[加速Android Studio/Gradle构建 | 码农明明桑](http://blog.isming.me/2015/03/18/android-build-speed-up/)

[android - Adding local .aar files to my gradle build | Stack Overflow](http://stackoverflow.com/questions/21882804/adding-local-aar-files-to-my-gradle-build)

[Android模块化编程之引用本地的aar | Stormzhang](http://stormzhang.com/android/2015/03/01/android-reference-local-aar/)
