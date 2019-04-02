# HTTP协议详解

## URL结构

URL是可以简单理解为一种特殊的URI（统一资源标识符），其格式如下：

http://www.demo.com:80/demo/demo.html?name=kelvin&password=123456#first

其主要包括以下几个部分：

1. 协议部分：即HTTP协议（或HTTPS协议），//为协议的分隔符
2. 域名部分：域名需要通过DNS解析为IP地址之后才能定位
3. 端口部分：默认端口为80，可以不写，其他端口需要指明
4. 虚拟目录部分： 一般对应的是你Web工程的工程名，非必须
5. 文件名部分：本次URL请求所指向的文件资源，如果为空将指向默认文件名
6. 参数部分：使用?开始和&分割，JAVA Web中，可以使用request获取这些简单参数
7. 锚部分：#之后为锚部分，主要是用于当前页面的快速定位

## 请求结构

### 请求头

请求头内容如下图所示：

![请求头内容](https://upload-images.jianshu.io/upload_images/1843940-d3214aa6ebf47292.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/466/format/webp)

本次请求的请求头示例如下所示：

```java
GET /demo/demo.html?name=kelvin&password=123456 HTTP/1.1
Host: www.demo.com
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Encoding: gzip, deflate, sdch
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6
```

其中请求头部的参数如下：

![请求头参数](https://springboot-blog-1256194683.cos.ap-beijing.myqcloud.com/%E8%AF%B7%E6%B1%82%E5%A4%B4%E6%96%87%E4%BB%B6.png)

### 响应头

HTTP响应头由以下几部分构成：状态行、响应头、空行、响应体

其中响应头部的参数如下：

![响应头参数](https://springboot-blog-1256194683.cos.ap-beijing.myqcloud.com/%E5%93%8D%E5%BA%94%E5%A4%B4%E6%96%87%E4%BB%B6.png)

# HTTPS协议详解
HTTPS协议详解可见如下：
[HTTPS协议](https://accright.com/article/60)