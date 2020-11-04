---

---

# learnJs
# Some notes about Js

## Day1

### **1**.关于 this 的一些思考 与感悟

 this这个 keyword非常的困惑,但是其实有一个好方法可以理解.

    1. 检查 ' . ' 左边是谁invoke 这个函数. 例如 xiaoming.age();  age函数里面有this, 然后 '. ' 旁边是xiaoming , 那么this就是指向xiaoming了.这种叫做 Implicit Binding.
    
    2. 如果点旁边没有,那就检查有没有用到 bind, apply, call 这三种, 有的话就是调用此方法的对象. 这种叫做 explicit binding.
    
    3. 如果上面两个都没有就检查代码里面有没有用到new 这个keyword, 有的话那就是指向new旁边的函数对象. 这种叫做new binding
    
    4. 上面三个都没有, 检查是不是有arrow function, 有arrow function的话就是, 那么指向是arrow function的lexical binding 的对象. 就是她的parent. 这种叫做 lexical binding
    
    5. 全部都没有如果不是strict mode那就是window对象了.. strict就是 error (undefined).
### ***2***.闭包

  1.当闭包 返回的函数中含有循环变量或者 会变化的变量时：

```
function count() {
        var arr = [];
        for (var i=1; i<=3; i++) {
            arr.push(function () {
                return i * i;
            });
        }
        return arr;
    }
    var results = count();
    var f1 = results[0];
    var f2 = results[1];
    var f3 = results[2];
```

  理解：

```
首先我们弄懂上面代码的运行流程：首先var results = count();之后，函数count已经被调用了，所以一次执行函数内的各段代码：var arr = [];，for (var i=1; i<=3; i++)，这个for循环尤其值得注意。

​	因为此时循环体执行了push方法，将一个个函数function () { return i * i;}添加到数组内，但是这个函数并没有被调用，还只是一个变量，所以for循环依次执行，直到i = 4。

​	因为闭包，内部函数function () { return i * i;}引用的i就是外部变量，for循环中的i = 4。所以，之后数组arr内的函数的i都是4。
​	调用函数count后，变量results已经是数组arr了。

​	数组里面元素依次是function f1() { return i * i;} function f2() { return i * i;} function f3() { return i * i;}。

​	但是三个函数都没有被调用，直到var f1 = results[0];，此时function f1() { return i * i;}开始执行，如上段所写，此时的i = 4，所以，返回值就是16了。
```

## Day2

### 1.在Js的世界里，一切皆为对象，但是某些对象依然不太一样

```
typeof 123; // 'number'
typeof NaN; // 'number'
typeof 'str'; // 'string'
typeof true; // 'boolean'
typeof undefined; // 'undefined'
typeof Math.abs; // 'function'
typeof null; // 'object'
typeof []; // 'object'
typeof {}; // 'object'
```

### 2.`null`和`undefined`没有toString()方法，这两个特殊值要除外，虽然`null`还伪装成了`object`类型。

### 3.`number`对象调用`toString()`报SyntaxError：

```
123.toString(); // SyntaxError
```

特殊处理为：

```
123..toString(); // '123', 注意是两个点！
(123).toString(); // '123'
```

原因：

```
个人认为是，它将 123. 看成是一个小数，然后你直接跟toString()，它不会认为 . 是调用方法，而是看成是数字的一部分。
```

我这样写没有一点问题：

```
const number = 123;console.log(number.toString());
```

但是如果直接：

```
console.log(123.toString());
```

则会报错。目的是为了消除歧义。

### 4.为什么要用parseInt()或者parseFloat()函数来将其他类型的对象转化为数字

