# 类和模块

## 类和原型

1. 类的所有实例对象都从同一个原型对象上继承属性——以下定义一个工厂方法，用来创建对象

   ```javascript
   function range(from, to) {
       // Use the inherit() function to create an object that inherits from the
       // prototype object defined below. The prototype object is stored as
       // a property of this function, and defines the shared methods (behavior)
       // for all range objects.
       var r = inherit(range.methods);
       // Store the start and end points (state) of this new range object.
       // These are noninherited properties that are unique to this object.
       r.from = from;
       r.to = to;
       // Finally return the new object
       return r;
   }
   
   range.methods = {
   	// Return true if x is in the range, false otherwise
       // This method works for textual and Date ranges as well as numeric.
       includes: function(x) { return this.from <= x && x <= this.to; },
       // Invoke f once for each integer in the range.
       // This method works only for numeric ranges.
       foreach: function(f) {
       for(var x = Math.ceil(this.from); x <= this.to; x++) f(x);
       },
       // Return a string representation of the range
       toString: function() { return "(" + this.from + "..." + this.to + ")"; }
   };
   // Here are example uses of a range object.
   var r = range(1,3); // Create a range object
   r.includes(2); // => true: 2 is in the range
   r.foreach(console.log); // Prints 1 2 3
   console.log(r); // Prints (1...3)
   ```

   

## 类和构造函数

1. 使用构造函数代替工厂方法

   ```JavaScript
   function Range(from, to) {
       // Store the start and end points (state) of this new range object.
       // These are noninherited properties that are unique to this object.
       this.from = from;
       this.to = to;
   }
   // All Range objects inherit from this object.
   // Note that the property name must be "prototype" for this to work.
   Range.prototype = {
       includes: function(x) { return this.from <= x && x <= this.to; },
       foreach: function(f) {
      		for(var x = Math.ceil(this.from); x <= this.to; x++) f(x);
       },
       toString: function() { return "(" + this.from + "..." + this.to + ")"; }
   };
   
   var r = new Range(1,3); // Create a range object
   r.includes(2); // => true: 2 is in the range
   r.foreach(console.log); // Prints 1 2 3
   console.log(r); // Prints (1...3)
   ```

2. 构造函数类型首字母大写，构造函数通过new关键字调用的

3. 当且仅当两个对象继承自同一个原型对象时，它们才是属于同一类的实例。构造函数不能作为类的标识。

4. 每个JavaScript函数都拥有一个prototype属性。这个属性的值是一个对象，这个对象有包含唯一一个不可枚举属性constructor

   ```javascript
   var F = function() {}; // This is a function object.
   var p = F.prototype; // This is the prototype object associated with it.
   var c = p.constructor; // This is the function associated with the prototype.
   c === F // => true: F.prototype.constructor==F for any function
   
   var o = new F(); // Create an object o of class F
   o.constructor === F // => true: the constructor property specifies the class
   ```

5. 重写预定义的Range.prototype，不含有constructor，可以显式地给原型添加一个构造函数

   ```JavaScript
   Range.prototype = {
       constructor: Range, // Explicitly set the constructor back-reference
       includes: function(x) { return this.from <= x && x <= this.to; },
       foreach: function(f) {
       for(var x = Math.ceil(this.from); x <= this.to; x++) f(x);
       },
       toString: function() { return "(" + this.from + "..." + this.to + ")"; }
   };
   ```

   

6. 或者使用预定义的原型对象，给它添加方法

   ```JavaScript
   Range.prototype.includes = function(x) { return this.from<=x && x<=this.to; };
   Range.prototype.foreach = function(f) {
   	for(var x = Math.ceil(this.from); x <= this.to; x++) f(x);
   };
   Range.prototype.toString = function() {
   	return "(" + this.from + "..." + this.to + ")";
   };
   ```

   

## javascript中java式的类继承

1. 构造函数对象——任何添加到构造函数对象中的属性都是类字段和类方法

2. 原型对象——原型对象的属性被类的所有实例所继承，如果属性是函数的话，这个函数作为类的实例的方法

3. 实例对象——定义在实例上的非函数属性，是实例的字段

4. 定义类

   ```JavaScript
   function defineClass(constructor,//用以设置实例的属性的函数
       methods, // 实例的方法，复制到原型中
       statics) // 类属性，复制到构造函数中
   {
       if (methods) extend(constructor.prototype, methods);
       if (statics) extend(constructor, statics);
       return constructor;
   }
   // This is a simple variant of our Range class
   var SimpleRange =
   	defineClass(function(f,t) { this.f = f; this.t = t; },
       {
           includes: function(x) { return this.f <= x && x <= this.t;},
           toString: function() { return this.f + "..." + this.t; }
       },
   	{ upto: function(t) { return new SimpleRange(0, t); } });
   ```

   

