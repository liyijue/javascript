### script
> src 对指定路径发起GET请求且不受同源策略影响, 但获取脚本后执行脚本的内容受同源策略影响

平时使用较多, 不需要栗子....

> defer 推迟执行脚本 - 告诉浏览器立即下载脚本, 但会在DOM结构生成后在执行脚本, **只适用于外部脚本**

文件: *defer*
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .bg_pink {
      width: 200px;
      height: 200px;
      background-color: pink;
    }
  </style>
  <script src="./deferJs.js" defer></script> // 去掉 defer会报错 报错原因是找不到对应的DOM元素
</head>

<body>
  <div id="app"></div>
</body>

</html>
```

文件: *defer*
```javascript
const dom = document.querySelector('#app')
dom.classList.add('bg_pink')
```

> async 异步执行脚本 - 告诉浏览器立即下载脚本, 不会按照顺序执行脚本文件, **同样只适用于外部脚本且使用async也同样告诉浏览器你不会使用 documet.write, 不过一般也不会使用这个方法**

文件: *async*
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .bg_pink {
      width: 200px;
      height: 200px;
      background-color: pink;
    }
    .bg_blue {
      width: 200px;
      height: 200px;
      background-color: blue;
    }
  </style>
  <script src="./asyncJs02.js" async></script>
  <script src="./asyncJs01.js" async></script>
</head>

<body>
  <div id="app"></div>
</body>

</html>
```

文件: *async - asyncJs01.js*
```javascript
var dom = document.querySelector('#app')
dom.classList.add('bg_pink')
```

文件: *async - asyncJs02.js*
```javascript
var dom = document.querySelector('#app')
dom.classList.add('bg_blue')
```

> 动态加载脚本, 通过创建一个 scrpit的DOM对其scr赋值, 在添加进DOM树上即可实现, 单这种方式获取支援对浏览器与加载器是不可见的, 这会严重影响它们在资源获取队列中的优先级, 这种方式可能会严重影响性能。想要让预加载器知道这些动态文件需要使用: 
```html
<!-- url - 脚本路径 -->
<link rel="preload" href="url"> 
```

文件: *dynamic*
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <!-- 告诉浏览器预加载 dynamicJs.js 文件 -->
  <link rel="preload" href="./dynamicJs.js">
  <style>
    .bg_pink {
      width: 200px;
      height: 200px;
      background-color: pink;
    }
  </style>
</head>

<body>
  <div id="app"></div>
  <script>
    const script = document.createElement('script')
    script.src = './dynamicJs.js'
    document.head.appendChild(script)
  </script>
</body>

</html>
```

文件: *dynamic*
```javascript
const dom = document.querySelector('#app')
dom.classList.add('bg_pink')
```

#### 行内script 与 外部script 对比
> 推荐将Javascript代码放在外部文件中, 理由如下

+ 可维护性
  - Javascript代码分散到很多HTML页面, 会导致维护苦难。而用一个目录保存所有 Javascript 文件, 则更容易维护, 这样开发者就可以独立于使用它们的HTML 页面来编辑代码。
+ 缓存
  - 浏览器会根据特定的设置缓存所有外部连接的 Javascript 文件, 这意味着如果两个页面都用到同一个文件, 则文件只需要下载一次, 这意味着提升页面加载速度
+ 适应未来
  - 将 Javascript 放到外部文件就不需要处理 XHTML中行内 script 的特殊情况, 外部文件中HTML中XHTML 语法是一样的

#### noscript
> 当浏览器不支持Javascript 执行里面的内容(现浏览器已经几乎100%都支持Javascript了)

```html
<noscript>这个页面显示必须要使用支持Javascript的浏览器。</noscript>
```

#### 总结
> javascript 是通过\<script> 元素插入到HTML 页面中的。这个元素可用于把Javascript 代码嵌入到HTML 页面中, 跟其他标记混合在一起, 也可用于引入保存在外部文件中的 Javascript。

+ 要包含外部 Javascript 文件, 必须将 src 属性设置为要包含文件的URL。文件可以跟网页在同一台服务器上, 也可以位于完全不同的域。

+ 所有\<script>元素会依照它们在网页中出现的次序进行解析。在不使用 defer 和 async 属性的情况下, 包含在\<script> 元素中的代码必须严格次序解析。

+ 对不推迟执行的脚本, 浏览器必须解析完位于\<script> 元素的代码, 然后才能继续渲染页面的剩余部分。为此, 通常把\<script>元素放在页面末尾, 介于主要内容之后, \<body>标签之前。

+ 可以使用 defer 属性把脚本推迟到文档渲染完毕后在执行。推迟的脚本原则上按照它们列出来的次序执行。

+ 可以使用 async 属性把脚本不需要等待其他脚本, 同时不阻塞稳定渲染, 既异步加载。异步加载不能保证它们在页面中出现的次序执行。

+ 通过使用\<noscript> 元素, 可以指定在浏览器不支持脚本时显示的内容。如果浏览器支持脚本则不会显示\<noscript> 中的内容
