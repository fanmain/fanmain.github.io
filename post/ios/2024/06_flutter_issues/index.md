# 06_flutter_issues


# flutter 升级 3.22.1 记录


### dependencies 升级报错修复 


### flutter attach 无法连接

#### 尝试：

> 1. 首先怀疑防火墙问题,开关防火墙 => 无效
> 2. 手机重启  => 无效
> 3. 调试 
```shell
    flutter attach -v 
    # 检索服务
    dns-sd -Z _dartobservatory._tcp 
    # 手动attach
    flutter attach --debug-uri="http://127.0.0.1:53168/HC5AIqfdvCQ\=/" -v   
    # 提示iproxy文件安全权限，在mac安全和隐私中允许
    # 在防火墙中将dart flutter iproxy 相关的文件加入
```
> 4. info.plist 加入mdns dart服务名 _dartVmService._tcp 
     



#### 参考
+ [官方issue](https://github.com/flutter/flutter/issues/127547)
+ [Flutter多引擎无法Attach问题分析及热重载卡死问题处理](https://blog.devlxx.com/2022/12/10/Flutter%E5%A4%9A%E5%BC%95%E6%93%8E%E6%97%A0%E6%B3%95Attach%E9%97%AE%E9%A2%98%E5%88%86%E6%9E%90%E5%8F%8A%E7%83%AD%E9%87%8D%E8%BD%BD%E5%8D%A1%E6%AD%BB%E9%97%AE%E9%A2%98%E5%A4%84%E7%90%86/)
+ [解决方法](https://www.jianshu.com/p/a0291c7baa8a)
