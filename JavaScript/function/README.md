# 函数	

## 函数定义

1. 函数语句和表达式两种方式定义

   ```
   //函数语句——声明
   function printprops(o) {
   for(var p in o)
   console.log(p + ": " + o[p] + "\n");
   }
   //函数表达式
   var square = function(x) { return x*x; }
   ```

   

2. 表达式定义的函数，函数的名称是可选的

3. 函数声明定义的函数可以在定义之前出现的代码调用

4. 以表达式定义的函数在定义之前无法调用（变量的声明提前，但是赋值不会提前）

5. 函数声明语句不能出现在循环，条件判断，或者try/catch/finally以及with语句中

## 函数调用

1. 4种方式调用：作为函数，作为方法，作为构造函数，通过它们的call()和apply()方法间接调用

2. ES 3和非严格的ES 5，调用上下文（this）是全局对象，严格模式下，则是undefined

3. 函数表达式作为对象的属性访问表达式，此时是一个方法，对象成为函数的调用上下文

   ```
   var calculator = { // An object literal
       operand1: 1,
       operand2: 1,
       add: function() {
           // Note the use of the this keyword to refer to this object.
           this.result = this.operand1 + this.operand2;
       }
   };
   calculator.add(); // A method invocation to compute 1+1.
   calculator.result // => 2
   ```

   

4. 任何函数的方法调用会传入一个隐式的实参——这个实参是对象，方法调用的母体。

5. 方法链——方法的返回值是一个对象，这个对象还可以再调用它的方法

6. this是关键字，非变量，所以this没有作用域的限制——嵌套的函数不会从调用它的函数中继承this，它的this指向调用它的对象

   ```JavaScript
   var o = { // An object o.
       m: function() { // Method m of the object.
           var self = this; // Save the this value in a variable.
           console.log(this === o); // Prints "true": this is the object o.
           f(); // Now call the helper function f().
           function f() { // A nested function f
               console.log(this === o); // "false": this is global or undefined
               console.log(self === o); // "true": self is the outer this value.
           }
       }
   };
   o.m(); // Invoke the method m on the object o.
   ```

   

7. 如果函数或者方法调用之前带有关键字new，就构成构造函数调用

8. 构造函数中的this指向这个新创建的对象

9. call和apply可以显示地指定调用所需的this值，即使该函数不是那个对象的方法

## 函数的实参和形参

1. 调用函数时传入的实参比函数声明时指定的形参个数要少，则剩下的形参设置为undefined，所以需要给省略的参数赋一个默认值

   ```JavaScript
   // Append the names of the enumerable properties of object o to the
   // array a, and return a. If a is omitted, create and return a new array.
   function getPropertyNames(o, /* optional */ a) {
       if (a === undefined) a = []; // If undefined, use a new array
       for(var property in o) a.push(property);
       return a;
   }
   // This function can be invoked with 1 or 2 arguments:
   var a = getPropertyNames(o); // Get o's properties into a new array
   getPropertyNames(p,a); // append p's properties to that array
   
   a = a || [];
   ```

   

2. 调用函数时传入的实参个数超过形参个数时，不能直接获得未命名值的引用——在函数体内，标识符arguments是指向实参对象的引用，是一个类数组对象

   ```
   function f(x, y, z)
   {
       // First, verify that the right number of arguments was passed
       if (arguments.length != 3) {
       	throw new Error("function f called with " + arguments.length +
       	"arguments, but it expects 3 arguments.");
   	}
   // Now do the actual function...
   }
   ```

   

3. 不定实参函数——接受任意个数的实参

   ```JavaScript
   function max(/* ... */) {
       var max = Number.NEGATIVE_INFINITY;
       // Loop through the arguments, looking for, and remembering, the biggest.
       for(var i = 0; i < arguments.length; i++)
           if (arguments[i] > max) max = arguments[i];
           // Return the biggest
       return max;
   }
   var largest = max(1, 10, 100, 2, 3, 1000, 4, 5, 10000, 6); // => 10000
   ```

   