5. 一个表述复数的类

   ```JavaScript
   // 构造函数定义了实例字段
   function Complex(real, imaginary) {
       if (isNaN(real) || isNaN(imaginary)) // Ensure that both args are numbers.
       	throw new TypeError(); // Throw an error if they are not.
       this.r = real; // The real part of the complex number.
       this.i = imaginary; // The imaginary part of the number.
   }
   // 类的实例方法定义为原型对象的函数值属性
   // Add a complex number to this one and return the sum in a new object.
   Complex.prototype.add = function(that) {
   	return new Complex(this.r + that.r, this.i + that.i);
   };
   // Multiply this complex number by another and return the product.
   Complex.prototype.mul = function(that) {
   	return new Complex(this.r * that.r - this.i * that.i,
   						this.r * that.i + this.i * that.r);
   };
   // Return the real magnitude of a complex number. This is defined
   // as its distance from the origin (0,0) of the complex plane.
   Complex.prototype.mag = function() {
   	return Math.sqrt(this.r*this.r + this.i*this.i);
   };
   // Return a complex number that is the negative of this one.
   Complex.prototype.neg = function() { return new Complex(-this.r, -this.i); };
   // Convert a Complex object to a string in a useful way.
   Complex.prototype.toString = function() {
   	return "{" + this.r + "," + this.i + "}";
   };
   // Test whether this Complex object has the same value as another.
   Complex.prototype.equals = function(that) {
   	return that != null && // must be defined and non-null
   			that.constructor === Complex && // and an instance of Complex
   			this.r === that.r && this.i === that.i; // and have the same values.
   };
   // 类字段和类方法直接定义为构造函数的属性
   // Here are some class fields that hold useful predefined complex numbers.
   // Their names are uppercase to indicate that they are constants.
   // (In ECMAScript 5, we could actually make these properties read-only.)
   Complex.ZERO = new Complex(0,0);
   Complex.ONE = new Complex(1,0);
   Complex.I = new Complex(0,1);
   // This class method parses a string in the format returned by the toString
   // instance method and returns a Complex object or throws a TypeError.
   Complex.parse = function(s) {
       try { // Assume that the parsing will succeed
           var m = Complex._format.exec(s); // Regular expression magic
           return new Complex(parseFloat(m[1]), parseFloat(m[2]));
       } catch (x) { // And throw an exception if it fails
           throw new TypeError("Can't parse '" + s + "' as a complex number.");
       }
   };
   //类的“私有”字段，前缀表示是类内部使用
   Complex._format = /^\{([^,]+),([^}]+)\}$/
   ```

   以上定义用到了构造函数、实例字段、实例方法、类字段和类方法

## 类的扩充

1. 可以给原型对象添加新方法来扩充JavaScript类
2. 给Object.prototype添加新方法，在ES 5之前，这些新增的方法不能设置为不可枚举的。

## 类和类型

1. **instanceof**——如果o继承自c.prototype，则o instanceof c为true

2. 构造函数时公共标识，原型是唯一的标识

3. 检测对象的原型链上是否存在某个特定的原型对象——**isPrototypeOf**

   ```
   range.methods.isPrototypeOf(r);  //range.method是原型对象
   ```

4. instanceof和isPrototypeOf无法通过对象来获得类名

5. **constructor**属性

   ```
   function typeAndValue(x) {
       if (x == null) return ""; // Null and undefined don't have constructors
           switch(x.constructor) {
               case Number: return "Number: " + x; // 原始类型
               case String: return "String: '" + x + "'";
               case Date: return "Date: " + x; // 内置类型
               case RegExp: return "Regexp: " + x;
               case Complex: return "Complex: " + x; // 自定义类型
       }
   }
   ```

   多个执行上下文环境中无法正常工作；并非所有的对象都包含constructor属性

6. 构造函数的名称——通过构造函数的名字进行判断

   ```javascript
   /** 以字符串形式返回o的类型：
    *  -如果o是null，返回“null”，如果是NaN，返回“nan”
    *	-如果typeof所返回的值不是“object”，则返回这个值
    *	（有些JavaScript的实现将正则表达式识别为函数）
    *	-如果o的类不是“Object”，则返回这个值
    *	-如果o包含构造函数并且这个构造函数具有名称，则返回这个名称
    *	—否则，一律返回“Object”
    **/
   function type(o) {
       var t, c, n; // type, class, name
       // Special case for the null value:
       if (o === null) return "null";
       // Another special case: NaN is the only value not equal to itself:
       if (o !== o) return "nan";
       // Use typeof for any value other than "object".
       // This identifies any primitive value and also functions.
       if ((t = typeof o) !== "object") return t;
       // Return the class of the object unless it is "Object".
       // This will identify most native objects.
       if ((c = classof(o)) !== "Object") return c;
       // Return the object's constructor name, if it has one
       if (o.constructor && typeof o.constructor === "function" &&
       	(n = o.constructor.getName())) 
           return n;
       // We can't determine a more specific type, so return "Object"
       return "Object";
   }
   // Return the class of an object.
   function classof(o) {
   	return Object.prototype.toString.call(o).slice(8,-1);
   };
   // Return the name of a function (may be "") or null for nonfunctions
   Function.prototype.getName = function() {
       if ("name" in this) return this.name;
       return this.name = this.toString().match(/function\s*([^(]*)\(/)[1];
   };
   ```

   并不是所有对象都具有constructor属性；并不是所有的函数都有名字。

7. 鸭式辩型——不要关注“对象的类是什么”，而是关注“对象能做什么”

8. 定义quacks()函数以检查一个对象是否实现了剩下的参数所表示的方法

   ```javascript
   function quacks(o /*, ... */) {
       for(var i = 1; i < arguments.length; i++) { // for each argument after o
           var arg = arguments[i];
           switch(typeof arg) { // If arg is a:
           case 'string': // string: check for a method with that name
               if (typeof o[arg] !== "function") return false;
               continue;
           case 'function': // function: use the prototype object instead
               // If the argument is a function, we use its prototype object
               arg = arg.prototype;
               // fall through to the next case
           case 'object': // object: check for matching methods
               for(var m in arg) { // For each property of the object
                   if (typeof arg[m] !== "function") continue; // skip non-methods
                   if (typeof o[m] !== "function") return false;
               }
           }
       }
       // If we're still here, then o implements everything
       return true;
   }
   ```

   

## JavaScript中的面向对象技术