[https://www.cnblogs.com/wangyang0210/p/9526440.html]: 

```
个人觉得：如上述连接所言，如果使用Number()函数来将其他对象转化为Number，数字值转化正常。而：
--
-Boolean值，true->1,false->0
null值，转为0
字符串为空，转为0
字符串中是纯科学计数，数值会直接转为十进制的数值
undefined , 对象 , 数组 ,字符串不为纯数字和科学计数时 返回NaN
--
parseInt()
--数值正常转化,如果为浮点数,则转为整型,舍弃小数点后的数值
--
--数字开头的字符串,只转换数字整数部分,忽略字母部分
--
--二进制,八进制,十六进制转化时,转化为十进制的数值
--
--科学计数法,能解析时正常解析,不能时返回第一个数字
--
--Boolean,undefined , 对象 , 数组 ,空字符串,字符串开头不为纯数字和科学计数时 返回NaN
--
-parseFloat()
--
--
--数值正常转化,整型为整型,浮点型为浮点型
--
--数字开头的字符串,只转换数字整数部分,忽略字母部分
--
--二进制,八进制,十六进制转化时,转化为十进制的数值
--
--科学计数法,能解析时正常解析,超出范围则返回Infinity
--
--Boolean,undefined , 对象 , 数组 ,空字符串,字符串开头不为纯数字和科学计数时 返回NaN
--
--不能有多个小数点报语法错误
```

## Day3

### 1.时间戳是个什么东西？时间戳是一个自增的整数，它表示从1970年1月1日零时整的GMT时区开始的那一刻，到现在的毫秒数。假设浏览器所在电脑的时间是准确的，那么世界上无论哪个时区的电脑，它们此刻产生的时间戳数字都是一样的，所以，时间戳可以精确地表示一个时刻，并且与时区无关。

### 2.时间戳的理解

```
时间戳是一个相对值 只是在不同的时区表示的时间不同 举个不恰当的例子：如果把时间戳当成货币值 那时区就是货币单位 那么 数字1￥和1$是不同的
```

```
每个时区的起点不同，像北京是1970.1.1. 8:00 所以时间戳这个差值一样。
```

```
// 时间戳=0:
var d = new Date(0);
d.toUTCString(); // GMT+0:00时区
d.toLocaleString(); // 本地时区
```

```
时间戳是数字，跟时区有啥关系？

而且时间戳是绝对值！绝对值！绝对值！永远不变。

当你想把时间戳显示给用户的时候，先确定时区，再“翻译”成那个时区的日期和时间。没有确定时区，你无法把时间戳转成日期和时间。
```

## Day4

### 1.正则表达式的某些简化写法

```
/^[0-9a-zA-Z]+&/ ==/^\w+&/
```

### 2.一个可以匹配邮箱的正则表达式

```
/^[\w+\.]+\@\w+\.(com|org)$/
```

[xx、xx]匹配范围，[\w+\.]+用于匹配如xx@... 或xx.xx@两种email格式，\@\w+\. 是email通用部分， 如@qq. @123. ，com|org表示结尾可以是com 或者org  

```
/^\<([\w\s\w]+)\>+\s+(\w+\@\w+\.org|com)$/
```

注意用`()`表示的就是要提取的分组（Group）。比如：

`^(\d{3})-(\d{3,8})$`分别定义了两个组，可以直接从匹配的字符串中提取出区号和本地号码：

注意前面名字部分有< >需要转义 \< 、\> ，注意名字字段可能有空格（测试样例有，但实际可以没有），所以在[ ]匹配范围内内需要加。

不能单单看测试样例，还要注意将正则表达式写的更通用。  

## Day5

### 1.JSON

1. JSON是JavaScript Object Notation的缩写，它是一种数据交换格式    
2.  把任何JavaScript对象变成JSON，就是把这个对象序列化成一个JSON格式的字符串，这样才能够通过网络传递给其他计算机。
3.   如果我们收到一个JSON格式的字符串，只需要把它反序列化成一个JavaScript对象，就可以在JavaScript中直接使用这个对象了。  

```javascript
'use strict';

var xiaoming = {
    name: '小明',
    age: 14,
    gender: true,
    height: 1.65,
    grade: null,
    'middle-school': '\"W3C\" Middle School',
    skills: ['JavaScript', 'Java', 'Python', 'Lisp']
};
var s = JSON.stringify(xiaoming);
console.log(s);
```

输出为：

```json
{"name":"小明","age":14,"gender":true,"height":1.65,"grade":null,"middle-school":"\"W3C\" Middle School","skills":["JavaScript","Java","Python","Lisp"]}
```

加上参数，按缩进输出：

```
JSON.stringify(xiaoming, null, '  ');
```

```javascript
{
  "name": "小明",
  "age": 14,
  "gender": true,
  "height": 1.65,
  "grade": null,
  "middle-school": "\"W3C\" Middle School",
  "skills": [
    "JavaScript",
    "Java",
    "Python",
    "Lisp"
  ]
}
```

第一个参数为对象名，第二个参数用于筛选对象的键值，如果我们只想输出指定对象的属性，可以传入一个Array。    
第二个参数也可以是一个函数名，用于对每个键值对进行预先处理。  
**拿到一个JSON格式的字符串，我们直接用`JSON.parse()`把它变成一个JavaScript对象。**

`JSON.parse()`还可以接收一个函数，用来转换解析出的属性。

### 2.用JavaScript实现面向对象编程

#### 1.JavaScript不区分类和实例的概念，而是通过原型（prototype）来实现面向对象编程。

#### 2.创建对象

##### 1.原形链

> 当我们用`obj.xxx`访问一个对象的属性时，JavaScript引擎先在当前对象上查找该属性，如果没有找到，就到其原型对象上找，如果还没有找到，就一直上溯到`Object.prototype`对象，最后，如果还没有找到，就只能返回`undefined`。

当我们创建一个Array对象时的原形链：

```javascript
arr/*数组名*/ ----> Array.prototype ----> Object.prototype ----> null
```

当我们创建一个函数时的原形链：

```javascript
foo /*函数名*/ ----> Function.prototype ----> Object.prototype ----> null
```

> 如果原型链很长，那么访问一个对象的属性就会因为花更多的时间查找而变得更慢，因此要注意不要把原型链搞得太长。

##### 2.构造函数

1.定义一个构造函数（普通函数）：

```javascript
function Student(name) {
    this.name = name;
    this.hello = function () {
        alert('Hello, ' + this.name + '!');
    }
}
```

2.用new来调用这个函数，并返回一个对象：

```javascript
var xiaoming = new Student('小明');
xiaoming.name; // '小明'
xiaoming.hello(); // Hello, 小明!
```

> 写了`new`，它就变成了一个构造函数，它绑定的`this`指向新创建的对象，并默认返回`this`，也就是说，不需要在最后写`return this;`

3.xiaoming的原形链:

```
xiaoming ----> Student.prototype ----> Object.prototype ----> null
```

## Day6

### 1.构造函数与普通函数

> 为了区分构造函数与普通函数，按照约定，构造函数首字母应该大写，而普通函数首字母应该小写。

**tip:在调用构造函数时，要写new**

### 2.利用空函数实现C++或Java式的继承：

- 创建一个PrimaryStudent函数：

- ```javascript
  function PrimaryStudent(props) {
      // 调用Student构造函数，绑定this变量:
      Student.call(this, props);
      this.grade = props.grade || 1;
  }
  ```

- 此时利用PrimaryStudent构造函数创建的对象的原型链是：

- ```
  new PrimaryStudent() ----> PrimaryStudent.prototype ----> Object.prototype ----> null
  ```

- 目标原型链：

- ```
  new PrimaryStudent() ----> PrimaryStudent.prototype ----> Student.prototype ----> Object.prototype ----> null
  ```

> 我们必须借助一个中间对象来实现正确的原型链，这个中间对象的原型要指向`Student.prototype`。为了实现这一点，参考道爷（就是发明JSON的那个道格拉斯）的代码，中间对象可以用一个空函数`F`来实现

```javascript
// PrimaryStudent构造函数:
function PrimaryStudent(props) {
    Student.call(this, props);
    this.grade = props.grade || 1;
}

// 空函数F:
function F() {
}

// 把F的原型指向Student.prototype:
F.prototype = Student.prototype;

// 把PrimaryStudent的原型指向一个新的F对象，F对象的原型正好指向Student.prototype:
PrimaryStudent.prototype = new F();

// 把PrimaryStudent原型的构造函数修复为PrimaryStudent:
PrimaryStudent.prototype.constructor = PrimaryStudent;

// 继续在PrimaryStudent原型（就是new F()对象）上定义方法：
PrimaryStudent.prototype.getGrade = function () {
    return this.grade;
};

// 创建xiaoming:
var xiaoming = new PrimaryStudent({
    name: '小明',
    grade: 2
});
xiaoming.name; // '小明'
xiaoming.grade; // 2

// 验证原型:
xiaoming.__proto__ === PrimaryStudent.prototype; // true
xiaoming.__proto__.__proto__ === Student.prototype; // true

// 验证继承关系:
xiaoming instanceof PrimaryStudent; // true
xiaoming instanceof Student; // true
```

> 小结：JavaScript的原型继承实现方式就是：
>
> 1. 定义新的构造函数，并在内部用`call()`调用希望“继承”的构造函数，并绑定`this`；
> 2. 借助中间函数`F`实现原型链继承，最好通过封装的`inherits`函数完成；
> 3. 继续在新的构造函数的原型上定义新方法。

### 3.class继承

> 由于利用原型实现类和继承的方法过于复杂，ES6引入了新的关键字class ，目的就是使定义类更简单。

新的class式创建类的方法：

```javascript
class Student {
    constructor(name) {
        this.name = name;
    }

    hello() {
        alert('Hello, ' + this.name + '!');
    }
}
```

**注意hello函数的关键字function被省略**

新的class式继承方法：

```javascript
class PrimaryStudent extends Student {
    constructor(name, grade) {
        super(name); // 记得用super调用父类的构造方法!
        this.grade = grade;
    }

    myGrade() {
        alert('I am at grade ' + this.grade);
    }
}
```

extends表示原型链对象来自Student  
通过super(name)来调用父类的构造函数，使得父类的name属性正常初始化。  

### 4.浏览器

- IE 6~11：国内用得最多的IE浏览器，历来对W3C标准支持差。从IE10开始支持ES6标准；
- Chrome：Google出品的基于Webkit内核浏览器，内置了非常强悍的JavaScript引擎——V8。由于Chrome一经安装就时刻保持自升级，所以不用管它的版本，最新版早就支持ES6了；
- Safari：Apple的Mac系统自带的基于Webkit内核的浏览器，从OS X 10.7 Lion自带的6.1版本开始支持ES6，目前最新的OS X 10.11 El Capitan自带的Safari版本是9.x，早已支持ES6；
- Firefox：Mozilla自己研制的Gecko内核和JavaScript引擎OdinMonkey。早期的Firefox按版本发布，后来终于聪明地学习Chrome的做法进行自升级，时刻保持最新；
- 移动设备上目前iOS和Android两大阵营分别主要使用Apple的Safari和Google的Chrome，由于两者都是Webkit核心，结果HTML5首先在手机上全面普及（桌面绝对是Microsoft拖了后腿），对JavaScript的标准支持也很好，最新版本均支持ES6。

> 不同的浏览器对JavaScript支持的差异主要是，有些API的接口不一样，比如AJAX，File接口。对于ES6标准，不同的浏览器对各个特性支持也不一样。
>
> 在编写JavaScript的时候，就要充分考虑到浏览器的差异，尽量让同一份JavaScript代码能运行在不同的浏览器中。

#### 1.浏览器对象

##### window

`window`对象有`innerWidth`和`innerHeight`属性，可以获取浏览器窗口的内部宽度和高度。

e.g.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
  	<title>Document</title>
    <script>
    	'use strict';
    	
    	alert('window innner size:' + window.innerWidth + 'x' + window.innerHeight);
  </script>
</head>
<body>
</body>
</html>
```

对应的，还有一个`outerWidth`和`outerHeight`属性，可以获取浏览器窗口的整个宽高。

##### navigator

`navigator`对象表示浏览器的信息，最常用的属性包括：

- navigator.appName：浏览器名称；
- navigator.appVersion：浏览器版本；
- navigator.language：浏览器设置的语言；
- navigator.platform：操作系统类型；
- navigator.userAgent：浏览器设定的`User-Agent`字符串。

> `navigator`的信息可以很容易地被用户修改，所以JavaScript读取的值不一定是正确的。

> 扩充知识：短路运算符 
>
> 首先我们来解释一下短路运算符：
>
> 短路运算符就是从左到右的运算中前者满足要求，就不再执行后者了； 可以理解为：
>
>  &&为取假运算，从左到右依次判断，如果遇到一个假值，就返回假值，以后不再执行，否则返回最后一个真值；
>
> || 为取真运算，从左到右依次判断，如果遇到一个真值，就返回真值，以后不再执行，否则返回最后一个假值。
>
>  下面举个例子吧：
>
> let str
>
> let num = str || 'foo'
>
> 如果str是真值就直接返回了，后面短路就不会被返回了，如果为假值，则会返回后面的foo
>
>  let str= param && param.prop
>
> 如果param如果为真值则返回param.prop属性，否则返回param这个假值，这样在某些地方防止param为undefined的时候还取其属性造成报错。

##### screen

`screen`对象表示屏幕的信息，常用的属性有：

- screen.width：屏幕宽度，以像素为单位；
- screen.height：屏幕高度，以像素为单位；
- screen.colorDepth：返回颜色位数，如8、16、24。

##### location