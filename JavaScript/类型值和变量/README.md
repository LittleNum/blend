# 类型、值和变量

JavaScript的数据类型分为原始类型（primitive type，包含数字、字符串和布尔值，以及null和undefined）和对象类型（object type）

词法作用域——不在任何函数内声明的变量乘坐全局变量

## 数字

1. JavaScript不区分整数值和浮点数值，所有数字均用浮点数值标识
2. 整形直接量，也能识别十六进制值（以0x或0X为前缀）；某些JavaScript的实现支持八进制直接量（以0 为前缀）
3. 浮点型直接量，由整数部分，小数点和小数部分组成；还可以使用指数计数法表示浮点型直接量
4. 算术运算，+-*/，还有Math对象定义的函数和常量来实现复杂的算术运算
5. 下溢是当运算结果无限接近于零并比JavaScript能表示的最小值还小时，会返回0；负数发生下溢时，会返回特殊的值”负零“
6. 被零整除不会保存，返回无穷大Infinity或负无穷大-Infinity，零除以零会返回NaN
7. JavaScript的非数字值：它和任何值都不相等，包括自身
8. 负零值和正零值相等
9. 二进制浮点数和四舍五入错误：二进制浮点数的精度问题
10. 日期和时间

## 文本

1. 字符串16位——utf-16编码的Unicode字符集

2. 不能表示为16位的Unicode字符，用两个16位值组成一个序列（代理项对）

3. 字符串直接量，ES 5可以将字符串换行，每行以\结束

4. 转义字符

   ```
   \0 The NUL character (\u0000)
   \b Backspace (\u0008) 退格符
   \t Horizontal tab (\u0009)	水平制表符
   \n Newline (\u000A)	换行符
   \v Vertical tab (\u000B) 垂直制表符
   \f Form feed (\u000C) 换页符
   \r Carriage return (\u000D)	回车符
   \" Double quote (\u0022)	双引号
   \' Apostrophe or single quote (\u0027)	撇号或单引号
   \\ Backslash (\u005C)	反斜线
   \x XX The Latin-1 character specified by the two hexadecimal digits XX	Latin-1字符
   \u XXXX The Unicode character specified by the four hexadecimal digits XXXX Unciode字符
   ```

   

5. 字符串的使用

6. 模式匹配——RegExp()构造函数

## 布尔值

## null和undefined

1. 描述空值，对null执行typeof，返回“object”
2. undefined——变量没有初始化，typeof undefined，返回“undefined”
3. null == undefined，返回true
4. undefined表示系统级的，出乎意料的或类似错误的值的空缺，而null是表示程序级的、正常的或在意料之中的值的空缺

## 全局对象

1. JavaScript解释器启动时，将创建一个新的全局对象，并定义一组初始属性
   - 全局属性，比如undefined，Infinity和Nan
   - 全局函数，比如isNaN()，parseInt()和eval()
   - 构造函数，比如Date()，RegExp()，String()，Object()和Array()
   - 全局对象，比如Math和JSON

## 包装对象

1. String()，Number()和Boolean()构造一个临时对象——包装对象
2. ==相等 和 ===不等的区别，原始值和其包装对象不同

## 不可变的原始值和可变的对象引用

1. 原始值不可更改，字符串中的所有方法都是返回一个新的字符串值
2. 对象值都是引用

## 类型转换

1. 类型转换规则：

   ![1560736200075](C:\Users\11100797\AppData\Roaming\Typora\typora-user-images\1560736200075.png)

2. 转换和相等性，==会做类型转换，===不做类型转换

3. 显式类型转换——使用Boolean()，Number()，String()或Object()

4. 隐式类型转换——+，！等运算符

5. Number类定义的toString()方法可以接收表述转换技术radix的可选参数，表示转传承其他进制数

6. toFixed()保留小数点指定位数

7. toExponential()使用指数计数法

8. toPrecisiion()指定的有效数字位数