4. 通过实参名字来修改实参值的话，通过arguments[]数组可以获取到更改后的值

   ```JavaScript
   function f(x) {
       console.log(x); // Displays the initial value of the argument
       arguments[0] = null; // Changing the array element also changes x!
       console.log(x); // Now displays "null"
   }
   x和arguments[0]指代同一个值
   ```

   

5. ES 5中移除了上述的特殊特性

6. ES 5的严格模式下，读写callee和caller属性会产生类型错误。callee指代当前正在执行的函数。caller指代调用当前正在执行的函数的函数。

   ```JavaScript
   var factorial = function(x) {
       if (x <= 1) return 1;
   	return x * arguments.callee(x-1);
   };
   ```

   

7. 形参过多时，可以将传入的实参写入一个单独的对象

   ```javascript
   // Copy length elements of the array from to the array to.
   // Begin copying with element from_start in the from array
   // and copy that element to to_start in the to array.
   // It is hard to remember the order of the arguments.
   function arraycopy(/* array */ from, /* index */ from_start,
                   /* array */ to, /* index */ to_start,
                   /* integer */ length)
   {
   	// code goes here
   }
   // This version is a little less efficient, but you don't have to
   // remember the order of the arguments, and from_start and to_start
   // default to 0.
   function easycopy(args) {
       arraycopy(args.from,
       args.from_start || 0, // Note default value provided
       args.to,
       args.to_start || 0,
       args.length);
   }
   // Here is how you might invoke easycopy():
   var a = [1,2,3,4], b = [];
   easycopy({from: a, to: b, length: 4});
   ```

   

8. 实参类型做类型检查

   ```JavaScript
   function sum(a) {
   	if (isArrayLike(a)) {
           var total = 0;
           for(var i = 0; i < a.length; i++) { // Loop though all elements
               var element = a[i];
               if (element == null) continue; // Skip null and undefined
               if (isFinite(element)) total += element;
               else throw new Error("sum(): elements must be finite numbers");
   		}
   		return total;
   	}
   	else throw new Error("sum(): argument must be array-like");
   }
   ```

   

## 作为值的函数

1. 函数可以赋值给变量

   ```JavaScript
   function square(x) { 
   	return x*x; 
   }
   ```

   

2. 函数除了赋值给变量，还可以赋值给对象的属性，此时函数就称为方法

   ```JavaScript
   // We define some simple functions here
   function add(x,y) { return x + y; }
   function subtract(x,y) { return x - y; }
   function multiply(x,y) { return x * y; }
   function divide(x,y) { return x / y; }
   // Here's a function that takes one of the above functions
   // as an argument and invokes it on two operands
   function operate(operator, operand1, operand2) {
   	return operator(operand1, operand2);
   }
   // We could invoke this function like this to compute the value (2+3) + (4*5):
   var i = operate(add, operate(add, 2, 3), operate(multiply, 4, 5));
   // For the sake of the example, we implement the simple functions again,
   // this time using function literals within an object literal;
   var operators = {
       add: function(x,y) { return x+y; },
       subtract: function(x,y) { return x-y; },
       multiply: function(x,y) { return x*y; },
       divide: function(x,y) { return x/y; },
       pow: Math.pow // Works for predefined functions too
   };
   // This function takes the name of an operator, looks up that operator
   // in the object, and then invokes it on the supplied operands. Note
   // the syntax used to invoke the operator function.
   function operate2(operation, operand1, operand2) {
       if (typeof operators[operation] === "function")
       	return operators[operation](operand1, operand2);
       else throw "unknown operator";
   }
   // Compute the value ("hello" + " " + "world") like this:
   var j = operate2("add", "hello", operate2("add", " ", "world"));
   // Using the predefined Math.pow() function:
   var k = operate2("pow", 10, 2);
   ```

   

3. 函数可以拥有属性，使信息在函数调用过程中持久化——不需要放到全局变量中

   ```JavaScript
   // Initialize the counter property of the function object.
   // Function declarations are hoisted so we really can
   // do this assignment before the function declaration.
   uniqueInteger.counter = 0;
   // This function returns a different integer each time it is called.
   // It uses a property of itself to remember the next value to be returned.
   function uniqueInteger() {
   	return uniqueInteger.counter++; // Increment and return counter property
   }
   ```

   

