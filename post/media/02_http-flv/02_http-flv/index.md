# 02_http Flv


 

#### 文章目录

+   [前言](#)
+   [一、HTTP-FLV 简介](#)
+   +   [1、市场上使用 http-flv 的商家](#)
    +   [2、http-flv、rtmp 和 hls 直播的优缺点](#)
    +   [3、http-flv 技术实现](#)
+   [二、Nginx 配置 http-flv](#)
+   +   [1、Windows 安装 nginx，已经集成 nginx-http-flv-module](#)
    +   [2、nginx.conf 配置文件](#)
    +   [3、运行 nginx 服务器](#)
    +   [4、ffmpeg 推流](#)
    +   [5、VLC 播放](#)
    +   [6、flv.js 网页播放](#)
+   [三、FLV 格式简介](#)
+   +   [1、简介](#)
    +   [2、FLV 格式解析](#)
    +   +   [①、header](#)
        +   [②、body](#)
+   [四、FLV Adobe 官方标准](#)
+   +   [1、单位说明](#)
    +   [2、FLV 文件头和文件体 (E.2, E.3)](#)
    +   [3、FLV Tag (E.4)](#)
    +   [4、AudioTag (E.4.2)](#)
    +   [5、VideoTag (E.4.3)](#)
    +   [6、SCRIPTDATA (E.4.4)](#)
    +   [7、onMetadata (E.5)](#)
    +   [8、keyframes 索引信息](#)
+   [五、FlvAnalyzer 分析 flv 文件](#)

* * *

## 前言

传统的直播[协议](https://so.csdn.net/so/search?q=%E5%8D%8F%E8%AE%AE&spm=1001.2101.3001.7020)要么使用 Adobe 的基于 TCP 的 RTMP 协议， 要么使用 Apple 的基于 HTTP 的 HLS 协议。本文介绍另外一种结合了 RTMP 的低延时， 以及可以复用现有 HTTP 分发资源的流式协议 HTTP-FLV。

* * *

## 一、[HTTP](https://so.csdn.net/so/search?q=HTTP&spm=1001.2101.3001.7020)\-FLV 简介

HTTP-FLV，即将音视频数据封装成 FLV，然后通过 HTTP 协议传输给客户端。

HLS 其实是一个 “文本协议” ，而并非流媒体协议，(ts0,ts1,…)。 那么，什么样的协议才能称之为流媒体协议呢？  
答：流（stream）：数据在网络上按时间先后次序传输和播放的连续音/视频数据流。之所以可以按照顺序传输和播放连续是因为在类似 [RTMP](https://so.csdn.net/so/search?q=RTMP&spm=1001.2101.3001.7020)、FLV 协议中，每一个音视频数据都被封装成了包含时间戳信息头的数据包。而当播放器拿到这些数据包解包的时候能够根据时间戳信息把这些音视频数据和之前到达的音视频数据连续起来播放。

> MP4、MKV 等等类似这种封装，必须拿到完整的音视频文件才能播放，因为里面的单个音视频数据块不带有时间戳信息，播放器不能将这些没有时间戳信息数据块连续起来，所以就不能实时的解码播放。

### 1、市场上使用 http-flv 的商家

优酷的 pc 网页直播，斗鱼、 熊猫 tv、 虎牙 pc 网页上也使用了 http-flv

### 2、http-flv、rtmp 和 hls 直播的优缺点

+   三者的延迟性
    +   http-flv：低延迟，内容延迟可以做到 2-5 秒；
    +   Rtmp：低延迟，内容延迟可以做到 2-5 秒。
    +   Hls：延迟较高（ts0，segment-time：5，10s）。
+   三者的易用性
    +   rtmp 和 http-flv：播放端安装率高。只要浏览器支持 FlashPlayer 就能非常简易的播放。
    +   hls：最大的优点：HTML5 可以直接打开播放；这个意味着可以把一个直播链接通过微信等转发分享，不需要安装任何独立的 APP，有浏览器即可。
+   rtmp 和 http-flv 比较
    +   穿墙：很多防火墙会墙掉 RTMP，但是不会墙 HTTP，因此 HTTP FLV 出现奇怪问题的概率很小。
    +   调度：RTMP 也有个 302，可惜是播放器 as 中支持的，HTTP FLV 流就支持 302 方便 CDN 纠正 DNS 的错误。
    +   容错：SRS 的 HTTP FLV 回源时可以回多个，和 RTMP 一样，可以支持多级热备。
    +   简单：FLV 是最简单的流媒体封装，HTTP 是最广泛的协议，这两个组合在一起维护性更高，比 RTMP 简单多了。

### 3、http-flv 技术实现

HTTP 协议中有个约定：content-length 字段，http 的 body 部分的长度。

+   服务器回复 http 请求的时候如果有这个字段，客户端就接收这个长度的数据然后就认为数据传输完成了。
+   如果服务器回复 http 请求中没有这个字段，客户端就一直接收数据，直到服务器跟客户端的 socket 连接断开。 (流式传输）

http-flv 直播就是利用第二个原理，服务器回复客户端请求的时候不加 content-length 字段，在回复了 http 内容之后，紧接着发送 flv 数据，客户端就一直接收数据了。

## 二、Nginx 配置 http-flv

### 1、Windows 安装 nginx，已经集成 nginx-http-flv-module

RTMP 服务器：Nginx+rtmp（windows）的环境搭建如有需要可自取：

链接：[https://pan.baidu.com/s/1AcIVERWUPbJL1zu8yCcAzw](https://pan.baidu.com/s/1AcIVERWUPbJL1zu8yCcAzw)  
提取码：mtdf

### 2、nginx.conf 配置文件

nginx.conf 配置文件如下：

```bash
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
#error_log  logs/error.log  debug;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

# 添加RTMP服务
rtmp {
    server {
        listen 1935; # 监听端口

        chunk_size 4000;
        application live {
            live on;
			gop_cache on;
			hls on;
			hls_path html/hls;
        }
    }
}

# HTTP服务
http {
    include       mime.types;
    default_type  application/octet-stream;

    #access_log  logs/access.log  main;

    server {
        listen       8080; # 监听端口
 
 location /flv {
		flv_live on;
		chunked_transfer_encoding on;	
		add_header 'Access-Control-Allow-Origin' '*';
		add_header "Access-Control-Allow-Credentials" "true";
		add_header "Access-Control-Allow-Methods" "*";
		add_header "Access-Control-Allow-Headers" "Content-Type,Access-Token";
		add_header "Access-Control-Expose-Headers" "*";	
		}

		location /stat.xsl {
            root html;
        }
		location /stat {
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }
		location / {
            root html;
        }
    }
}
```

**其中涉及到跨域问题：#http-flv**

```bash
 location /flv {
		flv_live on;
		chunked_transfer_encoding on;	
		add_header 'Access-Control-Allow-Origin' '*';
		add_header "Access-Control-Allow-Credentials" "true";
		add_header "Access-Control-Allow-Methods" "*";
		add_header "Access-Control-Allow-Headers" "Content-Type,Access-Token";
		add_header "Access-Control-Expose-Headers" "*";	
		}
```

### 3、运行 nginx 服务器

双击 nginx8080.exe  
![在这里插入图片描述](./assets/870c612551a548be90baa6f976343b8b.png)  
在任务管理器可以看到目前 nginx 已开始工作  
![在这里插入图片描述](./assets/30e9e44ace004d87ab6d1de39515ffc2.png)

### 4、ffmpeg 推流

```bash
ffmpeg -re -i SampleVideo_1280x720_20mb.mp4 -vcodec libx264 -acodec aac -f flv -y rtmp://127.0.0.1:1935/live/test1
```

这个命令使用 FFmpeg 工具来将输入视频文件 SampleVideo\_1280x720\_20mb.mp4 转换为 FLV 格式并通过 RTMP 协议流式传输到指定的 URL 地址 rtmp://127.0.0.1:1935/live/test1；

+   `-re`：以实时模式（real-time）读取输入文件，模拟实时流传输的速度。
+   `-i SampleVideo_1280x720_20mb.mp4`：指定输入文件名为 SampleVideo\_1280x720\_20mb.mp4。
+   `-vcodec libx264`：选择 H.264 编码器作为视频编码器；
+   `-acodec aac`：选择 AAC 编码器作为音频编码器；
+   `-f flv`：指定输出格式为FLV（Flash Video）；
+   `-y`：自动覆盖输出文件，如果存在同名文件则会被替换；
+   `rtmp://127.0.0.1:1935/live/test1`：指定输出的 URL 地址，以 RTMP 协议传输到 192.168.36.176 服务器的 1935 端口的 live 应用程序中的 test1 流

![在这里插入图片描述](./assets/2a8664ce3b5c41eba830f140d56a32a5.png)

### 5、VLC 播放

+   http-flv：`http://localhost:8080/flv?port=1935&app=live&stream=test1`  
    对应关系如下：  
    ![在这里插入图片描述](./assets/364754d32b694c96bb310a7562ba69c3.png)  
    VLC 进行拉流  
    ![在这里插入图片描述](./assets/f6de4df5b3014b2f982df609d036aec5.png)  
    ![在这里插入图片描述](./assets/4f49503a81a144ff94557bb10cd1cd19.png)
+   rtmp：`rtmp://127.0.0.1:1935/live/test1`  
    ![在这里插入图片描述](./assets/134935bb2a3f469696a0f55ffb172e38.png)  
    ![在这里插入图片描述](./assets/e1362983dd214ee0846167459de56def.png)
+   Hls：`http://localhost:8080/hls/test1.m3u8`  
    ![在这里插入图片描述](./assets/0518ccb1e0424b27bf91c4b02ee6ab05.png)  
    ![在这里插入图片描述](./assets/90ad978a51dc457d8feb5ae2f8987471.png)

此外，视频和音频内容分割为小的 TS 文件，并生成相应的 M3U8 文件，以便客户端能够获取和播放这些文件。M3U8 文件可以通过 HTTP 服务器提供给客户端，并使用流媒体播放器（如VLC、HLS播放器等）进行解析和播放。  
![在这里插入图片描述](./assets/e1d4c4472f1e4c2986f23c39b5d1e506.png)  
这个目录是由 nginx.conf 配置文件决定的：  
![在这里插入图片描述](./assets/e7855cf58e6a4a86acaf918616f06b46.png)

### 6、flv.js 网页播放

前面我们已经解决了跨域问题  
![在这里插入图片描述](./assets/aa09762a66ca428894ad25c42b500ade.png)  
html.flv 文件内容如下：

```html
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;
#error_log  logs/error.log  debug;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

# 添加RTMP服务
rtmp {
    server {
        listen 1935; # 监听端口

        chunk_size 4000;
        application live {
            live on;
			gop_cache on;
			hls on;
			hls_path html/hls;
        }
    }
}

# HTTP服务
http {
    include       mime.types;
    default_type  application/octet-stream;

    #access_log  logs/access.log  main;

    server {
        listen       8080; # 监听端口
 
 location /flv {
			flv_live on;
			chunked_transfer_encoding on;	
			add_header 'Access-Control-Allow-Origin' '*';
     add_header "Access-Control-Allow-Credentials" "true";
     add_header "Access-Control-Allow-Methods" "*";
     add_header "Access-Control-Allow-Headers" "Content-Type,Access-Token";
     add_header "Access-Control-Expose-Headers" "*";	
		}

		location /stat.xsl {
            root html;
        }
		location /stat {
            rtmp_stat all;
            rtmp_stat_stylesheet stat.xsl;
        }
		location / {
            root html;
        }
    }
}
```

因此双击 flv.html 文件，可以看到网页播放成功  
![在这里插入图片描述](./assets/5a2865f585c64159882f21e914102e0c.jpg)

## 三、FLV 格式简介

### 1、简介

FLV（Flash Video）是现在非常流行的流媒体格式，由于其视频文件体积轻巧、封装播放简单等特点，使其很适合在网络上进行应用，目前主流的视频网站无一例外地使用了 FLV 格式。另外由于当前浏览器与 Flash Player 紧密的结合，使得网页播放 FLV 视频轻而易举，也是 FLV 流行的原因之一。

FLV 是流媒体封装格式，我们可以将其数据看为二进制字节流。总体上看，FLV 包括文件头（File Header：9 字节）和文件体（File Body）两部分，其中文件体由一系列的 Tag 及 Tag Size 对组成。  
![在这里插入图片描述](./assets/a4fbcc535b1241948ed278fa86ad4e5e.png)

注意这个大小关系：`PreviosTagSize = TagDataSize + 11;`

### 2、FLV 格式解析

先来一张图，这是上面我们播放的视频文转换为 FLV 文件  
![在这里插入图片描述](./assets/2efb82dcd1cb4671b9233d2bd2694365.png)  
使用 Notepad++ 进行查看二进制数据（`注：这里要求 Notepad++ 安装了 HexEditor 插件`）

> 可以参考我以前的博客：[notepad++安装HexEditor插件查看二进制文件](https://blog.csdn.net/qq_41839588/article/details/130449117)

`SampleVideo_1280x720_20mb.flv` 文件二进制内容如下：  
![在这里插入图片描述](./assets/b6a374ac9f2c44d0947af390853b6562.png)

#### ①、header

头部分由以下几部分组成：`Signature（3 Byte）+Version（1 Byte）+Flags（1 Bypte）+DataOffset（4 Byte）`

+   `signature` 占 3 个字节：固定 FLV 三个字符作为标示。一般发现前三个字符为 FLV 时就认为他是 flv 文件。
+   `Version` 占 1 个字节：标示 FLV 的版本号。 这里我们看到是 1
+   `Flags` 占 1 个字节：内容标示。第 0 位和第 2 位，分别表示 video 与 audio 存在的情况。(1 表示存在，0 表示不存在)。截图看到是 0x05，也就是 00000101，代表既有视频，也有音频。
+   `DataOffset` 4 个字节：表示 FLV 的 header 长度。 这里可以看到固定是 9

#### ②、body

FLV 的 body 部分是由一系列的 `back-pointers`（ 后向指针） + `tag` 构成

+   `back-pointers` 固定 4 个字节，表示前一个 tag 的 size。
    +   从上图可以看到前一个 tag 的 size 为 0
+   `tag` 分三种类型：video、audio、scripts。
    +   **tag 组成**：`tag type`[1B]+`tag data size`[3B]+`Timestamp`[3B]+`TimestampExtended`[1B]+`stream id`[3B]+ `tag data`
        +   `type` 1 个字节。8 为 Audio，9 为 Video，18 为 scripts
            +   从上图可以看到 `type` 为 `0x123 = 18 --> scripts`
        +   `tag data size` 3 个字节。表示 tag data 的长度：从 streamd id 后算起。
            +   从上图可以看到 `tag data size` 为 `0x123 = 297`
        +   `Timestreamp` 3 个字节。 时间戳
            +   从上图可以看到 `Timestreamp` 为 `0x000000 = 0`
        +   `TimestampExtended` 1 个字节。 时间戳扩展字段
            +   从上图可以看到 `TimestampExtended` 为 `0x00 = 0`
        +   `stream id` 3 个字节。 总是 0
        +   `tag data` 数据部分
    +   **tag 头伪代码**：  
        ![在这里插入图片描述](./assets/d9685acb6c064fd38d7e9602cd28d000.png)

## 四、FLV [Adobe](https://so.csdn.net/so/search?q=Adobe&spm=1001.2101.3001.7020) 官方标准

FLV 文件格式标准是写在 F4V/FLV file format spec v10.1 的附录 E 里面的 FLV File Format。

### 1、单位说明

![在这里插入图片描述](./assets/d18fbb8efa634c2ea2c589ec9886688a.png)

### 2、FLV 文件头和文件体 (E.2, E.3)

从整个文件上看，`FLV = FLV File Header + FLV File Body`。  
![在这里插入图片描述](./assets/0fba52a4713a476ba9119f5580de3b3d.png)  
通常，FLV 的前 13 个字节（flv header + PreviousTagSize0）完全相同，所以，程序中会单独定义一个常量来指定。特殊，比如有的视频文件没有视频流或没有音频流。

### 3、FLV Tag (E.4)

![在这里插入图片描述](./assets/694ffb288f1b486fabfd3d2f05c52448.png)  
Timestamp 和 TimestampExtended 组成了这个 TAG 包数据的 PTS 信息，`PTS =Timestamp | TimestampExtended << 24`。

### 4、AudioTag (E.4.2)

由于 AAC 编码的特殊性， 这里着重说明了 AAC 编码的 Tag 格式。  
![在这里插入图片描述](./assets/31b02a57ac7946168d5f9145b0ddb9ba.png)  
AudioTagHeader 的第一个字节，也就是接跟着 StreamID 的 1 个字节包含了音频类型，采样率等的基本信息。

AudioTagHeader 之后跟着的就是 AUDIODATA 部分了。但是，这里有个特例，如果音频格式（SoundFormat）是 AAC，AudioTagHeader 中会多出 1 个字节的数据 AACPacketType，这个字段来表示 AACAUDIODATA 的类型：0 = AAC sequence header，1 = AAC raw。

AudioSpecificConfig 结构描述非常复杂，在标准文档中是用伪代码描述的，这里先假定要编码的音频格式，做一下简化。

音频编码为：AAC-LC，音频采样率为 44100。  
![在这里插入图片描述](./assets/f15ce7a0c51947c99e3dd1995230dd55.png)  
在 FLV 的文件中，一般情况下 AAC sequence header 这种包只出现 1 次，而且是第一个 audio tag，为什么需要这种 tag，因为在做 FLV demux 的时候，如果是 AAC 的音频，需要在每帧 AAC ES 流前边添加 7 个字节 ADST 头， ADST 是解码器通用的格式，也就是说 AAC 的纯 ES 流要打包成 ADST 格式的 AAC 文件，解码器才能正常播放。 就是在打包 ADST 的时候，需要 samplingFrequencyIndex 这个信息，samplingFrequencyIndex 最准确的信息是在 AudioSpecificConfig 中，这样，你就完全可以把 FLV 文件中的音频信息及数据提取出来， 送给音频解码器正常播放了。

### 5、VideoTag (E.4.3)

由于 AVC(H.264) 编码的特殊性， 这里着重说明了 AVC(H.264) 编码的 Tag 格式。  
![在这里插入图片描述](./assets/8b8c50f1700b4520a49b4cbb17f7f4e0.png)  
VideoTagHeader 的第一个字节，也就是接跟着 StreamID 的 1 个字节包含着视频帧类型及视频 CodecID 等最基本信息。

VideoTagHeader 之后跟着的就是 VIDEODATA 部分了。但是，这里有个特例，如果视频格式（CodecID）是 AVC， VideoTagHeader 会多出 4 个字节的信息。

AVCDecoderConfigurationRecord 包含着是 H.264 解码相关比较重要的 SPS 和 PPS 信息，在给 AVC 解码器送数据流之前一定要把 SPS 和 PPS 信息送出，否则的话，解码器不能正常解码。而且在解码器 stop 之后再次 start 之前， 如 seek，快进快退状态切换等，都需要重新送一遍 SPS 和 PPS 的信息。AVCDecoderConfigurationRecord 在 FLV 文件中一般情况也只出现 1 次，也就是第一个 video tag。

AVCDecoderConfigurationRecord 长度为 sizeof(UI8) \* (11 + sps\_size + pps\_size)。  
![在这里插入图片描述](./assets/7cfe05c5d1f046b3917970a21da42d52.png)

### 6、SCRIPTDATA (E.4.4)

ScriptTagBody 内容用 AMF 编码  
![在这里插入图片描述](./assets/b7913f85e19842d8ae6b4ffa41720783.png)  
一个 SCRIPTDATAVALUE 记录包含一个有类型的 ActionScript 值。

### 7、onMetadata (E.5)

FLV metadata object 保存在 SCRIPTDATA 中，叫 onMetaData。不同的软件生成的 FLV 的 properties 不同。  
![在这里插入图片描述](./assets/a0eb1f12b34a4eeda3202a4ac74f016c.png)

### 8、keyframes 索引信息

官方的文档中并没有对 keyframes index 做描述，但是，flv 的这种结构每个 tag 又不像 TS 有同步头，如果没有 keyframes index 的话，需要按顺序读取每一个 tag，seek 及快进快退的效果会非常差。后来在做 flv 文件合成的时候，发现网上有的 flv 文件将 keyframes 信息隐藏在 Script Tag 中。

keyframes 几乎是一个非官方的标准，也就是民间标准。两个常用的操作 metadata 的工具是 flvtool2 和 FLVMDI， 都是把 keyframes 作为一个默认的元信息项目。在 FLVMDI 的主页上有描述：  
![在这里插入图片描述](./assets/dbaa973a223541819f3a5c3651fefe62.png)  
也就是说 keyframes 中包含着 2 个内容 “filepositions” 和 “times”分别指的是关键帧的文件位置和关键帧的 PTS。通过 keyframes 可以建立起自己的 Index，然后在 seek 和快进快退的操作中，快速有效地跳转到你想要找的关键帧位置进行处理。

## 五、FlvAnalyzer 分析 flv 文件

参考我之前的博客：[音视频开发常用工具](https://blog.csdn.net/qq_41839588/article/details/132632031)

查看其中第三章的内容：  
![在这里插入图片描述](./assets/687ae0ab3c044b929331c1872586cdc1.png)

* * *

我的qq：2442391036，欢迎交流！

* * *

[原文](https://blog.csdn.net/qq_41839588/article/details/134340606)
