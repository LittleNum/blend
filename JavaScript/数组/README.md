# 数组

JavaScript数组是无类型的：

1. 数组元素可以是任意类型，并且同一个数组中的不同元素页可能有不同的类型。
2. 数组的元素甚至也可能是对象或其他数组
3. 数组的索引是基于零的32位数值，从0开始，最大2^32-2
4. JavaScript数组可能是稀疏的

## 创建数组

```JavaScript
var empty = []; // An array with no elements
var primes = [2, 3, 5, 7, 11]; // An array with 5 numeric elements
var misc = [ 1.1, true, "a", ]; // 3 elements of various types + trailing comma

var base = 1024;
var table = [base, base+1, base+2, base+3];

var b = [[1,{x:1, y:2}], [2, {x:3, y:4}]];

var count = [1,,3]; // An array with 3 elements, the middle one undefined.
var undefs = [,,]; // An array with 2 elements, both undefined.

var a = new Array();
var a = new Array(10);
var a = new Array(5, 4, 3, 2, 1, "testing, testing");
```



## 数组元素的读和写

```JavaScript
var a = ["world"]; // Start with a one-element array
var value = a[0]; // Read element 0
a[1] = 3.14; // Write element 1
i = 2;
a[i] = 3; // Write element 2
a[i + 1] = "hello"; // Write element 3
a[a[i]] = a[0]; // Read elements 0 and 2, write element 3

o = {}; // Create a plain object
o[1] = "one"; // 使用整数索引

a.length // => 4

a[-1.23] = true; // This creates a property named "-1.23
a["1000"] = 0; // This the 1001st element of the array
a[1.000] // Array index 1. Same as a[1]
```

数组索引仅仅是对象属性名的一种特殊类型，所以没有越界错误。

## 稀疏数组

1. 稀疏数组就是包含从0开始的不连续索引的数组。

2. 稀疏数组通常在实现上比稠密的数组更慢、内存利用率更高。

   ```
   a = new Array(5); // No elements, but a.length is 5.
   a = []; // Create an array with no elements and length = 0.
   a[1000] = 0; // Assignment adds one element but sets length to 1001.
   ```

   

3. 在数组直接量中省略值时不会创建稀疏数组。省略的原色在数组中式存在的，其值为undefined。

   ```
   var a1 = [,,,]; // This array is [undefined, undefined, undefined]
   var a2 = new Array(3); // This array has no values at all
   0 in a1 // => true: a1 has an element with index 0
   0 in a2 // => false: a2 has no element with index 0
   ```

   

4. 当省略数组直接量中的值时，如（[1,,3]），这时所得的数组是稀疏数组，省略掉的值是不存在的

   ```
   var a1 = [,,,]; // This array is [undefined, undefined, undefined]
   var a2 = new Array(3); // This array has no values at all
   0 in a1 // => true: a1 has an element with index 0
   0 in a2 // => false: a2 has no element with index 0
   ```

   

## 数组长度

1. 稠密数组，length代表数组中元素的个数。稀疏数组，length大于元素的个数。

2. 如果未一个数组元素赋值，它的索引i大于或等于现有数组的长度时，length属性的值将设置为i+1；设置length属性为一个小于当前长度的非负整数n时，当前数组中哪些索引值大于或等于n的元素将从中删除

   ```
   a = [1,2,3,4,5]; // Start with a 5-element array.
   a.length = 3; // a is now [1,2,3].
   a.length = 0; // Delete all elements. a is [].
   a.length = 5; // Length is 5, but no elements, like new Array(5)
   ```

   

3. ES 5 中数组的length属性是只读的。

## 数组元素的添加和删除

1. 为新索引赋值

   ```
   a = [] // Start with an empty array.
   a[0] = "zero"; // And add elements to it.
   a[1] = "one";
   ```

   

