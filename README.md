# 闭包相关问题？

闭包（closure）是Javascript语言的一个难点，也是它的特色，很多高级应用都要依靠闭包实现。

## 闭包的特性
###闭包有三个特性：

1. 函数嵌套函数
2. 函数内部可以引用外部的参数和变量
3. 参数和变量不会被垃圾回收机制回收

## 闭包的定义及其优缺点
闭包 是指有权访问另一个函数作用域中的变量的函数，创建闭包的最常见的方式就是在一个函数内创建另一个函数，通过另一个函数访问这个函数的局部变量

闭包的缺点就是常驻内存，会增大内存使用量，使用不当很容易造成内存泄露。

闭包是javascript语言的一大特点，主要应用闭包场合主要是为了：设计私有的方法和变量。

一般函数执行完毕后，局部活动对象就被销毁，内存中仅仅保存全局作用域。但闭包的情况不同！

嵌套函数的闭包
```javascript
   function aaa() {  
          var a = 1;  
          return function(){
           alert(a++)
          };  
        }         
        var fun = aaa();  
        fun();// 1 执行后 a++，，然后a还在~  
        fun();// 2   
        fun = null;//a被回收！！ 
```
闭包会使变量始终保存在内存中，如果不当使用会增大内存消耗。

## javascript的垃圾回收原理
1. 在javascript中，如果一个对象不再被引用，那么这个对象就会被GC回收；
2. 如果两个对象互相引用，而不再被第3者所引用，那么这两个互相引用的对象也会被回收。

## 使用闭包的好处
那么使用闭包有什么好处呢？使用闭包的好处是：

1. 希望一个变量长期驻扎在内存中
2. 避免全局变量的污染
3. 私有成员的存在

## 一、全局变量的累加
```javascript
var a = 1;
function abc(){
        a++;
        alert(a);
}
abc();              //2
abc();            //3
```
## 二、局部变量
```javascript
function abc(){
        var a = 1;
        a++;
        alert(a);
}
abc();                       //2
abc();                    //2
```
那么怎么才能做到变量a既是局部变量又可以累加呢？

## 三、局部变量的累加
```javascript
function outer(){
        var x=10;
        return function(){             //函数嵌套函数
                x++;
                alert(x);
        }
}
var y = outer();              //外部函数赋给变量y;
y();                 //y函数调用一次，结果为11，相当于outer()()；
y();                //y函数调用第二次，结果为12，实现了累加
```
函数声明与函数表达式
在js中我们可以通过关键字function来声明一个函数：

```javascript
function abc(){
        alert(123);
}
abc();
```
我们也可以通过一个"()"来将这个声明变成一个表达式：
```javascript
(function (){
        alert(123);
})();                   //然后通过()直接调用前面的表达式即可，因此函数可以不必写名字；
```

## 四、模块化代码，减少全局变量的污染
```javascript
var abc = (function(){      //abc为外部匿名函数的返回值
        var a = 1;
        return function(){
                a++;
                alert(a);
        }
})();
abc();    //2 ；调用一次abc函数，其实是调用里面内部函数的返回值    
abc();    //3
```

## 五、私有成员的存在
```javascript
var aaa = (function(){
        var a = 1;
        function bbb(){
                a++;
                alert(a);
        }
        function ccc(){
                a++;
                alert(a);
        }
        return {
                b:bbb,             //json结构
                c:ccc
        }
})();
aaa.b();     //2
aaa.c()      //3
```
## 六.使用匿名函数实现累加
```javascript
//使用匿名函数实现局部变量驻留内存中，从而实现累加
 function box(){
     var age = 100;
     return function(){          //匿名函数
          age++;
          return age;
     };
     
 } 
var b = box();
alert(b());
alert(b());    //即alert(box()())；
alert(b());
alert(b);            //     function () {
                        //   age++;
                       // return age;
                      //       }

b = null；  //解除引用，等待垃圾回收
```
过度使用闭包会导致性能的下降。函数里放匿名函数，则产生了闭包

## 七、在循环中直接找到对应元素的索引
```javascript
   <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
    <head>
            <meta http-equiv="Content-Type" content="text/html;charset=UTF-8" />
            <title></title>
    <script>
    window.onload = function(){
            var aLi = document.getElementsByTagName('li');
            for (var i=0;i<aLi.length;i++){
                    aLi[i].onclick = function(){        //当点击时for循环已经结束
                    alert(i);
                    };
            }
    }
    </script>
            
    </head>
    <body>
            <ul>
                    <li>123</li>
                    <li>456</li>
                    <li>789</li>
                    <li>010</li>
            </ul>
    </body>
    </html>
```
## 八、使用闭包改写上面代码
```javascript
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" 
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
    <head>
            <meta http-equiv="Content-Type" content="text/html;charset=UTF-8" />
            <title></title>
    <script>
    window.onload = function(){
            var aLi = document.getElementsByTagName('li');
            for (var i=0;i<aLi.length;i++){
                    (function(i){
                            aLi[i].onclick = function(){
                                    alert(i);
                            };
                    })(i);
            }
            };
    </script>
            
    </head>
    <body>
            <ul>
                    <li>123</li>
                    <li>456</li>
                    <li>789</li>
            </ul>
    </body>
    </html>
```
## 九.内存泄露问题
由于IE的js对象和DOM对象使用不同的垃圾收集方法，因此闭包在IE中会导致内存泄露问题，也就是无法销毁驻留在内存中的元素
```
function closure(){
    var oDiv = document.getElementById('oDiv');//oDiv用完之后一直驻留在内存中
    oDiv.onclick = function () {
        alert('oDiv.innerHTML');//这里用oDiv导致内存泄露
    };
}
closure();
//最后应将oDiv解除引用来避免内存泄露
function closure(){
    var oDiv = document.getElementById('oDiv');
    var test = oDiv.innerHTML;
    oDiv.onclick = function () {
        //alert(test);
    };
    oDiv = null;
}
```
# 参考文档
[详解js闭包](https://www.jianshu.com/p/0615640eb122)