1. 一个例子：集合类

   ```JavaScript
   function Set() { // This is the constructor
       this.values = {}; // The properties of this object hold the set
       this.n = 0; // How many values are in the set
       this.add.apply(this, arguments); // All arguments are values to add
   }
   // Add each of the arguments to the set.
   Set.prototype.add = function() {
       for(var i = 0; i < arguments.length; i++) { // For each argument
           var val = arguments[i]; // The value to add to the set
           var str = Set._v2s(val); // Transform it to a string
           if (!this.values.hasOwnProperty(str)) { // If not already in the set
               this.values[str] = val; // Map string to value
               this.n++; // Increase set size
           }
       }
   	return this; // Support chained method calls
   };
   // Remove each of the arguments from the set.
   Set.prototype.remove = function() {
       for(var i = 0; i < arguments.length; i++) { // For each argument
       	var str = Set._v2s(arguments[i]); // Map to a string
           if (this.values.hasOwnProperty(str)) { // If it is in the set
               delete this.values[str]; // Delete it
               this.n--; // Decrease set size
           }
       }
   return this; // For method chaining
   };
   // Return true if the set contains value; false otherwise.
   Set.prototype.contains = function(value) {
   	return this.values.hasOwnProperty(Set._v2s(value));
   };
   // Return the size of the set.
   Set.prototype.size = function() { return this.n; };
   // 遍历集合中的所有元素，在指定的上下文中调用f
   Set.prototype.foreach = function(f, context) {
       for(var s in this.values) // For each string in the set
       if (this.values.hasOwnProperty(s)) // Ignore inherited properties
       	f.call(context, this.values[s]); // Call f on the value
   };
   // 将任意JavaScript值和唯一的字符串对应起来
   Set._v2s = function(val) {
       switch(val) {
           case undefined: return 'u'; // Special primitive
           case null: return 'n'; // values get single-letter
           case true: return 't'; // codes.
           case false: return 'f';
           default: switch(typeof val) {
               case 'number': return '#' + val; // Numbers get # prefix.
               case 'string': return '"' + val; // Strings get " prefix.
               	default: return '@' + objectId(val); // Objs and funcs get @
   		}
   	}
   // For any object, return a string. This function will return a different
   // string for different objects, and will always return the same string
   // if called multiple times for the same object. To do this it creates a
   // property on o. In ES5 the property would be nonenumerable and read-only.
   	function objectId(o) {
           var prop = "|**objectid**|"; // Private property name for storing ids
           if (!o.hasOwnProperty(prop)) // If the object has no id
               o[prop] = Set._v2s.next++; // Assign it the next available
           return o[prop]; // Return the id
       }
   };
   Set._v2s.next = 100;
   ```

   

2. 一个例子：枚举类型

   ```JavaScript
   //创建一个新的枚举类型，实参对象标识类的每个势力的名字和值
   //返回的构造函数包含名/值对的映射表
   //包括由值组成的数组，以及一个foreach()迭代器函数
   function enumeration(namesToValues) {
       // 虚拟的构造函数时返回值，不能实例化
       var enumeration = function() { throw "Can't Instantiate Enumerations"; };
       // 枚举值继承自这个对象
       var proto = enumeration.prototype = {
           constructor: enumeration, // Identify type
           toString: function() { return this.name; }, // Return name
           valueOf: function() { return this.value; }, // Return value
           toJSON: function() { return this.name; } // For serialization
       };
       enumeration.values = []; // An array of the enumerated value objects
       // 创建新类型的实例
       for(name in namesToValues) { // For each value
           var e = inherit(proto); // Create an object to represent it
           e.name = name; // Give it a name
           e.value = namesToValues[name]; // And a value
           enumeration[name] = e; // Make it a property of constructor
           enumeration.values.push(e); // And store in the values array
       }
       // A class method for iterating the instances of the class
       enumeration.foreach = function(f,c) {
       	for(var i = 0; i < this.values.length; i++) f.call(c,this.values[i]);
       };
       // Return the constructor that identifies the new type
       return enumeration;
   }
   ```

   ```JavaScript
   // Create a new Coin class with four values: Coin.Penny, Coin.Nickel, etc.
   var Coin = enumeration({Penny: 1, Nickel:5, Dime:10, Quarter:25});
   var c = Coin.Dime; // 新的实例
   c instanceof Coin // => true: instanceof works
   c.constructor == Coin // => true: constructor property works
   Coin.Quarter + 3*Coin.Nickel // => 40: values convert to numbers
   Coin.Dime == 10 // => true: more conversion to numbers
   Coin.Dime > Coin.Nickel // => true: relational operators work
   String(Coin.Dime) + ":" + Coin.Dime // => "Dime:10": coerce to string
   ```

   扑克牌

   ```JavaScript
   // Define a class to represent a playing card
   function Card(suit, rank) {
       this.suit = suit; // Each card has a suit
   	this.rank = rank; // and a rank
   }
   // 使用枚举类型定义花色和点数
   Card.Suit = enumeration({Clubs: 1, Diamonds: 2, Hearts:3, Spades:4});
   Card.Rank = enumeration({Two: 2, Three: 3, Four: 4, Five: 5, Six: 6,
                           Seven: 7, Eight: 8, Nine: 9, Ten: 10,
                           Jack: 11, Queen: 12, King: 13, Ace: 14});
   // Define a textual representation for a card
   Card.prototype.toString = function() {
   	return this.rank.toString() + " of " + this.suit.toString();
   };
   // 比较大小
   Card.prototype.compareTo = function(that) {
       if (this.rank < that.rank) return -1;
       if (this.rank > that.rank) return 1;
       return 0;
   };
   // 扑克牌排序
   Card.orderByRank = function(a,b) { return a.compareTo(b); };
   // 桥牌排序
   Card.orderBySuit = function(a,b) {
       if (a.suit < b.suit) return -1;
       if (a.suit > b.suit) return 1;
       if (a.rank < b.rank) return -1;
       if (a.rank > b.rank) return 1;
       return 0;
   };
   // 定义标准扑克牌
   function Deck() {
       var cards = this.cards = []; // A deck is just an array of cards
       Card.Suit.foreach(function(s) { // Initialize the array
           Card.Rank.foreach(function(r) {
               cards.push(new Card(s,r));
           });
       });
   }
   // 洗牌
   Deck.prototype.shuffle = function() {
       // For each element in the array, swap with a randomly chosen lower element
       var deck = this.cards, len = deck.length;
       for(var i = len-1; i > 0; i--) {
           var r = Math.floor(Math.random()*(i+1)), temp; // Random number
           temp = deck[i], deck[i] = deck[r], deck[r] = temp; // Swap
       }
   	return this;
   };
   // 发牌
   Deck.prototype.deal = function(n) {
       if (this.cards.length < n) throw "Out of cards";
   	return this.cards.splice(this.cards.length-n, n);
   };
   // Create a new deck of cards, shuffle it, and deal a bridge hand
   var deck = (new Deck()).shuffle();
   var hand = deck.deal(13).sort(Card.orderBySuit);
   ```

   

