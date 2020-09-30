---
title: 初识Flutter之Fish-Redux_基础篇
date: 2020-09-30 11:22:58
tags: fish-redux
categories: 
- flutter
---


最近因为公司启动新项目，考虑技术选型，了解了下flutter，记录下，班门弄斧了！！！

<!-- more -->	

### 一. 引入插件

pubspec.yaml

```

dependencies:

	fish_redux: ^0.3.1

```

pub get

### 二. 安装插件
FishReduxTemplate

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c5b09fe2169c4ca0ad01b1854b59760f~tplv-k3u1fbpfcp-zoom-1.image)

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5c5595fb418a4e9aa74840f026d4305d~tplv-k3u1fbpfcp-zoom-1.image)

### 三. 编码

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b70e6cf9de934aabbd82df7b4a234954~tplv-k3u1fbpfcp-zoom-1.image)

##### 1. page.dart

xxxPage() 页面入口。继承自 component.

```

class UserOrderListPage extends Page<UserOrderListState, Map<String, dynamic>> {
  UserOrderListPage()
      : super(
            initState: initState,
            effect: buildEffect(),
            reducer: buildReducer(),
            view: buildView,
            dependencies: Dependencies<UserOrderListState>(
                adapter: null,
                slots: <String, Dependent<UserOrderListState>>{
                }),
            middleware: <Middleware<UserOrderListState>>[
            ],);

}

```

##### 2. view.dart

页面UI绘制。

```
Widget buildView(
    UserOrderListState state, Dispatch dispatch, ViewService viewService) {
  // return Container();
  return Scaffold(
    appBar: new AppBar(
      leading: BackButton(),
      title: new Text('用户资料'),
    ),
    body: state.orderList != null
        ? new ListView.builder(
            itemBuilder: (BuildContext context, int index) {
              return new Text(state.orderList[index].projectName);
              // return _buildItem(dispatch,state.orderList[index]);
            },
            itemCount: state.orderList.length,
          )
        : Center(child: CircularProgressIndicator()),
  );
}
}
```

##### 3. state.dart
页面数据状态，存储数据。

```
List<CustomerInfoOrderList> orderList;

  @override
  UserOrderListState clone() {
    return UserOrderListState()
      ..orderList = orderList;
  }

```

##### 4. effect.dart
生命周期调用，以及处理一些除state更新之外的逻辑。

```
Effect<UserOrderListState> buildEffect() {
  return combineEffects(<Object, Effect<UserOrderListState>>{
    UserOrderListAction.action: _onAction,
    Lifecycle.initState: _init, //页面初始化
  });
}

void _onAction(Action action, Context<UserOrderListState> ctx) {}

void _init(Action action, Context<UserOrderListState> ctx) async {
  try {
    var customer =
        await HttpUtils.postAsT<CustomerInfoEntity>(Api.queryCustomer, map: {
      "userId": 88360,
    });
    if (customer != null) {
      ctx.dispatch(UserOrderListActionCreator.onLoadData(
          UserOrderListState()..orderList = customer.orderList));
    }
  } catch (e) {
    LogUtil.v("用户订单资料列表 数据加载失败");
  }
}

```

##### 5. action.dart
定义的动作。我们需要处理某些操作或事件时，通过发送（dispatch）特定的 action，让对应action的接收者进行处理。

```
enum UserOrderListAction { action,loadData }

class UserOrderListActionCreator {

  static Action onAction() {
    return const Action(UserOrderListAction.action);
  }

  static Action onLoadData(UserOrderListState state) {
    return Action(UserOrderListAction.loadData,payload: state);
  }

}

```

##### 6. reducer.dart
同effect，但是主要是处理一些除state更新之外的逻辑。尽量不要参杂其他的逻辑处理，其他逻辑放到effect。

```
Reducer<UserOrderListState> buildReducer() {
  return asReducer(
    <Object, Reducer<UserOrderListState>>{
      UserOrderListAction.action: _onAction,
      UserOrderListAction.loadData: _onLoadData,
    },
  );
}

UserOrderListState _onAction(UserOrderListState state, Action action) {
  final UserOrderListState newState = state.clone();
  return newState;
}

//初始化数据
UserOrderListState _onLoadData(UserOrderListState state, Action action) {
  try {
    final UserOrderListState newState = state.clone();
    if (action.payload is UserOrderListState) {
      if ((action.payload as UserOrderListState).orderList is List) {
        newState.orderList  = (action.payload as UserOrderListState).orderList;
      }
    }

    return newState;
  } catch (e) {
    print(e);
    return state;
  }
}


```

> 省略了bean和dio网络访问代码。

> 注意点：数据请求到了，但是UI没有刷新问题。排查reducer里的newState里的orderList是否更新。


#### 1). Component
编写一个tab里面两个fragment的实现：

tab实现代码同上，区别点在于tab跳fragment不能使用xxPage()入口，使用component。
在tab的Page.dart 
```

dependencies: Dependencies<RecordTabState>(
        adapter: null,
        slots: <String, Dependent<RecordTabState>>{
          "record_info": RecordInfoConnector()+RecordInfoComponent(),
        }),
```
frgment 使用模版component生成。
Connector部分代码：
```
 @override
  RecordInfoState computed(RecordTabState state) {
    return RecordInfoState()..index = state.currentIndex;
  }
  @override
  List factors(RecordTabState state) {
    return [state.currentIndex];
  }
 @override
 RecordInfoState get(RecordTabState state) {
   final newState = RecordInfoState().clone();
   newState..index = state.currentIndex;
   newState..listHistory = state.listHistory;
   return newState;
 }

  @override
  void set(RecordTabState state, RecordInfoState subState) {
    state.currentIndex = subState.index;
    state.listHistory = subState.listHistory;
  }
```

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3457dd0cb4e64392b0a17a96c4f726de~tplv-k3u1fbpfcp-zoom-1.image)

### 2). 生命周期

```

enum Lifecycle {
  /// componenmt(page) or adapter receives the following events
  initState,
  didChangeDependencies,
  build,
  reassemble,
  didUpdateWidget,
  deactivate,
  dispose,
  // didDisposed,

  /// Only a adapter mixin VisibleChangeMixin will receive appear & disappear events.
  /// class MyAdapter extends Adapter<T> with VisibleChangeMixin<T> {
  ///   MyAdapter():super(
  ///     ///
  ///   );
  /// }
  appear,
  disappear,

  /// Only a componenmt(page) or adapter mixin WidgetsBindingObserverMixin will receive didChangeAppLifecycleState event.
  /// class MyComponent extends Component<T> with WidgetsBindingObserverMixin<T> {
  ///   MyComponent():super(
  ///     ///
  ///   );
  /// }
  didChangeAppLifecycleState,
}