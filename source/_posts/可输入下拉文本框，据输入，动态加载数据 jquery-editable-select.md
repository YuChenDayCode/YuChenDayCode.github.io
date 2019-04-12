---
title: 可输入下拉文本框，据输入，动态加载数据 jquery-editable-select
date: 2018/11/23 13:25
tags: jquery-editable-select
---
 # 可输入下拉文本框，据输入，动态加载数据 jquery-editable-select

用到一个**jquery-editable-select**的控件

github地址: <https://github.com/indrimuska/jquery-editable-select>



这个插件的原理是把多选框转化为input 并把项列为ul>li的形式

```
<select id="i_CustomerId_ES" name="CustomerName"></select>
```

![img](https://img-blog.csdn.net/20180608144149973?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvbW1hbmRCYWJ5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



**绑定动态数据，根据输入筛选 并获取value和txt：**



1.初始化editable-select控件



```
  $('#i_CustomerId_ES').editableSelect().on('select.editable-select', function (e, dom) {
                //console.log(dom.val() + '' + dom.text());         
         });
```





 2.由于会解析成input 可以通过给input绑定keydown 或 input时间来监控输入的目的：

```
   $('#i_CustomerId_ES).on('input', function (e) {
                bindSc($(this).val()); //调用获取数据的方法
            });
```



3.绑定数据：

```
  var bindSc = function (value) {
            var search = value;
            $.get('...', { search: search }, function (result) {
                $('#i_CustomerId_ES').editableSelect('clear');//清空现有数据
                $.each(result, function (i, t) {
                    $('#i_CustomerId_ES').editableSelect('add', function () {
                        $(this).val(t.Id);
                        $(this).text(t.Name);
                    });//调用add方法 通过函数的方式绑定上val和txt
                })
            })
```

以上。



重点方法：

```
.on('select.editable-select', function (e, dom) {}) //选择后触发
.editableSelect('clear');//清空现有数据
.editableSelect('add', function () {
                        $(this).val(t.Id);
                        $(this).text(t.Name);
                        //$(this).attr("remark",t.remark); //可以存储多个值 获取：$("#i_CustomerId_ES").find('option:selected').attr("remark")
                                    //$(this).attr("remark1",t.remark1);
                    });//add绑定添加上value txt
```

输入之后会出现数据框消失的情况，只需将源码中的

hiddens = this.$list.find('li').filter(function (i, li) { return $(li).text().toLowerCase().indexOf(search) < 0; }).hide().removeClass('es-visible').length;if (this.$list.find('li').length == hiddens) this.show();注释掉就好