3. 标准转换方法

   ```JavaScript
   extend(Set.prototype, {
   	// Convert a set to a string
       toString: function() {
           var s = "{", i = 0;
           this.foreach(function(v) { s += ((i++ > 0)?", ":"") + v;})
           return s + "}";
       },
       // Like toString, but call toLocaleString on all values
       toLocaleString : function() {
           var s = "{", i = 0;
           this.foreach(function(v) {
           if (i++ > 0) s += ", ";
           if (v == null) s += v; // null & undef
           else s += v.toLocaleString(); // all o
           });
           return s + "}";
       },
       // Convert a set to an array of values
       toArray: function() {
           var a = [];
           this.foreach(function(v) { a.push(v); });
           return a;
       }
   });
   // Treat sets like arrays for the purposes of JSON stringification.
   Set.prototype.toJSON = Set.prototype.toArray;
   ```

   

4. 比较方法

   equals()方法

   ```javascript
   Range.prototype.equals = function(that) {
       if (that == null) return false; // Reject null and undefined
       if (that.constructor !== Range) return false; // Reject non-ranges
       // Now return true if and only if the two endpoints are equal.
       return this.from == that.from && this.to == that.to;
   }
   ```

   ```javascript
   Set.prototype.equals = function(that) {
       // Shortcut for trivial case
       if (this === that) return true;
       // If the that object is not a set, it is not equal to this one.
       // We use instanceof to allow any subclass of Set.
       // We could relax this test if we wanted true duck-typing.
       // Or we could strengthen it to check this.constructor == that.constructor
       // Note that instanceof properly rejects null and undefined values
       if (!(that instanceof Set)) return false;
       // If two sets don't have the same size, they're not equal
       if (this.size() != that.size()) return false;
       // Now check whether every element in this is also in that.
       // Use an exception to break out of the foreach if the sets are not equal.
       try {
           this.foreach(function(v) { if (!that.contains(v)) throw false; });
           return true; // All elements matched: sets are equal.
       } catch (x) {
           if (x === false) return false; // An element in this is not in that.
           throw x; // Some other exception: rethrow it.
       }
   };
   ```

   compareTo()方法

   ```javascript
   Range.prototype.compareTo = function(that) {
       if (!(that instanceof Range))
       	throw new Error("Can't compare a Range with " + that);
       var diff = this.from - that.from; // Compare lower bounds
       if (diff == 0) diff = this.to - that.to; // If equal, compare upper bounds
       return diff;
   };
   ```

   定义了compareTo()方法可以进行排序

   ```javascript
   ranges.sort(function(a,b) { return a.compareTo(b); });
   Range.byLowerBound = function(a,b) { return a.compareTo(b); };
   ranges.sort(Range.byLowerBound);
   ```

   

5. 方法借用——泛型实现

   ```JavaScript
   var generic = {
       // 返回一个字符串，包含构造函数的名字，以及所有飞集成来的、非函数属性的名字和值
       toString: function() {
           var s = '[';
           // 如果这个对象包含构造函数，且构造函数包含名字
           if (this.constructor && this.constructor.name)
           	s += this.constructor.name + ": ";
           // 枚举所有非继承且非函数的属性
           var n = 0;
           for(var name in this) {
               if (!this.hasOwnProperty(name)) continue; // 跳过继承来的属性
               var value = this[name];
               if (typeof value === "function") continue; // skip methods
               if (n++) s += ", ";
               s += name + '=' + value;
           }
           return s + ']';
       },
       // 通过比较this和that的构造函数和实例属性来判断它们是否相等
       // 原始值可以通过“===”来比较
       // 忽略由Set类添加的特殊属性
       equals: function(that) {
           if (that == null) return false;
           if (this.constructor !== that.constructor) return false;
           for(var name in this) {
           if (name === "|**objectid**|") continue; // skip special prop.
           if (!this.hasOwnProperty(name)) continue; // skip inherited
           if (this[name] !== that[name]) return false; // compare values
           }
           return true; // If all properties matched, objects are equal.
       }
   };
   ```

   

6. 私有状态——通过将变量（或参数）闭包在一个构造函数内来模拟实现私有实例字段

   ```JavaScript
   function Range(from, to) {
       // Don't store the endpoints as properties of this object. Instead
       // define accessor functions that return the endpoint values.
       // These values are stored in the closure.
       this.from = function() { return from; };
       this.to = function() { return to; };
   }
   // The methods on the prototype can't see the endpoints directly: they have
   // to invoke the accessor methods just like everyone else.
   Range.prototype = {
       constructor: Range,
       includes: function(x) { return this.from() <= x && x <= this.to(); },
       foreach: function(f) {
       	for(var x=Math.ceil(this.from()), max=this.to(); x <= max; x++) f(x);
       },
       toString: function() { return "(" + this.from() + "..." + this.to() + ")"; }
   };
   ```

   

7. 使用闭包来封装类的状态的类会比不用封装的状态变量的等价类运行速度更慢，并占用更多内存

8. 构造函数的重载和工厂方法——对象初始化有多种方式

   重载构造函数：

   ```JavaScript
   function Set() {
       this.values = {}; // The properties of this object hold the set
       this.n = 0; // How many values are in the set
       // 如果传入一个类数组对象，将元素添加至集合中，否则，将所有参数添加至集合中
       if (arguments.length == 1 && isArrayLike(arguments[0]))
       	this.add.apply(this, arguments[0]);
       else if (arguments.length > 0)
       	this.add.apply(this, arguments);
   }
   ```

   使用工厂方法：

   ```JavaScript
   Complex.polar = function(r, theta) {
   	return new Complex(r*Math.cos(theta), r*Math.sin(theta));
   };
   
   // 通过数组初始化Set对象
   Set.fromArray = function(a) {
       s = new Set(); // Create a new empty set
       s.add.apply(s, a); // Pass elements of array a to the add method
       return s; // Return the new set
   };
   ```

