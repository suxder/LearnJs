# Some notes about Js

## 一、this与闭包

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

## 二、JS中的对象与方法

### 1.特殊对象

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

### 2.没有toString()方法的类型

> `null`和`undefined`没有toString()方法，这两个特殊值要除外，虽然`null`还伪装成了`object`类型。

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

### 4.parseInt()或者parseFloat()函数

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

## 三、时间戳

### 1.定义

> 时间戳是个什么东西？时间戳是一个自增的整数，它表示从1970年1月1日零时整的GMT时区开始的那一刻，到现在的毫秒数。假设浏览器所在电脑的时间是准确的，那么世界上无论哪个时区的电脑，它们此刻产生的时间戳数字都是一样的，所以，时间戳可以精确地表示一个时刻，并且与时区无关。

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

## 四、正则表达式

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

## 五、Json与面向对象编程

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

## 六、继承与浏览器

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

`location`对象表示当前页面的URL信息。  
可以用`location.href`获取完整的URL  要获得URL各个部分的值，可以这么写：

```javascript
location.protocol; // 'http'
location.host; // 'www.example.com'
location.port; // '8080'
location.pathname; // '/path/index.html'
location.search; // '?a=1&b=2'
location.hash; // 'TOP'
```

##### document

`document`对象表示当前页面。由于HTML在浏览器中以DOM形式表示为树形结构，`document`对象就是整个DOM树的根节点

要查找DOM树的某个节点，需要从`document`对象开始查找。最常用的查找是根据ID和Tag Name。

用`document`对象提供的`getElementById()`和`getElementsByTagName()`可以按ID获得一个DOM节点和按Tag名称获得一组DOM节点:

```html
<dl id="drink-menu" style="border:solid 1px #ccc;padding:6px;">
    <dt>摩卡</dt>
    <dd>热摩卡咖啡</dd>
    <dt>酸奶</dt>
    <dd>北京老酸奶</dd>
    <dt>果汁</dt>
    <dd>鲜榨苹果汁</dd>
</dl>
```

```javascript
var menu = document.getElementById('drink-menu');
var drinks = document.getElementsByTagName('dt');
var i, s;

s = '提供的饮料有:';
for (i=0; i<drinks.length; i++) {
    s = s + drinks[i].innerHTML + ',';
}
console.log(s);
```

**Cookie属性**

> `Cookie是由服务器发送的key-value标示符。因为HTTP协议是无状态的，但是服务器要区分到底是哪个用户发过来的请求，就可以用Cookie来区分。当一个用户成功登录后，服务器发送一个Cookie给浏览器，例如user=ABC123XYZ(加密的字符串)...，此后，浏览器访问该网站时，会在请求头附上这个Cookie，服务器根据Cookie即可区分出用户。`
>
> `Cookie还可以存储网站的一些设置，例如，页面显示的语言等等。`

Javascript能够通过`document.ducument`读取到当前页面的Cookie  
由于JavaScript能读取到页面的Cookie，而用户的登录信息通常也存在Cookie中，这就造成了巨大的安全隐患，这是因为在HTML页面中引入第三方的JavaScript代码是允许的  

>  `为了解决这个问题，服务器在设置Cookie时可以使用httpOnly，设定了httpOnly的Cookie将不能被JavaScript读取。这个行为由浏览器实现，主流浏览器均支持httpOnly选项，IE从IE6 SP1开始支持。`
>
> `为了确保安全，服务器端在设置Cookie时，应该始终坚持使用httpOnly。`

##### history

JavaScript可以调用`history`对象的`back()`或`forward ()`，相当于用户点击了浏览器的“后退”或“前进”按钮。

> 新手开始设计Web页面时喜欢在登录页登录成功时调用`history.back()`，试图回到登录前的页面。这是一种错误的方法。
>
> 任何情况，你都不应该使用`history`这个对象了。

## 七、DOM

### 1.操作DOM

- 始终记住DOM是一个树形结构。操作一个DOM节点实际上就是这么几个操作：
- - 更新：更新该DOM节点的内容，相当于更新了该DOM节点表示的HTML的内容；
  - 遍历：遍历该DOM节点下的子节点，以便进行进一步操作；
  - 添加：在该DOM节点下新增一个子节点，相当于动态增加了一个HTML节点；
  - 删除：将该节点从HTML中删除，相当于删掉了该DOM节点的内容以及它包含的所有子节点。
