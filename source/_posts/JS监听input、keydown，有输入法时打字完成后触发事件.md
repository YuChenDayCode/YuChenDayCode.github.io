---
title: JS监听input、keydown，有输入法时打字完成后触发事件
date: 2018/12/28 16:02
tags: JS
---
 # JS监听input、keydown，有输入法时打字完成后触发事件

在给输入框绑定input或keydown事件时 预期效果是有输入法时，输入中文后触发事件，不希望输一个字母就触发一次事件

可以用到**compositionstart，compositionend**。 主流浏览器都兼容

```javascript
var flag = true;
 $('#div-detail').delegate('#sclt-div input', 'input', function () {
     if (!flag) //默认都可。 输入法时 截断
          CustomerList($(this).val());
       }).on('compositionstart', function () {
          flag = true;
          console.log('输入法，录入开始');
       }).on('compositionend', function () {
          flag = false;
          CustomerList($('#sclt-div input').val()); //在input之后执行 所以需要手动调用一次
          console.log('输入法，输入结束');
       });
```

