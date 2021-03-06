# 数据读取

##1. 数据类型:

1. 字面量 : 字面量只代表本身,不存储在特定的位置,包括字符串,数字,数组,函数,null,undefiend等.
2. 本地变量 : var 定义的数据存储单元;
3. 数组元素 : js数组对象内部,以数字作为索引
4. 对象成员 : 存储在js对象内部,以字符串为索引

##2. 作用域

###2.1 基本知识
每个函数可以理解为`Function`对象的一个实例,里面包含编程访问属性和一系列不能通过代码访问而仅供js引擎存取的内部属性,其中包含`[[Scope]]`

`[[Scope]]`包含一个函数被创建时的作用域中对象的集合.既`作用域链`

```javascript
function add (num1,num2){
    var sum = num1 +num2;
    return sum;
}

var total = add(5,10);
```

执行一个函数时,会创建一个执行环境(`独一无二,执行完就销毁`)

每个执行环境也有自己的作用域链,用于解析标识符

创建执行环境时,函数中的变量会顺序的复制到执行环境的作用域链中,称为`活动对象`,包含所有局部便利、命名参数、参数集合、`this`.

![作用域链](QQ20151223-1.png)

活动对象在作用域链的最顶端,每遇到一个变量就会经历`标识符`的解析

解析过程: 从顶端作用域查找同名标识符.

所以可以理出两个原理

1. 同名变量后者覆盖前者
2. 各作用域存在同名变量,只取最前面的变量.
3. 越底层的作用域中查找变量,耗时最多.所以当多次用到底端变量,用一个局部变量 = 底端变量.
4. 使用函数可以在声明函数之前.
    
    因为执行一个函数,会创建一个`执行环境`,创建时,会把所有函数中的变量放到作用域链中,当`执行环境`创建完了才开始执行代码.

###2.2 改变作用域链

改变作用域: `with` & `try-catch`

1. `with`

    ```javascript
    with(document){
        var links = getElementByTagName("a");
    }
        
    ```
    这时,局部变量所在的`活动对象`作用域链上多了一层,就是可变对象(这里是document),这样访问局部变量都变成了第二层,所以使用时慎重考虑.
2. `try-catch`

    当异常发生后,`catch`中异常对象就会在作用域链的第一层.
    
    所以如果在`catch`中执行过多的代码,会导致和`with`一样的错误
    
    寻找每个局部变量都要先通过`异常对象`,所以建议:
    推荐使用:
    
    ```javascript
    try{
        ...
    } catch(ex){
        handleError(ex);
    }
    ```
    这样只有一个局部变量,那样到了`handleError`函数中,异常对象`ex`会在我们的活动对象层中.
    ```
    
###2.3 闭包 作用域 内存

```javascript
function K_404(){
    var id = 1;
    document.getElementByID("save-btn").onclick = function(event){
        k(id);
    }
}
```

当闭包代码环境生成时,闭包中的作用域链

1. 活动对象(闭包);
2. 活动对象(K_404);
3. 全局对象;

所以K_404的执行环境一直不能释放.

###2.4 原型

原型属于对象成员.

原型中一旦你实例化对象

该对象就会包含属性`_proto_`,从`Object`实例中获取.

`_proto_`中包含`toString()`方法

```javascript
var K = {
    name : 'mzk';
}

book.hasOwnProperty('name') //true
book.hasOwnProperty('toString') //false

'name' in book //true
'toString' in book //true
```

###2.4.1 原型链

```javascript
function K (name){
    this.name = name;
}

K.protoype.say_name = function (){
    ...
}

var k1 = new K('404');
var k2 = new K('404_');

k1 instanceof K ;//true
k1 instanceof Object //true
k1.toString(); //[object Object]

```
![原型链](QQ20151223-2.png)

k1的原型是K,K的原型是Object.

这其实可以看明白为何只给原型添加方法/属性,其子类都能在后续中使用.

因为他们用的是同一个原型.

而K原型中的构造对象又引用的是K函数,K函数分别别k1,k2实例化.所以能有不同的属性值.

###2.4.2总结

1. 使用多次的深层作用域,放在局部变量中.
2. `with` & `catch` 对应的对象会在活动对象上层.