- 在操作一个DOM节点前，我们需要通过各种方式先拿到这个DOM节点。最常用的方法是`document.getElementById()`和`document.getElementsByTagName()`，以及CSS选择器`document.getElementsByClassName()`。
- 由于ID在HTML文档中是唯一的，所以`document.getElementById()`可以直接定位唯一的一个DOM节点。`document.getElementsByTagName()`和`document.getElementsByClassName()`总是返回一组DOM节点。要精确地选择DOM，可以先定位父节点，再从父节点开始选择，以缩小范围。

例子：

```javascript
// 返回ID为'test'的节点：
var test = document.getElementById('test');

// 先定位ID为'test-table'的节点，再返回其内部所有tr节点：
var trs = document.getElementById('test-table').getElementsByTagName('tr');

// 先定位ID为'test-div'的节点，再返回其内部所有class包含red的节点：
var reds = document.getElementById('test-div').getElementsByClassName('red');

// 获取节点test下的所有直属子节点:
var cs = test.children;

// 获取节点test下第一个、最后一个子节点：
var first = test.firstElementChild;
var last = test.lastElementChild;
```

例题：

```html
<!-- HTML结构 -->
<div id="test-div">
<div class="c-red">
    <p id="test-p">JavaScript</p>
    <p>Java</p>
  </div>
  <div class="c-red c-green">
    <p>Python</p>
    <p>Ruby</p>
    <p>Swift</p>
  </div>
  <div class="c-green">
    <p>Scheme</p>
    <p>Haskell</p>
  </div>
</div>
```

```javascript
// 选择<p>JavaScript</p>:
var js = document.getElementById('test-p');
var js = document.querySelector('#test-p');

// 选择<p>Python</p>,<p>Ruby</p>,<p>Swift</p>:
var arr = document.getElementsByClassName('c-red')[1].children;
var arr = document.getElementsByClassName('c-red')[1].getElementsByTagName('p');
var arr = document.querySelectorAll('div.c-red.c-green > p');
var arr = document.querySelectorAll('.c-red.c-green > p');
var arr = document.querySelectorAll('div[class="c-red c-green"] > p');


// 选择<p>Haskell</p>:
var haskell = document.getElementsByClassName('c-green')[1].lastElementChild;
var haskell = document.querySelectorAll('div[class="c-green"] > p')[1];
var haskell = document.querySelectorAll('.c-green > p')[4];
```

### 2.更新DOM

#### 1.修改`innerHTML`属性

这种方式异常强大，不但可以修改一个DOM节点内的文本内容，该可以通过HTMl片段来修改节点内部的子树。

例：

```javascript
// 获取<p id="p-id">...</p>
var p = document.getElementById('p-id');
// 设置文本为abc:
p.innerHTML = 'ABC'; // <p id="p-id">ABC</p>
// 设置HTML:
p.innerHTML = 'ABC <span style="color:red">RED</span> XYZ';
// <p>...</p>的内部结构已修改
```

> 用`innerHTML`时要注意，是否需要写入HTML。如果写入的字符串是通过网络拿到的，要注意对字符编码来避免XSS攻击。

#### 2.修改`innerText`或者`textContent`属性

> 自动会对字符串进行编码，保证无法设置任何HTML标签。

```javascript
// 获取<p id="p-id">...</p>
var p = document.getElementById('p-id');
// 设置文本:
p.innerText = '<script>alert("Hi")</script>';
// HTML被自动编码，无法设置一个<script>节点:
// <p id="p-id">&lt;script&gt;alert("Hi")&lt;/script&gt;</p>
```

> 在读取属性时，`innerText`不返回任何隐藏元素的文本，`textContent`返回所有文本。

#### 3.修改CSS

DOM节点的`style`属性对应所有的CSS，可以直接获取或设置。因为CSS允许`font-size`这样的名称，但它并非JavaScript有效的属性名，所以需要在JavaScript中改写为**驼峰式**命名`fontSize`：

```javascript
// 获取<p id="p-id">...</p>
var p = document.getElementById('p-id');
// 设置CSS:
p.style.color = '#ff0000';
p.style.fontSize = '20px';
p.style.paddingTop = '2em';
```

### 3.插入DOM

#### 1.使用`appendChild`,将一个子节点添加到父节点的最后一个子节点。

**例子：**

```html
<!-- HTML结构 -->
<p id="js">JavaScript</p>
<div id="list">
    <p id="java">Java</p>
    <p id="python">Python</p>
    <p id="scheme">Scheme</p>
</div>
```

