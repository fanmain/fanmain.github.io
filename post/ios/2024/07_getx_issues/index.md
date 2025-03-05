# 07_Getx_issues


### 项目中使用Getx 遇到的问题

# 1.简单状态管理下
```dart
#view
#widget
class DropMenuWidget extends StatefulWidget {
  ...
  final String? selectedValue; //默认选中的值
}


GetBuilder(builder: (_){
    return DropMenuWidget(data: ...,
            selectCallBack: ...,
            selectedValue: logic.state.cmdType.toString(),
            );
})
#logic
update();//不刷新对应GetBuilder
#修改view 
GetBuilder(id:0x01,builder: (_){
    return DropMenuWidget(data: ...,
            selectCallBack: ...,
            selectedValue: logic.state.cmdType.toString(),
            );
})
update([0x01]);//刷新对应GetBuilder 但是DropMenuWidget未能刷新

#最终修改
#将第三方widget构造加入 key: Key("${state.cmdType}"),
GetBuilder(id:0x01,builder: (_){
    return DropMenuWidget(key:Key('...'),data: ...,
            selectCallBack: ...,
            selectedValue: logic.state.cmdType.toString(),
            );
})
update([0x01]); //问题解决

```
