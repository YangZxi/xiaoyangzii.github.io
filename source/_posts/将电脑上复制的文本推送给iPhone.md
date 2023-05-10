---
title: 将电脑上复制的文本推送给iPhone
date: 2020-10-25 12:15:57
tags:
categories:
  - Python
---

由于博主用的不是Mac，所以体验不到iCloud的云剪贴板功能

有时候电脑上复制的链接或者文本需要在手机上操作，就需要经过聊天工具转发，很是繁琐。就用Python写了个工具，配合Bark使用，达到电脑给手机发信息的效果

## 手机安装Bark
直接在App Store搜索Bark就好了
[App Store链接](https://apps.apple.com/cn/app/bark-customed-notifications/id1403753865)

## 复制Bark中的地址
![TIM图片20200608142626](https://qiniu.xiaosm.cn/blog/TIM%E5%9B%BE%E7%89%8720200608142626_1591597618071.jpg-blogshuiyin)

## 替换代码中的url
``` python
import PyHook3
import pythoncom
import requests
import win32clipboard as wc


class EventListener:
    keys = {}
    # 替换这里的地址
    url = "https://api.day.app/替换这里的地址/"

    def onKeyboardEvent(self, event):
        # 监听键盘事件
        self.keys[event.Key] = True
        # 这里是监听 Ctrl+Shift+Alt+P
        if self.keys['Lcontrol'] and self.keys['Lshift'] and self.keys['Lmenu'] and self.keys['P']:
            try:
                # print("监听到了")
                wc.OpenClipboard()
                text = wc.GetClipboardData()
                requests.get(self.url + text)
                print(text)
            finally:
                wc.CloseClipboard()

        # 同鼠标事件监听函数的返回值
        return True

    def unMarkKey(self, event):
        self.keys[event.Key] = False

        return True


def main():
    print("开始了")
    el = EventListener()
    # 创建一个“钩子”管理对象
    hm = PyHook3.HookManager()
    # 监听所有键盘事件
    hm.KeyDown = el.onKeyboardEvent
    hm.KeyUp = el.unMarkKey
    # 设置键盘“钩子”
    hm.HookKeyboard()

    # 进入循环，如不手动关闭，程序将一直处于监听状态
    pythoncom.PumpMessages()


if __name__ == "__main__":
    main()

```

## 修改监听事件
我默认快捷键是`Ctrl+Shift+Alt+P`
如果你需要更改快捷键修改16行最后的`P`就行了
注意字母要是大写

## 使用
在使用前请先安装下面三个库
``` bash
pip install PyHook3
pip install pywin32
pip install requests
```

安装完以后在cmd中运行即可

## 扩展
此工具还有许多扩展空间，如定时提醒等
添加到系统服务，设置自启动等
如果你是开发者请自行发掘吧
