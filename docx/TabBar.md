# 去除左侧边距，去除底部黑线

Flutter TabBar 在设置【isScrollable: true】后存在左侧边距和底部黑线

```dart
TabBar(
  tabs: [
    Text("第一个"),
    Text("第2个"),
    Text("第一个"),
    Text("第一个"),
    Text("第一个"),
    Text("第一个"),
    Text("第一个"),
  ],
  controller: _tabController,
  isScrollable: true,
)
```

去除方法

```dart
dividerHeight: 0,
tabAlignment: TabAlignment.start,
```

修改后

```dart
TabBar(
  dividerHeight: 0,
  tabAlignment: TabAlignment.start,
  tabs: [
    Text("第一个"),
    Text("第2个"),
    Text("第一个"),
    Text("第一个"),
    Text("第一个"),
    Text("第一个"),
    Text("第一个"),
  ],
  controller: _tabController,
  isScrollable: true,
)
```

## 左侧边距问题

TabBar 源码中 如果 tabAlignment 不设置，默认是 TabAlignment.startOffset，会使用【const double \_kStartOffset = 52.0;】设置左侧偏移量 52。导致左侧存在很大边距

## 底侧黑线问题

dividerHeight 和 dividerColor 可以设置线的高度和颜色，高度改成 0 或者 将颜色改成透明即可

# 自定义 indicator

参考https://blog.csdn.net/pengbo6665631/article/details/103645037

```dart

import 'package:flutter/material.dart';
import 'package:flutter/widgets.dart';

class RoundUnderlineTabIndicator extends Decoration {

  const RoundUnderlineTabIndicator({
    this.borderSide = const BorderSide(width: 2.0, color: Colors.white),
    this.insets = EdgeInsets.zero,
    this.width = 0.0,
    this.widthEqualTitle = true,
  });

  final BorderSide borderSide;
  final EdgeInsetsGeometry insets;
  final double width;
  final bool widthEqualTitle;

  @override
  Decoration? lerpFrom(Decoration? a, double t) {
    if (a != null && a is UnderlineTabIndicator ) {
      return UnderlineTabIndicator(
      borderSide: BorderSide.lerp(a.borderSide, borderSide, t),
      insets: EdgeInsetsGeometry.lerp(a.insets, insets, t) ?? EdgeInsets.zero,
    );
    }
    return super.lerpFrom(a, t);
  }

  @override
  Decoration? lerpTo(Decoration? b, double t) {
    if (b is UnderlineTabIndicator) {
      return UnderlineTabIndicator(
        borderSide: BorderSide.lerp(borderSide, b.borderSide, t),
        insets: EdgeInsetsGeometry.lerp(insets, b.insets, t) ?? EdgeInsets.zero,
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
        indicator.bottom - borderSide.width - 15,  // 指示器和文字的间距
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

class _UnderlinePainter extends BoxPainter {
  _UnderlinePainter(this.decoration, VoidCallback? onChanged)
      : super(onChanged);

  final RoundUnderlineTabIndicator decoration;

  @override
  void paint(Canvas canvas, Offset offset, ImageConfiguration configuration) {
    assert(configuration.size != null);
    assert(configuration.textDirection != null);
    final Rect rect = offset & configuration.size!;
    final TextDirection textDirection = configuration.textDirection!;
    final Rect indicator = decoration._indicatorRectFor(rect, textDirection).deflate(decoration.borderSide.width / 2.0);
    final Paint paint = decoration.borderSide.toPaint()..strokeCap = StrokeCap.round;
    canvas.drawLine(indicator.bottomLeft, indicator.bottomRight, paint);
  }
}
```

# tabBarView 自动填充剩余高度

TabBarView Viewports expand in the cross axis to fill their container and constrain their children to match their extent in the cross axis. In this case, a horizontal viewport was given an unlimited amount of vertical space in which to expand.

Alternatively, if the parent of a widget has constrained dimensions (in your case it has not), you could wrap TabBarView in an Expanded widget which would instruct it to expand to fill the available space.

```dart
Expanded(
  child: TabBarView(
    children: [Text("data"),Text("data2222"),Text("kjghjfghf")],
  ),
),
```