4. 函数使用自身的属性缓存上一次的计算结果

   ```javascript
   // Compute factorials and cache results as properties of the function itself.
   function factorial(n) {
       if (isFinite(n) && n>0 && n==Math.round(n)) { // Finite, positive ints only
       if (!(n in factorial)) // If no cached result
           factorial[n] = n * factorial(n-1); // Compute and cache it
           return factorial[n]; // Return the cached result
       }
       else return NaN; // If input was bad
   }
   factorial[1] = 1; // Initialize the cache to hold this base case.
   ```

   

## 作为命名空间的函数

1. 在函数中声明的变量在整个函数体内是可见的，在函数外是不可见的。不在任何函数内声明的变量是全局变量。

2. 通过将代码放入一个函数内，然后调用该函数，使全局变量变成函数内的局部变量。

   ```javascript
   function mymodule() {
       // Module code goes here.
       // Any variables used by the module are local to this function
       // instead of cluttering up the global namespace.
   }
   mymodule(); // But don't forget to invoke the function!
   
   (function() { // mymodule function rewritten as an unnamed expression
   	// Module code goes here.
   }()); // end the function literal and invoke it now.
   ```

   

3. 使用圆括号，JavaScript解释器将其解析为函数定义表达式，否则解析为函数声明语句

4. 特定场景下返回带补丁的extend()版本——for/in循环是否会枚举测试对象的toString属性

   ```javascript
   // Define an extend function that copies the properties of its second and
   // subsequent arguments onto its first argument.
   // We work around an IE bug here: in many versions of IE, the for/in loop
   // won't enumerate an enumerable property of o if the prototype of o has
   // a nonenumerable property by the same name. This means that properties
   // like toString are not handled correctly unless we explicitly check for them.
   var extend = (function() { // Assign the return value of this function
       // First check for the presence of the bug before patching it.
       for(var p in {toString:null}) {
           // If we get here, then the for/in loop works correctly and we return
           // a simple version of the extend() function
           return function extend(o) {
               for(var i = 1; i < arguments.length; i++) {
               	var source = arguments[i];
               	for(var prop in source) o[prop] = source[prop];
               }
           	return o;
           };
       }
       // If we get here, it means that the for/in loop did not enumerate
       // the toString property of the test object. So return a version
       // of the extend() function that explicitly tests for the nonenumerable
       // properties of Object.prototype.
   	return function patched_extend(o) {
       for(var i = 1; i < arguments.length; i++) {
           var source = arguments[i];
           // Copy all the enumerable properties
           for(var prop in source) o[prop] = source[prop];
               // And now check the special-case properties
               for(var j = 0; j < protoprops.length; j++) {
               	prop = protoprops[j];
              		if (source.hasOwnProperty(prop)) o[prop] = source[prop];
               }
           }
       	return o;
       };
       // This is the list of special-case properties we check for
       var protoprops = ["toString", "valueOf", "constructor", "hasOwnProperty",
       "isPrototypeOf", "propertyIsEnumerable","toLocaleString"];
   }());
   ```

   

## 闭包

1. JavaScript采用词法作用域，函数的执行依赖于变量作用域，是在函数定义时决定，而不是调用时决定的

2. 闭包——函数对象可以通过作用域链相互关联起来，函数体内部的变量都可以保存在函数作用域内

3. 词法作用域规则——作用域链是函数定义的时候创建的

   ```JavaScript
   var scope = "global scope"; // A global variable
   function checkscope() {
       var scope = "local scope"; // A local variable
       function f() { return scope; } // Return the value in scope here
       return f();
   }
   checkscope() // => "local scope"
   
   var scope = "global scope"; // A global variable
   function checkscope() {
       var scope = "local scope"; // A local variable
       function f() { return scope; } // Return the value in scope here
       return f;
   }
   checkscope()() // What does this return?
   ```

   

