# HTML 转 Markdown

搬题神器。

## 用法

![](https://arina.loli.net/2022/10/15/Iu3Bv2zPKFWsCMw.png)

在左侧框中输入 HTML 源码，在右侧框中就会出现对应的 Markdown 源码。

目前还不能解析太复杂的 HTML，因此对于生成出的 Markdown 建议进行手动检查与修正。

## 教程

### 基于 UOJ 搭建的 OJ

![](https://arina.loli.net/2022/10/15/3utWh8aKHRwM9As.png)

按键盘上的 <kbd>F12</kbd> 打开开发人员工具，切换到「网络」选项卡，刷新页面。

找到类型为 `document`，名称与页面链接匹配的请求，点击请求中的「响应」选项卡，向下翻找到形如 `<article>...</article>`（或 `<div id="tab-statement">...</div>`）的内含题面文本的标签，将其整体复制到左侧的「HTML 源码」输入框中：

![](https://arina.loli.net/2022/10/15/JrTmVA5QzR1ZInG.png)

几秒内右侧会自动显示转换好的 Markdown。
