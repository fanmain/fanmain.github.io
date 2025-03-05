# 01_swiftuiTips


### 基础
1. 控件一览
![](./assets/2024-04-12-10-08-03.png)

### 旧项目使用
1. UIKit 打开 swiftui页面
```c
UIHostingController(rootView: SwiftUIView())
```
2. 版本兼容
+ swiftUI 自iOS13问世以来，每个版本都不兼容，每个版本都有新的Api，适配不同版本工作量巨大🤣；
iOS目前（截止2023低） iOS15之下的设备只占3%，这3%中还包含iOS14，
因此最低版本建议iOS14。
+ 多平台适配（iOS、mac、ipad） 最低版本只能使用iOS14以上

### LIST
[SwiftUI 实战二、List 的使用&交互](https://juejin.cn/post/7307095960844615690?searchId=2024051714221507DC0289DF9D78A93088)

### 遇到的问题:
1. SPM加载过慢
+  通过终端打开xcode,终端开启全局代理。
```shell
    export ALL_PROXY=http://127.0.0.1:7890
    open -a Xcode.app
```
