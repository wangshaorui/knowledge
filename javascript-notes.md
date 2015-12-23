# JavaScript 基础 
主要参考 [廖雪峰的 JavaScript 教程](http://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000)

## 基本类型 

* number 
* boolean
* string
* array
* object
* NaN : 计算错误返回，所有与 NaN 相关的计算返回 NaN
* undefined
* null

注:

 
* 转换类型使用 `Number` `Boolean` `String`,例如 `Number('123')`
* 使用 `parseInt()` | `parseFloat()` 转换任意类型到 `number`
* 转换到 `string` 使用 `String()` | `obj.toString()`
* `number` 对象到 `string` 使用 `(123).toString()`
* 判断 `Array` 使用 `Array.isArray(arr)`
* 判断 `null` 使用 `myvar == null` | `isNaN()`
* 判断全局变量的存在 `typeof window.myVar === 'undefined'`
* 判断函数内部变量是否存在使用 `typeof myVar === 'undefined'`


## 闭包
### 使用 call 传递 this
使用 `call` 指定函数使用的 `this`, 若不指定 `this` 指向 `window`

* call : `fun.call(thisArg[, arg1[, arg2[, ...]]])`
* apply : `fun.apply(thisArg, [argsArray])`


```
function Product(name, price) {
  this.name = name;
  this.price = price;
}

function Food(name, price) {
  Product.call(this, name, price);
  this.category = 'food';
}

var apple = Foos('apple', 30)
```

## Date

### 常用方法
```javascript
var now = new Date()
now; // Tue Dec 22 2015 10:26:08 GMT+0800 (CST)
now.getFullYear() //205
.getMonth()
.getDate() // 24号
.getDay // 3 获取星期,从 1 开始
.getHours()
.getMinutes()
.getSeconds()
.geMilliseconds() // 875 毫秒数
.getTime() // 时间戳
```

### 创建自定义 Date

```
var d = new Date(2015, 5, 19, 20, 15, 30, 123)
// 2015.6.19 20：15:30:123 月份从 0 开始

var timestamp = Date.parse('2015-06-24T19:49:22.875+08:00')

var dateObj = new Date(timestamp)
```

### 时区
```
var d= new Date()
d.toLocaleString()
d.toUTCString()
```

**时间戳**

```
nowTimestamp = Data.now() | new Date().getTime()
```

## RegExp
### 创建
```
var re1 = /ABC\-001/
var re2 = new RegExp('ABC\\-001')
```

### 常用方法
```
var re = /xxx/
// 检测是否符合
re.test(str) // return -> boolean 判断是否符合条件

// 切割字符串
str.split(/\s+/) // 'a b  c' -> ['a', 'b', 'c']

// 分组
var re = /(\d)\d(\d)/
re.exec("123") // return -> ["123", "1", "3"]
re.exec("abc') // return -> null
```

### 匹配类型


#### 全局匹配

```
var re = /\d{3}/g // 使用 /g
var s = '123a456')
re.exec(s) // return -> ["123"]
re.lastIndex // 3 下一次搜索的起点
re.exec(s) // return -> ["456"]
re.lastIndex // 4
re.exec(s) // return -> null
re.lastIndex // 0
```

类似: `/m` 匹配多行 `/i` 忽略大小写

## JSON
```
// to json
obj = {hello:"world", age:12}

JSON.stringify(obj)
// return -> {"hello":"world", "age":12} 

JSON.stringify(obj, (value, key) => { //do some thing 
										return value;})

注: 传入的函数参数顺序为 value / key 
```

### 自定义 json 返回 
```
var obj = {hello:"world", 
			age:12,
			toJSON: function () {
				return {
					hello: this.hello
				}
			}}
// 使用 箭头函数不行 不知为何
```

### 过滤返回

```
JSON.stringify(obj,['needvar1', 'needvar2'])
```
### 反序列化
```
JSON.parse('[1, 2, 3, true]'); // [1, 2, 3, true]

// 处理 序列
JSON.parse('[1, 2, 3, true]', (v, k) => typeof v === 'number' ? v+1: v)

// k, v 在接收到 1，2，3， true 之后还会接受到整个处理后的数组 [2, 3, 4, true] 导致一些奇异的行为 ?
```

## 原型 

JavaScript不区分类和实例的概念，而是通过原型`prototype`来实现面向对象编程

```
xiaoming.__proto__ = Student;
// 把小明的原型指向 Student 类似于 xiaoming 继承于 Student

```

### Object.create()

`Object.create(parent)` 方法可以传入一个原型对象，并创建一个基于该原型的新对象，但是新对象只有 `parent` 的方法，没有 `parent` 的属性

### 原型链

当我们用`obj.xxx`访问一个对象的属性时，`JavaScript`引擎先在当前对象上查找该属性，如果没有找到，就到其`原型对象`上找，如果还没有找到，就一直上溯到`Object.prototype`对象，最后，如果还没有找到，就只能返回`undefined`

```
var arr = []
// arr -> Array.prototype -> Object.prototype -> null

var func = () => 1
// func -> Function.prototype -> Object.prototype -> null
```
### 构造函数
```
function Student(name) {
	this.name = name;
	this.getName = () => "hello " + this.name
}

xm = new Student('xm')
xm.getName() // return -> 'hello xm'
// xm ----> Student.prototype ----> Object.prototype ----> null
```
```
xh = new Student('xh')
```
此时 `xh` / `xm` 都保存有一份 `getName` 的代码，虽然函数是一样的
可以将 `Student` 的 `getName` 绑定 到 `Student` 的 `prototype` 上，这样 `xm` / `xh` 之类的可以共用一份方法节省内存

```
function Student(name) {
	this.name = name;
}

Student.prototype.getName = () => 'hello ' + this.name
```
一种更好的实现

```
function Student(props) {
    this.name = props.name || '匿名'; // 默认值为'匿名'
    this.grade = props.grade || 1; // 默认值为1
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
};

function createStudent(props) {
    return new Student(props || {})
}
```
## 常见对象
### screen
表示屏幕的信息

* width : 屏幕宽度，以像素为单位
* height : 屏幕高度,以像素为单位
* colorDepth : 放回颜色位数,如8、 16、 24

### navigater
表示浏览器的信息

* navigator.appName：浏览器名称；
* navigator.appVersion：浏览器版本；
* navigator.language：浏览器设置的语言；
* navigator.platform：操作系统类型；
* navigator.userAgent：浏览器设定的User-Agent字符串
 
### window
表示全局作用域，同时表示浏览器窗口

* innerWidth
* innerHeight

表示浏览器窗口的内部宽度和高度。内部宽高是指除去菜单栏、工具栏、边框等占位元素后，用于显示网页的净宽高

* outerWidth
* outerHeight

获取浏览器窗口的整个宽高


### location
表示当前 url 信息

* href : 当前完整 url 信息, 可通过赋值造成 `url` 跳转 `http://www.example.com:8080/path/index.html?a=1&b=2#TOP`, 
* protocol : 'http:'
* host : 'www.example.com'
* port : 8080
* pathname : '/path/index.html'
* search : '?a=1&b=2'
* hash : 'TOP'

* location.reload() 刷新当前页
* location.assign('new path') 调整到新的路径下

### document

表示当前页面，`document` 对象就是整个 `DOM` 树的根节点

* title : 当前窗口标题
* cookie : 获取当前页面 `cookie`
* getElementById()
* getElementByTagName() 
* getElementByClassName()

服务器在设置`Cookie`时可以使用`httpOnly`，设定了`httpOnly`的`Cookie`将不能被`JavaScript`读取

### history

表示浏览历史

* back()
* foward()

目前大量使用`AJAX`和页面交互，简单粗暴地调用`history.back()`可能会让用户感到非常愤怒

## 操作 DOM
### 选择

* document.getElementById()
* document.getElementByTagName()
* document.getElementByClassName()

**使用 CSS 选择器**

* document.querySelector('#id')
* document.querySelectorAll('div > p')

**获取一个元素的所有子节点**

* parent.children

### 更新 DOM

#### ele.innerHTML
不仅可直接修改文本，而且可以通过 `HTML` 直接修改 `DOM` 节点

注:此时不会对文本内容转义，需要注意是否可能会被 `XSS` 攻击

```
ele.innerHTML = 'ABC <p>hello</p> DEF'
```

#### ele.innerText / ele.textContent
修改的文本中若有 `HTML` 属性，会自动转义

`innerText` 不返回回隐藏元素的文本

`textContent` 返回所有文本

```
ele.innerText = 'hello<p>xyz</p>'
ele.textContent = 'hello<p>xyz</p>'
// hello&lt;p&gt;xyz&lt;/p&gt
```

#### ele.style

`ele.style` 返回所有的 `CSS` 属性，命名改为驼峰式

### 插入 DOM
#### 创建 DOM
* ele.createElement('style')
* ele.setAttribute('type', 'text/css')

#### 插入
* parent.appendChild(ele) : 在`parent`的最后插入
* parent.insertBefore(newele, refele) : 在`refele`前插入`newele`

### 删除 DOM
* parent.removeChild(childEle)

### 设置表单的值

对 `text、password、hidden以及select` 可以直接设置 `value`
`ele.value = 'value'`

对 `radio` 需要检查或者设置 `checked` 

### 提交表单

**使用 form.submit() 不推荐**

```
var form = document.querySelector('#form')
// do something
form.submit()
```

```
<form id="test-form" onsubmit="return checkForm()">
    <input type="text" name="test">
    <button type="submit">Submit</button>
</form>

<script>
function checkForm() {
	var form = document.querySelector('#form')
	// do something
	return true // false 不提交
}
</script>
```
没有`name`属性的`<input>`的数据不会被提交

## AJAX
### XMLHttpRequest

```
function success(text) {
    var textarea = document.getElementById('test-response-text');
    textarea.value = text;
}

function fail(code) {
    var textarea = document.getElementById('test-response-text');
    textarea.value = 'Error code: ' + code;
}

var request = new XMLHttpRequest(); // 新建XMLHttpRequest对象

request.onreadystatechange = function () { // 状态发生变化时，函数被回调
    if (request.readyState === 4) { // 成功完成
        // 判断响应结果:
        if (request.status === 200) {
            // 成功，通过responseText拿到响应的文本:
            return success(request.responseText);
        } else {
            // 失败，根据响应码判断失败原因:
            return fail(request.status);
        }
    } else {
        // HTTP请求还在继续...
    }
}

// 发送请求:
request.open('GET', '/api/categories');
request.send();

alert('请求已发送，请等待响应...');

```

### 安全限制

默认情况下，JavaScript在发送AJAX请求时，URL的域名必须和当前页面完全一致。绕过可以使用 `JSONP` 或 `CORS`

#### JSONP(旧式)
这种方式跨域实际上是利用了浏览器允许跨域引用`JavaScript资源`.它有个限制，只能用GET请求，并且要求返回`JavaScript`。

```
以163的股票查询URL为例，对于URL：http://api.money.126.net/data/feed/0000001,1399001?callback=refreshPrice，你将得到如下返回：

refreshPrice({"0000001":{"code": "0000001", ... });

因此我们需要首先在页面中准备好回调函数：

function refreshPrice(data) {
    var p = document.getElementById('test-jsonp');
    p.innerHTML = '当前价格：' +
        data['0000001'].name +': ' + 
        data['0000001'].price + '；' +
        data['1399001'].name + ': ' +
        data['1399001'].price;
}

最后用getPrice()函数触发：

function getPrice() {
    var
        js = document.createElement('script'),
        head = document.getElementsByTagName('head')[0];
    js.src = 'http://api.money.126.net/data/feed/0000001,1399001?callback=refreshPrice';
    head.appendChild(js);
}
```

#### CORS(HTML5)

`Cross-Origin Resource Sharing`，是`HTML5`规范定义的如何跨域访问资源。
`Origin`表示本域，也就是浏览器当前页面的域。当`JavaScript`向外域（如`sina.com`）发起请求后，浏览器收到响应后，首先检查`Access-Control-Allow-Origin`是否包含本域，如果是，则此次跨域请求成功，如果不是，则请求失败，`JavaScript`将无法获取到响应的任何数据。

![](http://www.liaoxuefeng.com/files/attachments/00143640805071744d58164a40e42ef92b9973824451595000/l)


只要响应头`Access-Control-Allow-Origin`为`http://my.com`，或者是`*`，本次请求就可以成功

上面这种跨域请求，称之为`“简单请求”`。简单请求包括`GET`、`HEAD`和`POST`（`POST`的`Content-Type`类型
仅限`application/x-www-form-urlencoded`、`multipart/form-data`和`text/plain`），并且不能出现任何自定义头（例如，`X-Custom: 12345`），通常能满足`90%`的需求

对于`PUT`、`DELETE`以及其他类型如`application/json`的`POST`请求，在发送`AJAX`请求之前，浏览器会先发送一个`OPTIONS`请求（称为`preflighted`请求）到这个`URL`上，询问目标服务器是否接受：
```
OPTIONS /path/to/resource HTTP/1.1
Host: bar.com
Origin: http://my.com
Access-Control-Request-Method: POST
```
服务器必须响应并明确指出允许的`Method`：

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://my.com
Access-Control-Allow-Methods: POST, GET, PUT, OPTIONS
Access-Control-Max-Age: 86400
```

浏览器确认服务器响应的`Access-Control-Allow-Methods`头确实包含将要发送的`AJAX`请求的`Method`，才会继续发送`AJAX`，否则，抛出一个错误。

由于以`POST`、`PUT`方式传送`JSON`格式的数据在`REST`中很常见，所以要跨域正确处理`POST`和`PUT`请求，服务器端必须正确响应`OPTIONS`请求


### Promise
更加友好的异步执行方式
![](http://www.liaoxuefeng.com/files/attachments/001436512391628944d5da9a5654a35b0ace38246f30b9c000/l)
```
function test(resolve, reject) {
	setTimeOut(() => resolve(result), 1000)
	setTimeOut(() => reject(reason), 1000)
}
new Promise(test).then(function (result) {
    console.log('成功：' + result);
}).catch(function (reason) {
    console.log('失败：' + reason);
});
```

#### 并行执行
我们需要从两个不同的URL分别获得用户的个人信息和好友列表，这两个任务是可以并行执行的，用`Promise.all()`实现如下：

```
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 500, 'P1');
});