2. push()方法

   ```
   a = []; // Start with an empty array
   a.push("zero") // Add a value at the end. a = ["zero"]
   a.push("one", "two") // Add two more values. a = ["zero", "one", "two"]
   ```

   

3. unshift()在数组的首部插入一个元素

4. 可以使用delete运算符删除数组元素

   1. 使用delete不会修改length属性
   2. 也不会将原元素从高索引处移下来填充已删除属性的空白
   3. 删除一个元素，编程稀疏数组

5. pop()从尾部删除元素

6. shift()从头部删除元素

7. slice()用来插入、删除或替换数组元素

## 数组遍历

1. 使用for循环

   ```
   var keys = Object.keys(o); // Get an array of property names for object o
   var values = [] // Store matching property values in this array
   for(var i = 0; i < keys.length; i++) { // For each index in the array
       var key = keys[i]; // Get the key at that index
       values[i] = o[key]; // Store the value in the values array
   }
   ```

2. 优化，只查询一次数组的长度

   ```
   for(var i = 0, len = keys.length; i < len; i++) {
   // loop body remains the same
   }
   ```

3. 遍历时，需要排除null、undefined和不存在的元素

   ```
   for(var i = 0; i < a.length; i++) {
       if (!a[i]) continue; // Skip null, undefined, and nonexistent elements
       // loop body here
   }
   ```

4. 只跳过undefined和不存在的元素

   ```
   for(var i = 0; i < a.length; i++) {
       if (a[i] === undefined) continue; // Skip undefined + nonexistent elements
       // loop body here
   }
   ```

5. 只跳过不存在的元素仍要处理存在的undefined元素

   ```
   for(var i = 0; i < a.length; i++) {
       if (!(i in a)) continue ; // Skip nonexistent elements
       // loop body here
   }
   ```

6. for/in循环处理稀疏数组

   ```
   for(var index in sparseArray) {
       var value = sparseArray[index];
       // Now do something with index and value
   }
   ```

7. for/in能枚举继承的属性名，如添加到Array.prototype中的方法。所以在数组上不应该使用for/in循环，除非使用额外的检测方法来过滤不想要的属性

   ```
   for(var i in a) {
       if (!a.hasOwnProperty(i)) continue; // Skip inherited properties
       // loop body here
   }
   for(var i in a) {
       // Skip i if it is not a non-negative integer
       if (String(Math.floor(Math.abs(Number(i)))) !== i) continue;
   }
   ```

   

8. 如果数组同时拥有对象属性和数组元素，返回的属性名很可能是按照创建的顺序而非数值的大小顺序。如果依赖遍历的顺序，不要使用for/in而用for循环

9. ES 5定义了forEach()

   ```
   var data = [1,2,3,4,5]; // This is the array we want to iterate
   var sumOfSquares = 0; // We want to compute the sum of the squares of data
   data.forEach(function(x) { // Pass each element of data to this function
   	sumOfSquares += x*x; // add up the squares
   });
   sumOfSquares // =>55 : 1+4+9+16+25
   ```

   函数式编程

## 多维数组

1. JavaScript不支持真正的多维数组，使用数组的数组来近似。

   ```javascript
   wo-dimensional array as a multiplication table:
   // Create a multidimensional array
   var table = new Array(10); // 10 rows of the table
   for(var i = 0; i < table.length; i++)
       table[i] = new Array(10); // Each row has 10 columns
       // Initialize the array
       for(var row = 0; row < table.length; row++) {
           for(col = 0; col < table[row].length; col++) {
           	table[row][col] = row*col;
   	}
   }
   // Use the multidimensional array to compute 5*7
   var product = table[5][7]; // 35
   ```

## 数组方法

1. join()——将数组中的所有元素都转化为字符串并连接在一起，可以指定分隔符，默认为逗号

   ```
   var a = [1, 2, 3]; // Create a new array with these three elements
   a.join(); // => "1,2,3"
   a.join(" "); // => "1 2 3"
   a.join(""); // => "123"
   var b = new Array(10); // An array of length 10 with no elements
   b.join('-') // => '---------': a string of 9 hyphens
   ```

