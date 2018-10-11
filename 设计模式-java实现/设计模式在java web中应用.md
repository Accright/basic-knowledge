## 问题： Servlet是单例模式吗？
##### 在spring mvc中servlet默认是单例模式的。java web项目中默认也是单例的。但是可以更改为多例模式。就是说servlet会在加载web.xml时只创建1次。在springmvc中可以使用@Scope("prototype")更改controller为多例模式。这样每个请求都会创建一个servlet。

