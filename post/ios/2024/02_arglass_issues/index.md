# 02_ARGlass_issues



### issues

1. iphone14  iOS16.6 保存相册是设置originalFilename 后读取不一致
代码
```swift
// 设置
let option = PHAssetResourceCreationOptions()
option.originalFilename = "2024-04-23_08-36-02_1.jpg" 
// 读取
let resources = PHAssetResource.assetResources(for: self)
if let resource = resources.first {
    fileName = resource.originalFilename
}
name = "2024-04-23_16-36-02_1.jpg"
```
字符串时间被莫名其妙+8了 
关闭iCloud相册同步 开启时间市区自动同步 虫害手机后恢复
