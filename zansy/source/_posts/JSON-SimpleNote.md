title: JSON 简单笔记
author: zansy
tags: []
categories:
  - 唯有爱与工作不可辜负
date: 2018-11-09 01:05:00
---
最近在学着用 postman 测试和写接口
<!--more-->

## 概述
- JSON: JavaScript Object Notation(JavaScript 对象表示法)
- JSON 是存储和交换文本信息的语法

## 实战
- JSON 实例：
```
{
    "State": 1000,
    "SearchItemsCount": 123,
    "SearchCount": 123,
    "ClassList": "1159|100|啤酒,1499|14|其它,0|4|,1720|2|饮用水,1156|2|白酒,1139|1|进口冲调/饮品",
    "BrandList": "10806|98|百威,0|20|,27296|3|白熊,10440|2|郎酒",
    "SiteList": "1|84|京东商城,190|11|天猫超市,6|11|苏宁易购,10|7|天猫商城,278|6|天猫旗舰店,322|2|景彤全球购,3|2|当当",
    "SearchResultList": [
        {
            "spname": "Budweiser 百威啤酒500ml*18听",
            "sppic": "https://img11.360buyimg.com/n1/s450x450_jfs/t20569/97/1194982666/203143/26dfa4f7/5b22050aNfd2ffc77.jpg",
            "spurl": "http://item.jd.com/703214.html",
            "spprice": "104.00",
            "className": "啤酒",
            "brandName": "百威",
            "siteName": "京东商城",
            "commentUrl": "",
            "commentCount": "550915",
            "TitleHighLighter": "<font color=\"red\">Budweiser</font> 百威<font color=\"red\">啤酒</font>500ml*18听",
            "ziying": "1",
            "taobaoid": null,
            "siteid": "1",
            "id": "36303200",
            "classid": "1159",
            "spbh": "1|703214"
        },
        {
            "spname": "苏宁超市自营 Budweiser百威啤酒 500ML*18听 箱装",
            "sppic": "https://imgservice.suning.cn/uimg1/b2c/image/JiEbxMRsX2ex0h69Yl8qFw.jpg_800w_800h_4e",
            "spurl": "https://product.suning.com/0000000000/123291616.html",
            "spprice": "99.00",
            "className": "啤酒",
            "brandName": "百威",
            "siteName": "苏宁易购",
            "commentUrl": "",
            "commentCount": "57221",
            "TitleHighLighter": "苏宁超市自营 <font color=\"red\">Budweiser</font>百威<font color=\"red\">啤酒</font> 500ML*18听 箱装",
            "ziying": "1",
            "taobaoid": null,
            "siteid": "6",
            "id": "170477927",
            "classid": "1159",
            "spbh": "6|123291616"
        }
    ]
}
```
- 目标是将如上的字符串转成JSON，再进行拆分，获取每一个 key 中对应的 value，存入数据库中。
```
JSONObject returnJSON = JSONObject.fromObject(returnString);//将返回的String转换成JSON
JSONArray searchResultList = returnJSON.getJSONArray("SearchResultList");
for(int i = 0; i < searchResultList.size(); i++) {
	JSONObject searchResult = searchResultList.getJSONObject(i);
    stdPrice = new StdPrice();
    tdPrice.setSpname(searchResult.getString("spname"));
}
```

## JSON 语法拾遗
- 可以通过`SearchResultList[0].spname = "unknown";`
- 访问对象值:
	- 可以使用点号（.）来访问对象的值：`SearchResultList[0].spname`
	- 可以使用中括号（[]）来访问对象的值：`SearchResultList[0]["spname"]`

- 在 for-in 循环对象的属性时，使用中括号（[]）来访问属性的值：
```
var myObj = { "name":"runoob", "alexa":10000, "site":null };
for (x in myObj) {
    document.getElementById("demo").innerHTML += myObj[x] + "<br>";
}
```
- JSON 对象中可以包含另外一个 JSON 对象。

## JSON.parse()
- JSON 通常用于与服务端交换数据，在接收服务器数据时一般是字符串。使用 JSON.parse() 方法将数据转换为 JavaScript 对象。 
- 语法：`JSON.parse(text[, reviver])`
	- text:必需， 一个有效的 JSON 字符串。
	- reviver: 可选，一个转换结果的函数， 将为对象的每个成员调用此函数。

- 实例：
```
var obj = JSON.parse('{ "name":"runoob", "alexa":10000, "site":"www.runoob.com" }');//要确保待解析的字符串是标准的 JSON 格式
```

### 异常
- JSON 不能存储 Date 对象。如果需要存储 Date 对象，需要将其转换为字符串。
- 实例
```
var text = '{ "name":"Runoob", "initDate":"2013-12-14", "site":"www.runoob.com"}';
var obj = JSON.parse(text, function (key, value) {
    if (key == "initDate") {
        return new Date(value);
    } else {
        return value;
}});
document.getElementById("demo").innerHTML = obj.name + "创建日期：" + obj.initDate;
```

## JSON.stringify()
- 可以使用 JSON.stringify() 方法将 JavaScript 对象转换为字符串。 
- 语法：`JSON.stringify(value[, replacer[, space]])`
	- value: 必需， 一个有效的 JSON 对象。
	- replacer: 可选。用于转换结果的函数或数组。
	- space: 可选，文本添加缩进、空格和换行符，如果 space 是一个数字，则返回值文本在每个级别缩进指定数目的空格，如果 space 大于 10，则文本缩进 10 个空格。space 有可以使用非数字，如：\t。

## JSONP
- Jsonp(JSON with Padding) 是 json 的一种"使用模式"，可以让网页从别的域名（网站）那获取资料，即跨域读取数据。
- 为什么我们从不同的域（网站）访问数据需要一个特殊的技术(JSONP )呢？这是因为同源策略。 同源策略，它是由Netscape提出的一个著名的安全策略，现在所有支持JavaScript 的浏览器都会使用这个策略。