2. reverse()——数组逆序，在原数组中重新排列，不生成新数组

   ```
   var a = [1,2,3];
   a.reverse().join() // => "3,2,1" and a is now [3,2,1]
   ```

3. sort()——排序，默认以字母表顺序排序

   ```
   var a = new Array("banana", "cherry", "apple");
   a.sort();
   var s = a.join(", "); // s == "apple, banana, cherry"
   ```

   可传比较函数

   ```
   var a = [33, 4, 1111, 222];
   a.sort(); // Alphabetical order: 1111, 222, 33, 4
   a.sort(function(a,b) { // Numerical order: 4, 33, 222, 1111
   	return a-b; // Returns &lt; 0, 0, or &gt; 0, depending on order
   });
   a.sort(function(a,b) {return b-a}); // Reverse numerical order
   ```

   例子：字符串数组不区分大小写排序

   ```
   a = ['ant', 'Bug', 'cat', 'Dog']
   a.sort(); // case-sensitive sort: ['Bug','Dog','ant',cat']
   a.sort(function(s,t) { // Case-insensitive sort
           var a = s.toLowerCase();
           var b = t.toLowerCase();
           if (a < b) return -1;
           if (a > b) return 1;
       	return 0;
       }); // => ['ant','Bug','cat','Dog']
   ```

4. concat()——返回一个新数组，元素包括调用concat()的原始数组的元素和concat()的每个参数

   ```
   var a = [1,2,3];
   a.concat(4, 5) // Returns [1,2,3,4,5]
   a.concat([4,5]); // Returns [1,2,3,4,5]
   a.concat([4,5],[6,7]) // Returns [1,2,3,4,5,6,7]
   a.concat(4, [5,[6,7]]) // Returns [1,2,3,4,5,[6,7]]
   ```

5. slice()——返回指定数组的一个片段或子数组，指定开始和结束的位置，负数表示相对于数组中最后一个元素的位置

   ```
   var a = [1,2,3,4,5];
   a.slice(0,3); // Returns [1,2,3]
   a.slice(3); // Returns [4,5]
   a.slice(1,-1); // Returns [2,3,4]
   a.slice(-3,-2); // Returns [3]
   ```

6. splice()——在数组中插入或删除元素，splice()能够从数组中删除元素、插入元素到数组中或者同事完成这两种操作。

   ```
   var a = [1,2,3,4,5,6,7,8];
   a.splice(4); // Returns [5,6,7,8]; a is [1,2,3,4]
   a.splice(1,2); // Returns [2,3]; a is [1,4]
   a.splice(1,1); // Returns [4]; a is [1]
   ```

   splice()的前两个参数指定了需要删除的元素，紧随的任意个数的参数制定了需要插入到数组中的原色，从第一个参数指定位置开始插入

   ```
   var a = [1,2,3,4,5];
   a.splice(2,0,'a','b'); // Returns []; a is [1,2,'a','b',3,4,5]
   a.splice(2,2,[1,2],3); // Returns ['a','b']; a is [1,2,[1,2],3,3,4,5]
   ```

   splice()会插入数组本身而不是数组的元素

7. push()和pop()——将数组当成栈来使用，两者都是替换原始数组而非生成一个新的数组

   ```
   var stack = []; // stack: []
   stack.push(1,2); // stack: [1,2] Returns 2
   stack.pop(); // stack: [1] Returns 2
   stack.push(3); // stack: [1,3] Returns 2
   stack.pop(); // stack: [1] Returns 3
   stack.push([4,5]); // stack: [1,[4,5]] Returns 2
   stack.pop() // stack: [1] Returns [4,5]
   stack.pop(); // stack: [] Returns 1
   ```

