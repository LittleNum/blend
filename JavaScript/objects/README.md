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
5. 

对象的三个属性

序列化对象

对象方法



