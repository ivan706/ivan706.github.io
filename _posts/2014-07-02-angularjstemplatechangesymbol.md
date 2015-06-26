---
layout: post
title: Angularjs更换模板标识
tags: Angularjs
---

{{ page.title }}
================

Angularjs的默认模板标识是 ```{{ }}``` , 但是很多时候，我们可能即需要使用Angularjs模板，又要使用后端渲染的模板，
这样，在模板的变量识别符上，可能就会出现因为识别符一样而解析不了或解析错误的问题。
Angularjs提供了更换识别符的功能

```
    var app = angular.module('feedback', ['ui.bootstrap']);
    app.config(['$interpolateProvider', function ($interpolateProvider) {
        $interpolateProvider.startSymbol('{/');
        $interpolateProvider.endSymbol('/}');
    }]);
```

这样，我们就可以使用 ```{/ /}``` 来代替默认的 ```{{ }}``` 来使用了。

例如：

```
    <a class="btn">
        {/buttonName/}
    </a>
```

#####转载请注明出处[ivan's view](http://blog.ivan706.com/2014/06/27/transdatawithchunked-django.html)，谢谢。