8. unshift()和shift()——在数组的头部进行元素的插入和删除操作

   ```
   var a = []; // a:[]
   a.unshift(1); // a:[1] Returns: 1
   a.unshift(22); // a:[22,1] Returns: 2
   a.shift(); // a:[1] Returns: 22
   a.unshift(3,[4,5]); // a:[3,[4,5],1] Returns: 3
   a.shift(); // a:[[4,5],1] Returns: 3
   a.shift(); // a:[1] Returns: [4,5]
   a.shift(); // a:[] Returns: 1
   ```

   当使用多个参数调用unshift()时，参数是一次性插入的，而非一次一个地插入

9. toString()和toLocaleString()

   ```
   [1,2,3].toString() // Yields '1,2,3'
   ["a", "b", "c"].toString() // Yields 'a,b,c'
   [1, [2,'c']].toString() // Yields '1,2,c
   ```

## ecmascript 5中的数组方法

1. forEach()——从头至尾，为每个元素调用指定的函数，无法提前终止遍历，只能try抛出异常

   ```
   function foreach(a,f,t) {
       try { a.forEach(f,t); }
       catch(e) {
           if (e === foreach.break) return;
           else throw e;
       }
   }
   foreach.break = new Error("StopIteration");
   ```

   

2. map()——需要有返回值

   ```
   a = [1, 2, 3];
   b = a.map(function(x) { return x*x; }); // b is [1, 4, 9]
   ```

   

3. fliter()——返回数组的一个子集，根据传递的函数返回的是true或false；会跳过稀疏数组中缺少的元素，返回的数组总是稠密的

   ```
   a = [5, 4, 3, 2, 1];
   smallvalues = a.filter(function(x) { return x < 3 }); // [2, 1]
   everyother = a.filter(function(x,i) { return i%2==0 }); // [5, 3, 1]
   ```

   为了压缩稀疏数组的空缺：

   ```
   var dense = sparse.filter(function() { return true; });
   ```

   压缩空缺并删除undefined和null元素

   ```
   a = a.filter(function(x) { return x !== undefined && x != null; });
   ```

4. every()和some()——对数组元素应用指定的函数进行判定

   ```
   a = [1,2,3,4,5];
   a.every(function(x) { return x < 10; }) // => true: all values < 10.
   a.every(function(x) { return x % 2 === 0; }) // => false: not all values even.
   ```

   ```
   a = [1,2,3,4,5];
   a.some(function(x) { return x%2===0; }) // => true a has some even numbers.
   a.some(isNaN) // => false: a has no non-numbers.
   ```

   

5. reduce()和reduceRight()——使用指定的函数将数组元素进行组合

   ```
   var a = [1,2,3,4,5]
   var sum = a.reduce(function(x,y) { return x+y }, 0); // Sum of values
   var product = a.reduce(function(x,y) { return x*y }, 1); // Product of values
   var max = a.reduce(function(x,y) { return (x>y)?x:y; }); // Largest value
   ```

   reduce()第一个参数是执行化简操作的函数；第二个参数是一个传递给函数的初始值

   如果省略初始值，将使用数组的第一个元素作为初始值

   空数组，不带初始值参数调用reduce()将导致类型错误异常

   reduceRight()是按照数组索引从高到低处理数组

   ```
   var a = [2, 3, 4]
   // Compute 2^(3^4). Exponentiation has right-to-left precedence
   var big = a.reduceRight(function(accumulator,value) {
   	return Math.pow(value,accumulator);
   })
   ```

   union计算数组并集

   ```
   var objects = [{x:1}, {y:2}, {z:3}];
   var merged = objects.reduce(union); // => {x:1, y:2, z:3}
   
   var objects = [{x:1,a:1}, {y:2,a:2}, {z:3,a:3}];
   var leftunion = objects.reduce(union); // {x:1, y:2, z:3, a:1}
   var rightunion = objects.reduceRight(union); // {x:1, y:2, z:3, a:3}
   ```

   

