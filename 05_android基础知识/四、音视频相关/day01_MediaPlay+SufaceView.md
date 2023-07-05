- MediaPlay声明周期、TextureView/SufaceView对比:
https://juejin.cn/post/7063417215382454302

```
为什么有 SurfaceView 和 TextureView 两种方式呢？然后哪种方式更适合用在项目中呢？
SurfaceView：SurfaceView 是一个绘图的面板，可以控制此面板的格式，也可以控制其大小，通过线程将视频渲染到屏幕上。
TextureView：TextureView 可用于显示内容流，这样的内容流可以是视频或 OpenGL 场景、可以是来自本地数据源或者是远程数据源。
TextureView 只能在有硬件加速的窗口中使用，当软件渲染时，TextureView 不绘制任何内容。与 SurfaceView 不同，TextureView 不会创建单独的窗口，而是充当常规 View，所以可以对 TextureView 进行移动，转换，设置动画等操作。
如果项目需求简单，当然可以选择 SurfaceView，但是在这里选择了后者，原因是 TextureView 更适合视频流，以及具备普通 View 的特性和高自定义性，可以在项目中达到想要的变化效果。既然选定了 TextureView ，接下来看看如何使用。

```

-VideoView + MediaController: https://juejin.cn/post/7085243234523283464

