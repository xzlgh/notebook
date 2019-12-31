# 2019-10-22 React-native触摸机制

## RN的单击组件 touchable* 系列

```javascript
TouchableOpacity
TouchableHightLight
TouchableNativeFeedback(仅限安卓)
TouchableWithoutFeedback
```

其内部实现了触摸之后的事件处理,只要声明简单的属性即可,如下代码：

```javascript
<TouchableOpacity
onPressIn={(evt)=> console.log(evt.nativeEvent,'点击开始')}
onPress={(evt)=> console.log(evt.nativeEvent,'单击')}
onLongPress={(evt)=> console.log(evt.nativeEvent,'长按')}
onPressOut={(evt)=>console.log(evt.nativeEvent,'点击结束')}>
<Text>点击按钮</Text>
</TouchableOpacity>
```

### 自定义触摸事件处理

事件触摸流程:
>申请成为触摸事件响应者 -> 成为触摸事件响应者 -> 处理触摸事件 -> 释放触摸事件 -> 触摸事件结束

![Image text](./1.jpg)
```javascript
 this.panResponder = PanResponder.create({
      /**
       * tips:
       * 0.通过调用 PanResponder 的 create 静态方法创建手势对象，传入 config 参数实现对应的触摸回调即可,
       *   调用 panResponer 的 panHandlers 作为 props 传给View组件
       * 1.包含set的  询问该组件是否申请成为响应者 返回bool值
       * 2.包含Capture为捕获询问,反之冒泡询问 返回bool值
       * 3.响应阶段, 手势操作, 移动阶段, 释放阶段, 重新分配响应权
       * 4.捕获询问   根元素 -> 中间元素 -> 最内层元素
       * 5.冒泡询问   最内层元素 -> 中间元素 -> 根元素
       */
      // start 响应阶段
      onStartShouldSetPanResponder: this.onStartShouldSetPanResponder, // 返回bool值
      onStartShouldSetPanResponderCapture: this.onStartShouldSetPanResponderCapture, // 返回bool值
      onMoveShouldSetPanResponder: this.onMoveShouldSetPanResponder, // 返回bool值
      // reject 拒绝响应 申请失败,说明其他响应组件正在处理事情
      onPanResponderReject: this.onPanResponderReject,
      // grant 手势操作阶段
      onPanResponderGrant: this.onPanResponderGrant,
      // move 移动阶段
      onPanResponderStart: this.onPanResponderStart,
      onPanResponderMove: this.onPanResponderMove,  //会多次执行
      onPanResponderEnd: this.onPanResponderEnd,
      // release 释放阶段
      onPanResponderRelease: this.onPanResponderRelease,
      // Terminate 重新分配响应权
      onPanResponderTerminationRequest: this.onPanResponderTerminationReques,  // 返回bool值
      onPanResponderTerminate: this.onPanResponderTerminate,
      onShouldBlockNativeResponder: ()=>false  //这个是指阻止原生组件成为响应者,(仅限安卓)
    });
```

触摸事件中，基本都包含这两个参数nativeEvent和gestureState对象（e，gestureState）=> {...}

```javascript
>>nativeEvent:

changedTouches - 在上一次事件之后，所有发生变化的触摸事件的数组集合（即上一次事件后，所有移动过的触摸点）

identifier - 触摸点的ID

locationX - 触摸点相对于父元素的横坐标

locationY - 触摸点相对于父元素的纵坐标

pageX - 触摸点相对于根元素的横坐标

pageY - 触摸点相对于根元素的纵坐标

target - 触摸点所在的元素ID

timestamp - 触摸事件的时间戳，可用于移动速度的计算

touches - 当前屏幕上的所有触摸点的集合

numberActiveTouches - 当前在屏幕上的有效触摸点的数量
```

```javascript
>>gestureState对象:

stateID - 触摸状态的 ID

moveX - 最近一次移动时的屏幕横坐标

moveY - 最近一次移动时的屏幕纵坐标

x0 - 当响应器产生时的屏幕横坐标

y0 - 当响应器产生时的屏幕纵坐标

dx - 从触摸操作开始时的累计横向路程

dy - 从触摸操作开始时的累计纵向路程

vx - 当前的横向移动速度

vy - 当前的纵向移动速度

numberActiveTouches - 当前在屏幕上的有效触摸点的数量
```

#### 下拉刷新的业务需求


react-native-pull
react-native-pullRefreshScrollView

涉及到的Animate API

```javascript
Animated.timing(
  this.state.pullAni,   // 绑定变量值
      {
        toValue: -60,      // 最终目标值
        easing: Easing.linear,  //动画缓动
        duration: 100      // 动画时间
}).start();          // 动画开始

pullAni: new Animated.Value(0)       // 定义动画变量值
pullAni.SetValue(60)                //  变量设置变化值
```

假如自己是实现的话自定义实现思路:

1.绑定list为响应视图 {...this.panResponder.panHandlers}

```javascript
<View style={{flex:1,backgroundColor: 'pink'}} onLayout={this._onLayout}>
  {/* 绑定响应视图 */}
  <Animated.View
      ref={aniView=>{this.aniView = aniView}}
      style={{
        transform:[{translateY:this.state.pullAni}],
        width:this.state.width,
        height:this.state.height + freshH
      }}
  >
    {/* 刷新的视图 */}
    {this._renderRefreshView()}
    <View {...this.panResponder.panHandlers}>
      {/* list */}
      {this._renderListView()}
    </View>
  </Animated.View>
</View>
```

2.onMoveShouldSetPanResponder在移动时通过gestureState.dy正负,判断向下滑动还是向上滑动

```javascript
  if(dy < 0){//判断手势向上滑
    this.setState({scrollEnabled:true});
    bool = false;
  }else {//判断手势向下拉
    bool = this.lastListY < 1;   // 判断当前响应组件的纵坐标是否为0, 为0就申请为响应组件, 不为0就不申请响应,正常list向下滑
    bool && this.setState({scrollEnabled: false})
  }
  return bool;
```

3.onPanResponderMove 触摸开始移动,this.state.pullAni.setValue(aniY);
4.onPanResponderRelease 触摸释放, 当dy超过可以刷新的距离,回到正在刷新的距离,dy小于可以刷新的距离,截断本次下拉刷新,回到没有下拉刷新的初始位置

