# mac上编译openJDK12

## 编译准备
### 1.bootopenJDK
编译一个版本的openJDK,需要一个bootopenJDK,一般选择编译前一个版本的openJDK,在[openJDK](https://adoptopenjdk.net/archive.html)中选择平台相关的二进制包进行下载。本次在mac平台编译，并且编译的是openJDK12,故选择的是mac平台64位的openJDK11。
### 2.Xcode
此处编译openJDK借助Xcode提供的clang编译器,需要安装clang。clang的版本选择为10.2(匹配osx10.14.6),在[苹果官网Xcode](https://developer.apple.com/xcode/)下载, 如果没有appid，则注册一个。
### 3.openJDK12源码
> 下载源码有两种方式
  - 使用hg下载  


  ```shell
  brew install hg
  hg clone https://hg.openjdk.java.net/jdk/jdk12
  ```  
  
  这种方式要求网速比较稳定, 我这边用这种方法下载失败了两次, 最终选择使用网页格式下载
  - 直接在网页下载  


    在[openJDK12](https://hg.openjdk.java.net/jdk/jdk12)页面点击zip(本机使用gz格式解压报错),进行下载。

### 4.freetype、ccache和autoconf
这两个工具是编译时使用的，直接使用brew下载  
```shell
brew install freetype
brew install ccache
brew install autoconf
```  
### 5.xcodebuild配置
提前配置是避免编译时报错,配置脚本如下,注意使用绝对路径
```shell
sudo xcode-select --switch /YourPATH/Xcode.app/Contents/Developer
```  
### 6.确认CLASSPATH环境变量
从Java9开始移除了dt.jar和tools.jar如果CLASSPATH中有这两个jar的路径,需要移除。另外如果配置了scala的CLASSPATH也需要移除,否则会报错找不到实现。
### 7.configure
编译之前需要configure,脚本如下
```shell
#!/bin/bash
/openjdk12_path/configure --with-jvm-variants=server --enable-ccache --with-debug-level=slowdebug --with-boot-jdk=/Users/guobin/openJDK/openjdk11 --with-num-cores=8 --with-memory-size=8192 --disable-warnings-as-errors
```  
+ --with-jvm-variants=server 指定编译后JDK的模式有server和client
+ --enable-ccache 使用编译缓存,可加快编译速度
+ --with-debug-level 有release、fastdebug、slowdebug 和 optimized四种模式，release供线上使用，fastdebug和slowdebug都是用于调试的
+ --with-boot-jdk 指定bootopenJDK home的绝对路径
+ --with-num-cores 编译使用的cpu核数, 根据电脑配置设置
+ --with-memory-size 编译使用的内存, 根据电脑配置设置
+ --disable-warnings-as-errors 默认会将提醒作为错误导致编译终止, 这里禁用。  

## 编译  
```shell
make images
```  
等待大约10-20分钟, 编译过程无error, 则编译成功。在openjdk12_path/build/macosx-x86_64-server-slowdebug/images目录中, jdk就是被编译出来的openJDK12,可将JAVA_HOME指向该路径,并执行
```shell
java -version
```  
进行验证

