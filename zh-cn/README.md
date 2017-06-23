# 易回购对接方案

---

##1.签名说明
为保障数据安全，对暴露在互联网环境的接口的调用应当采用https协议通信，并对参数进行签名和验签。


###1.1 私钥加签，公钥解签
    签名是在进行接口调用的过程中，利用密钥和接口包含的所有参数进行MD5签名的过程，具体流程如下：
 - 签名说明：

    接口调用或者生成二维码所有传递的参数都需要一起进行签名，假设本次参数包括targetId、typeId、channel、notifyTime、id等5个参数，并且密钥secret=34719280830192，参数中targetId=2200000001，notifyTime=1468780992，channel=alipay，storeId=5308，id=61028309128301298，则签名步骤如下：
```
第一步：参数及密钥拼装成6个元素存入到数组array=['2200000001=targetId','1468780992=notifyTime','alipay=channel','61028309128301298=id','5308=typeId','34719280830192=secret']；
第二步：对array数组根据ASCII码进行排序得到sortArray=['1468780992=notifyTime','2200000001=targetId','34719280830192=secret','5308=typeId','61028309128301298=id','alipay=channel']；
第三步：使用连接符‘&’对数组进行拼接，得到字符串source='1468780992=notifyTime&2200000001=targetId&34719280830192=secret&5308=typeId&61028309128301298=id&alipay=channel'；
第四步：对source进行私钥加签，得到sign=Upper(MD5(source))；
```

##2.模型数据更新通知
    易回购在模型数据更新时时，通过调用机蜜的通知接口通知机蜜更新。推送频率为(实时/1m/2m/10m/1h/6h/12h/24h)，直到8次之后或收到机蜜的返回的SUCCESS为止。

 - 访问方式：post
 - 参数格式：application/json
 - 参数说明：

|名称|含义|类型|必填|备注|
|----|:---|:---|:--:|--------|
|sign|参数签名|varchar(128)|Y|详见2.2.如何构造签名|
|notifyTime|消息通知时间|timestamp|Y|以秒为单位的时间戳，如：1468780992|
|notifyType|消息类型|varchar(10)|Y|create/update/delete|
|targetType|更新类型|varchar(20)|Y|brand/type|
|targetId|机型标识|varchar(32)|Y|targetType为brand时为cid，targetType为type时为typeId|


 - 参数示例：
 
```javascript
{
    "sign":"MDYCGQCNTJhYa4JghYuksPMsE8jO33sq",
    "notifyTime":"1468780992",
    "notifyType":"create",
    "targetType":"brand",
    "targetId":"da6692c0c1f04f0986d9bf687b7d9908"
}
```


 - 返回说明：

返回字符串为SUCCESS代表成功，无法回或返回其它字符串代表异常。

 - 返回示例：
 
```javascript
SUCCESS
```
