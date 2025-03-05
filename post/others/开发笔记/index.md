# 日常小技巧【未分类】


###  win10  本地时间为UTC时间
Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1

### edge缓存修改
2021-12-01更新：经过测试最新版Edge不再需要2种方法结合才能生效，2种方法单独都可以使用(谷歌Chrome同理)

方法一(一劳永逸)：

①创建新的缓存文件夹，以E:\Cache\Edge为例，必须在第3步之前创建；

②关闭Edge浏览器，删除 C:\Users\Administrator\AppData\Local\Microsoft\Edge\User Data\Default路径下的Cache文件夹(加粗字体修改成当前计算机登陆的用户名，Win10用户通过“设置-账户-账户信息”、Win7用户通过“控制面板-用户帐户-用户帐户”查看，用户名必须核实)；

③以管理员身份运行cmd命令行输入mklink /D "C:\Users\Administrator\AppData\Local\Microsoft\Edge\User Data\Default\Cache" "E:\Cache\Edge"并运行(加粗字体修改成当前登陆用户名，第二个引号内容修改成新路径)；

方法二(简单)：

①右键Edge图标 -> 属性 -> 目标 -> 最末尾加上 --disk-cache-dir="E:\Cache\Edge"(注意--前面有个空格，引号里面内容修改成新路径)，点击确定。此方法虽然简单但是必须通过这个图标打开浏览器才能正常缓存到新路径。