4. 改造uniqueInteger()函数，使局部变量变成私有状态

   ```JavaScript
   var uniqueInteger = (function() { // Define and invoke
           var counter = 0; // Private state of function below
           return function() { return counter++; };
   	}());
   ```

   

5. 类似counter的私有变量可以被多个嵌套函数访问

   ```JavaScript
   function counter() {
       var n = 0;
       return {
           count: function() { return n++; },
           reset: function() { n = 0; }
       };
   }
   var c = counter(), d = counter(); // Create two counters
   c.count() // => 0
   d.count() // => 0: they count independently
   c.reset() // reset() and count() methods share state
   c.count() // => 0: because we reset c
   d.count() // => 1: d was not reset
   ```

   

6. 将闭包合并为存取器方法getter和setter

   ```JavaScript
   function counter(n) { // Function argument n is the private variable
       return {
           // Property getter method returns and increments private counter var.
           get count() { return n++; },
           // Property setter doesn't allow the value of n to decrease
           set count(m) {
           if (m >= n) n = m;
           	else throw Error("count can only be set to a larger value");
           }
       };
   }
   var c = counter(1000);
   c.count // => 1000
   c.count // => 1001
   c.count = 2000
   c.count // => 2000
   c.count = 2000 // => Error!
   ```

   

7. 使用闭包技术共享私有状态

   ```JavaScript
   // This function adds property accessor methods for a property with
   // the specified name to the object o. The methods are named get<name>
   // and set<name>. If a predicate function is supplied, the setter
   // method uses it to test its argument for validity before storing it.
   // If the predicate returns false, the setter method throws an exception.
   //
   // The unusual thing about this function is that the property value
   // that is manipulated by the getter and setter methods is not stored in
   // the object o. Instead, the value is stored only in a local variable
   // in this function. The getter and setter methods are also defined
   // locally to this function and therefore have access to this local variable.
   // This means that the value is private to the two accessor methods, and it
   // cannot be set or modified except through the setter method.
   function addPrivateProperty(o, name, predicate) {
       var value; // This is the property value
       
       // The getter method simply returns the value.
       o["get" + name] = function() { return value; };
       
       // The setter method stores the value or throws an exception if
       // the predicate rejects the value.
       o["set" + name] = function(v) {
           if (predicate && !predicate(v))
               throw Error("set" + name + ": invalid value " + v);
           else
               value = v;
       };
   }
   
   // The following code demonstrates the addPrivateProperty() method.
   var o = {}; // Here is an empty object
   // Add property accessor methods getName and setName()
   // Ensure that only string values are allowed
   addPrivateProperty(o, "Name", function(x) { return typeof x == "string"; });
   
   o.setName("Frank"); // Set the property value
   console.log(o.getName()); // Get the property value
   o.setName(0); // Try to set a value of the wrong type
   ```

   

8. 闭包使用时，注意将不希望共享的变量共享给其他的闭包

   ```javascript
   // This function returns a function that always returns v
   function constfunc(v) { return function() { return v; }; }
   // Create an array of constant functions:
   var funcs = [];
   for(var i = 0; i < 10; i++) funcs[i] = constfunc(i);
   // The function at array element 5 returns the value 5.
   funcs[5]() // => 5
   
   // Return an array of functions that return the values 0-9
   function constfuncs() {
       var funcs = [];
       for(var i = 0; i < 10; i++)
       	funcs[i] = function() { return i; };
       return funcs;
   }
   var funcs = constfuncs();
   funcs[5]() // What does this return? -— 10
   ```

   

9. 闭包在外部函数内是无法访问this的，除非外部函数将this转存为一个变量

## 函数属性、方法和构造函数

1. 函数的length代表形参的个数

   ```JavaScript
   // This function uses arguments.callee, so it won't work in strict mode.
   function check(args) {
       var actual = args.length; // The actual number of arguments
       var expected = args.callee.length; // The expected number of arguments
       if (actual !== expected) // Throw an exception if they differ.
      		throw Error("Expected " + expected + "args; got " + actual);
   }
   function f(x, y, z) {
       check(arguments); // Check that the actual # of args matches expected #.
       return x + y + z; // Now do the rest of the function normally.
   }
   ```

   

