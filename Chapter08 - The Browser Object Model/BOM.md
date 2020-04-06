# 《JavaScript 高级程序设计》第八章 : BOM

## 简介   

`BOM` 与网页内容无关，它为 JavaScript 提供了操作浏览器的功能。

`BOM` 事实上的标准，实际上是浏览器厂商目前共有实现的部分。

`W3C` 已经将 `BOM` 中最基础的部分纳入了 `HTML5` 的规范中。

## Window

`window` 是浏览器窗口的实例，它为 JavaScript 提共了访问浏览器窗口的接口，同时也是 ECMAScript 标准中规定的 `Global` 对象的扩展实现，更是全局作用域中的“变量对象”。

因为 `window` 是全局对象，所以在全局中定义的变量或者是局部作用域中未通过变量声明关键字定义的变量都会被作为全局 `window` 对象的属性附加处理。

需要注意的是，我们无法使用 `delete` 操作符删除在全局作用域中通过“变量声明关键字” 为 `window` 对象声明的属性，但是可以删除 `window.property = value` 方式附加的属性。

```javascript
var a = 1;
window.b = 2;

console.log(delete a); //false
console.log(delete b); //true
```

> IE9 以下版本通过 `delete` 删除 `window` 的属性会报错。

究其原因在于这两种添加方式添加的属性其属性描述符并不相同，通过 `var` 关键字声明的变量，其属性特征 `configurable:false` 为不可配置，所以无法删除，而直接添加的全局属性，则为可配置，可删除。

```javascript
var a = 1;
window.b = 2;

var a_descriptor = Object.getOwnPropertyDescriptor(window, "a");
var b_descriptor = Object.getOwnPropertyDescriptor(window, "b");

console.log(a_descriptor.configurable); //false
console.log(b_descriptor.configurable); //true
```

### frames

如果网页使用到了框架技术，那么每个框架页面都会拥有一个属于自己独立的 `window` 对象，并且统一保存在父窗口的 `frames` 集合中，通过索引的方式即可获取：

```javascript
window.frames[0];
window.frames[1];
```

当然也可以使用框架的 `name` 作为索引来获取：

```javascript
window.frames["myIframe"];
```

### top

返回当前窗口的顶层窗口 `window` 对象（如果使用到了框架技术的话）。

如果当前的页面没有使用到框架技术，那么 `top === window`;

### parent

返回当前窗口的父窗口。

如果当前的页面没有使用到框架技术，那么 `parent === window`;

### self

永远指向当前窗口的引用。`self === window`

### 获取窗口位置

跨浏览器获取浏览器窗口距离屏幕左上角的距离需要借助兼容性属性 `screenLeft`,`screenTop` 与 `screenX`、`screenY`.

```javascript
function getBrowserLeftTop() {
  return {
    left:
      typeof window.screenLeft === "number"
        ? window.screenLeft
        : window.screenX || 0,
    top:
      typeof window.screenTop === "number"
        ? window.screenTop
        : window.screenY || 0
  };
}
```

### 移动窗口位置

`moveTo(x, y)` 与 `moveBy(x, y)` 方法可以移动浏览器窗口位置，但是兼容性很严重，而且只常用于 JS 建立的子窗口对象，除了 IE 基本上没有浏览器兼容移动主窗口。

### 获取窗口尺寸

窗口可以分为“浏览器窗口” 与 “视图窗口”。

“浏览器窗口”目前只支持 `IE9+、Firefox、Chrome` 等浏览器通过 `outerWidth/outerHeight` 来获取。

```javascript
function getBrowserSize() {
  return {
    width: window.outerWidth || 0,
    height: window.outerHeight || 0
  };
}
```

“视图窗口”指的是页面可是区域的尺寸，即 `viewport` 视口的尺寸。

通过以下方法即可以跨浏览器获取视口的尺寸：

```javascript
function getWindowSize() {
  return {
    pageHeight:
      window.innerHeight ||
      document.documentElement.clientHeight ||
      document.body.clientHeight,
    pageWidth:
      window.innerWidth ||
      document.documentElement.clientWidth ||
      document.body.clientWidht
  };
}
```

### 调整窗口尺寸

同样的 `window` 对象也提供了 `resizeTo(x , y)` 与 `resizeBy(x , y)` 方法来调整窗口的尺寸，但兼容性很差只有 IE 浏览器支持。

> 但是这些方法可以用于通过 JS 创建的子窗口对象。

### 导航与打开新窗口

`window.open(url?: string, target?: string, features?: string, replace?: boolean)` 既可以将指定的 URL 导航到指定已经存在的框架窗口中显示也可以打开一个全新的浏览器窗口来显示页面。

