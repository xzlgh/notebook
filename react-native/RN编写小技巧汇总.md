# RN 技巧汇总

## 目录

### [ActionSheet 上 InputText 键盘遮挡问题](#ActionSheet底部有InputText，软键盘唤起被遮挡)

---

## Modal

### ActionSheet

#### ActionSheet 底部有 InputText，软键盘唤起被遮挡

##### 业务场景描述

用户需要在一个 ActionSheet 上面输入邮箱和电话；此时底部的 ActionSheet 类似于我们的聊天工具的输入框，输入框 InputText 聚焦时，软键盘被调起

##### 解决办法

使用`KeyboardAvoidingView`解决这个问题，过渡效果平滑

ActionSheetWrapper.tsx

```tsx
import React from "react";
import {
  View,
  Text,
  StyleSheet,
  Dimensions,
  TouchableOpacity
} from "react-native";
import Modal from "react-native-translucent-modal";

interface Props {
  visible: boolean;
  isCloseMode?: boolean;
  onClose?: () => void;
}

const { width, height } = Dimensions.get("window");

export default class ActionSheetWrapper extends React.Component<Props> {
  static defaultProps = {
    onClose: () => {}
  };
  render() {
    const { visible, isCloseMode } = this.props;
    return (
      <Modal
        animationType="slide"
        transparent={true}
        visible={visible}
        onRequestClose={() => {}}
      >
        <TouchableOpacity
          activeOpacity={1}
          style={styles.box}
          onPress={this.closeModal}
        >
          {this.props.children}
        </TouchableOpacity>
      </Modal>
    );
  }

  // 关闭模态框
  closeModal = () => {
    const { isCloseMode, onClose = () => {} } = this.props;
    if (!isCloseMode) return;
    onClose();
  };
}

export const styles = StyleSheet.create({
  box: {
    width,
    height,
    backgroundColor: "rgba(52,52,52,0.5)",
    // 解决办法：注释下面这一行，解决因包裹组件的布局造成的不会跟随软键盘问题
    justifyContent: "flex-end"
  }
});
```

Demo.tsx

```tsx
import React from "react";
import { KeyboardAvoidingView } from "react-native";

// 自定义的ActionSheetWrapper组件，组件主要有Madol
import React from "react";
import {
  View,
  Text,
  StyleSheet,
  TouchableOpacity,
  Image,
  TextInput,
  KeyboardAvoidingView
} from "react-native";
import ActionSheet from "@Components/HOC/ActionSheetWrapper";

interface Props {
  visiable: boolean;
  closeActionSheet: () => void; // 关闭ActionSheet
}

export default class Demo extends React.Component<Props> {
  render() {
    const { visiable, closeActionSheet } = this.props;
    return (
      <ActionSheet visible={visiable} onClose={closeActionSheet}>
        // 解决方案，用一个View占位，避免使用position以及flexbox布局造成影响
        <View style={{ flex: 1 }} />
        // 此时使用`KeyboardAvoidingView`问题得到解决
        <KeyboardAvoidingView style={styles.box} behavior="padding">
          {/* 内容 */}
          <View style={styles.formBox}>
            <View
              style={[
                styles.itemBox,
                { borderBottomWidth: 1, borderBottomColor: "#F1F1F1" }
              ]}
            >
              <Text style={styles.itemText}>Email</Text>
              <View style={styles.inputWrapper}>
                <TextInput
                  style={styles.input}
                  placeholder={"Please enter a valid email"}
                  placeholderTextColor={"#A0ACB1"}
                />
              </View>
            </View>
            <View style={styles.itemBox}>
              <Text style={styles.itemText}>Phone</Text>
              <View style={styles.inputWrapper}>
                <TextInput
                  style={styles.input}
                  placeholder={"Please enter a valid phone number"}
                  placeholderTextColor={"#A0ACB1"}
                />
              </View>
            </View>
          </View>
        </KeyboardAvoidingView>
      </ActionSheet>
    );
  }
}

export const styles = StyleSheet.create({
  box: {
    backgroundColor: "#fff"
  },
  formBox: {
    marginTop: 32,
    marginBottom: 45,
    paddingLeft: 18,
    paddingRight: 18,
    borderTopColor: "#F1F1F1",
    borderTopWidth: 1,
    borderBottomWidth: 1,
    borderBottomColor: "#F1F1F1"
  },
  itemBox: {},
  inputWrapper: {
    margin: 0,
    padding: 0
  },
  input: {
    fontSize: 14
  },
  itemText: {
    marginTop: 18,
    color: "#37474F",
    fontSize: 12
  }
});
```

---

### scheme

#### 外部h5页面唤起App且跳转到App指定页面

