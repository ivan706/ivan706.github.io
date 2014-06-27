---
layout: post
title: ajax请求使用chunked方式从django中获取数据
tags: django
---

{{ page.title }}
================
## Transfer-encoding : chunked

这玩意儿是HTTP1.1自带的东东，主要用于数据分块传输，不知道是啥的详见[wiki](http://zh.wikipedia.org/wiki/%E5%88%86%E5%9D%97%E4%BC%A0%E8%BE%93%E7%BC%96%E7%A0%81)，具体我就不赘述了。

要说这玩意儿有啥好的，就是在大量数据传输的时候，可以分块传输数据，而且它属于http协议原生的，
浏览器基本都支持(ie9及以下需要hack一下)，不需要引入第三方插件。

本文后端封装chunked数据块是用得django，前端接收使用原生javascript。废话不多说，直接上代码：
## django

在django的views.py中
<pre>
def stream_response(request):
    condition(etag_func=None, last_modified_func=None)
    aresponse = StreamingHttpResponse(stream_response_generator())
    aresponse['Transfer-Encoding:'] = 'chunked'
    aresponse['Cache-Control:'] = 'no-cache'
    return aresponse

```chunked块是由格式的，这个方法就是按照格式封装chunked数据块，然后1秒返回一个chunked块```
def stream_response_generator():
    for x in range(1, 11):
        chunk = 'this is block %s,' % x
        yield '%X\r\n%s\r\n' % (len(chunk), chunk)
        time.sleep(1)
    yield '0\r\n\r\n'
</pre>

## javascript

在接收的javascript文件中
注意，ajax的responsetext中，接收chunked是增量表示，
比如数据传输的时候是三个数据块
<pre>
1
2
3
</pre>

ajax接收的时候是这么展示的
<pre>
1
12
123
</pre>
也就是说，每次收到一个新的chunk，都会把之前的所有的chunk带上一起暴露，
这个问题在使用浏览器直接渲染返回数据的时候是不存在的。
坑啊！

<pre>
var format = function (data) {
    var newdata = data.substring(last_length);//这块儿就是去掉之前已经接收过的chunk
    data = newdata;
    if (data === "0\r\n\r\n")return '';   //分块输出结束符
    last_length += data.length;
    data = $.trim(data);
    var d = data.indexOf("\r\n");
    if (d) {
        var len = hexdec(data.substr(0, d));
        if (len > 0 && len + 2 + d == data.length) {
            data = data.substr(d + 2);
        }
    }
    return data;
};
var hexdec = function (hex_string) {
    hex_string = (hex_string + '').replace(/[^a-f0-9]/gi, '');
    return parseInt(hex_string, 16);
};
function go(){
    var showText = function(text) {
        $("#show").append("&lt;p&gt;"+text+"&lt;/p&gt;")
    };
    last_length = 0;
    var xhr = new XMLHttpRequest();
    xhr.previous_text = '';
    xhr.open('GET', 'http://localhost:8000/search/streamtest/', true);
    xhr.onreadystatechange = function () {
        console.log(xhr.readyState);
        if (xhr.readyState == 3) {
            console.log(xhr.responseText);
            showText(format(xhr.responseText));
        }
    };
    xhr.send(null);
}
</pre>

#####转载请注明出处和原作者ivan，谢谢。