9. 定义多个构造函数继承自同一个原型对象，它们所创建的对象都属于同一类型

   ```JavaScript
   // Set类的一个辅助构造函数
   function SetFromArray(a) {
   // 通过以函数的形式调用Set()来初始化这个新对象
   Set.apply(this, a);
   }
   // Set the prototype so that SetFromArray creates instances of Set
   SetFromArray.prototype = Set.prototype;
   var s = new SetFromArray([1,2,3]);
   s instanceof Set // => true
   ```

   

## 子类

1. 定义子类——O是类B的实例，B是A的子类，那么O也一定从A中继承了属性

   ```JavaScript
   B.prototype = inherit(A.prototype); // Subclass inherits from superclass
   B.prototype.constructor = B; 
   ```

   ```JavaScript
   function defineSubclass(superclass, // Constructor of the superclass
           constructor, // The constructor for the new subclass
           methods, // 实例方法，复制至原型中
           statics) // 类属性：复制至构造函数中
   {
       // 建立子类的原型对象
       constructor.prototype = inherit(superclass.prototype);
       constructor.prototype.constructor = constructor;
       // Copy the methods and statics as we would for a regular class
       if (methods) extend(constructor.prototype, methods);
       if (statics) extend(constructor, statics);
       // Return the class
       return constructor;
   }
   ```

   ```JavaScript
   // 通过父类构造函数的方法来做到这一点
   Function.prototype.extend = function(constructor, methods, statics) {
   	return defineSubclass(this, constructor, methods, statics);
   };
   ```

   实现一个SingletonSet：

   ```JavaScript
   // The constructor function
   function SingletonSet(member) {
   	this.member = member; // Remember the single member of the set
   }
   // Create a prototype object that inherits from the prototype of Set.
   SingletonSet.prototype = inherit(Set.prototype);
   // Now add properties to the prototype.
   // These properties override the properties of the same name from Set.prototype.
   extend(SingletonSet.prototype, {
               // Set the constructor property appropriately
               constructor: SingletonSet,
               // This set is read-only: add() and remove() throw errors
               add: function() { throw "read-only set"; },
               remove: function() { throw "read-only set"; },
               // A SingletonSet always has size 1
               size: function() { return 1; },
               // Just invoke the function once, passing the single member.
               foreach: function(f, context) { f.call(context, this.member); },
               // The contains() method is simple: true only for one value
               contains: function(x) { return x === this.member; }
           });
   ```

   

2. 构造函数和方法链

   定义一个NonNullSet

   ```JavaScript
   function NonNullSet() {
       // 链接到父类，调用父类的构造函数来初始化通过该构造函数调用创建的对象
       Set.apply(this, arguments);
   }
   // 设置NonNullSet为Set的子类
   NonNullSet.prototype = inherit(Set.prototype);
   NonNullSet.prototype.constructor = NonNullSet;
   // 重写add方法
   NonNullSet.prototype.add = function() {
       // Check for null or undefined arguments
       for(var i = 0; i < arguments.length; i++)
       	if (arguments[i] == null)
       	throw new Error("Can't add null or undefined to a NonNullSet");
       // 调用父类的add方法
       return Set.prototype.add.apply(this, arguments);
   };
   ```

   

3. 类工厂和方法链

   ```JavaScript
   // Define a set class that holds strings only
   var StringSet = filteredSetSubclass(Set,
   					function(x) {return typeof x==="string";});
   // Define a set class that does not allow null, undefined or functions
   var MySet = filteredSetSubclass(NonNullSet,
   					function(x) {return typeof x !== "function";});
   ```

   ```JavaScript
   function filteredSetSubclass(superclass, filter) {
       var constructor = function() { // The subclass constructor
           superclass.apply(this, arguments); // 调用父类构造函数
       };
       var proto = constructor.prototype = inherit(superclass.prototype);
       proto.constructor = constructor;
       proto.add = function() {
           // Apply the filter to all arguments before adding any
           for(var i = 0; i < arguments.length; i++) {
               var v = arguments[i];
               if (!filter(v)) throw("value " + v + " rejected by filter");
      	 	}
           // Chain to our superclass add implementation
           superclass.prototype.add.apply(this, arguments);
       };
       return constructor;
   }
   ```

   

4. 使用包装函数和extend重写

   ```JavaScript
   var NonNullSet = (function() { // Define and invoke function
       var superclass = Set; // Only specify the superclass once.
       return superclass.extend(
           function() { superclass.apply(this, arguments); }, // the constructor
               { // the methods
               add: function() {
                   // Check for null or undefined arguments
                   for(var i = 0; i < arguments.length; i++)
                   if (arguments[i] == null)
                   throw new Error("Can't add null or undefined");
                   // Chain to the superclass to perform the actual insertion
                   return superclass.prototype.add.apply(this, arguments);
               }
           });
       }());
   ```

   

5. 组合VS子类——组合优于继承

   ```JavaScript
   var FilteredSet = Set.extend(
       function FilteredSet(set, filter) { // The constructor
           this.set = set;
           this.filter = filter;
       },
       { // The instance methods
           add: function() {
               // If we have a filter, apply it
               if (this.filter) {
                   for(var i = 0; i < arguments.length; i++) {
                       var v = arguments[i];
                       if (!this.filter(v))
                           throw new Error("FilteredSet: value " + v +
                           " rejected by filter");
                   }
               }
               // Now forward the add() method to this.set.add()
               this.set.add.apply(this.set, arguments);
               return this;
           },
           // The rest of the methods just forward to this.set and do nothing else.
           remove: function() {
               this.set.remove.apply(this.set, arguments);
               return this;
           },
           contains: function(v) { return this.set.contains(v); },
           size: function() { return this.set.size(); },
           foreach: function(f,c) { this.set.foreach(f,c); }
       });
   ```

   ```JavaScript
   var s = new FilteredSet(new Set(), function(x) { return x !== null; })
   var t = new FilteredSet(s, { function(x} { return !(x instanceof Set); });
   ```

   

