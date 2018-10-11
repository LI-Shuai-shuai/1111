**arguments** 是一个对应于传递给函数的参数的类数组对象。

## 语法

```
arguments
```

## 描述

`arguments`对象是所有（非箭头）函数中都可用的**局部变量**。你可以使用`arguments`对象在函数中引用函数的参数。此对象包含传递给函数的每个参数，第一个参数在索引0处。例如，如果一个函数传递了三个参数，你可以以如下方式引用他们：

```
arguments[0]
arguments[1]
arguments[2]
```

参数也可以被设置：

```
arguments[1] = 'new value';
```

`arguments`对象不是一个 `Array`。它类似于`Array`，但除了length属性和索引元素之外没有任何`Array`属性。例如，它没有 pop方法。但是它可以被转换为一个真正的`Array`：

```
var args = Array.prototype.slice.call(arguments);
var args = [].slice.call(arguments);

// ES2015
const args = Array.from(arguments);
```

对参数使用slice会阻止某些JavaScript引擎中的优化 (比如 V8 - [更多信息](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers#3-managing-arguments))。如果你关心性能，尝试通过遍历arguments对象来构造一个新的数组。另一种方法是使用被忽视的`Array`构造函数作为一个函数：

```
var args = (arguments.length === 1 ? [arguments[0]] : Array.apply(null, arguments));
```

如果调用的参数多于正式声明接受的参数，则可以使用`arguments`对象。这种技术对于可以传递可变数量的参数的函数很有用。使用 `arguments.length`来确定传递给函数参数的个数，然后使用`arguments`对象来处理每个参数。要确定函数[签名](https://developer.mozilla.org/zh-CN/docs/Glossary/Signature/Function)中（输入）参数的数量，请使用`Function.length`属性。

### 对参数使用 `typeof`

`typeof`参数返回 `object`。

```
console.log(typeof arguments);    // 'object'
// arguments 对象只能在函数内使用
function test(a){
    console.log(a,Object.prototype.toString.call(arguments));
    console.log(arguments[0],arguments[1]);
    console.log(typeof arguments[0]);
}
test(1);
/*
1 "[object Arguments]"
1 undefined
number
*/
```

可以使用索引确定单个参数的类型。

```
console.log(typeof arguments[0]); //this will return the typeof individual arguments.
```

### 对参数使用扩展语法

您还可以使用`Array.from()`方法或 扩展运算符 将参数转换为真实数组：

```
var args = Array.from(arguments);
var args = [...arguments];
```

## 属性

- `arguments.callee`

  指向当前执行的函数。

- `arguments.caller` **

  指向调用当前函数的函数。

- `arguments.length`

  指向传递给当前函数的参数数量。

- `arguments[@@iterator]`

  返回一个新的Array迭代器对象，该对象包含参数中每个索引的值。

- 注意:现在在严格模式下，`arguments`对象已与过往不同。`arguments[@@iterator]`不再与函数的实际形参之间共享，同时caller属性也被移除。

### 遍历参数求和

```
function add() {
    var sum =0,
        len = arguments.length;
    for(var i=0; i<len; i++){
        sum += arguments[i];
    }
    return sum;
}
add()                           // 0
add(1)                          // 1
add(1,2,3,4);                   // 10
```

### 定义连接字符串的函数

这个例子定义了一个函数来连接字符串。这个函数唯一正式声明了的参数是一个字符串，该参数指定一个字符作为衔接点来连接字符串。该函数定义如下：

```
function myConcat(separator) {
  var args = Array.prototype.slice.call(arguments, 1);
  return args.join(separator);
}
```

你可以传递任意数量的参数到该函数，并使用每个参数作为列表中的项创建列表。

```
// returns "red, orange, blue"
myConcat(", ", "red", "orange", "blue");

// returns "elephant; giraffe; lion; cheetah"
myConcat("; ", "elephant", "giraffe", "lion", "cheetah");

// returns "sage. basil. oregano. pepper. parsley"
myConcat(". ", "sage", "basil", "oregano", "pepper", "parsley");
```

### 定义创建HTML列表的方法

这个例子定义了一个函数通过一个字符串来创建HTML列表。这个函数唯一正式声明了的参数是一个字符。当该参数为 "`u`" 时，创建一个无序列表 (项目列表)；当该参数为 "`o`" 时，则创建一个有序列表 (编号列表)。该函数定义如下：

```
function list(type) {
  var result = "<" + type + "l><li>";
  var args = Array.prototype.slice.call(arguments, 1);
  result += args.join("</li><li>");
  result += "</li></" + type + "l>"; // end list

  return result;
}
```

你可以传递任意数量的参数到该函数，并将每个参数作为一个项添加到指定类型的列表中。例如：

```
var listHTML = list("u", "One", "Two", "Three");

/* listHTML is:

"<ul><li>One</li><li>Two</li><li>Three</li></ul>"

*/
```

### 剩余参数、默认参数和解构赋值参数

`arguments`对象可以与剩余参数、默认参数和解构赋值参数结合使用。

```
function foo(...args) {
  return args;
}
foo(1, 2, 3);  // [1,2,3]
```

在严格模式下，剩余参数、默认参数和解构赋值 参数的存在不会改变 `arguments`对象的行为，但是在非严格模式下就有所不同了。

当非严格模式中的函数**没有**包含剩余参数、默认参数和解构赋值，那么`arguments`对象中的值**会**跟踪参数的值（反之亦然）。看下面的代码：

```
function func(a) { 
  arguments[0] = 99;   // 更新了arguments[0] 同样更新了a
  console.log(a);
}
func(10); // 99
```

并且

```
function func(a) { 
  a = 99;              // 更新了a 同样更新了arguments[0] 
  console.log(arguments[0]);
}
func(10); // 99
```

当非严格模式中的函数**有**包含剩余参数、默认参数和解构赋值，那么`arguments`对象中的值**不会**跟踪参数的值（反之亦然）。相反, `arguments`反映了调用时提供的参数：

```
function func(a = 55) { 
  arguments[0] = 99; // updating arguments[0] does not also update a
  console.log(a);
}
func(10); // 10
```

并且

```
function func(a = 55) { 
  a = 99; // updating a does not also update arguments[0]
  console.log(arguments[0]);
}
func(10); // 10
```

并且

```
function func(a = 55) { 
  console.log(arguments[0]);
}
func(); // undefined
```

## 个人观点

1. arguments像数组但不是数组  称之为伪数组，是一个Arguments对象实例。
2. arguments有length属性，代表传给函数的参数个数。
3. 可以数组下标访问参数，如arguments[0]，无数组其它方法。
4. 存储的是实参不是行参。
5. 可以使用callee属性调用自身。

