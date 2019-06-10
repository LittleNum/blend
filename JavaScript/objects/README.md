# 第6章 对象

## 创建对象

1. **对象直接量**

2. **通过new创建对象**

3. **原型**

   1. 对象直接量创建的对象具有同一个原型对象——Object.prototype获得引用
   2. 通过关键字new和构造函数调用创建的对象——构造函数的prototype属性的值
   3. Object.prototype对象没有原型
   4. 内置构造函数的原型继承自Object.prototype

4. **Object.create()**

   1. 第一个参数为原型

   2. ```javascript
      var o2 = Object.create(null)  //o2不继承任何属性和方法
      ```

   3. ```javascript
      var o3 = Object.create(Object.prototype) //普通的空对象
      ```

   4. 通过任意原型创建新对象

      ```javascript
      function inherit(p){
      	if(p==null) throw TypeError()
      	if(Object.create){
      		return Object.create(p)
      	}
      	var t = typeof p
      	if(t != "object" && t !== "function") throw TypeError()
      	function f(){}
      	f.prototype = p
      	return new f()
      }
      ```

   5. inherit()函数放置库函数修改对象属性。只能修改继承来的属性，不改变原始对象。

      

## 属性的查询和设置

ECMAScript 3，点运算符的标识符不能是保留字，但是可以用方括号访问，如object["for"].ES 5放宽了限制

1. **作为关联数组的对象**

   1. js对象都是关联数组——通过字符串索引而不是数字索引

   2. for/in循环遍历关联数组

      ```javascript
      function getValue(object){
      	var key
      	for(key in object){
      		let val = object[key]
      		console.info(`key=${key},val=${val}`)
      	}
      }
      ```

2. **继承**

   1. 原型链

      ```
      var o ={}
      o.x = 1
      var p = inherit(o)
      p.y = 2
      var q = inherit(p)
      q.z = 4
      var s = q.toString() // 4 toString继承自Object.prototype
      q.x + q.y  //3
      ```

      

   2. 属性查询时会检查原型链，设置属性与继承无关——总是在对象上创建属性和赋值，不会修改原型链

   3. 例外：如果o继承自属性x，而这个属性时一个具有setter方法的accessor属性，那么此时调用setter方法而不是给o创建一个属性x——setter方法由对象o调用，而不是原型对象调用

3. **属性访问错误**

   1. 查询不存在的属性，返回undefined

      ```javascript
      book.title //undefined	
      ```

   2. 对象不存在会报错，null和undefined值没有属性

      ```javascript
      book.title.length //error
      
      //判空
      var len
      if(book){
          if(book.title){
              len = book.title.length
          }
      }
      //常用——&&的短路行为
      var len = book && book.title && book.title.length
      ```

   3. 内置构造函数的原型是只读的——ES 5已修复该bug，在严格模式中，任何失败的属性设置会抛出类型错误异常

      ```JavaScript
      Object.prototype = o //赋值失败，不会报错
      ```

   4. 以下场景给对象o设置属性p会失败

      1. o中的属性p是只读（defineProperty()方法中有一个例外，可以对可配置的只读属性重新赋值）
      2. o中的属性p是继承属性，且它是只读的——不同通过同名自有属性覆盖只读的继承属性
      3. o中不存在自有属性p：o没有使用setter方法继承属性p，并且o的可扩展性是false

## 删除属性

1. delete可以删除对象的属性

   ```JavaScript
   delete object.property
   delete object["property"]
   ```

   

2. delete只能删除自由属性，不能删除继承（影响原型）

3. 成功返回true，如果delete后不是属性访问表达式，也返回true

   ```
   //以下都返回true
   delete o.x
   delete o.x
   delete o.toString
   delete 1
   ```

4. delete不能删除可配置性为false的属性（可删除不可扩展对象的可配置属性）

5. 某些内置对象的属性是不可配置的，严格模式下，删除不可配置属性会报类型错误，非严格模式下，返回false

   ```
   delete Object.prototype	 //不可配置
   var x = 1
   delete this.x	//不能删除
   function f(){}
   delete this.f   //不能删除
   ```

   

6. 非严格模式下删除全局对象的可配置属性时，可省略全局对象的引用；严格模式会报错



## 检测属性

1. 通过in，hasOwnProperty()和propertyIsEnumerable()判断某个属性是否在某个对象中

   ```
   var o = {x:1}
   "x" in o
   "y" in o
   "toString" in o
   
   o.hasOwnProperty("x")   //自有属性
   o.hasOwnProperty("y")
   o.hasOwnProperty("toString")
   ```