6. 类的层次结构和抽象类

   ```JavaScript
   //抽象方法
   function abstractmethod() { throw new Error("abstract method"); }
   ```

   ```JavaScript
   //抽象类，定义了一个抽象方法
   function AbstractSet() { throw new Error("Can't instantiate abstract classes");}
   AbstractSet.prototype.contains = abstractmethod;
   ```

   ```JavaScript
   //非抽象子类
   var NotSet = AbstractSet.extend(
       function NotSet(set) { this.set = set; },
       {
           contains: function(x) { return !this.set.contains(x); },
           toString: function(x) { return "~" + this.set.toString(); },
           equals: function(that) {
           	return that instanceof NotSet && this.set.equals(that.set);
       	}
       }
   );
   ```

   ```JavaScript
   //定义了抽象方法size()，foreach()，实现了几个非抽象方法
   var AbstractEnumerableSet = AbstractSet.extend(
       function() { throw new Error("Can't instantiate abstract classes"); },
       {
           size: abstractmethod,
           foreach: abstractmethod,
           isEmpty: function() { return this.size() == 0; },
           toString: function() {
               var s = "{", i = 0;
               this.foreach(function(v) {
                   if (i++ > 0) s += ", ";
                       s += v;
                   });
               return s + "}";
           },
           toLocaleString : function() {
               var s = "{", i = 0;
               this.foreach(function(v) {
                       if (i++ > 0) s += ", ";
                       if (v == null) s += v; // null & undefined
                       else s += v.toLocaleString(); // all others
                   });
               return s + "}";
           },
           toArray: function() {
               var a = [];
               this.foreach(function(v) { a.push(v); });
               return a;
           },
           equals: function(that) {
               if (!(that instanceof AbstractEnumerableSet)) return false;
               // If they don't have the same size, they're not equal
               if (this.size() != that.size()) return false;
               // Now check whether every element in this is also in that.
               try {
                   this.foreach(function(v) {if (!that.contains(v)) throw false;});
                   return true; // All elements matched: sets are equal.
               } catch (x) {
               	if (x === false) return false; // Sets are not equal
               	throw x; // Some other exception occurred: rethrow it.
           	}
       	}
   });
   ```

   ```JavaScript
   //AbstractEnumerableSet的非抽象子类
   var SingletonSet = AbstractEnumerableSet.extend(
   	function SingletonSet(member) { this.member = member; },
       {
           contains: function(x) { return x === this.member; },
           size: function() { return 1; },
           foreach: function(f,ctx) { f.call(ctx, this.member); }
       }
   );
   ```

   ```JavaScript
   //AbstractEnumerableSet的抽象子类，定义了抽象方法add()，remove()
   var AbstractWritableSet = AbstractEnumerableSet.extend(
       function() { throw new Error("Can't instantiate abstract classes"); },
       {
           add: abstractmethod,
           remove: abstractmethod,
           union: function(that) {
               var self = this;
               that.foreach(function(v) { self.add(v); });
               return this;
           },
           intersection: function(that) {
               var self = this;
               this.foreach(function(v) { if (!that.contains(v)) self.remove(v);});
               return this;
           },
           difference: function(that) {
               var self = this;
               that.foreach(function(v) { self.remove(v); });
               return this;
           }
   	});
   ```

   ```JavaScript
   //AbstractWritableSet的非抽象子类
   var ArraySet = AbstractWritableSet.extend(
       function ArraySet() {
           this.values = [];
           this.add.apply(this, arguments);
       },
       {
           contains: function(v) { return this.values.indexOf(v) != -1; },
           size: function() { return this.values.length; },
           foreach: function(f,c) { this.values.forEach(f, c); },
           add: function() {
               for(var i = 0; i < arguments.length; i++) {
                   var arg = arguments[i];
                   if (!this.contains(arg)) this.values.push(arg);
               }
               return this;
           },
           remove: function() {
               for(var i = 0; i < arguments.length; i++) {
                   var p = this.values.indexOf(arguments[i]);
                   if (p == -1) continue;
                       this.values.splice(p, 1);
                   }
               return this;
           }
       }
   );
   ```

   

## ES 5中的类

1. ES 5给属性特性增加了方法支持（getter，setter，可枚举性，可写性和可配置性），而且增加了可扩展性的限制

2. 让属性不可枚举

   ```javascript
   // 将代码包装在一个匿名函数中，这样定义的变量就在这个函数作用域内
   (function() {
       // Define objectId as a nonenumerable property inherited by all objects.
       // When this property is read, the getter function is invoked.
       // 没有setter，只读
       // 不可配置，无法删除
       Object.defineProperty(Object.prototype, "objectId", {
           get: idGetter, // Method to get value
           enumerable: false, // Nonenumerable
           configurable: false // Can't delete it
       });
       // This is the getter function called when objectId is read
       function idGetter() { // A getter function to return the id
           if (!(idprop in this)) { // If object doesn't already have an id
               if (!Object.isExtensible(this)) // And if we can add a property
                   throw Error("Can't define id for nonextensible objects");
               Object.defineProperty(this, idprop, { // Give it one now.
                   value: nextid++, // This is the value
                   writable: false, // Read-only
                   enumerable: false, // Nonenumerable
                   configurable: false // Nondeletable
               });
           }
           return this[idprop]; // Now return the existing or new value
       };
       // These variables are used by idGetter() and are private to this function
       var idprop = "|**objectId**|"; // Assume this property isn't in use
       var nextid = 1; // Start assigning ids at this #
   }()); // 立即执行这个包装函数
   ```

   

