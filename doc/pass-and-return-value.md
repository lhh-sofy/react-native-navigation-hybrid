# RN 页面与原生页面相互跳转和传值

我们为原生和 RN 页面提供了一致的转场和传值方式。

RN 页面如何跳转和传值，我们 [容器与导航](./navigation.md) 一章已经提及，你不需要理会目标页面是原生的还是 RN 的，只要当前页面是 RN 的，处理方式都一样。

下面我们来说原生页面的跳转和传值方式：

## 创建原生页面

Android 需要继承 `HybridFragment`，具体可以参考 playground 项目中 `OneNativeFragment` 这个类：

```java
// android
public class OneNativeFragment extends HybridFragment {

}
```

HybridFragment 继承于 `AwesomeFragment`，关于 AwesomeFragment 更多细节，请看 [AndroidNavigation](https://github.com/listenzz/AndroidNavigation) 这个子项目。

iOS 需要继承 `HBDViewController`，具体可以参考 playground 项目中 `OneNativeViewController` 这个类：

```objc
// ios
#import <NavigationHybrid/NavigationHybrid.h>

@interface OneNativeViewController : HBDViewController

@end
```

## 注册原生页面

Android 注册方式如下

```java
public class MainApplication extends Application implements ReactApplication{
    @Override
    public void onCreate() {
        super.onCreate();
        SoLoader.init(this, false);

        ReactBridgeManager bridgeManager = ReactBridgeManager.instance;
        bridgeManager.install(getReactNativeHost());

        // 注册原生模块
        bridgeManager.registerNativeModule("OneNative", OneNativeFragment.class);
    }
}
```

iOS 注册方式如下

```objc
@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    NSURL *jsCodeLocation = [[RCTBundleURLProvider sharedSettings] jsBundleURLForBundleRoot:@"playground/index" fallbackResource:nil];
    [[HBDReactBridgeManager sharedInstance] installWithBundleURL:jsCodeLocation launchOptions:launchOptions];

    // 注册原生模块
    [[HBDReactBridgeManager sharedInstance] registerNativeModule:@"OneNative" forController:[OneNativeViewController class]];

    return YES;
}

@end
```

> 如果 RN 和原生都注册了同样的模块，即模块名相同，会优先采用 RN 模块。一个应用场景是，如果线上原生模块有严重 BUG，可以通过热更新用 RN 模块临时替换，并指引用户升级版本。

## 原生页面的跳转

首先实例化目标页面

```java
// android
AwesomeFragment fragment = getReactBridgeManager().createFragment("moduleName");
```

```objc
// ios
HBDViewController *vc = [[HBDReactBridgeManager sharedInstance] controllerWithModuleName:@"moduleName" props:nil options:nil];
```

就这样实例化目标页面，不管这个页面是 RN 的还是原生的。

接下来使用原生方式跳转

```java
// android
NavigationFragment navigationFragment = getNavigationFragment();
if (navigationFragment != null) {
    navigationFragment.pushFragment(fragment);
}
```

关于 NavigationFragment 的更多细节，请看 [AndroidNavigation](https://github.com/listenzz/AndroidNavigation) 这个子项目。

```objc
// ios
[self.navigationController pushViewController:vc animated:YES];
```

> 从原生页面跳转和传值到原生页面，除了上面的方式，你还可以用纯粹原生的方式来实现，就像引入 RN 之前那样

## 原生页面传值和返回结果

### Android

实例化时传值即可，不管这个页面是原生的还是 RN 的

```java
Bundle props = new Bundle();
props.putInt("user_id", 1);
AwesomeFragment fragment = getReactBridgeManager().createFragment("moduleName", props, null);
```

通过以下方式获取其它页面传递过来的值，不管这个页面是原生的还是 RN 的

```java
Bundle props = getProps();
```

通过调用以下方法返回结果给之前的页面，不管这个页面是原生的还是 RN 的

```java
public void setResult(int resultCode, Bundle data);
```

通过重写以下方法来接收结果，不管这个页面是原生的还是 RN 的

```java
public void onFragmentResult(int requestCode, int resultCode, Bundle data) {

}
```

更多细节，请看 [AndroidNavigation](https://github.com/listenzz/AndroidNavigation) 这个子项目。

### iOS

实例化时传值，不管这个页面是原生的还是 RN 的

```objc
NSDictionary *props = @{@"user_id": @1};
HBDViewController *vc = [[HBDReactBridgeManager sharedInstance] controllerWithModuleName:@"moduleName" props:props options:nil];
```

通过 `self.props` 获取其它页面传递过来的值，不管这个页面是原生的还是 RN 的

通过调用以下方法返回结果给之前的页面，不管这个页面是原生的还是 RN 的

```objc
- (void)setResultCode:(NSInteger)resultCode resultData:(NSDictionary *)data;
```

通过重写以下方法来接收结果，不管结果来自原生还是 RN 页面

```objc
- (void)didReceiveResultCode:(NSInteger)resultCode resultData:(NSDictionary *)data requestCode:(NSInteger)requestCode;
```
