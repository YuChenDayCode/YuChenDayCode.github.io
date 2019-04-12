---
title: ajax,get时请求异步缓存
date: 2018/11/22 11:21
tags: ajax
---
 # ajax,get时请求异步缓存

ajax中的get为何有时执行，有时不执行?

（九十岁老太为何起死回生，数百头母猪为何半夜惨叫；女生宿舍为何频频失窃，超市方便面为何惨遭黑手。在达一切的背后，是人性的扭曲、还是道德的沦丧。敬请... 咳咳）



在IE中用ajax的get方式请求同一个地址获取数据时，经常碰到回调函数成功执行，前台又有数据的情况，但是无法请求到后台获得最新的数据。

原因是**ajax存在异步缓存**的问题

因为ajax本身自带有实时异步请求的功能，而IE缓存导致请求时不会请求后台，会直接读取缓存的数据。

解决办法：

**第一种：ajax get请求时比较简单 只需将cache设置为false就好**

```javascript
 $.ajax({
                type: 'get',//get请求时
                url: '........',
                cache: false,//不缓存
                data: { },
                success: function (result) {
                    //
                }
            });
```

**第二种：$.get();时 设置一个参数为随机数tempPara: Math.random() 或者获取当前时间new Date().getTime()**

```javascript
     $.get('...........', { tempPara: Math.random() }, function (result) {
              //
            });
```

**因为请求同一个地址会直接读取缓存，所以可以在参数中加一个随机数数 让每次参数不一样就好**