3. 定义不可变的类——使用Object.defineProperties() 和Object.create() 

   ```JavaScript
   function Range(from,to) {
       // These are descriptors for the read-only from and to properties.
       var props = {
           from: {value:from, enumerable:true, writable:false, configurable:false},
           to: {value:to, enumerable:true, writable:false, configurable:false}
       };
       if (this instanceof Range) // 如果是构造函数
       	Object.defineProperties(this, props); // Define the properties
       else // Otherwise, as a factory
       	return Object.create(Range.prototype, // Create and return a new
       			props); // Range object with props
   }
   
   //设置方法的可没举行，可写性和可配置性默认为false
   Object.defineProperties(Range.prototype, {
       includes: {
       	value: function(x) { return this.from <= x && x <= this.to; }
       },
       foreach: {
           value: function(f) {
           	for(var x = Math.ceil(this.from); x <= this.to; x++) f(x);
           }
       },
       toString: {
       	value: function() { return "(" + this.from + "..." + this.to + ")"; }
       }
   });
   ```

   

4. 定义属性描述符工具函数

   ```JavaScript
   //设置为不可写的和不可配置的
   function freezeProps(o) {
       var props = (arguments.length == 1) // If 1 arg
       		? Object.getOwnPropertyNames(o) // use all props
       		: Array.prototype.splice.call(arguments, 1); // else named props
       props.forEach(function(n) { // Make each one read-only and permanent
           // Ignore nonconfigurable properties
           if (!Object.getOwnPropertyDescriptor(o,n).configurable) return;
           Object.defineProperty(o, n, { writable: false, configurable: false });
       });
       return o; // So we can keep using it
   }
   //设置为不可枚举的和可配置的
   function hideProps(o) {
       var props = (arguments.length == 1) // If 1 arg
       		? Object.getOwnPropertyNames(o) // use all props
       		: Array.prototype.splice.call(arguments, 1); // else named props
       props.forEach(function(n) { // Hide each one from the for/in loop
           // Ignore nonconfigurable properties
           if (!Object.getOwnPropertyDescriptor(o,n).configurable) return;
           Object.defineProperty(o, n, { enumerable: false });
       });
       return o;
   }
   
   function Range(from, to) { // 不可变的类的构造函数
       this.from = from;
       this.to = to;
       freezeProps(this); // Make the properties immutable
   }
   Range.prototype = hideProps({ // 使用不可枚举的属性来定义原型
       constructor: Range,
       includes: function(x) { return this.from <= x && x <= this.to; },
       foreach: function(f) {for(var x=Math.ceil(this.from);x<=this.to;x++) f(x);},
       toString: function() { return "(" + this.from + "..." + this.to + ")"; }
   });
   ```

   

5. 封装对象状态

   ```JavaScript
   //定义getter和setter方法将装填变量更健壮地封装起来
   function Range(from, to) {
       // Verify that the invariant holds when we're created
       if (from > to) throw new Error("Range: from must be <= to");
       // Define the accessor methods that maintain the invariant
       function getFrom() { return from; }
       function getTo() { return to; }
       function setFrom(f) { // Don't allow from to be set > to
       if (f <= to) from = f;
       else throw new Error("Range: from must be <= to");
   }
   function setTo(t) { // Don't allow to to be set < from
       if (t >= from) to = t;
       else throw new Error("Range: to must be >= from");
   }
   // Create enumerable, nonconfigurable properties that use the accessors
   Object.defineProperties(this, {
       from: {get: getFrom, set: setFrom, enumerable:true, configurable:false},
       to: { get: getTo, set: setTo, enumerable:true, configurable:false }
       });
   }
   // The prototype object is unchanged from previous examples.
   // The instance methods read from and to as if they were ordinary properties.
   Range.prototype = hideProps({
       constructor: Range,
       includes: function(x) { return this.from <= x && x <= this.to; },
       foreach: function(f) {for(var x=Math.ceil(this.from);x<=this.to;x++) f(x);},
       toString: function() { return "(" + this.from + "..." + this.to + ")"; }
   });
   ```

   

6. 防止类的扩展 

   ```JavaScript
   Object.preventExtensions()将对象设置为不可扩展的，不能添加任何新属性
   ```

   ```JavaScript
   Object.seal(Object.prototype)不能给对象添加新属性，还能将当前已有的属性设置为不可配置的，不能删除这些属性（不可配置的属性可以是可写的，页可以转换为只读属性）
   ```

   ```JavaScript
   //对象方法可以随时替换——monkey-patch
   var original_sort_method = Array.prototype.sort;
   Array.prototype.sort = function() {
       var start = new Date();
       original_sort_method.apply(this, arguments);
       var end = new Date();
       console.log("Array sort took " + (end - start) + " milliseconds.");
   };
   ```

   可以通过将实例方法设置为只读来防止这类修改，使用freezeProps()工具函数，或者使用Object.freeze()，将所有属性设置为只读和不可配置的。

7. 子类和ES 5

   ```JavaScript
   function StringSet() {
       this.set = Object.create(null); // 无原型
       this.n = 0;
       this.add.apply(this, arguments);
   }
   
   StringSet.prototype = Object.create(AbstractWritableSet.prototype, {
       constructor: { value: StringSet },
       contains: { value: function(x) { return x in this.set; } },
       size: { value: function(x) { return this.n; } },
       foreach: { value: function(f,c) { Object.keys(this.set).forEach(f,c); } },
       add: {
           value: function() {
               for(var i = 0; i < arguments.length; i++) {
                   if (!(arguments[i] in this.set)) {
                       this.set[arguments[i]] = true;
                       this.n++;
                   }
               }
           return this;
       }
       },
       remove: {
           value: function() {
               for(var i = 0; i < arguments.length; i++) {
                   if (arguments[i] in this.set) {
                       delete this.set[arguments[i]];
                       this.n--;
                   }
               }
           return this;
       }
       }
   })
   ```

   

