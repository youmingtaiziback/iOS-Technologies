# [微信读书 iOS 性能优化总结](https://wereadteam.github.io/2016/05/03/WeRead-Performance/)

## 如何发现性能问题 {#如何发现性能问题}

从两个维度进行了监控

* 业务性能监控，是指在App本地，业务的开始和结束处打点上报，然后后台统计达到监控目的
* 卡顿监控。卡顿监控的实现一般有两种方案

  * 主线程卡顿监控，[具体原理和实现](http://www.tanhao.me/code/151113.html/)

  * FPS监控，RDM\(bugly\)的卡顿监控\(也是基于微信团队的卡顿标准\)，通过下发配置，对现网用户进行抽样检测，并上报卡顿的堆栈信息

## 性能问题的解决方法 {#性能问题的解决方法}

#### 优化业务流程 {#1-优化业务流程}

#### 合理的线程分配 {#2-合理的线程分配}

一些基本原则：

* UI 操作和 DataSource 的操作一定在主线程

* DB 操作、日志记录、网络回调都在各自的固定线程

* 不同业务，可以通过创建队列保证数据一致性

#### 预处理和延时加载 {#3-预处理和延时加载}

#### 缓存 {#4-缓存}

注意以下几点：

* 并发访问 cache 时，数据一致性问题

* cache 线程安全问题，防止一边修改一边遍历的 crash

* cache 查找时性能问题

* cache 的释放与重建，避免占用空间无限扩大，同时释放的粒度也要依实际需求而定

#### 使用正确的API {#5-使用正确的API}

* imageNamed:

* NSDateFormatter

* \(NSDate \*\)dateFromString:\(NSString \)string

* NSLog\(\)

* stat代替NSFileManager

## 如何预防性能问题 {#如何预防性能问题}

#### 内存泄露检测工具 {#1-内存泄露检测工具}

#### FPS/SQL性能监测工具条 {#2-FPS-SQL性能监测工具条}

#### UI / DataSource主线程检测工具 {#3-UI-DataSource主线程检测工具。}

#### 排版引擎自动化检测工具 {#4-排版引擎自动化检测工具}

#### 书源检测工具 {#5-书源检测工具}

# [微信读书 iOS 质量保证及性能监控](http://wereadteam.github.io/2016/12/12/Monitor/)

## 对网络层成功率的监控 {#对网络层成功率的监控}

统计网络请求的成功、失败率，了解所有错误码的分布情况

## 数据层的性能监控 {#数据层的性能监控}

数据层框架是[GYDataCenter](https://wereadteam.github.io/2016/07/06/GYDataCenter/)

## UI数据源监控 {#UI数据源监控}

我们只需hook了UIDataSource的alloc、NSMutableArray的add等方法，当调用NSMutableArray的add方法时，去遍历所有UIDataSource对象，通过class\_copyIvarList、valueForKey函数把所有的成员变量都找出来看看是否为当前的NSMutableArray，通过NSAssert确保当前在主线程，否则在debug模式下会crash提醒开发者修改

## UI主线程监控 {#UI主线程监控}

#### 防止子线程访问UI

hook掉UIView、CALayer的setNeedsLayout、setNeedsDisplay、setNeedsDisplayInRect:三个方法

#### FPS监控

[demo](https://github.com/featuretower/GYMonitor)

#### 线上用户监控

使用RDM sdk来监控，原理是通过RunLoop的几个Observer来确定主线程是否卡住了

## 业务实时监控 {#业务实时监控}

## 统计Assert断言 {#统计Assert断言}

发布环境下Assert是默认不生效的，需要使其生效

如何捕捉所有的Assert

* NSAssertionHandler线程相关
* 重写NSAssert宏，可能会引起未知问题

* 自己包装NSAssert，使用plcrashreporter这个库来获取堆栈的

## 总结 {#总结}