2. prototype属性

3. call()和apply()

   ```JavaScript
   f.call(o, 1, 2);
   f.apply(o, [1,2]);
   ```

   ES 5严格模式中，call和apply的第一个实参都会变为this的值（即使是null和undefined），在ES 3和非严格模式中，传入的null和undefined会被全局变量代替

4. 动态修改已有方法-monkey-patching

   ```JavaScript
   function trace(o, m) {
       var original = o[m]; // Remember original method in the closure.
       o[m] = function() { // Now define the new method.
           console.log(new Date(), "Entering:", m); // Log message
           var result = original.apply(this, arguments); // Invoke original.
           console.log(new Date(), "Exiting:", m); // Log message.
           return result; // Return result.
       };
   }
   ```

   

5. bind()方法——将函数绑定到某个对象

   实现bind方法

   ```JavaScript
   function bind(f, o) {
       if (f.bind) return f.bind(o); // Use the bind method, if there is one
       else return function() { // Otherwise, bind it like this
       	return f.apply(o, arguments);
       };
   }
   ```

   

6. 柯里化

   ```JavaScript
   var sum = function(x,y) { return x + y }; // Return the sum of 2 args
   // Create a new function like sum, but with the this value bound to null
   // and the 1st argument bound to 1. This new function expects just one arg.
   var succ = sum.bind(null, 1);
   succ(2) // => 3: x is bound to 1, and we pass 2 for the y argument
   function f(y,z) { return this.x + y + z }; // Another function that adds
   var g = f.bind({x:1}, 2); // Bind this and y
   g(3) // => 6: this.x is bound to 1, y is bound to 2 and z is 3
   ```

   

7. ES 3的Function.bind方法

   ```JavaScript
   if (!Function.prototype.bind) {
       Function.prototype.bind = function(o /*, args */) {
           // Save the this and arguments values into variables so we can
           // use them in the nested function below.
           var self = this, boundArgs = arguments;
           // The return value of the bind() method is a function
           return function() {
               // Build up an argument list, starting with any args passed
               // to bind after the first one, and follow those with all args
               // passed to this function.
               var args = [], i;
               for(i = 1; i < boundArgs.length; i++) args.push(boundArgs[i]);
               for(i = 0; i < arguments.length; i++) args.push(arguments[i]);
               // Now invoke self as a method of o, with those arguments
               return self.apply(o, args);
           };
       };
   }
   ```

   

8. toString()方法——返回源码，内置函数返回[native code]

9. Function()构造函数

   ```JavaScript
   var f = new Function("x", "y", "return x*y;");
   ```

   - Function()构造函数允许JavaScript在运行时动态地创建并编译函数

   - 每次调用Function()构造函数都会解析函数体，并创建新的函数对象

   - 它所创建的函数并不是用词法作用域，总是在顶层函数执行——无法捕获局部作用域

     ```JavaScript
     var scope = "global";
     function constructFunction() {
     	var scope = "local";
     	return new Function("return scope"); // Does not capture the local scope!
     }
     // This line returns "global" because the function returned by the
     // Function() constructor does not use the local scope.
     constructFunction()(); // => "global
     ```

     

10. 可调用的对象——callable object

    ```JavaScript
    检测一个对象是否是真正的函数对象
    function isFunction(x) {
    	return Object.prototype.toString.call(x) === "[object Function]";
    }
    ```

    

## 函数式编程