目标:把`<p id="js">JavaScript</p>`添加到`<div id="list">`的最后一项

```javascript
var
    js = document.getElementById('js'),
    list = document.getElementById('list');
list.appendChild(js);
```

HTML结构：

```html
<!-- HTML结构 -->
<div id="list">
    <p id="java">Java</p>
    <p id="python">Python</p>
    <p id="scheme">Scheme</p>
    <p id="js">JavaScript</p>
</div>
```

> 上述例子存在一些bug，亟待解决

#### 2.创建一个新的节点，插入到指定位置。

```javascript
var
    list = document.getElementById('list'),
    haskell = document.createElement('p');
haskell.id = 'haskell';
haskell.innerText = 'Haskell';
list.appendChild(haskell);
```

> 动态的创建一个节点，然后添加到DOM树中可以实现很多功能。

##### 1.使用`insertBefore`

使用`parentElement.insertBefore(newElement, referenceElement);`，子节点会插入到`referenceElement`之前。

**例子**：

原有HTMl结构：

```html
<!-- HTML结构 -->
<div id="list">
    <p id="java">Java</p>
    <p id="python">Python</p>
    <p id="scheme">Scheme</p>
</div>
```

javascript：

```javascript
var
    list = document.getElementById('list'),
    ref = document.getElementById('python'),
    haskell = document.createElement('p');
haskell.id = 'haskell';
haskell.innerText = 'Haskell';
list.insertBefore(haskell, ref);
```

新的HTMl结构：

```html
<!-- HTML结构 -->
<div id="list">
    <p id="java">Java</p>
    <p id="haskell">Haskell</p>
    <p id="python">Python</p>
    <p id="scheme">Scheme</p>
</div>
```

## 八、表单 

#### 1.操作表单

##### 1.表单的输入控件

HTML表单的输入控件主要有以下几种：

- 文本框，对应的`<input type="text">`，用于输入文本；
- 口令框，对应的`<input type="password">`，用于输入口令；
- 单选框，对应的`<input type="radio">`，用于选择一项；
- 复选框，对应的`<input type="checkbox">`，用于选择多项；
- 下拉框，对应的`<select>`，用于选择一项；
- 隐藏文本，对应的`<input type="hidden">`，用户不可见，但表单提交时会把隐藏文本发送到服务器。

##### 2.获取值

###### 1.值是有实际意义的表单控件

对于`text` `password` `select` `hidden` 等可以直接 获取该节点的引用，再用xxx.value 来获取值

###### 2.对于值为HTML预设的表单控件

我们需要获取的是用户是否“勾上了”选项，应该用`checked` 来进行判断：

```javascript
// <label><input type="radio" name="weekday" id="monday" value="1"> Monday</label>
// <label><input type="radio" name="weekday" id="tuesday" value="2"> Tuesday</label>
var mon = document.getElementById('monday');
var tue = document.getElementById('tuesday');
mon.value; // '1'
tue.value; // '2'
mon.checked; // true或者false
tue.checked; // true或者false
```

##### 3.设置值

- 同上，对于`text` `password` `select` `hidden` 等可以直接 获取该节点的引用，再用xxx.value 进行值的设置
- 对于 单选框 ，设置`checked`为`true` 或 `false` 即可

##### 4.提交表单

> 利用javascript 来对表单提交进行控制有两种方式

###### 1.方式1

利用<form>元素的的submit方法 来进行提交 ，但该方式 会妨碍浏览器默认的提交方式。  
浏览器默认用户点击<button type = 'submit'>时或者 用户**在最后一个输入框按回车键**提交表单。

```javascript
<!-- HTML -->
<form id="test-form">
    <input type="text" name="test">
    <button type="button" onclick="doSubmitForm()">Submit</button>
</form>

<script>
function doSubmitForm() {
    var form = document.getElementById('test-form');
    // 可以在此修改form的input...
    // 提交form:
    form.submit();
}
</script>
```

###### 2.方式2

响应<form>本身的`onsubmit`事件，在提交<form>时做修改。  


```javascript
<!-- HTML -->
<form id="test-form" onsubmit="return checkForm()">
    <input type="text" name="test">
    <button type="submit">Submit</button>
</form>

<script>
function checkForm() {
    //利用form 指向form节点
    var form = document.getElementById('test-form');
    // 可以在此修改form的input...
    // 继续下一步:
    return true;
}
</script>
```