- url ：要在窗口中显示的网址
- target ：目标窗口的名称
- features ：窗口特殊配置的字符串，由 `&` 与 `=` 进行拼接
- replace ：是否要用当前的 url 来替换上一个打开的 URL 历史记录

如果只指定了 `url` 与 `target` 这两个参数：

```javascript
window.open("http://example.com", "_blank");
```

则与 `a` 链接的使用效果相同：

```html
<a href="url" target="_blank | target"></a>
```

一般 `window.open` 常用于在新打开的窗口中显示页面，而第三个参数 `features` 支持以下参数来配置新打开窗口的外观：

|   参数名称   |   值   |               说明               | 默认值 |
| :----------: | :----: | :------------------------------: | :----: |
|  fullscreen  | yes/no |        窗口是否可以最大化        |  N/A   |
| height/width |  数值  |           新窗口的大小           |  N/A   |
|   left/top   |  数值  |         新窗口的左上坐标         |  N/A   |
|   location   | yes/no |          是否显示地址栏          |   no   |
|   menubar    | yes/no |          是否显示菜单栏          |   no   |
|  resizable   | yes/no |   是否可以通过拖动改变窗口大小   |   no   |
|  scrollbars  | yes/no | 页面内容显示不下时，是否允许滚动 |   no   |
|    status    | yes/no |          是否显示状态栏          |   no   |
|   toolbar    | yes/no |          是否显示工具栏          |   no   |

调用 `window.open` 方法打开或导航窗口时，会返回当前要打开或导航窗口的 `window` 对象引用。

对于 `window.open` 方法返回的子窗口对象，具有以下三个属性非常有用：

- opener ：返回新窗口的父窗口的引用，即该窗口是在那个主窗口中打开的。
- close() ：关闭打开的子窗口，注意：该方法无法关闭浏览器主窗口。
- closed ：子窗口是否关闭，很明显该属性对主窗口无用。

```javascript
var newWindow = window.open("http://example.com", "newWindow");
newWindow.opener === self; //true
newWindow.close(); //关闭
newWindow.closed; //true
```

除此之外，由于使用 JS 打开的窗口其灵活性要比主窗口大的多，因此之前的哪些受限的对窗口操作的方法，基本上都可以在新窗口中使用：

- resizeBy();
- resizeTo();
- moveTo();
- moveBy();

由于黑历史的原因（打开新窗口技术被常用于广告弹出），当我们在使用 `open` 方法再打开新窗口的时候，最好进行一定的验证，确保新窗口是否确实打开成功。

```javascript
var blocked = false;
var newWindow = null;
try {
  newWindow = window.open("http://example.com", "_blank");
  if (!newWindow) {
    blocked = true;
  }
} catch (e) {
  blocked = true;
}
```

### 定时器

`setTimeout` 是单次定时器，`setInterval` 则是循环定时器。

每次调用定时器函数都会返回一个定时器的 ID，类型为数值型，我们可以将这个 ID 传入到对应的 `clearTimeout` 与 `clearInterval` 方法中进行定时器的清除。

每次清除定时器后，最好也将定时器的 ID 置为 null，以方便全局环境中垃圾的回收机制进行回收处理：

```javascript
var timer = setTimeout(fn, 1000);
clearTimeout(timer);

timer = null;
```

对于循环定时器的调用而言可能会出现下一次的调用会发生在前一次调用结束之前调用（`setInterval` 只会在指定的间隔时间中重复调用，但不会判断上次调用是否完成），因此我们建议使用单次定时器来模拟循环定时器（在 `setTimeout` 内再调用 `setTimeout` 这样便可以保证上一次定时执行已经完成）。

最后由于定时器是异步代码，JS 是单线程会首先执行同步代码，然后将异步代码放在一个异步队列中排队轮询执行，所以如果当前的定时器之前还有其它的异步程序在执行，那么便无法准确的在规定的时间间隔中被调用，但总的来说执行时间大于等于间隔时间，这是可以确定的。

虽然我们可以设置定时器的间隔时间为 `0`，但通常浏览器默认的最小间隔时 `4ms`。

> 我们不能在 `setTimeout` 进行定时操作，因为它的调用时间是自身的间隔时间加上其内部定时任务执行完成所需的时间。

### 系统对话框

- alert : 警告框
- confirm : 确认框
- prompt : 输入框
- print : 打印框
- find : 查找。

**find**

在页面中搜索某个单词或字符且高亮显示，如果在页面中查找到，则返回 `true` 否则 返回 `false` 。

```javascript
var result = find("test");
result; //true
```