2. propertyIsEnumerable只有检测到是自有属性且这个属性的可枚举性为true才返回true

   1. 某些内置属性是不可枚举的
   2. 通常由JavaScript代码创建的属性都是可枚举的

3. 使用"!=="判断属性是否是undefined

   

## 枚举属性

1. for/in遍历对象中所有可枚举的属性

   ```javascript
   for(p in 0){
   	if(!o.hasOwnProperty(p)) continue;   //跳过继承的属性
   }
   for(p in o){
   	if(typeof o[p] === "function") continue;   //跳过方法
   }
   ```

   

2. extend，merge等方法

   ```javascript
   function extend(o, p){
   	for(prop in p){
   		o[prop] = p[prop]
   	}
   	return o
   }
   
   function merge(o, p){
   	for(prop in p){
   		if(o.hasOwnPropery(prop)){
   			continue;
   		}
   		o[prop] = p[prop]
   	}
   	return o
   }
   
   function restrict(o,p){
   	for(prop in o){
   		if(!(prop in p)){
   			delete o[prop]
   		}
   	}
   	return o
   }
   
   function subtract(o,p){
   	for(prop in p){
   		delete o[prop]
   	}
   	return o
   }
   
   function union(o,p){
   	return extend(extend({}, o), p)
   }
   
   function intersection(o, p){
   	return restrict(extend({}, o), p)
   }
   
   function keys(o){
   	if(typeof o !== "object") thorw TypeError()
   	var result = []
   	for( var prop in o){
   	 	if(o.hasOwnProperty(prop)){
   	 		result.push(prop)
   	 	}
   	}
   	return result
   }
   ```

   

3. Object.keys()返回对象可枚举的自有属性

4. Object.getOwnPropertyNames()返回对象自有属性的名称

## 属性getter和setter

1. 由getter和setter定义的属性称作“存取器属性”，不同于“数据属性”

2. 定义存取器属性

   ```
   var o ={
   	data_prop:value,
   	get accessor_prop() {},
   	set accessor_prop(value){}
   }
   ```

   

3. 存取器属性是可继承的

4. 自增序列号

   ```
   var serialnum = {
    	$n:0, //私有属性
    	get next() {
    		return this.$n++
    	},
    	set next(n){
    		if(n >= this.$n){
    			this.$n = n
    		} else {	
    			throw "..."	
    		}
    	}
   }
   ```

   

5. 返回随机数

   ```
   var random = {
   	get octet() {
   		return Math.floor(Math.random() * 256)
   	}
   	get uint16(){
   		return Math.floor(Math.random() * 65536)
   	}
   	get int16(){
   		return Math.floor(Math.random() * 65526 - 32768)
   	}
   }
   ```

   

## 属性的特性

1. 一个属性包含名字和四个特性

2. 数据属性的4个特性分别是值value，可写性writable，可枚举型enumerable和可配置性configuratble

3. 存取器属性不具有值value和可写性，它的可写性由setter方法存在与否决定——读取get，写入set，可枚举型和可配置性

4. Object.getOwnPropertyDescriptor()可获得某个对象特定属性的属性描述符——自有属性

   ```JavaScript
   // Returns {value: 1, writable:true, enumerable:true, configurable:true}
   Object.getOwnPropertyDescriptor({x:1}, "x");
   // Now query the octet property of the random object defined above.
   // Returns { get: /*func*/, set:undefined, enumerable:true, configurable:true}
   Object.getOwnPropertyDescriptor(random, "octet");
   // Returns undefined for inherited properties and properties that don't exist.
   Object.getOwnPropertyDescriptor({}, "x"); // undefined, no such prop
   Object.getOwnPropertyDescriptor({}, "toString"); // undefined, inherited
   ```

   

5. 设置属性的特性Object.defineProperty——不能修改继承属性

   ```javascript
   var o = {}; // Start with no properties at all
   // Add a nonenumerable data property x with value 1.
   Object.defineProperty(o, "x", { value : 1,
       writable: true,
       enumerable: false,
   	configurable: true});
   // Check that the property is there but is nonenumerable
   o.x; // => 1
   Object.keys(o) // => []
   // Now modify the property x so that it is read-only
   Object.defineProperty(o, "x", { writable: false });
   // Try to change the value of the property
   o.x = 2; // Fails silently or throws TypeError in strict mode
   o.x // => 1
   // The property is still configurable, so we can change its value like this:
   Object.defineProperty(o, "x", { value: 2 });
   o.x // => 2
   // Now change x from a data property to an accessor property
   Object.defineProperty(o, "x", { get: function() { return 0; } });
   o.x // => 0
   ```

   

