# iOS启动优化

## 启动流程简介

### main函数之前

main函数之前的启动流程：

加载可执行文件(.o文件) -> 加载dyld -> 加载动态链接库 -> rebase -> bind -> ObjC setup -> initialize

其中加载可执行文件，即打包后的.o文件。dyld则是一个系统库，此系统库的作用是加载App依赖的其他动态链接库。前两步都是系统必备的工作，且没可优化的点，此处不再介绍。下面看看后面的几个步骤：

#### 1) 加载动态库

当系统库dyld加载完成之后，系统会调用此库来分析可执行文件所依赖的其他动态链接库(如Foundation.framework等)，根据分析结果把所有的动态链接库加载到内存中。

加载动态链接库的工作有ImageLoader来完成，并且每个动态链接库由独立的ImageLoader对象分别加载。

#### 2) rebase/bind

由于ASLR(address space layout randomization)的存在，可执行文件和动态链接库在虚拟内存中的加载地址每次启动都不固定，所以需要这2步来修复镜像中的资源指针，来指向正确的地址。

rebase修复指向当前资源内部的指针。

bind修复指向当前资源外部的指针。

#### 3) ObjC Setup

此步主要工作是注册ObjC类、将Category定义的方法插入方法列表及保证selector唯一等工作。

#### 4) initialize

此步主要工作是调用ObjC类的+load方法、C++的构造函数属性函数 形如attribute((constructor)) void DoSomeInitializationWork()及非基本类型的C++全局变量创建等

### main函数之后

main函数中主要创建autoreleasepool、调用UIApplicationMain函数等。在UIApplicationMain函数中创建UIApplication及调用AppDelegate的相关回调。

在main函数之后，一些系统的工作并不会影响性能(如创建UIApplication实例等)。最重要的在于AppDelegate中的application:willFinishLaunchingWithOptions:和application:didFinishLaunchingWithOptions:回调中，容易存在耗时操作。而这两个方法中的代码有开发者实现，可以完全控制。

### 启动流程总结

App启动流程主要分为main函数之前和main函数之后两大部分，main函数之前的工作主要是一些相关库的加载链接、环境初始化等工作。main函数之后则是真正进入App代码执行阶段。

## App启动时间优化

通过上面的App启动流程简介，对于App的启动有一个初步的了解。接下来App启动时间的优化，也是针对上面的启动流程，不同的步骤做不同的优化工作。

### 加载动态库

通过上面介绍了解到，此步骤会加载所有App依赖的动态链接库，并且每个库单独加载。这些动态链接库可以分为系统库(如Foundation.framework等)和App依赖的第三方库。其中系统库加载过程中，由系统生成缓存，所有的App启动都可以使用缓存，故可优化空间比较小，不考虑。对于第三方库，可以有一下优化点：

1) 尽量减少非系统库的依赖数量
2) 合并非系统库
3) 使用静态库，比如把代码加入主程序

### rebase/bind

rebase和bind主要工作是修复资源指针，此处的优化思路就是尽可能减少需要修复的指针，具体可优化的工作如下：

1) 减少ObjC类的数量，减少selector数量
2) 减少C++虚函数
3) 转而使用Swift struct

### ObjC setup

通过第一部分的介绍，了解到此处的工作。由于上两个步骤的优化，已经包含了此步的工作，故此步骤无需额外优化。

### initialize

优化工作：

1) 减少+load中的工作，或者去掉+load，转而使用+initialize
2) 尽量不要用C++虚函数
3) 减少全局变量

### main函数之后的优化

#### 1、AppDelegate优化

1) 简化application:willFinishLaunchingWithOptions:和application:didFinishLaunchingWithOptions:方法中的操作
2) 删除或延迟非必须操作
3) 耗时操作采用多线程(background线程)
4) 尽量避免io相关操作

#### 2、首页优化

1) 页面避免使用nib或storyboard
2) 如果必须使用storyboard，则需要简化storyboard文件，减少文件中的对象和view的层次等
3) 大型资源文件异步加载
4) NSLog会隐式创建Calendar，故在正式版本中要删除所有NSLog
5) 如果有启动广告图、或引导图，则可以在显示广告图引导图后，预加载首页视图和内容

## App启动时间衡量

### main函数之前

Xcode提供了一种方法，在Scheme -> Edit Scheme -> Run -> Arguments -> Environments Variable增加一个环境变量并勾选，环境变量名：DYLD_PRINT_STATISTICS， 值为：1

增加上面的环境变量之后，运行scheme会在App启动之后，在Xcode日志面板中打印出main函数之前各步骤的耗时明细。

另外一种方法就是使用Timer Profiler工具来监测App性能，在启动之后，Timer Profiler中可以看到main函数之前的启动耗时和启动之后各操作的耗时。

### main函数之后

调用main函数时，代码由开发者控制，则可以在main调用时打印启动时间，然后在各关键点打印时间，来计算main函数之后的启动时间。

另外也可以通过上面介绍的Timer Profiler来看各操作时间。