> 这个方法的缺点在于每次只能高亮选中一个搜索结果。

## Location

`Location` 对象保存了浏览器所加载文档的 URL 信息。

`Location` 对象非常特殊，它即被 `window` 引用，也被 `document` 对象引用。

```javascript
window.location === document.location; //true
```

下面是 `location` 对象的常用属性，这里从属性的可写性以及对浏览器的影响情况和是否产生历史记录来分类说明。

| 属性名称 |                               说明                                |  可写性   |  影响  | 历史记录 |
| :------: | :---------------------------------------------------------------: | :-------: | :----: | -------- |
|   href   |                返回浏览器所加载文档完整的 URL 信息                | 可读/可写 |  刷新  | 产生记录 |
|  search  |   返回文档 URL 地址中的查询字符串(即 `?` 之后的 0 个或多个字符)   | 可读/可写 |  刷新  | 产生记录 |
|   hash   | 返回文档 URL 地址中的`hash`字符串（即 `#` 之后的 0 个或多个字符） | 可读/可写 | 不刷新 | 产生记录 |
|   host   |              返回 URL 地址中的主机名与端口号(如果有)              | 可读/可写 |  刷新  | 产生记录 |
| hostname |               返回 URL 地址中的主机名（不含端口号）               | 可读/可写 |  刷新  | 产生记录 |
|   port   |                      返回 URL 地址中的端口号                      | 可读/可写 |  刷新  | 产生记录 |
| pathname |                返回 URL 地址中文档的路径(目录)信息                | 可读/可写 |  刷新  | 产生记录 |
| protocol |                       返回 URL 地址中的协议                       | 可读/可写 |  刷新  | 产生记录 |

从上表我们可以直观的得出 `location` 对象的所有属性都是可读可写的，通过对以上属性的赋值我们可以逐段或整体性地修改浏览器的 URL。

当我们对 URL 的值进行修改时，除了 `hash` 属性在写入新值时不会刷新浏览器，其它属性都会触发浏览器刷新，从新请求资源与加载页面。

下面则是 `location` 对象的方法，重点是关注每种方法是否会覆盖浏览器的历史访问记录。

|       方法名        |                                          说明                                           |          历史记录          |
| :-----------------: | :-------------------------------------------------------------------------------------: | :------------------------: |
|     assign(url)     |               加载指定 URL 地址的文档，其操作类似与 `location.href` 赋值                |          产生记录          |
|     toString()      |                   获取完整 URL 地址信息，相当于 `location.href` 取值                    |             无             |
| reolad(forceReload) | 重新加载当前页面，相当于 `F5` 刷新，如果传入值为 `true`，则表示硬加载，即`Ctrl+F5` 刷新 |          产生记录          |
|    replace(url)     |                            用指定的 URL 来覆盖当前打开的页面                            | 会覆盖上一次的历史访问记录 |

**不记录历史记录**

如果我们想让浏览器的上一步按钮失效，则使用 `replace` 方法就做到，它会用当前的 url 来覆盖上一次访问页面的历史记录，这样当我们点击上一个按钮时，永远都无法回到被覆盖记录的那个页面。

```javascript
// Link to www.a.com
// Link to www.b.com
location.replace("www.c.com");
history.go(-1); // => www.a.com
```

**解析 URL 地址信息**

实际上在 JavaScript 中不仅 `Location` 对象可以保存浏览器加载文档的 URL 地址信息，实际上 `<a>` 标签也可以保存 href 中有关的 URL 信息，这一点非常有用（舍去了正则大量的比对工作）.

```javascript
function byLinkGetUrl(url) {
  var oLink = document.createElement("a");
  var property = {
    href: void 0,
    search: void 0,
    hash: void 0,
    host: void 0,
    hostname: void 0,
    hostname: void 0,
    port: void 0,
    protocol: void 0,
    pathname: void 0
  };

  oLink.href = url;

  for (var k in property) {
    var value = oLink[k];
    if (value) property[k] = value;
  }

  return property;
}
```

**获取 URL 参数**

使用 `location` 对象或者利用 `a`标签的特性，我们只能获取 URL 上粗粒度的信息，如果想获取更细粒度的 URL 参数，则需要我们单独使用 JS 去处理了。

```javascript
function getUrlParams(search) {
  if (!search) return "";
  if (typeof search != "string") throw new Error("string type error to Parse");
  if (/^[?#]$/.test(search.charAt())) {
    search = search.slice(1);
  }

  var args = search.split("&");
  var decode = window.decodeURIComponent || null;
  var result = {};
  var key;
  var value;

  for (var i = 0; i < args.length; i++) {
    var items = args[i].split("=");
    if (items.length) {
      key = items[0] ? (decode ? decode(items[0]) : items[0]) : "";
      value = items[1] ? (decode ? decode(items[1]) : items[1]) : "";
      result[key] = value;
    }
  }

  return result;
}
```

