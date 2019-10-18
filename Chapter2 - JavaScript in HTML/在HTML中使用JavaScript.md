# 《JavaScript 高级程序设计》第二章：在 HTML 中使用 JavaScript

`script`  标记是 Netspace 公司最早为在 html 中引入 javascript 代码而创造的 HTML 元素，并最终被 HTML 规范采纳。
`script`  标记有四个比较重要的属性：

- [x] src
- [x] type
- [x] defer
- [x] async

## defer

添加了`defer` 属性的脚本会等待页面加载并解析完成后执行（即在浏览器解析到`</html>`之后）。并且多个延迟脚本会根据顺序执行。

```html
<head>
  <script defer="defer" src="1.js"></script>
  <script defer="defer" src="2.js"></script>
</head>
```

> 兼容性：IE6.0+ 、FF3.5、SF5、CH1+

## async

与延迟脚本 `defer`  相同，异步脚本 `async` 也会等待页面加载并解析完成后执行，只是多个异步脚本其执行顺序并不相同。

```html
<head>
  <script async src="1.js"></script>
  <script async src="2.js"></script>
</head>
```

> 兼容性：IE10.0+ 、FF3.6、SF5、CH1+

总的来说，`defer` 与 `async` 都会等待页面加载并解析完成后执行，但是前者有序后者无序。
