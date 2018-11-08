# Web版API
## 调用方式
HttpClient.post(url,param)方式调用
链接格式如下： > http://api.map.baidu.com/place/v2/search?query=ATM机&tag=银行&region=北京&output=json&ak=您的ak //GET请求
## 简介
可以检索矩形或圆形区域内的地点数据，包括各类企业 生活 娱乐地点等。返回的参数主要有地点简称、地点的省份、地点的区县、唯一标识、经纬度、地点的详细信息（例如餐厅的评分信息）。
## 主要参数表
全部参数见：[参数](http://lbsyun.baidu.com/index.php?title=webapi/guide/webservice-placeapi)
全量搜索主要用到的参数如下：
|参数     | 含义 |
|-------- | ----------------|
|query  | 检索关键字 |
|region | 区域 |
|output | json |
|page_size | 分页数 |
|page_num | 页码数 |
|ak | 秘钥 |
圆形范围搜索主要用到的参数如下： 
|参数     | 含义 |
|-------- | ----------------|
|query  | 检索关键字 |
|location | 中心点 |
|radius | 半径 |
|output | json |
|page_size | 分页数 |
|page_num | 页码数 |
|ak | 秘钥 |
# JS版API
## 调用方式
### 城市检索
search方法提供根据关键字检索特定POI信息服务。 如下示例，为根据关键字“天安门”检索POI：
var map = new BMap.Map("container");      
map.centerAndZoom(new BMap.Point(116.404, 39.915), 11);      
var local = new BMap.LocalSearch(map, {      
    renderOptions:{map: map}      
});      
local.search("天安门");
### 圆形区域检索
searchNearby方法提供圆形区域检索服务。您可以在某个地点附近进行搜索，也可以在某一个特定结果点周围进行搜索。 下面示例展示如何在前门附近搜索小吃：
var map = new BMap.Map("container");         
map.centerAndZoom(new BMap.Point(116.404, 39.915), 11);      
var local = new BMap.LocalSearch(map,   
              { renderOptions:{map: map, autoViewport: true}});      
local.searchNearby("小吃", "前门");
## 简介
作用于Web API类似。结果主要有各类企业 生活 娱乐地点等。返回的参数主要有地点简称、地点的省份、地点的区县、唯一标识、经纬度。