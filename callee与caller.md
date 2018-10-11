caller返回一个函数的引用，这个函数调用了当前的函数;callee放回正在执行的函数本身的引用，它是arguments的一个属性

**caller**
caller返回一个函数的引用，这个函数调用了当前的函数。
使用这个属性要注意:
1 这个属性只有当函数在执行时才有用
2 如果在javascript程序中，函数是由顶层调用的，则返回null

functionName.caller: functionName是当前正在执行的函数。

```
var a = function() { 
	alert(a.caller); 
}
var b = function() { 
	a(); 
} 
b();
```

上面的代码中，b调用了a，那么a.caller返回的是b的引用，结果如下:

```
var b = function() { 
	a(); 
} 
```

如果直接调用a(即a在任何函数中被调用，也就是顶层调用),返回null:

```
var a = function() { 
	alert(a.caller); 
} 
var b = function() { 
	a(); 
} 
//b(); 
a();

```

输出结果:

null

**callee**
callee放回正在执行的函数本身的引用，它是arguments的一个属性
使用callee时要注意:
1 这个属性只有在函数执行时才有效
2 它有一个length属性，可以用来获得形参的个数，因此可以用来比较形参和实参个数是否一致，即比较arguments.length是否等于arguments.callee.length
3 它可以用来递归匿名函数。

```
var a = function() { 
	alert(arguments.callee); 
} 
var b = function() { 
	a(); 
} 
b();

```

a在b中被调用，但是它返回了a本身的引用，结果如下:

```
var a = function() { 
	alert(arguments.callee); 
} 

```