6. 修改多个属性的特性Object.defineProperties() 

   ```JavaScript
   var p = Object.defineProperties({}, {
       x: { value: 1, writable: true, enumerable:true, configurable:true },
       y: { value: 1, writable: true, enumerable:true, configurable:true },
       r: {
       	get: function() { return Math.sqrt(this.x*this.x + this.y*this.y) },
           enumerable:true,
           configurable:true
       }
   });
   ```

   

7. 规则：

   1. 如果对象是不可扩展的，则可以编辑已有的自有属性，但不能给他添加新属性
   2. 如果属性是不可配置的，则不能修改他的可配置性和可枚举性
   3. 如果存取器属性是不可配置的，则不能修改其getter和setter方法，页不能将它转换为数据属性
   4. 如果数据属性是不可配置的，则不能将它转换为存取器属性
   5. 如果数据属性是不可配置的，则不能将它的可写性从false修改为true，但可以从true修改为false
   6. 如果数据属性是不可配置且不可写的，则不能修改它的值。然而可配置但不可写属性的值是可以修改的

8. 上文中的extend()方法没有复制属性的特性，改进的extend()

   ```JavaScript
   Object.defineProperty(Object.prototype,
   	"extend", // Define Object.prototype.extend
   	{
           writable: true,
           enumerable: false, // Make it nonenumerable
           configurable: true,
           value: function(o) { // Its value is this function
               // Get all own props, even nonenumerable ones
               var names = Object.getOwnPropertyNames(o);
               // Loop through them
               for(var i = 0; i < names.length; i++) {
               // Skip props already in this object
               if (names[i] in this) continue;
               // Get property description from o
               var desc = Object.getOwnPropertyDescriptor(o,names[i]);
               // Use it to create property on this
               Object.defineProperty(this, names[i], desc);
           }
       }
   });
   ```

   

## 对象的三个属性

1. 对象具有原型prototype，类class和可扩展性extensible attrubute

2. 原型属性——用来继承属性

   1. ES5：使用Object.getPrototypeOf()查询它的原型

   2. ES3：使用obj.constructor.prototype检测对象原型——不可靠

   3. 使用isPrototypeOf()检测一个对象是否是另一个对象的原型，或在原型链中

      ```JavaScript
      var p = {x:1}; // Define a prototype object.
      var o = Object.create(p); // Create an object with that prototype.
      p.isPrototypeOf(o) // => true: o inherits from p
      Object.prototype.isPrototypeOf(o) // => true: p inherits from Object.prototype
      ```

      

   4. ```_proto_```是Mozilla，Safari和Chrome支持，IE和Opera未实现

3. 类属性

   1. 类属性是一个字符串，使用toString()来查询，返回```[object class]```

   2. toString被重写，故使用以下方法获取

      ```JavaScript
      function classof(o) {
          if (o === null) return "Null";
          if (o === undefined) return "Undefined";
          return Object.prototype.toString.call(o).slice(8,-1);
      }
      ```

      

   3. ## 查询结果

      ```JavaScript
      classof(null) // => "Null
      classof(1) // => "Number"
      classof("") // => "String"
      classof(false) // => "Boolean"
      classof({}) // => "Object"
      classof([]) // => "Array"
      classof(/./) // => "Regexp"
      classof(new Date()) // => "Date"
      classof(window) // => "Window" (a client-side host object)
      function f() {}; // Define a custom constructor
      classof(new f()); // => "Object"
      ```

      

4. 可扩展性

   1. 用以表示是否可以给对象添加新属性，ES5中所有的内置对象和自定义对象都是可扩展的

   2. 使用Object.isExtensible()来判断是否可扩展

   3. 使用Object.preventExtensions()转换为不可扩展——只影响对象本身的可可扩展性

   4. Object.seal()除了将对象设置为不可扩展的，还可以将对象的自有属性设置为不可配置的——不可解封

   5. Object.freeze()，将对象设置为不可扩展和属性设置为不可配置，所有的自有数据属性设置为只读——存取器属性有setter方法，则不受影响

   6. 以上方法都会返回传入的对象

      ```JavaScript
      // Create a sealed object with a frozen prototype and a nonenumerable property
      var o = Object.seal(Object.create(Object.freeze({x:1}),
      					{y: {value: 2, writable: true}}));
      ```

      

## 序列化对象

1. JSON.stringify()和JSON.parse()
2. 支持对象，数组，字符串，无穷大数字，true，false和null
3. NaN，Infinity和-Infinity序列化的结果为null
4. 函数，RegExp，Error对象和undefined值不能序列化和还原
5. JSON.stringify只能序列化对象可枚举的自有属性
6. 可接受第二个可选参数

## 对象方法

1. toString()
2. toLocaleString()
3. toJSON()
4. valueOf()



