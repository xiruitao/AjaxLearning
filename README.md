### Ajax

AJAX，即 Asynchronous JavaScript and XML（异步的 JavaScript 和 XML），是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。

#### 使用原生代码

```js
document.getElementById("search").onclick=function(){
    //新建XMLHttpRequest对象
    var request = new XMLHttpRequest();
    // 发送请求:
    request.open("GET","service.php?number="+document.getElementById("keyword").value);
    request.send();
    request.onreadystatechange = function(){// 状态发生变化时，函数被回调
        if(request.readyState===4){// 成功完成
            // 判断响应结果:
            if(request.status===200){
                 // 成功，通过responseText拿到响应的文本:
                return success(request.responseText);
            }else{
                // 失败，根据响应码判断失败原因:
                alert("发生错误："+request.status);
            }
        }
    }
}
document.getElementById("save").onclick=function(){
    var request = new XMLHttpRequest();
    request.open("POST","service.php");
    var data = "name=" + document.getElementById("staffName").value 
                + "&number=" + document.getElementById("staffNumber").value 
                + "&sex=" + document.getElementById("staffSex").value 
                + "&job=" + document.getElementById("staffJob").value;
    request.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
    request.send(data);
    request.onreadystatechange = function(){
        if(request.readyState===4){
            if(request.status===200){
                return success(request.responseText);
            }else{
                alert("发生错误："+request.status);
            }
        }
    }
}

```

#### 使用Json

```javascript
document.getElementById("search").onclick=function(){
    var request = new XMLHttpRequest();
    request.open("GET","service2.php?number="+document.getElementById("keyword").value);
    request.send();
    request.onreadystatechange = function(){
        if(request.readyState===4){
            if(request.status===200){
                var data = JSON.parse(request.responseText);
                if(data.success){
                    return success(data.msg);
                }else{
                    return success("出现错误:" + data.msg);
                }
            }else{
                alert("发生错误："+request.status);
            }
        }
    }
}
document.getElementById("save").onclick=function(){
    var request = new XMLHttpRequest();
    request.open("POST","service2.php");
    var data = "name=" + document.getElementById("staffName").value 
                + "&number=" + document.getElementById("staffNumber").value 
                + "&sex=" + document.getElementById("staffSex").value 
                + "&job=" + document.getElementById("staffJob").value;
    request.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
    request.send(data);
    request.onreadystatechange = function(){
        if(request.readyState===4){
            if(request.status===200){
                var data = JSON.parse(request.responseText);
                if(data.success){
                    return success(data.msg);
                }else{
                    return success("出现错误:" + data.msg);
                }
            }else{
                alert("发生错误："+request.status);
            }
        }
    }
}
```

#### 使用Json+JQuery

```javascript
$(document).ready(function(){
    
	$("#search").click(function(){        
		$.ajax({
            type: "GET",
            url: "service2.php?number="+$("#keyword").val(),
            dataType: "json",
            success: function(data){
                if(data.success){
                    $("#searchResult").html(data.msg);
                }else{
                    $("#searchResult").html("出现错误:" + data.msg);
                }
            },
            error: function(jqXHR){
                alert("发生错误："+jqXHR.status);
            }
        })
    })

    $("#search").click(function(){   
        $.ajax({
            type: "POST",
            url: "service2.php",
            dataType: "json",
            data: {
                name: $("#staffName").val(),
                number: $("#staffNumber").val(),
                sex: $("#staffSex").val(),
                job: $("#staffJob").val()
            },
            success: function(data){
                if(data.success){
                    $("#createResult").html(data.msg);
                }else{
                    $("#createResult").html("出现错误:" + data.msg);
                }
            },
            error: function(jqXHR){
                alert("发生错误："+jqXHR.status);
            }
        })
    })
    
})
```

在现代浏览器上写AJAX主要依靠`XMLHttpRequest`对象，对于低版本的IE，需要换一个`ActiveXObject`对象：

```js
var request;
if (window.XMLHttpRequest) {
    request = new XMLHttpRequest();
} else {
    request = new ActiveXObject('Microsoft.XMLHTTP');
}
```

#### 跨域

使用HTML5规范定义的跨域策略：CORS(Cross-Origin Resource Sharing)

##### 配置跨域过滤器

```java
package Filter;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class CorsFilter implements Filter {
    public void destroy() {}

    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain) throws ServletException, IOException {
        HttpServletResponse res = (HttpServletResponse) resp;
        res.setHeader("Access-Control-Allow-Origin", "*");
        res.setHeader("Access-Control-Allow-Methods", "*");
        res.setHeader("Access-Control-Allow-Headers", "Content-Type,x-requested-with");
        res.setCharacterEncoding("UTF-8");
        res.setContentType("application/json");
        chain.doFilter(req, res);
    }

    public void init(FilterConfig config) throws ServletException {}

}
```

在web.xml中

```xml
<filter>
        <filter-name>CorsFilter</filter-name>
        <filter-class>Filter.CorsFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>CorsFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

##### @CrossOrigin注解

Spring 4.2之后提供了跨域注解 @CrossOrigin，可以用在方法或Controller上;

在controller类上加上如下注解：

```java
@CrossOrigin(allowCredentials = "true",allowedHeaders = "*")  //实现跨域
```

DEFAULT_ALLOW_CREDENTIALS = true：
需配合前端设置xhrFields授信后，使得跨域session共享，前端**ajax**请求添加设置：

xhrFields: {withCredentials:true}

DEFAULT_ALLOWED_HEADERS = *：允许跨域传输所有的header参数，将用于使用token放入header域做session共享的跨域请求

#### JQuery

jQuery 是一个高效、精简并且功能丰富的 JavaScript 工具库，它的所有功能都是通过JavaScript访问的。它提供的 API 易于使用且兼容众多浏览器，这让诸如 HTML 文档遍历和操作、事件处理、动画和 Ajax 操作更加简单。[jQuery API 中文文档](https://www.jquery123.com/)

使用：通过 script 标签引入 jQuery 库。推荐[BootCDN](https://www.bootcdn.cn/)，里面收录了很多前端开源库，界面简洁干净美观。

入手学习：慕课网[Aaron艾伦](https://www.imooc.com/u/290139/courses?sort=publish)老师的JQuery基础课程



#### 参考

慕课课程《[Ajax全接触](https://www.imooc.com/learn/250)》