## Navigator

`navigator`对象是由 Netscape Navigator 2.0 引入，现已经被大多数其它浏览器支持。

`navigator`对象中保存了浏览器自身的相关信息，下面列举了 HTML5 时代现代浏览器（Firefox\Chrome\Microsoft Edge）具有的一些有用的属性：

|        属性         |                                                                                                                                 说明                                                                                                                                  |      兼容性      |
| :-----------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :--------------: |
|     appVersion      |                                                                                                                                                                                                                                                                       |       All        |
|      bluetooth      |                                                                                             `navigator.bluetooth.requestDevice({filters:[]})` 可呼起浏览器的蓝牙配对界面                                                                                              |      Chrome      |
|      clipboard      |                                                                                    剪贴板对象，该对象专门用来标准化复制、粘贴（即取代 `document.execommand()`）使用需要用户授权。                                                                                     |  Chrom,Firefox   |
|     connection      |                                                                                                                             网络相关信息                                                                                                                              |      Chrome      |
|    cookieEnabled    |                                                                                                                            cookie 是否开启                                                                                                                            |       All        |
|     geolocation     |                                                                                                                               地理定位                                                                                                                                |       All        |
| hardwareConcurrency |                                                                                                                        返回地理定位设备的状态                                                                                                                         |       All        |
|      languages      |                                                                                                                           浏览器支持的语言                                                                                                                            |       All        |
|   maxTouchPoints    |                                                                                                                          支持的最大触摸点数                                                                                                                           |       All        |
|       onLine        |                                                                                                                        是否链接 internet 网络                                                                                                                         |       All        |
|      platform       |                                                                                                                            当前的操作系统                                                                                                                             |       All        |
|       plugins       |                                                                                                                              安装的插件                                                                                                                               |       All        |
|    mediaDevices     |                                                                                               使用多媒体设备，可以在浏览器中调用摄像机，录音社保等，使用需要用户授权。                                                                                                |   All（HTML5)    |
|  mediaCapabilities  |                                                                                                                     检查设备播放多媒体资源的能力                                                                                                                      |  Chrome,Firefox  |
|    mediaSession     | 通过扩展多媒体数据源信息(metadata)来自定义通知信息（例如音频文件播放时在通知栏、锁屏时的显示信息）同时还可以在通知栏、锁屏中为相应的操作按钮绑定事件处理方法（下一曲、暂停、播放等），该属性常用于移动端 Chrome，对于 PC 端，可以想到的使用场景就是带 touchbar 的 mac |     Chrome57     |
|     doNotTrack      |                                                   DNT，默认值为 false，每次请求时浏览器都会通知服务器用户是否允许被跟踪，例如搜索记录等，但最终的决定权属于服务端。设置为 true,标识通知服务端不要跟踪用户的操作数据                                                   |       All        |
|      userAgent      |                                                                                                          用户代理信息,旧时代的浏览器检测就主要使用该属性的值                                                                                                          |       All        |
|    serviceWorker    |                                                                    用于离线应用的持久化缓存，它相当于 JS 多线程的一种变体，主要用于静态资源的缓存、读取本地资源、拦截代理请求，自定义响应内容等。                                                                     |       All        |
|       storage       |                                                                                    `navigator.storage` 返回一个 `storageManage` 存储管理器，可以获取本地存储空间的配额以及使用情况                                                                                    | Chrome , Firefox |

**插件检测**

在非 IE 浏览器中，我们检测插件非常简单，枚举 `navigator.plugins` 属性即可：

```javascript
function hasPlugin(name) {
  name = name.toLowerCase();
  for (var i = 0; i < navigator.plugins.length; i++) {
    if (navigator.plugins[i].name.toLowerCase().indexOf(name) > -1) {
      return true;
    }
  }
  return false;
}
```

而对于 IE 浏览器特别是老版本的 IE 浏览器，则实现起来就没那么容易了：

```javascript
function hasIePlugin(name) {
  try {
    new ActiveXObject(name);
    return true;
  } catch (e) {
    return false;
  }
}

alert(hasIEPlugin("ShockwaveFlash.ShockwaveFlash"));
```

> 通常我们为了兼容性使用，两个方法会一同使用，先判断 hasPlugin 的值如果不满足再判断 hasIePlugin 方法。

## Screen

