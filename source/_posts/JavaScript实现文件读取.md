---
title: JavaScript实现文件读取
date: 2020-11-28 19:49:11
tags:
---

``` js
function load(name) {
    let xhr = new XMLHttpRequest(),
        okStatus = document.location.protocol === "file:" ? 0 : 200;
    xhr.open('GET', name, false);
    xhr.overrideMimeType("text/html;charset=utf-8");//默认为utf-8
    xhr.send(null);
    return xhr.status === okStatus ? xhr.responseText : null;
}
 
let text = load("test.txt");
console.log(text);
```