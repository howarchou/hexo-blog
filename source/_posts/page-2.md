---
title: 闭包经典题目

date: 2020-04-16 14:48:41

tags:
- js
- 前端

---

记录一道关于闭包的经典题目。

``` javascript
function fun(n,o) {
  console.log(o)
  return {
    fun:function(m){
      return fun(m,n);
    }
  };
}
var a = fun(0);  a.fun(1);  a.fun(2);  a.fun(3);    //undefined,?,?,?
var b = fun(0).fun(1).fun(2).fun(3);                //undefined,?,?,?
var c = fun(0).fun(1);  c.fun(2);  c.fun(3);        //undefined,?,?,?
//问:三行a,b,c的输出分别是什么？

```


