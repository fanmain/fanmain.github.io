# 老项目mp4v2简单升级


### 起因：
目前这家公司项目中用到的mp4v2还是19年之前的版本，不支持h265，bugly崩溃率 < 0.01%。

### 检查更新
[github上新版本](https://github.com/TechSmith/mp4v2)

### 编译&xcode打包
1. 下载代码
```shell
git clone https://github.com/TechSmith/mp4v2.git
```
2. 配置静态、动态
> CMakeLists.txt
```
//动态链接
add_library(mp4v2 SHARED ${HEADER_FILES} ${SOURCE_FILES})
//静态链接
add_library(mp4v2 STATIC ${HEADER_FILES} ${SOURCE_FILES})
```
3. 使用cmake生成xcode工程
```shell
cd mp4v2
cmake -G "Xcode" .
```
4. xcode 打开,选择对应target 配置打包

