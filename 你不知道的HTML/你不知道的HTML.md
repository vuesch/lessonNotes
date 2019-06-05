# ❤️你不知道的HTML❤️

[web前端面试之同源策略及解决方法](https://blog.csdn.net/animatecat/article/details/82744098)

## 1.img标签src属性

### 1⃣️同源策略

- 含义：协议、域名、端口号相同

- 目的：为了保证用户信息安全，防止恶意的网站窃取数据

  **注意⚠️：提交表单不受同源策略的限制（这样就产生了跨站脚本攻击）**

- 限制范围：

  + **Cookie、LocalStorage、IndexDB** 无法读取

    😳Cookie一般用来存用户名和密码，比较小，如果两个网页的一级域名相同，只是二级域名不同，浏览	 器允许通过设置**document.domain**共享cookie

    😳LocalStorage是直接存到浏览器的，做离线缓存，最大是5M，是按照域名来存储的，异步的js会存在  	 这里，可以用js来操作，是同步的

    😳SessionStorage是会话间的机制，关闭浏览器以后就销毁

    😳IndexDB和Web Sql是两种关系型数据库，是异步的，要用回调

    ​	 WebSql使用方法https://blog.csdn.net/hello_zyg/article/details/79148713

  + **DOM**无法获得

  + **AJAX**请求不能发送

- 如何设置同源策略（hosts）前提是必须在**一级域名相同**的前提下，

  例如：bbb.example.com   中的b.html要访问   aaa.example.com 中的a.html中的cookie

```html
aaa.example.com中的a.html要这样设置：
<script>
  document.domain = 'example.com';//设置同源策略
  document.cookie = 'test=hello';
</script>
bbb.example.com中的b.html就可以拿到a.html中的cookie
<script>
  document.cookie
</script>
```

- ⚠️最实用的设置cookie的方式

```html
在服务器进行设置，指定cooki的所属域名为一级域名，比如  .example.com

Set-Cookie:key=value;domain=.example.com;path=/
```

### 2⃣️src属性

img、ifram、script三个标签没有被同源策略限制，是因为其src属性没有被限制

### 3⃣️jsonp跨域原理及实现（只支持GET）

- 原理：利用标签的src属性访问一个跨域的地址，并传入一个前后端商量一致的回调函数名称，比如callback，然后将服务端的数据以该函数的参数传到前端。

- 实现

  + 原生js实现

  ```html
  后端代码：
  <?php
      $cb = $_GET['callback'];
      $data = array(
          'name'=> 'zs',
        'age'=>18,
          'gender'=>true
       );
      if($cb){
          echo $cb.'('.json_encode($data).')';
      }else {
          echo "nothing!";
      }
      
      
   ?>  
  
  前端代码：
  <script type="text/javascript">
      function test (data){
          console.log(data)
      }
  </script>
  <script src="http://book/test2.php?callback=test"></script>
  ```

  

  + jquery实现

  ```html
  <script>
      $('#btn').click(function () {
         $.ajax({
              url: "http://localhost:9090/student",
              type: "GET",
              dataType: "jsonp", //指定服务器返回的数据类型
              jsonp: "theFunction", //指定参数名称
              jsonpCallback: "showData", //指定回调函数名称
              success: function (data) {
                  console.info("调用success");
              }
          });
      })
  </script>
  ```

## 2.link标签

- 前端代码

```html
<head>
<meta charset="UTF-8">
<title>Title</title>
// 利用link的href属性跨域
<link rel="stylesheet" href="02link.php">
```

- 后端代码,模拟服务器的php文件

```php
<?php
    //告诉客户端服务器的文本类型
    header("Content-Type:text/css;charset=utf-8");
    echo "body{background-color: red}"

    //若果客户端需要输出js文件的话，文本类型就是text/javascript
    //header("Content-Type:text/javascript;charset=utf-8");
    //echo "alert(1)";
?>
```

## 3.css跨域

利用**background**可以引入外部文件，css攻击

## 4.html语义化

1.使用div进行布局， 不要用div进行无意义的包裹

2.尽可能少地使用无语义的标签，比如span和div

3.在语义不明显时，既可使用div或p时，尽量用p，因为p在默认情况下有上下间距，对兼容特殊终端有利

4.不要使用纯样式标签，如：b、font 、u等，改用css设置

5.需要强调的文本，可以包含在strong或者em标签中（浏览器的预设样式，能用css指定就不用他们），strong默认样式时加粗（不要用p），em是斜体（不要用i）

6.使用表格时，标题要用caption，表头要用thead，主体部分用tbody包裹，尾部用tfoot包裹，表头和一般单元格要区分开，表头用th，单元格用td

7.表单域要用fieldset标签包裹起来，并用legend标签说明表单的用途

8.每个input标签对应的说明文本都需要使用lable标签，并且通过为input设置id属性，在lable标签中设置for=someId来让说明文本和相对应的input关联起来

9.dl表格用法

```html
<dl>
  <dt>列表标题</dt>
  <dd>列表内容</dd>
</dl>
```

10.ol  有序列表   ul   无序列表

11.**h5语义化标签**，为了SEO

```html
<header>
  <nav></nav>		导航
</header>		头部

<article></article>		文章 正文
<section>
	<h1></h1>
  <p></p>
</section>		块

<aside></aside>		边栏
<footer></footer>		底部
<address></address>		地址
```

12.尽量少写html，多使用**伪元素**

- 减少DOM渲染的时间
- 浪费资源，代码冗余

## 5.CORS跨域

分为简单请求和非简单请求

- 设置  Access-Control-Allow-Origin

  + 设置为*表示，可以接收任意域名的访问

    ```html
    http.createServer((request, response) => {
    
        response.writeHead(200, {
            'Access-Control-Allow-Origin': '*'
        })
    }).listen(port);
    ```

  + 也可以设置为特定域名访问

    ```
    http.createServer((request, response) => {
    
        response.writeHead(200, {
            'Access-Control-Allow-Origin': 'http://127.0.0.1:3010/'
        })
    }).listen(port);
    ```

  + 如果有多个域名访问可以在服务端动态设置

  + ```
    http.createServer((request, response) => {
        const origin = request.headers.origin;
    
        if ([
            'http://127.0.0.1:3010'
        ].indexOf(origin) !== -1) {
            response.writeHead(200, {
                'Access-Control-Allow-Origin': origin,
            })
        }
    }).listen(port);
    ```

    

贴一篇峰哥的文章，更好地了解同源，[浏览器同源政策及规避方法](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)



