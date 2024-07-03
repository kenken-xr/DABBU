
![自定义圆弧指示器](/img/ui_widget/圆弧.png)

在Flutter中通常使用TabBar来实现标签栏，其中的指示器（Indicator）可以用来高亮当前选中的标签。默认的指示器是一个下划线，但有时候需要更加个性化的指示器，比如上图所示是一个圆弧形的指示器。

# 创建一个自定义的Decoration类

创建一个继承自Decoration的类，这个类将定义圆弧指示器。

在Decoration对象的动画过渡中，逐步从一个RoundUnderlineTabIndicator对象过渡到另一个RoundUnderlineTabIndicator对象。通过使用线性插值方法lerp，可以在动画过程中平滑地过渡各个属性的值，从而实现平滑的视觉效果。

```dart
dart
import 'dart:math' as math;
import 'dart:ui';
import 'package:flutter/material.dart';
class RoundUnderlineTabIndicator extends Decoration {
  const RoundUnderlineTabIndicator({
    this.borderSide = const BorderSide(width: 2.0, color: Colors.white),
    this.insets = EdgeInsets.zero,
    this.width = 0.0,
    this.widthEqualTitle = true,
    this.arcDiameter = 20.0,
    this.startAngle = (math.pi) * 1/4,
    this.sweepAngle = math.pi / 2,
  });
  final BorderSide borderSide;
  final EdgeInsetsGeometry insets;
  final double width;
  final bool widthEqualTitle;
  final double arcDiameter;
  final double startAngle;
  final double sweepAngle;
  @override
  Decoration? lerpFrom(Decoration? a, double t) {
    if (a != null && a is RoundUnderlineTabIndicator) {
      return RoundUnderlineTabIndicator(
        borderSide: BorderSide.lerp(a.borderSide, borderSide, t),  // 圆弧的边框样式，包括宽度和颜色。
        insets: EdgeInsetsGeometry.lerp(a.insets, insets, t) ?? EdgeInsets.zero,  // 圆弧的边距，用于确定圆弧相对于其容器的内边距。
        width: lerpDouble(a.width, width, t) ?? 0.0,  // 圆弧的宽度。当 widthEqualTitle 为 false 时使用。
        widthEqualTitle: t < 0.5 ? a.widthEqualTitle : widthEqualTitle,  // 指示圆弧的宽度是否与标题的宽度相等。如果为 true，圆弧宽度与标题宽度相同；否则，使用固定宽度。
        arcDiameter: lerpDouble(a.arcDiameter, arcDiameter, t)!,  // 圆弧的直径。
        startAngle: lerpDouble(a.startAngle, startAngle, t)!,  // 圆弧的起始角度，以弧度为单位。
        sweepAngle: lerpDouble(a.sweepAngle, sweepAngle, t)!,  // 圆弧的扫过角度，即圆弧从起始角度开始的覆盖角度。
      );
    }
    return super.lerpFrom(a, t);
  }
  @override
  Decoration? lerpTo(Decoration? b, double t) {
    if (b is RoundUnderlineTabIndicator) {
      return RoundUnderlineTabIndicator(
        borderSide: BorderSide.lerp(borderSide, b.borderSide, t),
        insets: EdgeInsetsGeometry.lerp(insets, b.insets, t) ?? EdgeInsets.zero,
        width: lerpDouble(width, b.width, t) ?? 0.0,
        widthEqualTitle: t < 0.5 ? widthEqualTitle : b.widthEqualTitle,
        arcDiameter: lerpDouble(arcDiameter, b.arcDiameter, t)!,
        startAngle: lerpDouble(startAngle, b.startAngle, t)!,
        sweepAngle: lerpDouble(sweepAngle, b.sweepAngle, t)!,
      );
    }
    return super.lerpTo(b, t);
  }
  @override
  _UnderlinePainter createBoxPainter([VoidCallback? onChanged]) {
    return _UnderlinePainter(this, onChanged);
  }
  Rect _indicatorRectFor(Rect rect, TextDirection textDirection) {
    final Rect indicator = insets.resolve(textDirection).deflateRect(rect);
    if (widthEqualTitle) {
      return Rect.fromLTWH(
        indicator.left,
        indicator.bottom - borderSide.width - 10,
        indicator.width,
        borderSide.width,
      );
    } else {
      double w = (indicator.left + indicator.right) / 2;
      return Rect.fromLTWH(w - width / 2, indicator.bottom - borderSide.width, width, borderSide.width);
    }
  }
  @override
  Path getClipPath(Rect rect, TextDirection textDirection) {
    return Path()..addRect(_indicatorRectFor(rect, textDirection));
  }
}
```

- `lerp(a.borderSide, borderSide, t)`：在a.borderSide和当前对象的borderSide之间进行线性插值。参数t表示插值系数，取值范围为0到1。t=0时，取第一个对象的值，t=1时，取第二个对象的值。
- `insets`:圆弧的边距，用于确定圆弧相对于其容器的内边距。
- `widthEqualTitle`:用于控制指示器的宽度是否与 Tab 标题的宽度相等。决定了指示器是根据标题宽度调整宽度，还是使用固定宽度。如果 widthEqualTitle 为 true，指示器的宽度将与 Tab 标题的宽度相等；如果 widthEqualTitle 为 false，指示器将使用固定宽度。
- `lerpFrom` 和 `lerpTo` 方法用于在动画过渡中进行插值计算，这些方法在过渡期间平滑地变换各个属性。

# 创建一个自定义的BoxPainter类

接下来需要创建一个继承自BoxPainter的类，用来实际绘制圆弧指示器。

```dart
dart
// 自定义的BoxPainter类，用于绘制圆弧指示器
class _UnderlinePainter extends BoxPainter {
  _UnderlinePainter(this.decoration, VoidCallback? onChanged)
      : super(onChanged);

  final RoundUnderlineTabIndicator decoration;  // 引用自定义的Decoration

  @override
  void paint(Canvas canvas, Offset offset, ImageConfiguration configuration) {
    assert(configuration.size != null);  // 确保配置的大小不为空
    assert(configuration.textDirection != null);  // 确保配置的文本方向不为空
    final Rect rect = offset & configuration.size!;  // 获取绘制区域的矩形
    final TextDirection textDirection = configuration.textDirection!;  // 获取文本方向
    final Rect indicator = decoration._indicatorRectFor(rect, textDirection).deflate(decoration.borderSide.width / 2.0);  // 计算指示器的矩形区域并缩小边框宽度的一半
    final Paint paint = decoration.borderSide.toPaint()..strokeCap = StrokeCap.round;  // 创建绘制的画笔，并设置笔触样式为圆形

    final Rect arcRect = Rect.fromCircle(
      center: Offset(indicator.center.dx, indicator.bottom - decoration.arcDiameter / 2),  // 设置圆弧的中心位置
      radius: decoration.arcDiameter / 2,  // 设置圆弧的半径
    );

    // 绘制圆弧
    canvas.drawArc(
      arcRect,  // 圆弧所在的矩形区域
      decoration.startAngle,  // 圆弧的起始角度
      decoration.sweepAngle,  // 圆弧的角度范围
      false,  // 是否使用中心点绘制圆弧
      paint,  // 使用的画笔
    );
  }
}
```

# 使用

```dart
TabBar(
  controller: _tabController,
  indicator: const RoundUnderlineTabIndicator(
    borderSide: BorderSide(width: 2.0, color: Colors.red),
    arcDiameter: 20.0,
    startAngle: math.pi,
    sweepAngle: math.pi / 2,
  ),
  tabs: [
    Tab(text: 'Tab 1'),
    Tab(text: 'Tab 2'),
  ],
)
```