1. 使用函数处理数组——计算平均值和标准差

   ```JavaScript
   // First, define two simple functions
   var sum = function(x,y) { return x+y; };
   var square = function(x) { return x*x; };
   // Then use those functions with Array methods to compute mean and stddev
   var data = [1,1,3,5,5];
   var mean = data.reduce(sum)/data.length;
   var deviations = data.map(function(x) {return x-mean;});
   var stddev = Math.sqrt(deviations.map(square).reduce(sum)/(data.length-1));
   ```

   ```JavaScript
   //ES 3 实现自定义的map()和reduce()函数
   // Call the function f for each element of array a and return
   // an array of the results. Use Array.prototype.map if it is defined.
   var map = Array.prototype.map
   	? function(a, f) { return a.map(f); } // Use map method if it exists
   	: function(a, f) { // Otherwise, implement our own
           var results = [];
           for(var i = 0, len = a.length; i < len; i++) {
               if (i in a) results[i] = f.call(null, a[i], i, a);
           }
           return results;
   	};
   // Reduce the array a to a single value using the function f and
   // optional initial value. Use Array.prototype.reduce if it is defined.
   var reduce = Array.prototype.reduce
   	? function(a, f, initial) { // If the reduce() method exists.
           if (arguments.length > 2)
           	return a.reduce(f, initial); // If an initial value was passed.
           else 
               return a.reduce(f); // Otherwise, no initial value.
       }
       : function(a, f, initial) { // This algorithm from the ES5 specification
           var i = 0, len = a.length, accumulator;
           // Start with the specified initial value, or the first value in a
           if (arguments.length > 2) accumulator = initial;
           else { // Find the first defined index in the array
               if (len == 0) throw TypeError();
               while(i < len) {
                   if (i in a) {
                       accumulator = a[i++];
                       break;
                   }
                   else i++;
               }
               if (i == len) throw TypeError();
           }
           // Now call f for each remaining element in the array
           while(i < len) {
               if (i in a)
               	accumulator = f.call(undefined, accumulator, a[i], i, a);
               i++;
           }
           return accumulator;
       }
   ```

   ```JavaScript
   var data = [1,1,3,5,5];
   var sum = function(x,y) { return x+y; };
   var square = function(x) { return x*x; };
   var mean = reduce(data, sum)/data.length;
   var deviations = map(data, function(x) {return x-mean;});
   var stddev = Math.sqrt(reduce(map(deviations, square), sum)/(data.length-1));
   ```

   

2. 高阶函数——操作函数的函数，接收一个或多个函数作为参数，返回一个新的函数

   ```javascript
   //not()函数时一个高阶函数
   function not(f) {
       return function() { // Return a new function
           var result = f.apply(this, arguments); // that calls f
           return !result; // and negates its result.
       };
   }
   var even = function(x) { // A function to determine if a number is even
   	return x % 2 === 0;
   };
   var odd = not(even); // A new function that does the opposite
   [1,1,3,5,5].every(odd); // => true: every element of the array is odd
   ```

   ```JavaScript
   function mapper(f) {
   	return function(a) { return map(a, f); };
   }
   var increment = function(x) { return x+1; };
   var incrementer = mapper(increment);
   incrementer([1,2,3]) // => [2,3,4]
   ```

   ```JavaScript
   function compose(f,g) {
       return function() {
           // We use call for f because we're passing a single value and
           // apply for g because we're passing an array of values.
       	return f.call(this, g.apply(this, arguments));
       };
   }
   var square = function(x) { return x*x; };
   var sum = function(x,y) { return x+y; };
   var squareofsum = compose(square, sum);
   squareofsum(2,3) // => 25
   ```