8. 属性描述符

   ```JavaScript
   (function namespace() { // 将所有逻辑闭包在一个私有函数作用域中
       // This is the function that becomes a method of all object
       function properties() {
           var names; // An array of property names
           if (arguments.length == 0) // 所以的自有属性
               names = Object.getOwnPropertyNames(this);
           else if (arguments.length == 1 && Array.isArray(arguments[0]))
               names = arguments[0]; // 名字组成的数组
           else // 参数列表本身就是名字
               names = Array.prototype.splice.call(arguments, 0);
               // Return a new Properties object representing the named properties
           return new Properties(this, names);
       }
       // Make it a new nonenumerable property of Object.prototype.
       // 从私有函数作用域导出的唯一一个值
       Object.defineProperty(Object.prototype, "properties", {
           value: properties,
           enumerable: false, writable: true, configurable: true
       });
       // This constructor function is invoked by the properties() function above.
       // The Properties class represents a set of properties of an object.
       function Properties(o, names) {
           this.o = o; // The object that the properties belong to
           this.names = names; // The names of the properties
       }
       // Make the properties represented by this object nonenumerable
       Properties.prototype.hide = function() {
           var o = this.o, hidden = { enumerable: false };
           this.names.forEach(function(n) {
           if (o.hasOwnProperty(n))
           	Object.defineProperty(o, n, hidden);
           });
           return this;
       };
       // Make these properties read-only and nonconfigurable
       Properties.prototype.freeze = function() {
           var o = this.o, frozen = { writable: false, configurable: false };
           this.names.forEach(function(n) {
           if (o.hasOwnProperty(n))
           	Object.defineProperty(o, n, frozen);
           });
           return this;
       };
       // 返回名字到属性描述符的映射表
       Properties.prototype.descriptors = function() {
           var o = this.o, desc = {};
           this.names.forEach(function(n) {
           if (!o.hasOwnProperty(n)) return;
           	desc[n] = Object.getOwnPropertyDescriptor(o,n);
           });
           return desc;
       };
       // Return a nicely formatted list of properties, listing the
       // name, value and attributes. Uses the term "permanent" to mean
       // nonconfigurable, "readonly" to mean nonwritable, and "hidden"
       // to mean nonenumerable. Regular enumerable, writable, configurable
       // properties have no attributes listed.
       Properties.prototype.toString = function() {
           var o = this.o; // Used in the nested function below
           var lines = this.names.map(nameToString);
           return "{\n " + lines.join(",\n ") + "\n}";
           function nameToString(n) {
               var s = "", desc = Object.getOwnPropertyDescriptor(o, n);
               if (!desc) return "nonexistent " + n + ": undefined";
               if (!desc.configurable) s += "permanent ";
               if ((desc.get && !desc.set) || !desc.writable) s += "readonly ";
               if (!desc.enumerable) s += "hidden ";
               if (desc.get || desc.set) s += "accessor " + n
               else s += n + ": " + ((typeof desc.value==="function")?"function"
               :desc.value);
               return s;
           }
       };
       // Finally, make the instance methods of the prototype object above
       // nonenumerable, using the methods we've defined here.
       Properties.prototype.properties().hide();
   }()); // Invoke the enclosing function as soon as we're done defining it.
   ```

## 模块

1. 模块化的目标是支持大规模的程序开发，处理分散源中代码的组装

2. 用做命名空间的对象

   ```JavaScript
   var sets = {};
   sets.SingletonSet = sets.AbstractEnumerableSet.extend(...);
   var s = new sets.SingletonSet(1)
   
   var Set = sets.Set; // Import Set to the global namespace
   var s = new Set(1,2,3); // Now we can use it without the sets prefix.
   ```

   

3. 作为私有命名空间的函数

   ```JavaScript
   //声明全局变量Set，使用一个函数的返回值给它复制
   //立即执行
   //它是一个函数表达式
   var Set = (function invocation() {
   function Set() { // This constructor function is a local variable.
       this.values = {}; // The properties of this object hold the set
       this.n = 0; // How many values are in the set
       this.add.apply(this, arguments); // All arguments are values to add
       }
       // Now define instance methods on Set.prototype.
       // For brevity, code has been omitted here
       Set.prototype.contains = function(value) {
           // Note that we call v2s(), not the heavily prefixed Set._v2s()
           return this.values.hasOwnProperty(v2s(value));
       };
       Set.prototype.size = function() { return this.n; };
       Set.prototype.add = function() { /* ... */ };
       Set.prototype.remove = function() { /* ... */ };
       Set.prototype.foreach = function(f, context) { /* ... */ };
       // These are helper functions and variables used by the methods above
       // They're not part of the public API of the module, but they're hidden
       // within this function scope so we don't have to define them as a
       // property of Set or prefix them with underscores.
       function v2s(val) { /* ... */ }
       function objectId(o) { /* ... */ }
       var nextId = 1;
       //这个模块的共有API是Set()构造函数
       //将这个函数从私有命名空间中导出来，使外部可以使用
       return Set;
   }()); // Invoke the function immediately after defining it.
   ```

   ```JavaScript
   // Create a single global variable to hold all collection-related modules
   var collections;
   if (!collections) collections = {};
   // 定义sets模块
   collections.sets = (function namespace() {
       // 定义多种“集合”类，使用局部变量和函数
       // 通过命名空间对象将API导出
       return {
           // Exported property name : local variable name
           AbstractSet: AbstractSet,
           NotSet: NotSet,
           AbstractEnumerableSet: AbstractEnumerableSet,
           SingletonSet: SingletonSet,
           AbstractWritableSet: AbstractWritableSet,
           ArraySet: ArraySet
       };
   }());
   ```

   将模块函数当做构造函数，通过new调用，将它们赋值给this来导出

   ```JavaScript
   var collections;
   if (!collections) collections = {};
   collections.sets = (new function namespace() {
       // ... Lots of code omitted...
       // Now export our API to the this object
       this.AbstractSet = AbstractSet;
       this.NotSet = NotSet; // And so on....
       // Note no return value.
   }());
   ```

   如果已经定义了全局命名空间，可以直接设置对象的属性

   ```JavaScript
   var collections;
   if (!collections) collections = {};
   collections.sets = {};
   (function namespace() {
       // ... Lots of code omitted...
       // Now export our public API to the namespace object created above
       collections.sets.AbstractSet = AbstractSet;
       collections.sets.NotSet = NotSet; // And so on...
       // No return statement is needed since exports were done above.
   }());
   ```

   