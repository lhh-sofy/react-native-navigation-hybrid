# 额外的生命周期回调函数

为注册组件提供了额外的生命周期回调函数

* componentDidAppear

当页面可见时回调，确保在 componentDidMount 之后回调

* componentDidDisappear

当页面不可见时回调

> 从 A 页面切换至 B 页面时，确保 A 页面的 componentDidDisappear 在 B 页面的 componentDidAppear 之前执行

* onComponentResult(requestCode, resultCode, data)

当从前一个页面返回结果时回调，包含 pop、dismiss、hideModal 等操作。

> onComponentResult 总是在该页面的 componentDidAppear 之后执行

* onBackPressed

仅对 Android 平台生效。用于处理通过 showModal 弹出的注册组件的物理或虚拟返回键。