6. indexOf()和lastIndexOf()——搜索数组中具有给定值的元素

   ```javascript
   a = [0,1,2,1,0];
   a.indexOf(1) // => 1: a[1] is 1
   a.lastIndexOf(1) // => 3: a[3] is 1
   a.indexOf(3) // => -1: no element has value 3
   
   // Find all occurrences of a value x in an array a and return an array
   // of matching indexes
   function findall(a, x) {
       var results = [], // The array of indexes we'll return
           len = a.length, // The length of the array to be searched
           pos = 0; // The position to search from
       while(pos < len) { // While more elements to search...
           pos = a.indexOf(x, pos); // Search
           if (pos === -1) break; // If nothing found, we're done.
           results.push(pos); // Otherwise, store index in array
           pos = pos + 1; // And start next search at next element
       }
       return results; // Return array of indexes
   }
   ```

   第一个参数是需要搜索的值

   第二个参数是指定数组的一个索引，从那里开始搜索。第二个参数也可以是负数，代表相对数组末尾的偏移量

## 数组类型

1. ES 5中用```Array.isArray()```判断是否为数组

2. ES 3中使用以下代码检测：

   ```javascript
   var isArray = Function.isArray || function(o) {
       return typeof o === "object" &&
       Object.prototype.toString.call(o) === "[object Array]";
   };
   ```

   

## 类数组对象

1. 类数组对象

   ```javascript
   var a = {}; // Start with a regular empty object
   // Add properties to make it "array-like"
   var i = 0;
   while(i < 10) {
       a[i] = i * i;
       i++;
   }
   a.length = i;
   // Now iterate through it as if it were a real array
   var total = 0;
   for(var j = 0; j < a.length; j++)
   	total += a[j
   ```

   

2. 检测类数组对象

   ```javascript
   // Determine if o is an array-like object.
   // Strings and functions have numeric length properties, but are
   // excluded by the typeof test. In client-side JavaScript, DOM text
   // nodes have a numeric length property, and may need to be excluded
   // with an additional o.nodeType != 3 test.
   function isArrayLike(o) {
       if (o && // o is not null, undefined, etc.
           typeof o === "object" && // o is an object
           isFinite(o.length) && // o.length is a finite number
           o.length >= 0 && // o.length is non-negative
           o.length===Math.floor(o.length) && // o.length is an integer
           o.length < 4294967296) // o.length < 2^32
       	return true; // Then o is array-like
       else
       	return false; // Otherwise it is not
   }
   ```

   

3. 类数组对象没有继承Array.prototype，不能直接调用数组方法，可以用Function.call来调用

   ```
   var a = {"0":"a", "1":"b", "2":"c", length:3}; // An array-like object
       Array.prototype.join.call(a, "+") // => "a+b+c"
       Array.prototype.slice.call(a, 0) // => ["a","b","c"]: true array copy
       Array.prototype.map.call(a, function(x) {
       return x.toUpperCase();
   }) // => ["A","B","C"]
   ```

4. 兼容

   ```
   Array.join = Array.join || function(a,sep) {
   	return Array.prototype.join.call(a,sep);
   };
   Array.slice = Array.slice || function(a,from,to) {
   	return Array.prototype.slice.call(a,from,to);
   };
   Array.map = Array.map || function(a, f, thisArg) {
   	return Array.prototype.map.call(a, f, thisArg);
   }
   ```

   

## 作为数组的字符串

1. 字符串的行为类似于只读的数组，Array.isArray()会返回false

2. 数组方法可以应用到字符串上：

   ```
   s = "JavaScript"
   Array.prototype.join.call(s, " ") // => "J a v a S c r i p t"
   Array.prototype.filter.call(s, // Filter the characters of the string
       function(x) {
           return x.match(/[^aeiou]/); // Only match nonvowels
       }).join("") // => "JvScrpt"
   ```

   

3. 字符串只读，push，sort，reverse和splice在字符串上无效