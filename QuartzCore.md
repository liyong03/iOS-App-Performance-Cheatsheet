# QuartzCore

- [CALayer](#calayer)

---

### CALayer

#####@property BOOL allowsGroupOpacity

在 iOS7 中，这个属性表示 layer 的 sublayer 是否继承父 layer 的透明度，主要用途是当在动画中改变一个 layer 的透明度时（会引起子 view 的透明度显示出来）。但是如果你不需要这种绘制类型，可以关闭这个属性来提高性能。

> When true, and the layer's opacity property is less than one, the layer is allowed to composite itself as a group separate from its parent. This gives the correct results when the layer contains multiple opaque components, but may reduce performance. 
> 
> The default value of the property is read from the boolean UIViewGroupOpacity property in the main bundle's Info.plist. If no value is found in the Info.plist the default value is YES for applications linked against the iOS 7 SDK or later and NO for applications linked against an earlier SDK.

上述引用来源已不存在，可以参考 `CALayer.h`。

> (Default on iOS 7 and later) Inherit the opacity of the superlayer. This option allows for more sophisticated rendering in the simulator but can have a noticeable impact on performance.

[Source](https://developer.apple.com/library/ios/documentation/general/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW6)

#####@property BOOL drawsAsynchronously

`drawsAsynchronously` 属性会导致 layer 的 `CGContext` 延迟到后台线程绘制。这个属性对于频繁绘制的 leyer 有很大的好处。

> When this property is set to YES, the graphics context used to draw the layer’s contents queues drawing commands and executes them on a background thread rather than executing them synchronously. Performing these commands asynchronously can improve performance in some apps. However, you should always measure the actual performance benefits before enabling this capability.

[Source](https://developer.apple.com/library/ios/documentation/GraphicsImaging/Reference/CALayer_class/Introduction/Introduction.html#//apple_ref/occ/instp/CALayer/drawsAsynchronously)

> Any drawing that you do in your delegate’s drawLayer:inContext: method or your view’s drawRect: method normally occurs synchronously on your app’s main thread. In some situations, though, drawing your content synchronously might not offer the best performance. If you notice that your animations are not performing well, you might try enabling the drawsAsynchronously property on your layer to move those operations to a background thread. If you do so, make sure your drawing code is thread safe.

[Source](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/ImprovingAnimationPerformance/ImprovingAnimationPerformance.html)

#####@property CGPathRef shadowPath

如果要操作 `CALayer` 的阴影属性，推荐设置 layer 的 `shadowPath` 属性，系统将会缓存阴影减少不必要的重绘。但当改变 layer 的 bounds 时，一定要重设 `shadowPath`。

```objective-c
CALayer *layer = view.layer;
layer.shadowOpacity = 0.5f;
layer.shadowRadius = 10.0f;
layer.shadowOffset = CGSizeMake(0.0f, 10.0f);
UIBezierPath *bezierPath = [UIBezierPath bezierPathWithRect:layer.bounds];
layer.shadowPath = bezierPath.CGPath;
```

> Letting Core Animation determine the shape of a shadow can be expensive and impact your app’s performance. Rather than letting Core Animation determine the shape of the shadow, specify the shadow shape explicitly using the shadowPath property of CALayer. When you specify a path object for this property, Core Animation uses that shape to draw and cache the shadow effect. For layers whose shape never changes or rarely changes, this greatly improves performance by reducing the amount of rendering done by Core Animation.

[Source](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/ImprovingAnimationPerformance/ImprovingAnimationPerformance.html)

#####@property BOOL shouldRasterize

如果 layer 只需要绘制依此，那么可以设置 `CALayer` 的属性 `shouldRasterize` 为 `YES`。但是如果该 layer 让然会被移动、缩放或者变形，那么将 `shouldRasterize` 设置为 `YES` 会损伤绘制性能，因为系统每次绘制完后会尝试再次重绘。

> When the value of this property is YES, the layer is rendered as a bitmap in its local coordinate space and then composited to the destination with any other content. Shadow effects and any filters in the filters property are rasterized and included in the bitmap.

[Source](https://developer.apple.com/library/mac/documentation/GraphicsImaging/Reference/CALayer_class/Introduction/Introduction.html#//apple_ref/occ/instp/CALayer/shouldRasterize)
