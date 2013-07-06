Zhuozr.com API Docs  

 version:1.0  
 date:2013-07-03  
 author:cn.popeye@gmail.com  


零、一些约定
===========

**请求**：REST  
**result**：base64  
**加密**:  

    GET请求
        客户端 ---->  服务器        明文
        服务器 ---->  客户端        base64(response data)

    POST请求
        客户端 ---->  服务器        dict(data=base64(post data))
        服务器 ---->  客户端        base64(response data)
  
**客户端解析result结果：**  
 
    base64.decodestring(response data)
  
**错误信息:**  
    API请求通过HTTP Status Code来区分，status!=200无result值。  
  
    无错误： 
       http status code = 200
       result base64(response data)
    API验证失败: 
       http status code = 403
    没有查询到记录：
       http status code = 204
    操作失败：
       http status code = 417
    其他错误：
       http status code = 500
  
  
API 有效性验证
=============
每个渠道都拥有一个`src_id`和`src_key`。  
每个 API 请求时必须在 HTTP 请求头中加入以下字段：  
  
    * X-Date: time_string
    * X-ID: src_id|src_key
    注：
        * time_string：客户端当前时间（HTTP标准header日期时间字符串， GMT 格式，如Tue, 26 Feb 2013 05:26:10 GMT）
        * src_key：由验证算法计算得出的验证字符串。
  
API 请求验证算法描述
-------------------
**parameters:**  

* key: API key
* src_id: 渠道 ID
* gmt_time: HTTP 标准头时间字符串（GMT格式）
* request: 请求内容
 * GET   URI路径部分（不含host）,如 /add
 * POST  使用request body

**result:**  

    timstamp =  int(time.time()) // 时间戳
    return md5(key+'|'+src_id+'|'+timstamp+'|'+request)

*生成`src_id`和`sec_key`算法*  
  src_id: 不重复的数字字符串   
  sec_key: md5(指定随机字符串)  
  

API
===
  
tfeet相关
---------

### 提交捉字 ###

**uri:** /z/add   
**method:** post  
**request:**{"data":"base64(request body)"}  
**parameters**：
* text 描述
* shot 截图
* url  网址
**result**:  

     http=200  return Nothing
     http=417  return Nothing

### 获取捉字详细信息 ###

**uri:** /z/{zid}
**method:** get  
**parameters**  

    zid int  

**result:**  

    http=200 return  zhuozr详细信息
    http=204 return  Nothing
    http=417 return  Nothing

**示例:**  

    {
    "zid":121321,
    "text":"asdfasdf",
    "url":"http://ww.com.cn/aa.html",
    "shot":"http://shot.zhuozr.com/121321.jpg",
    "author":"", // 匿名为空字符串
    "created_at":"1212349586" // timestamp
    "comments":{
        "count":12, //评论数
        "item": [
           {
            "cid":2323232,
            "author":"李四",
            "created_at":"23234121" //时间戳
            "text":"asflkkjnskfa"   //评论内容
           },
           ...
          ]
      }
}

### 获取捉字列表 ###

**uri:** /{type}
**method:** get  
**parameters**  

    type string {popular,latest,poorest}  

**result:**  

    http=200 return  zhuozr 列表
    http=204 return  Nothing
    http=417 return  Nothing

**示例:**  

    {
      {捉字详细},
      {捉字详细}  //参考 ·获取捉字详细信息· 内容
    }

### 获取排行 ###

**uri:** /rank
**method:** get  
**parameters**  

    None

**result:**  

    http=200 return  媒体网站被捉字排行
    http=204 return  Nothing
    http=417 return  Nothing

**示例:**  

    {
      {
        "title":"一家媒体",
        "url":"http://yijiameit.com",
        "count":123, // 捉字次数
      },
      ...
   }
