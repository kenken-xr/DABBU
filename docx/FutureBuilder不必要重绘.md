# FutureBuilder 用法和实现

一个基于与 Future 交互的最新快照构建自己的小部件。

- Widget that builds itself based on the latest snapshot of interaction with a Future.

## 构造方法

```dart
  const FutureBuilder({
    Key key,
    this.future,          //获取数据的方法
    this.initialData,   //初始的默认数据
    @required this.builder
  }) : assert(builder != null),
       super(key: key);
```

接收两个参数：`BuildContext context`, `AsyncSnapshot snapshot`。

```dart
//FutureBuilder控件
new FutureBuilder<String>(
  future: _calculation, // 用户定义的需要异步执行的代码，类型为Future<String>或者null的变量或函数
  builder: (BuildContext context, AsyncSnapshot<String> snapshot) {      //snapshot就是_calculation在时间轴上执行过程的状态快照
    switch (snapshot.connectionState) {
      case ConnectionState.none: return new Text('Press button to start');    //如果_calculation未执行则提示：请点击开始
      case ConnectionState.waiting: return new Text('Awaiting result...');  //如果_calculation正在执行则提示：加载中
      default:    //如果_calculation执行完毕
        if (snapshot.hasError)    //若_calculation执行出现异常
          return new Text('Error: ${snapshot.error}');
        else    //若_calculation执行正常完成
          return new Text('Result: ${snapshot.data}');
    }
  },
)
```

`FutureBuilder` 通过子属性 `future` 获取用户需要异步处理的代码，用 `builder` 回调函数暴露出异步执行过程中的快照。通过 `builder` 的参数 `snapshot` 暴露的快照属性，定义好对应状态下的处理代码

```dart
dynamic _futureBuilderFuture;

@override
void initState() {
  super.initState();
  _futureBuilderFuture = _fetchUserNoteList(1);
}

@override
Widget build(BuildContext context) {
  return Scaffold(
    body: Padding(
      padding: EdgeInsets.only(left: 20.w, right: 20.w, bottom: 22.w),
      child: FutureBuilder(future: _futureBuilderFuture, builder: _buildFuture),
    ),
  );
}

Widget _buildFuture(BuildContext context, AsyncSnapshot snapshot) {
  if (snapshot.connectionState == ConnectionState.waiting) {
    return const Center(child: CircularProgressIndicator());  // 加载中
  } else if (snapshot.hasError) {
    return const Center(child: Text('Error fetching data'));  // 错误
  } else {
    if (!snapshot.hasData) {  // 重试
      return Center(
        child: GestureDetector(
          onTap: () async {
            _futureBuilderFuture = _fetchUserNoteList(1, withLoading: true);
          },
          child: Text("retry"),
        ),
      );
    }
    return _buildNoteList(snapshot.data);  // 显示数据
  }
}

```