3. 不完全函数——把一次完整的函数调用拆成多次函数调用，每次传入的实参都是完整实参的一部分，每次拆分开的函数叫做不完全函数，每次函数调用叫做不完全调用

   ```javascript
   //将类数组对象转换为真正的数组
   function array(a, n) { return Array.prototype.slice.call(a, n || 0); }
   
   //这个函数的实参传递至左侧
   function partialLeft(f /*, ...*/) {
   	var args = arguments; // Save the outer arguments array
   	return function() { // And return this function
           var a = array(args, 1); // Start with the outer args from 1 on.
           a = a.concat(array(arguments)); // Then add all the inner arguments.
           return f.apply(this, a); // Then invoke f on that argument list.
       };
   }
   
   //这个函数的实参传递至右侧
   function partialRight(f /*, ...*/) {
       var args = arguments; // Save the outer arguments array
       return function() { // And return this function
           var a = array(arguments); // Start with the inner arguments.
           a = a.concat(array(args,1)); // Then add the outer args from 1 on.
           return f.apply(this, a); // Then invoke f on that argument list.
   	};
   }
   
   //这个函数的实参被用作模板
   //实参列表中的undefined值都被填充
   function partial(f /*, ... */) {
       var args = arguments; // Save the outer arguments array
       return function() {
           var a = array(args, 1); // Start with an array of outer args
           var i=0, j=0;
           // Loop through those args, filling in undefined values from inner
           for(; i < a.length; i++)
               if (a[i] === undefined) 
                   a[i] = arguments[j++];
               // Now append any remaining inner arguments
               a = a.concat(array(arguments, j))
           return f.apply(this, a);
       };
   }
   ```

   ```JavaScript
   var f = function(x,y,z) { return x * (y - z); };
   // Notice how these three partial applications differ
   partialLeft(f, 2)(3,4) // => -2: Bind first argument: 2 * (3 - 4)
   partialRight(f, 2)(3,4) // => 6: Bind last argument: 3 * (4 - 2)
   partial(f, undefined, 2)(3,4) // => -6: Bind middle argument: 3 * (2 - 4)
   ```

   利用已有函数来定义新的函数：

   ```JavaScript
   var increment = partialLeft(sum, 1);
   var cuberoot = partialRight(Math.pow, 1/3);
   String.prototype.first = partial(String.prototype.charAt, 0);
   String.prototype.last = partial(String.prototype.substr, -1, 1)
   ```

   当将不完全调用和其他高阶函数整合在一起

   ```JavaScript
   var not = partialLeft(compose, function(x) { return !x; });
   var even = function(x) { return x % 2 === 0; };
   var odd = not(even);
   var isNumber = not(isNaN)
   ```

   使用不完全调用的组合来重新组织求平均数和标准差的代码——函数式编程！！

   ```JavaScript
   var data = [1,1,3,5,5]; // Our data
   var sum = function(x,y) { return x+y; }; // Two elementary functions
   var product = function(x,y) { return x*y; };
   var neg = partial(product, -1); // Define some others
   var square = partial(Math.pow, undefined, 2);
   var sqrt = partial(Math.pow, undefined, .5);
   var reciprocal = partial(Math.pow, undefined, -1);
   // Now compute the mean and standard deviation. This is all function
   // invocations with no operators, and it starts to look like Lisp code!
   var mean = product(reduce(data, sum), reciprocal(data.length));
   var stddev = sqrt(product(reduce(map(data,
                                           compose(square,
                                           		partial(sum, neg(mean)))),
                                     sum),
                              reciprocal(sum(data.length,-1))));
   ```

   

4. 记忆函数——将上次的计算结果缓存起来，在函数式编程中，这种缓存技巧叫做“记忆”。

   memorize缓存数据到局部变量

   ```javascript
   // Return a memoized version of f.
   // It only works if arguments to f all have distinct string representations.
   function memoize(f) {
       var cache = {}; // Value cache stored in the closure.
       return function() {
           // Create a string version of the arguments to use as a cache key.
           var key = arguments.length + Array.prototype.join.call(arguments,",");
           if (key in cache) return cache[key];
           else return cache[key] = f.apply(this, arguments);
       };
   }
   ```

   ```JavaScript
   //欧几里得算法-求两个整数的最大公约数
   function gcd(a,b) { // Type checking for a and b has been omitted
       var t; // Temporary variable for swapping values
       if (a < b) t=b, b=a, a=t; // Ensure that a >= b
       while(b != 0) t=b, b = a%b, a=t; // This is Euclid's algorithm for GCD
       return a;
   }
   var gcdmemo = memoize(gcd);
   gcdmemo(85, 187) // => 17
   // Note that when we write a recursive function that we will be memoizing,
   // we typically want to recurse to the memoized version, not the original.
   var factorial = memoize(function(n) {
   	return (n <= 1) ? 1 : n * factorial(n-1);
   });
   factorial(5) // => 120. Also caches values for 4, 3, 2 and 1.
   ```