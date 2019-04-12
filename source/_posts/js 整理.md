---
title: js 整理
date: 2019-3-19 17:15:06
tags: JS
---
 # js 整理

- **函数**

  - 箭头函数

  ```javascript
  let sayHi = () => alert("Hello!"); //无参数：
  
  let sum = (a, b) => a + b; //等价 let sum = function(a, b) {  return a + b;};
  
  let sum = (a, b) => {  // 花括号打开多线功能
    let result = a + b;
    return result; // 如果我们使用花括号，使用返回来获得结果
  };
  ```

