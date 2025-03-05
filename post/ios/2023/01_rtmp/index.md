# 01_rtmp

# Mac搭建nginx+rtmp服务

后台搭建直播服务时，安装nginx：

+   下载

```ruby
$ brew tap denji/homebrew-nginx
```

\*安装

```ruby
$ brew install nginx-full --with-rtmp-module
```

**注意⚠️**  
`--with-rtmp-module`，一定要加上rtmp模块，不然添加rtmp服务时就会报错误：`unknown directive "rtmp" in /usr/local/etc/nginx/nginx.conf:117`  
如果遇到这种错误，只能是卸载重装了，下面是卸载命令

```ruby
$ brew uninstall nginx-full
```

然后重新安装。

打开文件`/usr/local/etc/nginx/nginx.conf`，编辑文件，在最下边添加如下rtmp配置：

```csharp
rtmp {
    server {
        listen 1935;
        ping 30s;
        notify_method get;

        application live {
            live on;
            record off;
        max_connections 1024;
        }
        }
}
```

更新配置：(版本号替换为自己的)

```bash
/usr/local/Cellar/nginx-full/1.19.0/bin/nginx -s reload
```

+   然后就可以启动服务了。

```shell
nginx
```

在浏览器里打开[http://localhost:8080](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A8080)  
如果看到如下页面，说明配置成功了！  

想要停止服务，命令：（stop是强制退出，quit是执行完任务后退出）

```ruby
$ nginx -s quit
或者
$ nginx -s stop
```

* * *

### 可以用ffmpeg推流，来测试直播服务。

+   安装`ffmpeg`

```ruby
$ brew install ffmpeg
```

安装成功后，就可以推流本地视频，如下命令：

```cpp
ffmpeg -re -i 本地视频路径 -vcodec libx264 -acodec aac -strict -2 -f flv rtmp://localhost:1935/live/room
```

开启推流后，用VLC播放器播放下面直播地址视频：

```cpp
rtmp://localhost:1935/live/room
```

这样一个简单的本地直播服务就搭建好了！

**iOS集成LFLiveKit直播库，替换ffmpeg推流，就可以测试直播功能了！**