9. parseInt()

   ```
   parseInt("3 blind mice") // => 3
   parseFloat(" 3.14 meters") // => 3.14
   parseInt("-12.34") // => -12
   parseInt("0xFF") // => 255
   parseInt("0xff") // => 255
   parseInt("-0XFF") // => -255
   parseFloat(".1") // => 0.1
   parseInt("0.1") // => 0
   parseInt(".1") // => NaN: integers can't start with "."
   parseFloat("$72.47"); // => NaN: numbers can't start with "$"
   ```

   

   ```
   parseInt("11", 2); // => 3 (1*2 + 1)
   parseInt("ff", 16); // => 255 (15*16 + 15)
   parseInt("zz", 36); // => 1295 (35*36 + 35)
   parseInt("077", 8); // => 63 (7*8 + 7)
   parseInt("077", 10); // => 77 (7*10 + 7)
   ```

10. 对象转换为原始值，toString()，valueOf()

11. JavaScript对象到字符串的转换过程：

    1. 有toString方法，则调用
    2. 如果没有toString，则调用valueOf
    3. 否则，会抛出类型错误

12. JavaScript对象到数字的转换过程：

    1. 如果有valueOf，先调用它，得到一个原始值，并将原始值转为数字
    2. 否则，如果有toString，则调用，返回一个原始值，转换为数字
    3. 否则，抛出一个类型错误

13. 空数组被转换为数字0，具有单个元素的数组会转换成一个数字。数组继承了默认的valueOf方法，返回一个对象而不是原始值，所以数组到数字的转换则调用toString方法。

    1. 空数组转换为空字符串，空字符串转换成为数字0
    2. 含有一个元素的数组转换为字符串，再转换成数字

14. JavaScript中的“+”进行数学加法和字符串连接操作。如果是对象，则使用特殊的方法将对象转换为原始值，而不是使用其他算术运算符的方法执行对象到数字的转换，“==“相等运算符榆次类似。

15. 对象到原始值的转换基本上是对象到数字的转换——valueOf，日期对象是对象到字符串的转换模式——toString，且valueOf和toString返回的原始值将被直接使用，而不是被强制转换为数字或字符串

## 变量声明

## 变量作用域

1. 在函数内生命的变量只在函数体内有定义，它们是局部变量，作用域是局部性的。函数参数也是局部变量，它们只在函数体内有定义。

2. 在函数体内，局部变量的优先级高于同名的全局变量。同名的全局变量会被局部变量覆盖

3. 声明局部变量时必须使用var，否则显式地声明了一个新的全局变量

4. 函数定义可以嵌套，每个函数都有它自己的作用域

5. JavaScript没有块级作用域，使用了函数作用域——变量在声明它们的函数体以及这个函数体嵌套的任意函数体内都是有定义的。

   ```JavaScript
   function test(o) {
       var i = 0; // i is defined throughout function
       if (typeof o == "object") {
           var j = 0; // j is defined everywhere, not just block
           for(var k=0; k < 10; k++) { // k is defined everywhere, not just loop
           	console.log(k); // print numbers 0 through 9
           }
           console.log(k); // k is still defined: prints 10
       }
       console.log(j); // j is defined, but may not be initialized
   }
   ```

   

6. 函数作用域是指在函数内声明的所有变量在函数体内始终是可见的。

7. 声明提前——JavaScript函数里声明的所有变量都被“提前”至函数体的顶部：

   ```JavaScript
   var scope = "global";
   function f() {
       console.log(scope); // Prints "undefined", not "global"
       var scope = "local"; // Variable initialized here, but defined everywhere
       console.log(scope); // Prints "local"
   }
   ```

   将函数声明“提前”至函数体顶部，同时变量初始化留在原来的位置

8. 使用var声明的变量，定义了全局对象的一个属性，创建的这个属性是不可配置的，无法通过delete运算符删除。

   ```JavaScript
   var truevar = 1; // A properly declared global variable, nondeletable.
   fakevar = 2; // Creates a deletable property of the global object.
   this.fakevar2 = 3; // This does the same thing.
   delete truevar // => false: variable not deleted
   delete fakevar // => true: variable deleted
   delete this.fakevar2 // => true: variable deleted
   ```

   

9. 全局变量是全局对象的属性，局部变量当做跟函数调用相关的某个对象的属性——ES 3称为调用对象，ES 5 称为声明上下文对象

10. 作用域链——全局变量在程序中始终都是有定义的，局部变量在声明它的函数体内以及其所嵌套的函数内始终是由定义的。

11. 在不包含嵌套的函数体内，作用域链上有两个对象，一个是定义函数参数和局部变量的对象，第二个是全局对象。



