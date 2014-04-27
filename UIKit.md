# UIKit

- [UIImage](#uiimage)
- [UIView](#uiview)

---

### UIImage

#####+ (UIImage *)imageNamed:(NSString *)fileName

如果 boundle 中得某个图片只显示一次，推荐使用 `+ (UIImage *)imageWithContentsOfFile:(NSString *)path`，系统就不会缓存该图片。参考 Apple 的文档：

> If you have an image file that will only be displayed once and wish to ensure that it does not get added to the system’s cache, you should instead create your image using imageWithContentsOfFile:. This will keep your single-use image out of the system image cache, potentially improving the memory use characteristics of your app.

[Source](https://developer.apple.com/library/ios/documentation/uikit/reference/UIImage_Class/Reference/Reference.html#//apple_ref/occ/clm/UIImage/imageNamed:)

### UIView

#####@property(nonatomic) BOOL clearsContextBeforeDrawing

将 `UIView` 的属性 `clearsContextBeforeDrawing` 设置为 `NO` 在多数情况下可以提高绘制性能，尤其是在你自己用绘制代码实现了一个定制 view 的时候。

> If you set the value of this property to NO, you are responsible for ensuring the contents of the view are drawn properly in your drawRect: method. If your drawing code is already heavily optimized, setting this property is NO can improve performance, especially during scrolling when only a portion of the view might need to be redrawn.

[Source](https://developer.apple.com/library/ios/documentation/uikit/reference/uiview_class/uiview/uiview.html#//apple_ref/occ/instp/UIView/clearsContextBeforeDrawing)

> By default, UIKit clears a view’s current context buffer prior to calling its drawRect: method to update that same area. If you are responding to scrolling events in your view, clearing this region repeatedly during scrolling updates can be expensive. To disable the behavior, you can change the value in the clearsContextBeforeDrawing property to NO.

[Source](https://developer.apple.com/library/ios/documentation/2ddrawing/conceptual/drawingprintingios/DrawingTips/DrawingTips.html)

#####@property(nonatomic) CGRect frame

当设置一个 `UIView` 的 frame 属性时，应该保证坐标值和像素位置对齐，否则将会触发反锯齿降低性能，也有可能引起图形界面的边界模糊（译者注：尤其是涉及到绘制文字时将会引起文字模糊不清，非 retina 设备特别明显）。一种简单直接的办法就是使用 `CGRectIntegral()` 自动将 `CGRect` 的值四舍五入到整数。对于像素密度大于1的设备，可以将坐标值近似为 `1.0f / screen.scale` 整数倍。
