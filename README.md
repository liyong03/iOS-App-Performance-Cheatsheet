# iOS App 性能备忘

**本备忘收集了很多可以提高 iOS 中 Objective-C 代码性能的代码片段和配置**

这些文档中大部分代码片段和配置包括：将平时随手使用会用到的优先考虑灵活性而不是性能的高级 API，替换为功能相同的底层 API；一些会影响到绘制性能的类属性配置。对于 app 性能来说，好的架构和恰当的多线程总是很重要的，但是有时需要具体问题具体对待。

### 目录

**iOS App 性能备忘** 按照 Objective-C framework 组织为不同章节的 Markdown 文件：

- [**Foundation**](Foundation.md)
	- [NSDateFormatter](Foundation.md#nsdateformatter)
	- [NSFileManager](Foundation.md#nsfilemanager)
	- [NSObjCRuntime](Foundation.md#nsobjcruntime)
	- [NSString](Foundation.md#nsstring)
- [**UIKit**](UIKit.md)
	- [UIImage](UIKit.md#uiimage)
	- [UIView](UIKit.md#uiview)
- [**QuartzCore**](QuartzCore.md)
	- [CALayer](QuartzCore.md#calayer)

### 为什么放在 Github?

- 无论保存到磁盘还是从网络访问都很赞
- 鼓励大家贡献和改进
- [GitHub Flavored Markdown](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) 看起来不错

### 免责声明

这就是为什么这仅仅是一个*备忘*。你应该避免过早优化，仅仅在你已经确定某一块具体的代码确实是性能瓶颈时再做代码替换。但我也希望，这份备忘能够让你洞察一些常常会出现的性能瓶颈，和一些解决方案。

### 贡献

欢迎 Pull requests ！如果能包含 app 生命周期中阻塞主线程的类或者方法的替换代码或者配置最好了。

### 联系方式

- hello@danielamitay.com
- http://www.danielamitay.com

### 翻译

- liyong03@gmail.com
- http://github.com/liyong03

