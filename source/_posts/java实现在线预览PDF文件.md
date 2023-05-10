---
title: java实现在线预览PDF文件
date: 2020-10-25 12:18:29
tags:
categories:
  - Java
---

最近碰到一个需求，要求实现office文件和PDF文件能够实现实时预览的效果

最后经过查找发现现在有新的头部信息application/pdf

在后端读取文件以后设置头**`Content-Type: application/pdf`**

![](https://qiniu.xiaosm.cn/blog_img/Snipaste_2020-03-05_09-31-39.png-blogshuiyin)

现在主流浏览器均支持这种资源的MIME类型 ( 测试了ie会直接进行下载 )

对于office文件我们就可以将它转变为pdf文件进行输出，当然这会对服务器的压力较大，我会寻找更好的方法

最后附上代码

``` java
@RequestMapping(value = "/pdf")
public String viewPdf(HttpServletResponse response) throws IOException {
    FileInputStream in = new FileInputStream(new File(""));
    OutputStream out = response.getOutputStream();
    response.setHeader("Content-Type", "application/pdf");
    byte[] buffer = new byte[1024];
    int read;
    while ((read = in.read(buffer)) != -1) {
        out.write(buffer, 0, read);
    }
    return "";
}
```