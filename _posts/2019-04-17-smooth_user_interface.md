---
layout: post
title: 流畅性和资源消耗的tips
date: 2019-04-17 17:56:29 +0800
categories: iOS develop
---

参考链接 https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios

### cpu资源消耗问题

- 1.尽量使用轻量对象代替重量的对象，如用CALayer代替UIView
- 2.不涉及UI操作的对象，尽量放在后台线程创建
- 3.用StoryBoard创建视图对象时，会消耗更多的资源，性能敏感的界面中尽量不使用StoryBoard
- 4.尽量推迟对象建立的时间
- 5.当对象可以复用且复用的代价较低时，可以考虑将对象放入一个缓存池中复用
- 6.特别的 CALayer和UIView :  
 　　CALayer 内部并没有属性，当调用属性方法时，它内部是通过运行时 resolveInstanceMethod 为对象临时添加一个方法，并把对应属性值保存到内部的一个 Dictionary 里，同时还会通知 delegate、创建动画等等，非常消耗资源。UIView 的关于显示相关的属性（比如 frame/bounds/transform）等实际上都是 CALayer 属性映射来的，所以对 UIView 的这些属性进行调整时，消耗的资源要远大于一般的属性。　　
 　　应该尽量减少不必要的属性修改
 - 7.对象释放时可以放到后台线程去释放  
 tips：把对象捕获到 block 中，然后扔到后台队列去随便发送个消息以避免编译器警告，让对象在后台线程销毁
``` objectivec
NSArray *temp = self.array;
self.array = nil;
dispatch_async(queue, ^{
    [tmp class];
});
```

- 8.在后台线程提前计算好视图布局，并且对视图布局进行缓存
