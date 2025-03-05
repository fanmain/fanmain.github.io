# 摄像头项目中遇到的问题


## 入职当前公司，接手App时遇到的问题和解决方案

### Object-c

### Swift

### 音视频

#### 1.iOS录制h265无法保存到相册
```c
    if (videoStream->codec->codec_id == AV_CODEC_ID_H265) {
        avformat_alloc_output_context2(&mOutFormatContext, NULL, "mov", [fileName UTF8String]);
        videoStream->codec->codec_tag = MKTAG('h', 'v', 'c', '1');
    }
```
参考：
1. [文件输出指定mov](https://www.zybuluo.com/ltlovezh/note/1725387)
2. [hev和hvc tag兼容测试](https://www.zybuluo.com/ltlovezh/note/1725387)

