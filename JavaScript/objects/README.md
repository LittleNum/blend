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

1. 



检测属性

枚举属性

属性getter和setter

属性的特性

对象的三个属性

序列化对象

对象方法



