# HDNetwork

## 前言
```ruby
本来是想在GitHub找一个符合自己App的网络请求库，结果就是要么不支持数据缓存或者不支持RESTful API 请求，
又或者带缓存的又不支持过滤某一些参数字段(否则无法得到缓存)，带着这样的需求之下就有了 “HDNetwork” 这个库。
初衷就是尽最大的努力最大程度下满足所有App的日常需求。
```
	
<br /> 
<img src="https://github.com/hongdong/HDNetwork/blob/master/HDNetworkDemo.png"  width="313" height="616"  alt="Demo效果" align=right />

## 特点
- HDNetwork 对 AFHTTPSessionManager 进行二次封装。包括网络请求、文件上传、文件下载这三个方法。并且支持RESTful API GET、POST、PUT、DELETE、PATCH的请求。同时使用YYCache做了强大的缓存策略，并做了RAC的封装。

- 拥有 AFNetwork 大部分常用功能，包括网络状态监听等，提供类方法和实例方法调用。

- 非常好的扩展性，开放出了YYCache和AFNetwork的实例对象，更便于满足各种不同需求。

- 支持多种缓存策略。

- 按App版本号缓存网络请求内容也可自定义版本号。


## 安装

### 支持 Cocoapods 安装

```ruby
pod 'HDNetwork'
```
<br /> 

## 使用
**所有方法都可以直接看 HDNetworking.h 中的声明以及注释。**

### HDNetwork 的全局配置

#### 设置请求根路径

```objc
[HDNetwork setBaseURL:@"https://atime.com/app/v1/"];
```
#### 设置缓存过滤参数key(如时间戳，随机数)否则会导致无法得到缓存数据

```objc
[HDNetwork setFiltrationCacheKey:@[@"time",@"ts"]];
```


#### 设置日志

##### 日志打印的开关

```objc
[HDNetwork setLogEnabled:YES];
```
### 网络状态

#### 网络状态监听

```objc
[HDNetwork getNetworkStatusWithBlock:^(HDNetworkStatusType status) {
        switch (status) {
            case HDNetworkStatusUnknown:
                //未知网络
                break;
            case HDNetworkStatusNotReachable:
                //无网路
                break;
            case HDNetworkStatusReachableWWAN:
                //手机网络
                break;
            case HDNetworkStatusReachableWiFi:
                //WiFi网络
                break;
            default:
                break;
        }
    }];
```
<br /> 

### 网络请求

#### 缓存策略

```objc
typedef NS_ENUM(NSUInteger, HDCachePolicy){
    /**只从网络获取数据，且数据不会缓存在本地*/
    HDCachePolicyIgnoreCache = 0,
    /**只从缓存读数据，如果缓存没有数据，返回一个空*/
    HDCachePolicyCacheOnly = 1,
    /**先从网络获取数据，同时会在本地缓存数据*/
    HDCachePolicyNetworkOnly = 2,
    /**先从缓存读取数据，如果没有再从网络获取*/
    HDCachePolicyCacheElseNetwork = 3,
    /**先从网络获取数据，如果没有在从缓存获取，此处的没有可以理解为访问网络失败，再从缓存读取*/
    HDCachePolicyNetworkElseCache = 4,
    /**先从缓存读取数据，然后在从网络获取并且缓存，在这种情况下，Block将产生两次调用*/
    HDCachePolicyCacheThenNetwork = 5
};
```
#### 请求方式
**以 POST 方法为例，方法定义：**
```objc
/**
 POST请求
 
 @param url 请求地址
 @param parameters 请求参数
 @param cachePolicy 缓存策略
 @param callback 请求回调
 */
+ (void)POSTWithURL:(NSString *)url
         parameters:(NSDictionary *)parameters
        cachePolicy:(HDCachePolicy)cachePolicy
            callback:(HDHttpRequest)callback;
```
**自定义请求方式：**
```objc
/**
 自定义请求方式
 
 @param method 请求方式(GET, POST, HEAD, PUT, PATCH, DELETE)
 @param url 请求地址
 @param parameters 请求参数
 @param cachePolicy 缓存策略
 @param callback 请求回调
 */
+ (void)HTTPWithMethod:(HDRequestMethod)method
                    url:(NSString *)url
             parameters:(NSDictionary *)parameters
            cachePolicy:(HDCachePolicy)cachePolicy
                callback:(HDHttpRequest)callback;
```
#### 取消请求

```objc
/**
 取消所有HTTP请求
 */
+ (void)cancelAllRequest;

/**
 取消指定URL的HTTP请求
 */
+ (void)cancelRequestWithURL:(NSString *)url;
```
#### 上传

```objc
/**
 上传文件
 
 @param url 请求地址
 @param parameters 请求参数
 @param name 文件对应服务器上的字段
 @param filePath 文件路径
 @param progress 上传进度
 @param callback 请求回调
 */
+ (void)uploadFileWithURL:(NSString *)url
               parameters:(NSDictionary *)parameters
                     name:(NSString *)name
                 filePath:(NSString *)filePath
                 progress:(HDHttpProgress)progress
                  callback:(HDHttpRequest)callback;
```


#### 下载

```objc
/**
 下载文件

 @param url 请求地址
 @param fileDir 文件存储的目录(默认存储目录为Download)
 @param progress 文件下载的进度信息
 @param callback 请求回调
 */
+ (void)downloadWithURL:(NSString *)url
                fileDir:(NSString *)fileDir
               progress:(HDHttpProgress)progress
                callback:(HDHttpDownload)callback;
```
<br /> 

### 缓存处理
#### 设置最大缓存内存

```objc
/**
 *  磁盘最大缓存开销大小 bytes(字节)
 */
+ (void)setCostLimit:(NSInteger)costLimit;

```
#### 获取网络缓存的总大小
```objc
/**
 *  获取网络缓存的总大小 bytes(字节)
 *  推荐使用该方法 不会阻塞主线程，通过block返回
 */
+ (void)getAllHttpCacheSizeBlock:(void(^)(NSInteger totalCount))block;

```
#### 删除所有网络缓存
```objc
/**
 *  删除所有网络缓存
 *  推荐使用该方法 不会阻塞主线程，同时返回Progress
 */
+ (void)removeAllHttpCacheBlock:(void(^)(int removedCount, int totalCount))progress
                       endBlock:(void(^)(BOOL error))end;

```



待完善中