##### 5.form

###### 1.利用`hidden`

```javascript
<!-- HTML -->
<form id="login-form" method="post" onsubmit="return checkForm()">
    //文本输入框
    <input type="text" id="username" name="username">
    //密码输入框,由于该文本框没有name属性，故该文本框的内容不会被提交
    <input type="password" id="input-password">
    //隐藏的文本输入框，最后实际提交的是该隐藏文本框的内容
    <input type="hidden" id="md5-password" name="password">
    <button type="submit">Submit</button>
</form>

<script>
function checkForm() {
    var input_pwd = document.getElementById('input-password');
    var md5_pwd = document.getElementById('md5-password');
    // 把用户输入的明文变为MD5:
    md5_pwd.value = toMD5(input_pwd.value);
    // 继续下一步:
    return true;
}
</script>
```

> 注意到`id`为`md5-password`的`<input>`标记了`name="password"`，而用户输入的`id`为`input-password`的`<input>`没有`name`属性。没有`name`属性的`<input>`的数据不会被提交。

## 九、文件

### 1.基础操作

HTML中上传文件的控件 <input type = 'file'>

> 当一个表单包含`<input type="file">`时，表单的`enctype`必须指定为`multipart/form-data`，`method`必须指定为`post`，浏览器才能正确编码并以`multipart/form-data`格式发送表单的数据。


利用JavaScript可以获取上传文件的路径以及扩展名，从而可以实现对文件扩展名的检查与控制。

```javascript
var f = document.getElementById('test-file-upload');
var filename = f.value; // 'C:\fakepath\test.png'
if (!filename || !(filename.endsWith('.jpg') || filename.endsWith('.png') || filename.endsWith('.gif'))) {
    alert('Can only upload image file.');
    return false;
}
```

### 2.FileAPI

- 在HTML5之前，很多需要对文件进行更多操作的网站不得不使用`Flash`这种第三方插件。
- 随着HTML5的普及，新增的File API允许JavaScript读取文件内容，获得更多的文件信息。
- HTML5的File API提供了`File`和`FileReader`两个主要对象，可以获得文件信息并读取文件。

```javascript
var
    fileInput = document.getElementById('test-image-file'),
    info = document.getElementById('test-file-info'),
    preview = document.getElementById('test-image-preview');
// 监听change事件:
fileInput.addEventListener('change', function () {
    // 清除背景图片:
    preview.style.backgroundImage = '';
    // 检查文件是否选择:
    if (!fileInput.value) {
        info.innerHTML = '没有选择文件';
        return;
    }
    // 获取File引用:
    var file = fileInput.files[0];
    // 获取File信息:
    info.innerHTML = '文件: ' + file.name + '<br>' +
                     '大小: ' + file.size + '<br>' +
                     '修改: ' + file.lastModifiedDate;
    if (file.type !== 'image/jpeg' && file.type !== 'image/png' && file.type !== 'image/gif') {
        alert('不是有效的图片文件!');
        return;
    }
    // 读取文件:
    var reader = new FileReader();
    reader.onload = function(e) {
        var
            data = e.target.result; // 'data:image/jpeg;base64,/9j/4AAQSk...(base64编码)...'            
        preview.style.backgroundImage = 'url(' + data + ')';
    };
    // 以DataURL的形式读取文件:
    reader.readAsDataURL(file);
});
```

如何理解上述代码：  
https://developer.mozilla.org/zh-CN/docs/Web/Reference/API 文件处理API参考

https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener 添加事件监听

https://developer.mozilla.org/zh-CN/docs/Web/Events 事件 类型

### 3.回调函数

> 在JavaScript中，浏览器的JavaScript执行引擎在执行JavaScript代码时，总是以单线程模式执行，也就是说，任何时候，JavaScript代码都不可能同时有多于1个线程在执行。

在javascript中执行多线程实际上都是异步调用，譬如上面的：

`reader.readAsDataURL(file);`

就会发起一个异步操作来读取文件内容。因为是异步操作，所以我们在JavaScript代码中就**不知道什么时候操作结束**，因此需要**先设置一个回调函数**：

```javascript
reader.onload = function(e) {
    // 当文件读取完成后，自动调用此函数:
};
```

当文件读取完成后，JavaScript引擎将自动调用我们设置的回调函数。执行回调函数时，文件已经读取完毕，所以我们可以在回调函数内部安全地获得文件内容。