[参考链接](https://www.jianshu.com/p/351c2f690569)

##### 场景描述

*我当前的App的Detail页面通过Linking跳转指定的页面A。在A页面中有按钮`backApp`, 当我点击按钮时，页面需要回到App中的特定页面MyCenter*

##### 解决方案

使用scheme可以实现外部唤醒App,再结合Linking.getInitialURL来获取当前唤醒App的url

##### 具体实现

在Andorid/app/src/main/AndroidManifest.xml文件中添加如下内容

```xml
<activity
  android:name="com.Test.MainActivity"
  android:label="@string/app_name"
  android:configChanges="keyboard|keyboardHidden|orientation|screenSize"
  android:windowSoftInputMode="adjustResize"
  android:screenOrientation="portrait"
  android:launchMode="singleTask"> /* add launchMode */
  <intent-filter>
      <action android:name="android.intent.action.MAIN" />
      <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>

/* add start */
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="myapp"
      android:host="myhost"
      android:port="666"
      android:path="/path"
    />
  </intent-filter>
/* add end*/
  ...
</activity>
```

1. 增加设置launchMode为singleTask模式
2. intent-filter设置scheme的app名称和host和port以及path等等

##### 外部调用方式

test.html

```html
<a href="myapp://myhost:666/path">点我唤醒myapp</a>
```

##### 出现的问题

*以上步骤操作只完成了唤醒App这一步，在react-navigation的官网介绍中，我们是可以直接跳转到app指定的路由位置的，但是此时和官网介绍的表现并不一致，scheme只唤醒了app，无法跳转到指定的路由。*

###### 解决以上问题

> 解决思路

1. 通过Linking.getInitialURL获得唤醒app是的url值，也就是上面html中href的值：myapp://myhost:666/path
2. 根据获取到的url值，来进行符合业务场景的代码实现

> 解决过程

1. 在Linking到test.html的app页面增加如下代码

```tsx
const BACK_APP = 'myapp://myhost:666/path'
AppState.addEventListener('change', (appState) => {
  // 如果app不是被唤醒状态（从后台切到当前），直接返回
  if(appState !== 'active') return
  // 处理url
  const handleUrl = (url: string) => {
    console.log(url)
    if(url === BACK_APP) {
      // TODO 根据业务做处理
      ...
    } else {
      // TODO 根据业务做一些其他处理
      ...
    }
  }
  // 获取url
  Linking.getInitialURL().then(handleUrl)
})
```

2. 此时会出现另外一个坑，当我们已经Linking过一次之后，直接点击手机的物理Home键，再重新唤醒app，上面的代码会再次执行，且获得的url还是上一次的url,即url='myapp://myhost:666/path?****'这显然不是我们想要的。
当出现这个问题时，我们可以使用在Android原生代码中增加一个ResetLinkingUrl的类，将url重置为null,提供给RN来调用

- Android/app/src/main/java/com/*/Package/ 目录下新增一个LinkingPackage.java文件（文件名名字可以根据自己需要和习惯起），写入内容如下

```java
package com.包名.Package;

import com.包名.Module.CustomLinkingModule;
import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class LinkingPackage implements ReactPackage {
    @Override
    public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
        List<NativeModule> modules = new ArrayList<>();
        CustomLinkingModule module = new CustomLinkingModule(reactContext);
        modules.add(module);
        return modules;
    }

    @Override
    public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
        return Collections.emptyList();
    }
}

```

- Android/app/src/main/java/com/*/Module/ 目录下新建一个CustomerLinkingModule.java （文件名名字可以根据自己需要和习惯起），写入如下内容

```java
package com.包名.Module;

import android.app.Activity;
import android.content.Intent;

import com.facebook.react.bridge.JSApplicationIllegalArgumentException;
import com.facebook.react.bridge.Promise;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReactContextBaseJavaModule;
import com.facebook.react.bridge.ReactMethod;

public class CustomLinkingModule extends ReactContextBaseJavaModule {

    private String MODULE_NAME = "linkingModule";

    public CustomLinkingModule(ReactApplicationContext reactContext) {
        super(reactContext);
    }

    @Override
    public String getName() {
        return MODULE_NAME;
    }

    /*
    * 不可以直接将结果return，因为js侧是异步获取结果的，这里将结果返回成promise，
    * 这个函数即为提供给RN调用的函数，RN调用方式为await NativeModules.linkingModule.resetURL()
    */
    @ReactMethod
    public void resetURL(Promise promise) {
        try {
            Activity currentActivity = getCurrentActivity();
            if (currentActivity != null) {
                Intent intent = new Intent(Intent.ACTION_MAIN);
                currentActivity.setIntent(intent);
            }
            promise.resolve(true);
        } catch (Exception e) {
            promise.reject(new JSApplicationIllegalArgumentException("Could not reset URL"));
        }
    }
}
```

- 此时我们可以在解决方案步骤一中，唤醒app获得url后重置url

==至此我们就解决了上述——外部h5页面唤起App且跳转到App指定页面的业务场景==

---