|    属性     |                                               说明                                               |     兼容性     |
| :---------: | :----------------------------------------------------------------------------------------------: | :------------: |
|   height    |                                          屏幕的像素高度                                          |      all       |
|    width    |                                          屏幕的像素宽度                                          |      all       |
| availHeight | 有效的屏幕高度（即去掉系统底部状态栏或其它工具栏的高度）可以理解为浏览器最大化时所占据的屏幕高度 |      all       |
| availWidth  |      有效的屏幕宽度（由于系统默认水平方向没有状态栏或其它工具栏）通常情况下与屏幕的宽度相同      |      all       |
|  availLeft  |                浏览器距离屏幕左侧有效的间隔距离（未被系统部件占用的左侧的像素值）                | Chrome,Firefox |
|  availTop   |                浏览器距离屏幕顶部的有效间隔距离（未被系统部件占用的顶部的像素值）                | Chrome,Firefox |
| colorDepth  |                              显示器的颜色深度，通常都是 24 位真彩色                              |                |
| orientation |                 获取屏幕的角度，绑定屏幕方向改变时的事件，以及锁定或解锁屏幕方向                 | Chrome,Firefox |

**获取屏幕方向**

```javascript
screen.orientation.angle; //获取角度
screen.orientation.type; //字符串描述的屏幕方向
```

**屏幕翻转检测**

```javascript
screen.orientation.onchange = function() {};
screen.orientation.addEventListener("change", function() {}, false);
```

**兼容性**

虽然 Microsoft Edge 与 IE11 不支持 `orientation` 对象来判断屏幕的方向以及屏幕翻转时的触发事件，但它们也有自己的私有实现

```javascript
// IE,Edge - orientation
screen.msOrientation; //获取屏幕方向
screen.onmsorientationchange; //当屏幕方向发生改变时的触发方法。
```

在非 IE 浏览器中 `screen.orientation` 对象还提供了 `lock()` 与 `unlock()` 方法来锁定屏幕方向与解锁，但是貌似很鸡肋：

- 必须要全屏才能锁定，但是全屏必须要通过用户手动触发事件
- 在有的手机浏览器中，全屏便会自动横向。

## History

`history` 对象保存了用户访问页面的历史记录。

通过`history.length`属性返回的数值，我们可以得出用户已经访问了多少个页面，如果 `history.length === 0`，那么很明显用户启动浏览器第一个打开的就是我们的页面。

使用 `history.forward()` 与 `history.back()` 等方法可以模拟浏览器原生的“前进”与“后退”的按钮。如果想跳转到指定位置的历史记录，则可以使用 `history.go(delta)` 方法，传入一个数值，则表示要跳转到该历史记录位置，如果数值为负数，则表示后退到指定的位置：

```javascript
history.go(1); //前进一步
history.go(2); //前进两步
history.go(-1); //后退一步
```

到了 HTML5 时期，则又新增了两个一方法与一个属性：

- `pushState()` ：浏览器地址栏会导航到指定的 url ，但并不会触发浏览器刷新重新加载资源，并且会把每次导航的 url 加入页面的访问历史记录中。
- `replaceState()` ：浏览器地址栏会导航到指定的 url ，但并不会触发浏览器刷新重新加载资源，与 `pushState()` 方法不同的时候，`replaceState()` 方法会用当前的 URL 替换上一个历史记录，即不产生历史记录。
- `state` ：返回 URL 导航时所附加的数据，这个数据就是 `pushState()` 或 `replaceState()` 方法接收的第一个值，其格式与类型完全由使用者自己控制。

**pushState(data , title , url)**

- data：每次导航指定 URL 时附加的数据，类型可以为任意类型，常用对象类型来描述当前 URL 的状态信息。
- title：跳转 URL 的标题，通常默认空字符串即可。
- url ： 要导航到的 URL 地址。

> 由于 pushState 与 replaceState 的参数相同，故不重复介绍。

为了监听 `pushState()` 与 `replaceState()` 对页面地址（URL）的改变，HTML5 还为 `window` 对象提供了 `onpopstate` 事件，但是该事件的触发比较严苛，只能通过以下方式触发：

- 用户点击浏览器的“上一个/下一个”按钮
- 执行 `history.go()`
- 执行 `history.forward()`
- 执行 `history.back()`

```javascript
history.pushState({ data: "label1" }, "", "test.html#label");
history.pushState({ data: "label2" }, "", "test.html#label");
history.back();

window.onpopstate = function(data) {
  console.log(history.state); //{data: "label1"}
};
```

> 注意：使用 `pushState()` 与 `replaceState()` 修改 URL 的 hash 值，并不会触发 `window.onhashchange` 事件。