var p2 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 600, 'P2');
});
// 同时执行p1和p2，并在它们都完成后执行then:
Promise.all([p1, p2]).then(function (results) {
    console.log(results); // 获得一个Array: ['P1', 'P2']
});
```

#### 获取最先完成的结果
多个异步任务是为了容错。比如，同时向两个URL读取用户的个人信息，只需要获得先返回的结果即可。这种情况下，用`Promise.race()`实现：

```
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 500, 'P1');
});
var p2 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 600, 'P2');
});
Promise.race([p1, p2]).then(function (result) {
    console.log(result); // 'P1'
});
```

## JQuery

### 选择器
```
$('css 选择器')
```

### 查找和过滤

#### find()
最常见的查找是在某个节点的所有子节点中查找，使用`find()`方法，它本身又接收一个任意的选择器

#### parent()
* 无参数时获取当前节点的父节点
* 接受 `CSS` 选择器时从当前的父节点开始向上查找，直到找到某个符合条件的节点并返回

#### next() 和 prev()

同级节点使用 `next()` 和 `prev()` 来获取

#### filter()
* 传入 `CSS` 选择器

```
 var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
var a = langs.filter('.dy'); // 拿到JavaScript, Python, Scheme
```

* 传入过滤函数

```
var langs = $('ul.lang li'); // 拿到JavaScript, Python, Swift, Scheme和Haskell
langs.filter(function () {
    return this.innerHTML.indexOf('S') === 0; // 返回S开头的节点
}); // 拿到Swift, Scheme
```

#### map()
```
eles.map(function () {// do something})
```

#### first() / last() / slice()

### 操作 DOM

#### 获取 / 设置 文本
```
// 获取
ele.html()
ele.text()
//设置
ele.html('new html')
ele.text('new text')
```
此操作会作用在 `JQuery` 对象包含的所有 `DOM` 节点下
#### 获取 / 设置 CSS
```
// 获取
ele.css('name')

// 设置
$('ele').css('name', 'value') // name 支持 CSS 命名和驼峰
$('#test-css li.dy>span').css('background-color', '#ffd351').css('color', 'red');
ele.css('name', '') // 清除 CSS 属性
```

#### 操作 class 
```
var div = $('div')
div.hasClass('highlight'); // false， class是否包含highlight
div.addClass('highlight'); // 添加highlight这个class
div.removeClass('highlight'); // 删除highlight这个class
```
#### show() / hide()
隐藏或者显示 `JQuery` 对象

#### 操作 DOM 元素大小
```
ele.width()
ele.height()

//设置
ele.width(200)
ele.height('300px')
```

#### 操作 DOM 元素的属性
```
// 获取
ele.attr('type')

// 设置
ele.attr('type', 'button')

// 移除
ele.removeAttr('type')
```

**prop()**

```
prop()方法和attr()类似，但是HTML5规定有一种属性在DOM节点中可以没有值，只有出现与不出现两种，例如：

<input id="test-radio" type="radio" name="test" checked value="1">
等价于：

<input id="test-radio" type="radio" name="test" checked="checked" value="1">

attr()和prop()对于属性checked处理有所不同：

var radio = $('#test-radio');
radio.attr('checked'); // 'checked'
radio.prop('checked'); // true

prop()返回值更合理一些。不过，用is()方法判断更好：

var radio = $('#test-radio');
radio.is(':checked'); // true
类似的属性还有selected，处理时最好用is(':selected')
```

#### 操作表单 var()
```
// 获取 
ele.var()

// 设置
ele.var('hello')
```

### 修改 DOM
#### 尾添加 append() 头添加 prepend()
可以传入原始的`DOM`对象，`jQuery`对象和函数对象
传入函数时，要求返回一个字符串、`DOM`对象或者`jQuery`对象。


若 `append()`的对象已经存在，则会移动这些对象
#### 把新节点插入到指定位置
同级节点可以用`after()`或者`before()`方法

```
var js = $('#test-div>ul>li:first-child');
js.after('<li><span>Lua</span></li>');
```

#### 删除 DOM remove()
```
ele.remove()
```

#### 绑定事件

```
ele.on('click', () => alert('hello'))
// 或者
ele.click(() => alert('hello'))
```

可绑定的事件列表

```
鼠标事件

click: 鼠标单击时触发；
dblclick：鼠标双击时触发；
mouseenter：鼠标进入时触发；
mouseleave：鼠标移出时触发；
mousemove：鼠标在DOM内部移动时触发；
hover：鼠标进入和退出时触发两个函数，相当于mouseenter加上mouseleave。

键盘事件

键盘事件仅作用在当前焦点的DOM上，通常是<input>和<textarea>。

keydown：键盘按下时触发；
keyup：键盘松开时触发；
keypress：按一次键后触发。

其他事件

focus：当DOM获得焦点时触发；
blur：当DOM失去焦点时触发；
change：当<input>、<select>或<textarea>的内容改变时触发；
submit：当<form>提交时触发；
ready：当页面被载入并且DOM树完成初始化后触发
ready仅作用于document对象。由于ready事件在DOM完成初始化后触发，且只触发一次，所以非常适合用来写其他的初始化代码
```

#### 事件参数

有些事件，如`mousemove`和`keypress`，我们需要获取鼠标位置和按键的值，否则监听这些事件就没什么意义了。所有事件都会传入`Event`对象作为参数，可以从`Event`对象上获取到更多的信息

#### 取消绑定

一个已被绑定的事件可以解除绑定，通过`off('click', function)`实现：

```
function hello() {
    alert('hello!');
}

a.click(hello); // 绑定事件

// 10秒钟后解除绑定:
setTimeout(function () {
    a.off('click', hello);
}, 10000);
```

#### 事件触发条件

一个需要注意的问题是，事件的触发总是由用户操作引发的

```
// 手动触发
ele.change()
ele.click()
// 为 ele.tirgger('click')的简写
```

### 动画
[效果](http://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434500456006abd6381dc3bb439d932cb895b62d9eee000)
#### show / hide
```
ele.show(3000); // 3秒内消失
ele.hide(3000); // 3秒内展示
ele.toggle(3000); / 根据当前状态决定是 show() 还是 hide()
```

### slideUp /slideDown 窗帘形式
```
ele.slideUp()
ele.slideDown()
ele.slideToggle()
```

#### fadeIn / fadeOut 淡入淡出
```
ele.fadeIn('slow')
ele.fadeOut('slow')
ele.fadeToggle('slow')
```

#### 自定义动画 animate()
```
var div = $('#test-animate');
div.animate({
    opacity: 0.25,
    width: '256px',
    height: '256px'
}, 3000); // 在3秒钟内CSS过渡到设定值
```

`animate()`还可以再传入一个函数，当动画结束时，该函数将被调用：

```
var div = $('#test-animate');
div.animate({
    opacity: 0.25,
    width: '256px',
    height: '256px'
}, 3000, function () {
    console.log('动画已结束');
    // 恢复至初始状态:
    $(this).css('opacity', '1.0').css('width', '128px').css('height', '128px');
});
```

### AJAX

`jQuery`在全局对象`jQuery`（也就是`$`）绑定了`ajax()`函数。ajax(url, settings)函数需要接收一个URL和一个可选的settings对象，常用的选项如下：

```
async：是否异步执行AJAX请求，默认为true，千万不要指定为false；

method：发送的Method，缺省为'GET'，可指定为'POST'、'PUT'等；

contentType：发送POST请求的格式，默认值为'application/x-www-form-urlencoded; charset=UTF-8'，也可以指定为text/plain、application/json；

data：发送的数据，可以是字符串、数组或object。如果是GET请求，data将被转换成query附加到URL上，如果是POST请求，根据contentType把data序列化成合适的格式；

headers：发送的额外的HTTP头，必须是一个object；

dataType：接收的数据格式，可以指定为'html'、'xml'、'json'、'text'等，缺省情况下根据响应的Content-Type猜测。
```
#### 响应处理
```
var jqxhr = $.ajax('/api/categories', {
    dataType: 'json'
}).done(function (data) {
    ajaxLog('成功, 收到的数据: ' + JSON.stringify(data));
}).fail(function (xhr, status) {
    ajaxLog('失败: ' + xhr.status + ', 原因: ' + status);
}).always(function () {
    ajaxLog('请求完成: 无论成功或失败都会调用');
});
```
#### get / post / getJSON
```
$.get(url, paramsObj)
$.post(url, paramsObj) // 默认 content-type 为 application/x-www-form-urlencoded

var jqxhr = $.getJSON('/path/to/resource', {
    name: 'Bob Lee',
    check: 1
}).done(function (data) {
    // data已经被解析为JSON对象了
});
```

#### 安全限制

`jQuery`的`AJAX`完全封装的是`JavaScript`的`AJAX`操作，所以它的安全限制和前面讲的用`JavaScript`写`AJAX`完全一样。

如果需要使用`JSONP`，可以在`ajax()`中设置`jsonp: 'callback'`，让`jQuery`实现`JSONP`跨域加载数据

### jQuery 插件
给`jQuery`对象绑定一个新方法是通过扩展`$.fn`对象实现的


```
$.fn.highlight1 = function () {
    // this已绑定为当前jQuery对象:
    this.css('backgroundColor', '#fffceb').css('color', '#d85030');
    return this;
}
```