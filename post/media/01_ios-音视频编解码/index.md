# iOS 音视频编解码


原文地址 [blog.csdn.net](https://blog.csdn.net/sinat_27741463/article/details/102463158)

编解码协议 H264（视频）和 AAC（音频）有软编解码和硬编解码。

网络传输都是用的大端序（高地址低字节），H264 网络传输的 startcode 是数据的 length，不是 0x00000001。NALU 有两种格式：Annex B 和 AVCC。Annex B 格式 startcode 以 0x 00 00 01 或 0x 00 00 00 01 开头， AVCC 格式以 NALU 的长度开头。

**AAC 也有两种传输格式：ADTS 和 ADIF**

*   ADIF：Audio Data Interchange Format 音频数据交换格式。这种格式的特征是可以确定的找到这个音频数据的开始，不需进行在音频数据流中间开始的解码，即它的解码必须在明确定义的开始处进行。故这种格式常用在磁盘文件中。
*   ADTS：Audio Data Transport Stream 音频数据传输流。这种格式的特征是它是一个有同步字的比特流，解码可以在这个流中任何位置开始。它的特征类似于 mp3 数据流格式。

**软编码：使用 CPU 进行编码。编码框架 ffmpeg+x264。**

*   https://www.jianshu.com/p/e631b041e96d
*   https://www.jianshu.com/p/3de01105d735

**硬编码：不使用 CPU 进行编码，使用显卡（GPU）进行硬件加速。** 专用的 DSP、FPGA、ASIC 芯片等硬件进行编码。ios 上硬编码框架 Video ToolBox 和 AudioToolbox。

*   Intel 硬编码使用 Intel 处理器内部集成的显卡进行硬件加速，qsv 加速方法便对应着 Intel 硬编码。Intel 硬编码对 H.264 加速效果明显，且不需要安装额外库（仅使用相应的 ffmpeg 命令）。
*   NVIDIA 硬编码使用英伟达的显卡对视频编码进行加速。CUDA 加速方法对应着 NVIDIA 硬编码。使用英伟达硬编码之前需要安装 CUDA 与英伟达的必要驱动。安装好两个环境后就可以使用 NVIDIA 的硬编码了。英伟达关于视频的编解码提供了两个相关的 SDK：NVENC（硬编码）和 NVCUVID（硬解码），前者负责硬件编码，二后者负责硬件解码。CUDA 支持 Windows、Linux、MacOS 三种主流操作系统。https://blog.csdn.net/qq_29350001/article/details/75144665（CUDA 详解）
*   FFmpeg 中也支持了硬编码，集成了显示视频处理模块。在命令行中使用 ffmpeg -hwaccels 可以查看 ffmpeg 支持的硬件加速方法。  
    FFMPEG 目前存在一个编码器 nvenc 是对于 NVIDIA 的 NVENC 的封装，通过使用它可以和 FFMPEG 无缝的整合起来。不过 FFMPEG 只存在 NVENC 的接口，不存在 NVCUVID（解码器） 的封装。如果需要实现相关的解码器可能需要自己实现 FFMPEG 接口。FFMPEG 实现了对于 Intel QSV 的封装。
*   DXVA 是微软定制的视频加速规范、在 Linux 平台上则是由 NVIDIA 提供的 VDPAU 和 Intel 提供的 VAAPI 加速规范。

在不同平台上可通过不同 API 使用 Intel GPU 的硬件加速能力。目前主要由两套 API：VAAPI 以及 libmfx。VAAPI (视频加速 API，Video Acceleration API) 包含一套开源的库 (LibVA) 以及 API 规范, 用于硬件加速下的视频编解码以及处理，只有 Linux 上的驱动提供支持。libmfx。Intel Media SDK 中的 API 规范，支持视频编解码以及媒体处理。支持 Windows 以及 Linux。除了 Intel 自己的 API，在 Windows 系统上还有其他 API 可使用 Intel GPU 的硬件加速能力，这些 API 属于 Windows 标准，由 Intel 显卡驱动实现。DXVA2 / D3D11VA。标准 Windows API，支持通过 Intel 显卡驱动进行视频编解码，FFmpeg 有对应实现。Media Foundation。标准 Windows API，支持通过 Intel 显卡驱动进行视频编解码，FFmpeg 不支持该 API。https://blog.jianchihu.net/intel-gpu-hw-video-codec-develop.html

**目前的主流 GPU 加速平台：**

```
INTEL、AMD、NVIDIA
```

**目前主流的 GPU 平台开发框架：**

*   CUDA：NVIDIA 的封闭编程框架，通过框架可以调用 GPU 计算资源
*   AMD APP：AMD 为自己的 GPU 提出的一套通用并行编程框架，标准开放，通过在 CPU、GPU 同时支持 OpenCL 框架，进行计算力融合。
*   OpenCL：开放计算语言，为异构平台编写程序的该框架，异构平台可包含 CPU、GPU 以及其他计算处理器，目标是使相同的运算能支持不同平台硬件加速。
*   Inel QuickSync：集成于 Intel 显卡中的专用视频编解码模块。

https://www.jianshu.com/p/8423724dffc1  
https://blog.csdn.net/haowei0926/article/details/56012139

[ios 中的硬编码文档](https://developer.apple.com/documentation/videotoolbox)  
ios 上硬编码框架 Video ToolBox 和 AudioToolbox。Video ToolBox 是一个底层框架，可以直接访问硬件编码器和解码器。 它提供视频压缩和解压缩服务，并在 CoreVideo 像素缓冲区中存储的光栅 raster 图像格式之间进行转换。 这些服务以会话对象（压缩，解压缩和像素传输）的形式提供，它们以 Core Foundation（CF）类型呈现。 不需要直接访问硬件编码器和解码器的应用程序 App 就不需要直接使用 VideoToolbox。iOS 8.0 及以上苹果开放了 VideoToolbox 框架来实现 H264 硬编码（H264 是一种编解码协议，有多种编解码器能编解码 H264，这里是利用硬件进行编解码，FFmpeg 中可以利用硬编解码和软编解码）。

*   CVPixelBufferRef/CVImageBufferRef：存放编码前和解码后的图像数据（未压缩的数据），这两个是相同的对象。
*   CMTime：时间戳相关，时间以 64-bit/32-bit 的形式出现
*   CMBlockBufferRef：编码后输出的数据（压缩后的数据）
*   CMFormatDescriptionRef/CMVideoFormatDescriptionRef：图像存储方式，编解码器等格式描述。这两个是相同的对象。
*   CMSampleBufferRef：存放编解码前后的视频图像的容器数据，iOS 中表示一帧音频 / 视频数据
*   CMSampleBuffer 可能是一个压缩的数据，也可能是一个未压缩的数据。取决于 CMSampleBuffer 里面是 CMBlockBuffer（压缩后） 还是 CVPixelBuffer（未压缩）。

**硬编码的步骤** ：从相机或读取视频文件输出的 CVPixelBuffer（也是以 CMSampleBufferRef 封装形式存在）—>Encoder—>CMSampleBufferRef（编码后得到的数据封装）—> 重新组装 NALUs。

1.  通过 VTCompressionSessionCreate 创建编码器
    
    ```
    VTCompressionSessionCreate(
    	    CM_NULLABLE CFAllocatorRef                          allocator,
    	    int32_t                                             width,
    	    int32_t                                             height,
    	    CMVideoCodecType                                    codecType,
    	    CM_NULLABLE CFDictionaryRef                         encoderSpecification,
    	    CM_NULLABLE CFDictionaryRef                         sourceImageBufferAttributes,
    	    CM_NULLABLE CFAllocatorRef                          compressedDataAllocator,
    	    CM_NULLABLE VTCompressionOutputCallback             outputCallback,
    	    void * CM_NULLABLE                                  outputCallbackRefCon,
    	    CM_RETURNS_RETAINED_PARAMETER CM_NULLABLE VTCompressionSessionRef * CM_NONNULL compressionSessionOut)
    	    
    	allocator：内存分配器，填NULL为默认分配器
    	width、height：视频帧像素的宽高，如果编码器不支持这个宽高的话可能会改变
    	codecType：编码类型，枚举
    	encoderSpecification：指定特定的编码器，填NULL的话由VideoToolBox自动选择
    	sourceImageBufferAttributes：源像素缓冲区的属性，如果这个参数有值的话，VideoToolBox会创建一个缓冲池，不需要缓冲池可以设置为NULL
    	compressedDataAllocator：压缩后数据的内存分配器，填NULL使用默认分配器
    	outputCallback：视频编码后输出数据回调函数
    	outputCallbackRefCon：回调函数中的自定义指针，我们通常传self，因为我们需要在C函数中调用self的方法，而C函数无法直接调self,
    	compressionSessionOut：编码器句柄，传入编码器的指针
    ```
    
2.  通过 VTSessionSetProperty 设置编码器属性，是否实时编码输出、是否产生 B 帧、设置关键帧、设置期望帧率、设置码率、最大码率值等等。
    
    ```
    VTSessionSetProperty(
      // 解码会话
      CM_NONNULL VTSessionRef       session,
      // 属性 KEY
      CM_NONNULL CFStringRef        propertyKey,
      // 设置的属性值
      CM_NULLABLE CFTypeRef         propertyValue )
    kVTCompressionPropertyKey_AverageBitRate：设置编码的平均码率，单位是bps，这不是一个硬性指标，设置的码率会上下浮动。VideoToolBox框架只支持ABR模式。H264有4种码率控制方法：
    
    CBR（Constant Bit Rate）是以恒定比特率方式进行编码，有Motion发生时，由于码率恒定，只能通过增大QP来减少码字大小，图像质量变差，当场景静止时，图像质量又变好，因此图像质量不稳定。这种算法优先考虑码率(带宽)。
    VBR（Variable Bit Rate）动态比特率，其码率可以随着图像的复杂程度的不同而变化，因此其编码效率比较高，Motion发生时，马赛克很少。码率控制算法根据图像内容确定使用的比特率，图像内容比较简单则分配较少的码率(似乎码字更合适)，图像内容复杂则分配较多的码字，这样既保证了质量，又兼顾带宽限制。这种算法优先考虑图像质量。
    *CVBR（Constrained VariableBit Rate）,这样翻译成中文就比较难听了，它是VBR的一种改进方法。但是Constrained又体现在什么地方呢？这种算法对应的Maximum bitRate恒定或者Average BitRate恒定。这种方法的兼顾了以上两种方法的优点：在图像内容静止时，节省带宽，有Motion发生时，利用前期节省的带宽来尽可能的提高图像质量，达到同时兼顾带宽和图像质量的目的。
    ABR (Average Bit Rate) 在一定的时间范围内达到设定的码率，但是局部码率峰值可以超过设定的码率，平均码率恒定。可以作为VBR和CBR的一种折中选择。
    kVTCompressionPropertyKey_ProfileLevel：设置H264编码的画质，H264有4种Profile：BP、EP、MP、HP
    
    BP(Baseline Profile)：基本画质。支持I/P 帧，只支持无交错（Progressive）和CAVLC；主要应用：可视电话，会议电视，和无线通讯等实时视频通讯领域
    EP(Extended profile)：进阶画质。支持I/P/B/SP/SI 帧，只支持无交错（Progressive）和CAVLC；
    MP(Main profile)：主流画质。提供I/P/B 帧，支持无交错（Progressive）和交错（Interlaced），也支持CAVLC 和CABAC 的支持；主要应用：数字广播电视和数字视频存储
    HP(High profile)：高级画质。在main Profile 的基础上增加了8×8内部预测、自定义量化、 无损视频编码和更多的YUV 格式；应用于广电和存储领域
    
    Level就多了，这里不一一列举，可参考h264 profile & level，iPhone上常用的方案如下：
    
    实时直播：
    低清Baseline Level 1.3
    标清Baseline Level 3
    半高清Baseline Level 3.1
    全高清Baseline Level 4.1
    存储媒体：
    低清 Main Level 1.3
    标清 Main Level 3
    半高清 Main Level 3.1
    全高清 Main Level 4.1
    高清存储：
    半高清 High Level 3.1
    全高清 High Level 4.1
    kVTCompressionPropertyKey_RealTime：设置是否实时编码输出
    kVTCompressionPropertyKey_AllowFrameReordering：配置是否产生B帧，High profile 支持 B 帧
    kVTCompressionPropertyKey_MaxKeyFrameInterval、kVTCompressionPropertyKey_MaxKeyFrameIntervalDuration：配置I帧间隔
    ```
    
3.  调用 VTCompressionSessionPrepareToEncodeFrames 准备编码
    
    ```
    VTCompressionSessionPrepareToEncodeFrames( CM_NONNULL VTCompressionSessionRef session )
    
    session：编码器句柄，传入编码器的指针
    ```
    
4.  输入采集到的视频数据 CVImageBufferRef /CVPixelBufferRef，调用 VTCompressionSessionEncodeFrame 进行编码
    
    ```
    VTCompressionSessionEncodeFrame(
        CM_NONNULL VTCompressionSessionRef  session,
        CM_NONNULL CVImageBufferRef         imageBuffer,
        CMTime                              presentationTimeStamp,
        CMTime                              duration, // may be kCMTimeInvalid
        CM_NULLABLE CFDictionaryRef         frameProperties,
        void * CM_NULLABLE                  sourceFrameRefCon,
        VTEncodeInfoFlags * CM_NULLABLE     infoFlagsOut )
    session：创建编码器时的句柄
    imageBuffer：YUV数据，iOS通过摄像头采集出来的视频流数据类型是CMSampleBufferRef，我们要从里面拿到CVImageBufferRef来进行编码。通过CMSampleBufferGetImageBuffer方法可以从sampleBuffer中获得imageBuffer。
    presentationTimeStamp：这一帧的时间戳，单位是毫秒
    duration：这一帧的持续时间，如果没有持续时间，填kCMTimeInvalid
    frameProperties：指定这一帧的属性，这里我们可以用来指定产生I帧
    encodeParams：自定义指针
    infoFlagsOut：用于接收编码操作的信息，不需要就置为NULL
    ```
    
5.  获取到编码后的数据并进行处理并组装 NALU，添加起始码 "\x00\x00\x00\x01"，如果这一帧是个关键帧，需要添加 sps pps** 等。将硬编码成功的 CMSampleBuffer 转换成 H264 码流，解析出参数集 SPS & PPS，加上开始码组装成 NALU。提取出视频数据，将长度码转换为开始码，组成 NALU，将 NALU 写入到文件中。NALU 只要有两种格式：Annex B 和 AVCC。Annex B 格式以 0x 00 00 01 或 0x 00 00 00 01 开头， AVCC 格式以所在 NALU 的长度开头。  
    编码后的数据通过步骤一 VTCompressionSessionCreate 方法中参数的回调函数 encodeOutputDataCallback 返回。编码后的数据以及这一帧的基本信息都在 CMSampleBufferRef 中。
    
    ```
    void encodeOutputDataCallback(void * CM_NULLABLE outputCallbackRefCon, void * CM_NULLABLE sourceFrameRefCon, OSStatus status, VTEncodeInfoFlags infoFlags, CM_NULLABLE CMSampleBufferRef sampleBuffer){ }。
    ```
    
6.  调用 VTCompressionSessionCompleteFrames 停止编码器
    
    ```
    VT_EXPORT OSStatus
    VTCompressionSessionCompleteFrames(
    	CM_NONNULL VTCompressionSessionRef	session,//编码器句柄
    	CMTime								completeUntilPresentationTimeStamp//kCMTimeInvalid等
    )
    ```
    
7.  调用 VTCompressionSessionInvalidate 销毁编码器
    
    ```
    VTCompressionSessionInvalidate(编码器句柄compressionSessionRef);
    CFRelease(编码器句柄compressionSessionRef);
    _compressionSessionRef = NULL;
    ```
    

_代码示范：_

```
#import <VideoToolbox/VideoToolbox.h>
@interface Nextvc ()
{
    NSInteger frameID;
    VTCompressionSessionRef cEncodeingSession;//编码器上下文
     dispatch_queue_t cEncodeQueue;
}
 
 
 
//videoToolbox硬编码
-(void)videoToolboxHardEncode{
    
           frameID = 0;
           int width = 480,height = 640;
           
           //创建编码session
           OSStatus status = VTCompressionSessionCreate(NULL, width, height, kCMVideoCodecType_H264, NULL, NULL, NULL, didCompressH264, (__bridge void *)(self), &cEncodeingSession);
           NSLog(@"H264:VTCompressionSessionCreate:%d",(int)status);
           
           if (status != 0) {
               NSLog(@"H264:Unable to create a H264 session");
               return ;
           }
        /**
            设置编码器属性
         */
           //设置实时编码输出（避免延迟）
           VTSessionSetProperty(cEncodeingSession, kVTCompressionPropertyKey_RealTime, kCFBooleanTrue);
           VTSessionSetProperty(cEncodeingSession, kVTCompressionPropertyKey_ProfileLevel,kVTProfileLevel_H264_Baseline_AutoLevel);
           
           //是否产生B帧(因为B帧在解码时并不是必要的,是可以抛弃B帧的)
           VTSessionSetProperty(cEncodeingSession, kVTCompressionPropertyKey_AllowFrameReordering, kCFBooleanFalse);
           
           //设置关键帧（GOPsize）间隔，GOP太小的话图像会模糊
           int frameInterval = 10;
           CFNumberRef frameIntervalRaf = CFNumberCreate(kCFAllocatorDefault, kCFNumberIntType, &frameInterval);
           VTSessionSetProperty(cEncodeingSession, kVTCompressionPropertyKey_MaxKeyFrameInterval, frameIntervalRaf);
           
           //设置期望帧率，不是实际帧率
           int fps = 10;
           CFNumberRef fpsRef = CFNumberCreate(kCFAllocatorDefault, kCFNumberIntType, &fps);
           VTSessionSetProperty(cEncodeingSession, kVTCompressionPropertyKey_ExpectedFrameRate, fpsRef);
           
           //码率的理解：码率大了话就会非常清晰，但同时文件也会比较大。码率小的话，图像有时会模糊，但也勉强能看
           //码率计算公式，参考印象笔记
           //设置码率、上限、单位是bps
           int bitRate = width * height * 3 * 4 * 8;
           CFNumberRef bitRateRef = CFNumberCreate(kCFAllocatorDefault, kCFNumberSInt32Type, &bitRate);
           VTSessionSetProperty(cEncodeingSession, kVTCompressionPropertyKey_AverageBitRate, bitRateRef);
           
           //设置码率，均值，单位是byte
           int bigRateLimit = width * height * 3 * 4;
           CFNumberRef bitRateLimitRef = CFNumberCreate(kCFAllocatorDefault, kCFNumberSInt32Type, &bigRateLimit);
           VTSessionSetProperty(cEncodeingSession, kVTCompressionPropertyKey_DataRateLimits, bitRateLimitRef);
           
           //准备编码
           VTCompressionSessionPrepareToEncodeFrames(cEncodeingSession);
    
}
 
/**
 输入待编码数据CMSampleBufferRef，开始编码
 @param sampleBuffer 待编码数据，可以是从摄像头获取的数据，也可以是从视频文件中获取的数据
 @param forceKeyFrame 是否强制I帧
 @return 结果
 */
- (BOOL)videoEncodeInputData:(CMSampleBufferRef)sampleBuffer forceKeyFrame:(BOOL)forceKeyFrame
{
    if (NULL == cEncodeingSession)
    {
        return NO;
    }
    
    if (nil == sampleBuffer)
    {
        return NO;
    }
    CMTime presentationTimeStamp = CMTimeMake(frameID++, 1000); // CMTimeMake(分子，分母)；分子/分母 = 时间(秒)
 
    CVImageBufferRef pixelBuffer = (CVImageBufferRef)CMSampleBufferGetImageBuffer(sampleBuffer);
    NSDictionary *frameProperties = @{(__bridge NSString *)kVTEncodeFrameOptionKey_ForceKeyFrame: @(forceKeyFrame)};
    
    OSStatus status = VTCompressionSessionEncodeFrame(cEncodeingSession, pixelBuffer, kCMTimeInvalid, kCMTimeInvalid, (__bridge CFDictionaryRef)frameProperties, NULL, NULL);//第三个参数可以换成presentationTimeStamp
    if (noErr != status)
    {
        NSLog(@"VEVideoEncoder::VTCompressionSessionEncodeFrame failed! status:%d", (int)status);
        return NO;
    }
    return YES;
}
 
//VideoToolBox硬编码回调
void didCompressH264(void *outputCallbackRefCon, void *sourceFrameRefCon, OSStatus status, VTEncodeInfoFlags infoFlags, CMSampleBufferRef sampleBuffer)
{
    if (noErr != status || nil == sampleBuffer)
    {
        NSLog(@"VEVideoEncoder::encodeOutputCallback Error : %d!", (int)status);
        return;
    }
    
    if (nil == outputCallbackRefCon)
    {
        return;
    }
    
    if (!CMSampleBufferDataIsReady(sampleBuffer))
    {
        return;
    }
    
    if (infoFlags & kVTEncodeInfo_FrameDropped)
    {
        NSLog(@"VEVideoEncoder::H264 encode dropped frame.");
        return;
    }
    
    Nextvc *encoder = (__bridge Nextvc *)outputCallbackRefCon;
    const char header[] = "\x00\x00\x00\x01";
    size_t headerLen = (sizeof header) - 1; // 最后一位是\0结束符，要减掉
    NSData *headerData = [NSData dataWithBytes:header length:headerLen];
    
//    // 判断是否是关键帧
//    bool isKeyFrame = !CFDictionaryContainsKey((CFDictionaryRef)CFArrayGetValueAtIndex(CMSampleBufferGetSampleAttachmentsArray(sampleBuffer, true), 0), (const void *)kCMSampleAttachmentKey_NotSync);
    //判断当前帧是否为关键帧
        CFArrayRef array = CMSampleBufferGetSampleAttachmentsArray(sampleBuffer, true);
        CFDictionaryRef dic = CFArrayGetValueAtIndex(array, 0);
        bool isKeyFrame = !CFDictionaryContainsKey(dic, kCMSampleAttachmentKey_NotSync);
 
    
    if (isKeyFrame)
    {
        NSLog(@"VEVideoEncoder::编码了一个关键帧");
        //图像的存储方式，编解码器等格式描述
        CMFormatDescriptionRef formatDescriptionRef = CMSampleBufferGetFormatDescription(sampleBuffer);
        
        /*关键帧需要加上SPS、PPS信息
        获取sps & pps 数据 只获取1次，保存在h264文件开头的第一帧中
        sps(sample per second 采样次数/s),是衡量模数转换（ADC）时采样速率的单位
         CMVideoFormatDescriptionGetH264ParameterSetAtIndex获取sps和pps信息，并转换为二进制写入文件或者进行上传
         */
        size_t sParameterSetSize;//参数集合占的字节大小
        size_t sParameterSetCount;//参数集合元素个数
        const uint8_t *sParameterSet;//参数集合
        OSStatus spsStatus = CMVideoFormatDescriptionGetH264ParameterSetAtIndex(formatDescriptionRef, 0, &sParameterSet, &sParameterSetSize, &sParameterSetCount, 0);//index为0的位置是sps；
        
        size_t pParameterSetSize, pParameterSetCount;
        const uint8_t *pParameterSet;
        OSStatus ppsStatus = CMVideoFormatDescriptionGetH264ParameterSetAtIndex(formatDescriptionRef, 1, &pParameterSet, &pParameterSetSize, &pParameterSetCount, 0);//index为1的位置是pps；
        
        if (noErr == spsStatus && noErr == ppsStatus)
        {
            //把sps和pps参数集合转换成二进制数据，组装成sps帧和pps帧；
            NSData *sps = [NSData dataWithBytes:sParameterSet length:sParameterSetSize];
            NSData *pps = [NSData dataWithBytes:pParameterSet length:pParameterSetSize];
            NSMutableData *spsData = [NSMutableData data];
            [spsData appendData:headerData];
            [spsData appendData:sps];
           
            NSMutableData *ppsData = [NSMutableData data];
            [ppsData appendData:headerData];
            [ppsData appendData:pps];
            
           
        }
    }
    
    //获取编码后的h264流数据
    CMBlockBufferRef blockBuffer = CMSampleBufferGetDataBuffer(sampleBuffer);
    size_t length;//单个NALU长度
    size_t totalLength;//所有NALU总长度
    char *dataPointer;//指针偏移
   // 通过 首地址blockBuffer 、单个长度length、 总长度totalLength通过dataPointer指针偏移做遍历
    status = CMBlockBufferGetDataPointer(blockBuffer, 0, &length, &totalLength, &dataPointer);
    if (noErr != status)
    {
        NSLog(@"VEVideoEncoder::CMBlockBufferGetDataPointer Error : %d!", (int)status);
        return;
    }
    
    size_t bufferOffset = 0;//Nalu的开始位置，每次增加加一个stratcode+nalu的长度
    static const int avcHeaderLength = 4;//返回的nalu数据前4个字节不是0x00000001的startcode,而是大端模式的帧长度length,读取数据时有个大小端模式：网络传输一般都是大端模式
    while (bufferOffset < totalLength - avcHeaderLength)
    {
        // 读取  一单元长度的nalu数据
        uint32_t nalUnitLength = 0;
        memcpy(&nalUnitLength, dataPointer + bufferOffset, avcHeaderLength);//目标地址，源地址，字节数，（从源地址拷贝n个字节到目标地址），这里其实是设置每个nalUnitLength的值，即各个Nalu的长度
        
        // 大端转小端(系统端是小端序)
        nalUnitLength = CFSwapInt32BigToHost(nalUnitLength);
        //获取nalu数据
        NSData *frameData = [[NSData alloc] initWithBytes:(dataPointer + bufferOffset + avcHeaderLength) length:nalUnitLength];
        //Nalu头+NALU数据
        NSMutableData *outputFrameData = [NSMutableData data];
        [outputFrameData appendData:headerData];
        [outputFrameData appendData:frameData];
        //可以把outputFrameData写入文件，然后就得到了H264编码的文件。
        
        //读取下一个nalu 一次回调可能包含多个nalu数据，
        bufferOffset += avcHeaderLength + nalUnitLength;
        
        
    }
    
}
```

**audioToolBox 硬编码**

_编码步骤：_

1.  配置编码参数、获取编码器描述 description、获取编码器
2.  设置缓冲列表 AudioBufferList
3.  开始编码，将数据写入编码器 AudioConverterFillComplexBuffer，
4.  在回调函数中，将数据写入缓冲区
5.  编码完成后，获取缓冲区列表数据 outAudioBUfferList，添加 ADTS 头
6.  将数据写入文件

_代码示例：_

```
{  AudioConverterRef _audioConverter;//音频编码上下文
    size_t _pcmBufferSize;
    char *_pcmBuffer;
    size_t  _aacBufferSize;
    char *_aacBuffer;
}
 
@property(nonatomic,strong)NSFileHandle *audioFileHandle;
@property(nonatomic,strong)dispatch_queue_t encoderQueue;)
 
 
 
//创建存储音频的文件，先移除以前的文件，再重新创建
- (NSFileHandle *)audioFileHandle {
    if (!_audioFileHandle) {
        NSString *documentPath = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject;
        NSString * filePath = [documentPath stringByAppendingPathComponent:@"demo01.aac"];
        [[NSFileManager defaultManager] removeItemAtPath:filePath error:nil];
        BOOL createFile = [[NSFileManager defaultManager] createFileAtPath:filePath contents:nil attributes:nil];
        NSAssert(createFile, @"create audio path error");
        _audioFileHandle = [NSFileHandle fileHandleForWritingAtPath:filePath];
    }
    return _audioFileHandle;
}
 
 
 
- (id)init {
    if (self = [super init]) {
        _encoderQueue = dispatch_queue_create("aac encode queue", DISPATCH_QUEUE_SERIAL);
        _audioConverter = NULL;
        _pcmBufferSize = 0;
        _pcmBuffer = NULL;
        _aacBufferSize = 1024;
        _aacBuffer = malloc(_aacBufferSize * sizeof(uint8_t));
        memset(_aacBuffer, 0, _aacBufferSize);
    }
    return self;
}
 
//停止编码
- (void)stopEncodeAudio {
    [self.audioFileHandle closeFile];
    self.audioFileHandle = NULL;
}
 
// 配置编码参数
- (void)setupEncoderFromSampleBuffer:(CMSampleBufferRef)sampleBuffer {
    
    NSLog(@"开始配置编码参数。。。。");
    /*
     AudioStreamBasicDescription是输入输出流的结构体描述，
     */
    // 获取原音频声音格式设置
    AudioStreamBasicDescription inAudioStreamBasicDescription = *CMAudioFormatDescriptionGetStreamBasicDescription((CMAudioFormatDescriptionRef)CMSampleBufferGetFormatDescription(sampleBuffer));
    AudioStreamBasicDescription outAudioStreamBasicDescription = {0};
    
   /*
    设置输出格式参数
    */
    // 采样率，音频流，在正常播放情况下的帧率。如果是压缩的格式，这个属性表示解压缩后的帧率。帧率不能为0。
    outAudioStreamBasicDescription.mSampleRate = inAudioStreamBasicDescription.mSampleRate;
    // 格式  kAudioFormatMPEG4AAC  = 'aac' ,
    outAudioStreamBasicDescription.mFormatID = kAudioFormatMPEG4AAC;
    // 标签格式 无损编码 ， 无损编码 ，0表示没有
    outAudioStreamBasicDescription.mFormatFlags = kMPEG4Object_AAC_LC;
    // 每一个packet的音频数据大小。如果的动态大小，设置为0。动态大小的格式，需要用AudioStreamPacketDescription 来确定每个packet的大小。
    outAudioStreamBasicDescription.mBytesPerPacket = 0;
    //  每个packet的帧数。如果是未压缩的音频数据，值是1。动态帧率格式，这个值是一个较大的固定数字，比如说AAC的1024。如果是动态大小帧数（比如Ogg格式）设置为0。
    outAudioStreamBasicDescription.mFramesPerPacket = 1024;
    // 每帧的大小。每一帧的起始点到下一帧的起始点。如果是压缩格式，设置为0 。
    outAudioStreamBasicDescription.mBytesPerFrame = 0;
    // 声道数：1 单声道 2 立体声
    outAudioStreamBasicDescription.mChannelsPerFrame = 1;
    // 每采样点占用位数
    outAudioStreamBasicDescription.mBitsPerChannel = 0;
    // 保留参数（对齐当时）8字节对齐，填0.
    outAudioStreamBasicDescription.mReserved = 0;
    
    // 获取编码器描述
    AudioClassDescription * description = [self getAudioClassDescriptionWithType:kAudioFormatMPEG4AAC fromManufacturer:kAppleSoftwareAudioCodecManufacturer];
    
    // 创建编码器
    /*
     inAudioStreamBasicDescription 传入源音频格式
     outAudioStreamBasicDescription 目标音频格式
     第三个参数：传入音频编码器的个数
     description 传入音频编码器的描述
     */
    OSStatus status = AudioConverterNewSpecific(&inAudioStreamBasicDescription, &outAudioStreamBasicDescription, 1, description, &_audioConverter);
    if (status != 0) {
        NSLog(@"创建编码器失败");
    }
    
}
 
 
// 获取编码器描述
/*type   编码格式
 manufacturer  软/硬编 kAppleHardwareAudioCodecManufacturer、kAppleSoftwareAudioCodecManufacturer
 */
- (AudioClassDescription *)getAudioClassDescriptionWithType:(UInt32)type
                                           fromManufacturer:(UInt32)manufacturer
{
    NSLog(@"开始获取编码器。。。。");
    // 选择aac编码
    /*AudioClassDescription结构体包含以下成员
     OSType  mType;
     OSType  mSubType;
     OSType  mManufacturer;
     */
    static AudioClassDescription desc;
    UInt32 encoderS = type;
    OSStatus status;
    UInt32 size;
    /*获取所用有的编码器属性信息
     kAudioFormatProperty_Encoders 编码ID
     编码说明大小
     编码类型
     属性当前值的大小
     */
    status = AudioFormatGetPropertyInfo(kAudioFormatProperty_Encoders, sizeof(encoderS), &encoderS, &size);
    if (status) {
        NSLog(@"编码aac错误");
        return nil;
    }
    
    // 计算编码器的个数
    unsigned int count = size / sizeof(AudioClassDescription);
    
    // 定义编码器数组
    AudioClassDescription description[count];
    //分配编码器属性信息到数组
    status = AudioFormatGetProperty(kAudioFormatProperty_Encoders, sizeof(encoderS), &encoderS, &size, description);
    
    for (unsigned int i = 0; i < count; i++) {
        if (type == description[i].mSubType && manufacturer == description[i].mManufacturer) {
            // 拷贝编码器到desc
            memcpy(&desc, &description[i], sizeof(desc));
            NSLog(@"找到aac编码器");
            return &desc;
        }
    }
    
    return nil;
}
 
 
 
 
// 编码数据
- (void)encodeAudioSampleBuffer:(CMSampleBufferRef)sampleBuffer {
    
    CFRetain(sampleBuffer);
    dispatch_sync(_encoderQueue, ^{
        if (!_audioConverter) {
            // 配置编码参数
            [self setupEncoderFromSampleBuffer:sampleBuffer];
        }
        
        // 获取CMBlockBufferRef
        CMBlockBufferRef blockBuffer = CMSampleBufferGetDataBuffer(sampleBuffer);
        CFRetain(blockBuffer);
        
        // 获取_pcmBufferSize 和 _pcmBuffer
        OSStatus status = CMBlockBufferGetDataPointer(blockBuffer, 0, NULL, &self->_pcmBufferSize, &self->_pcmBuffer);
        if (status != kCMBlockBufferNoErr) {
            NSLog(@"获取 pcmBuffer 数据错误");
            return ;
        }
        
        // 清空
        memset(self->_aacBuffer, 0, self->_aacBufferSize);
        
        // 初始化缓冲列表
        AudioBufferList outAudioBufferList = {0}; // 结构体
        // 缓冲区个数
        outAudioBufferList.mNumberBuffers = 1;
        // 渠道个数
        outAudioBufferList.mBuffers[0].mNumberChannels = 1;
        // 缓存区大小
        outAudioBufferList.mBuffers[0].mDataByteSize = (int)self->_aacBufferSize;
        // 缓冲区内容
        outAudioBufferList.mBuffers[0].mData = self->_aacBuffer;
        
        // 编码
        AudioStreamPacketDescription * outPD = NULL;
        UInt32 inPutSize = 1;
        /*
         _audioConverter 音频编码上下文
         inInputDataProc 自己实现的编码数据的callback引用
         self 获取的数据
         inPutSize 输出数据的长度
         outAudioBUfferList 输出的缓冲区列表数据
         outPD  输出数据的描述
         */
        status = AudioConverterFillComplexBuffer(self->_audioConverter,
                                                 inInputDataProc,
                                                 (__bridge void*)self,
                                                 &inPutSize,
                                                 &outAudioBufferList,
                                                 outPD
                                                 );
        
        // 编码后完成,AudioConverterFillComplexBuffer方法返回的是AAC原始码流，需要在AAC每帧添加ADTS头
        NSData * data = nil;
        if (status == noErr) {
            // 获取缓冲区的原始数据acc数据
            NSData * rawAAC = [NSData dataWithBytes:outAudioBufferList.mBuffers[0].mData length:outAudioBufferList.mBuffers[0].mDataByteSize];
            
            // 加头ADTS
            NSData * adtsHeader = [self adtsDataForPacketLength:rawAAC.length];
            NSMutableData * fullData = [NSMutableData dataWithData:adtsHeader];
            [fullData appendData:rawAAC];
            data = fullData;
        } else {
            NSLog(@"数据错误");
            return;
        }
        
     
        // 写入数据
        [self.audioFileHandle writeData:data];
        
        CFRelease(sampleBuffer);
        CFRelease(blockBuffer);
    });
}
 
 
// audioToolBox回调函数，将数据写入缓冲区
OSStatus inInputDataProc(AudioConverterRef inAudioConverter, UInt32 *ioNumberDataPackets, AudioBufferList *ioData, AudioStreamPacketDescription **outDataPacketDescription, void *inUserData)
{
    // 编码器
    Nextvc *encoder = (__bridge Nextvc *) inUserData;
    
    // 编码包的数据
    UInt32 requestPackes = *ioNumberDataPackets;
    // 将ioData填充到缓冲区
    size_t cp = [encoder copyPCMSamplesIntoBuffer:ioData];
    if (cp < requestPackes) {
        //PCM 缓冲区还没满
        *ioNumberDataPackets = 0; // 清空
        return -1;
    }
    
    *ioNumberDataPackets = 1;
    return noErr;
}
 
 
// pcm -> 缓冲区
- (size_t)copyPCMSamplesIntoBuffer:(AudioBufferList*)ioData {
    // 获取pcm大小
    size_t os = _pcmBufferSize;
    if (!_pcmBufferSize) {
        return 0;
    }
    
    ioData->mBuffers[0].mData = _pcmBuffer;
    ioData->mBuffers[0].mDataByteSize = (int)_pcmBufferSize;
    // 清空
    _pcmBuffer = NULL;
    _pcmBufferSize = 0;
    return os;
}
 
/**
 *  Add ADTS header at the beginning of each and every AAC packet.
 *  This is needed as MediaCodec encoder generates a packet of raw
 *  AAC data.
 *
 *  Note the packetLen must count in the ADTS header itself.
 注意：packetLen 必须在ADTS头身计算
 
 **/
- (NSData*)adtsDataForPacketLength:(NSUInteger)packetLength {
    int adtsLength = 7;
    char *packet = malloc(sizeof(char) * adtsLength);
    
    int profile = 2;
    int freqIdx = 4;
    int chanCfg = 1;
    NSUInteger fullLength = adtsLength + packetLength;
    packet[0] = (char)0xFF;
    packet[1] = (char)0xF9;
    packet[2] = (char)(((profile-1)<<6) + (freqIdx<<2) +(chanCfg>>2));
    packet[3] = (char)(((chanCfg&3)<<6) + (fullLength>>11));
    packet[4] = (char)((fullLength&0x7FF) >> 3);
    packet[5] = (char)(((fullLength&7)<<5) + 0x1F);
    packet[6] = (char)0xFC;
    
    NSData *data = [NSData dataWithBytesNoCopy:packet length:adtsLength freeWhenDone:YES];
    return data;
}
```

**FFmpeg 中的硬编码：**

*   FFmpeg 中的硬编码有 videotoolbox（苹果的 ios 和 MACos）、mediacodec（安卓的）、qsv（Intel 的 LIBMFX api）、DXVA（是微软定制的视频加速规范，如 DXVA2 / D3D11VA）、VDPAU（在 Linux 平台上由 NVIDIA 指定的加速规范）、VAAPI（在 Linux 平台上由 Intel 提供的加速规范）

**Intel 支持的硬编码：**

*   windows：libmfx（Intel 自己的 api，FFmpeg 中 qsv 技术对外接口就是 LIBMF）、DXVA2 / D3D11VA（微软出的对 Intel 支持的 api，FFmpeg 中有封装）、Media Foundation（微软出的对 Intel 支持的 api）
*   linux：VAAPI（Intel 自己的 api，FFmpeg 中有封装）、libmfx（Intel 自己的 api，FFmpeg 中 qsv 技术对外接口就是 LIBMF）

**NVIDIA 支持的硬编码：**

*   windows：CUDA（NVIDIA 自己的 api，FFmpeg 中封装包含 NVENC（硬编码）和 NVCUVID（硬解码））
*   linux：CUDA（NVIDIA 自己的 api，FFmpeg 中封装包含 NVENC（硬编码）和 NVCUVID（硬解码））、VDPAU（FFmpeg 中有封装）

硬解码：

问题和优化方案：https://www.jianshu.com/p/57581485717b

硬编解码图示：https://www.cnblogs.com/edisongz/p/7062098.html

**videoToolBox 硬解码**

```
NALU + SPS,PPS—>CMBlockBuffer—>CMSampleBufferRef，再将CMSampleBufferRef包装的帧数据输入到 VTDecompressionSessionDecodeFrame，通过回调中CVImageBufferRef 直接上传OpenGL ES 显示。

  序列参数集SPS（sequence Parameter Set）：作用于一系列连续的编码图像

  图像参数集PPS(Picture Parameter Set)：作用于编码视频序列中一个或多个独立的图像；
```

**硬解码流程：**  
1、解析 H264 数据

解码前的 CMSampleBuffer = CMTime + FormatDesc + CMBlockBuffer。需要从 H264 的码流里面提取出以上的三个信息。最后组合成 CMSampleBuffer，提供给硬解码接口来进行解码工作。

NALU 单元包含视频图像数据和 H264 的参数信息。其中视频图像数据就是 CMBlockBuffer，而 H264 的参数信息则可以组合成 FormatDesc。

2、初始化解码器（VTDecompressionSessionCreate）  
3、将解析后的 H264 数据送入解码器（VTDecompressionSessionDecodeFrame）  
4、解码器回调输出解码后的数据（CVImageBufferRef）

_代码示例：_

```
/** sps数据 */
@property (nonatomic, assign) uint8_t *sps;
/** sps数据长度 */
@property (nonatomic, assign) NSInteger spsSize;
/** pps数据 */
@property (nonatomic, assign) uint8_t *pps;
/** pps数据长度 */
@property (nonatomic, assign) NSInteger ppsSize;
/** 解码器句柄 */
@property (nonatomic, assign) VTDecompressionSessionRef deocderSession;
/** 视频解码信息句柄 */
@property (nonatomic, assign) CMVideoFormatDescriptionRef decoderFormatDescription;
 
 
/*
*读取本地视频文件
*/
 
 
/**
 解码NALU数据
 @param naluData NALU数据
 */
-(void)decodeNaluData:(NSData *)naluData
{
    uint8_t *frame = (uint8_t *)naluData.bytes;
    uint32_t frameSize = (uint32_t)naluData.length;
    // frame的前4位是NALU数据的开始码，也就是00 00 00 01，第5个字节是表示数据类型，转为10进制后，7是sps,8是pps,5是IDR（I帧）信息
    int nalu_type = (frame[4] & 0x1F);
    
    /* 将NALU的开始码替换成NALU的长度信息
    方法一：
     */
//    uint32_t nalSize = (uint32_t)(frameSize - 4);
//    uint8_t *pNalSize = (uint8_t*)(&nalSize);
//    frame[0] = *(pNalSize + 3);
//    frame[1] = *(pNalSize + 2);
//    frame[2] = *(pNalSize + 1);
//    frame[3] = *(pNalSize);
    
    //方法二：
    uint32_t nalSize = (uint32_t)(frameSize - 4);
    uint32_t *pNalSize = (uint32_t *)frame;
    *pNalSize = CFSwapInt32HostToBig(nalSize);
    
    switch (nalu_type)
    {
        case 0x05: // I帧
            NSLog(@"NALU type is IDR frame");
            if([self initH264Decoder])
            {
                [self decode:frame withSize:frameSize];
            }
            
            break;
        case 0x07: // SPS
            NSLog(@"NALU type is SPS frame");
            _spsSize = frameSize - 4;
            _sps = malloc(_spsSize);
            memcpy(_sps, &frame[4], _spsSize);
            
            break;
        case 0x08: // PPS
            NSLog(@"NALU type is PPS frame");
            _ppsSize = frameSize - 4;
            _pps = malloc(_ppsSize);
            memcpy(_pps, &frame[4], _ppsSize);
            break;
            
        default: // B帧或P帧
            NSLog(@"NALU type is B/P frame");
            if([self initH264Decoder])
            {
                [self decode:frame withSize:frameSize];
            }
            break;
    }
}
 
/**
 初始化解码器
 @return 结果
 */
-(BOOL)initH264Decoder
{
    if(_deocderSession)
    {
        return YES;
    }
    
    const uint8_t* const parameterSetPointers[2] = {_sps, _pps};
    const size_t parameterSetSizes[2] = {_spsSize, _ppsSize};
    // 根据sps pps创建解码视频参数
    OSStatus status = CMVideoFormatDescriptionCreateFromH264ParameterSets(kCFAllocatorDefault, 2, parameterSetPointers, parameterSetSizes, 4, &_decoderFormatDescription);
    if(status != noErr)
    {
        NSLog(@"H264Decoder::CMVideoFormatDescriptionCreateFromH264ParameterSets failed status = %d", (int)status);
    }
    
    // 从sps pps中获取解码视频的宽高信息
    CMVideoDimensions dimensions = CMVideoFormatDescriptionGetDimensions(_decoderFormatDescription);
    
    // kCVPixelBufferPixelFormatTypeKey 解码图像的采样格式
    // kCVPixelBufferWidthKey、kCVPixelBufferHeightKey 解码图像的宽高
    // kCVPixelBufferOpenGLCompatibilityKey制定支持OpenGL渲染，经测试有没有这个参数好像没什么差别
    NSDictionary* destinationPixelBufferAttributes = @{(id)kCVPixelBufferPixelFormatTypeKey : @(kCVPixelFormatType_420YpCbCr8BiPlanarVideoRange), (id)kCVPixelBufferWidthKey : @(dimensions.width), (id)kCVPixelBufferHeightKey : @(dimensions.height),
                                                       (id)kCVPixelBufferOpenGLCompatibilityKey : @(YES)};
    
    // 设置解码输出数据回调
    VTDecompressionOutputCallbackRecord callBackRecord;
    callBackRecord.decompressionOutputCallback = decodeOutputDataCallback;
    callBackRecord.decompressionOutputRefCon = (__bridge void *)self;
    // 创建解码器
    status = VTDecompressionSessionCreate(kCFAllocatorDefault, _decoderFormatDescription, NULL, (__bridge CFDictionaryRef)destinationPixelBufferAttributes, &callBackRecord, &_deocderSession);
    // 解码线程数量
    VTSessionSetProperty(_deocderSession, kVTDecompressionPropertyKey_ThreadCount, (__bridge CFTypeRef)@(1));
    // 是否实时解码
    VTSessionSetProperty(_deocderSession, kVTDecompressionPropertyKey_RealTime, kCFBooleanTrue);
    
    return YES;
}
/**
 解码数据
 @param frame 数据
 @param frameSize 数据长度
 */
-(void)decode:(uint8_t *)frame withSize:(uint32_t)frameSize
{
    CMBlockBufferRef blockBuffer = NULL;
    // 创建 CMBlockBufferRef
    OSStatus status  = CMBlockBufferCreateWithMemoryBlock(NULL, (void *)frame, frameSize, kCFAllocatorNull, NULL, 0, frameSize, FALSE, &blockBuffer);
    if(status != kCMBlockBufferNoErr)
    {
        return;
    }
    CMSampleBufferRef sampleBuffer = NULL;
    const size_t sampleSizeArray[] = {frameSize};
    // 创建 CMSampleBufferRef
    status = CMSampleBufferCreateReady(kCFAllocatorDefault, blockBuffer, _decoderFormatDescription , 1, 0, NULL, 1, sampleSizeArray, &sampleBuffer);
    if (status != kCMBlockBufferNoErr || sampleBuffer == NULL)
    {
        return;
    }
    // VTDecodeFrameFlags 0为允许多线程解码
    VTDecodeFrameFlags flags = 0;
    VTDecodeInfoFlags flagOut = 0;
    // 解码 这里第四个参数会传到解码的callback里的sourceFrameRefCon，可为空
    OSStatus decodeStatus = VTDecompressionSessionDecodeFrame(_deocderSession, sampleBuffer, flags, NULL, &flagOut);
    
    if(decodeStatus == kVTInvalidSessionErr)
    {
        NSLog(@"H264Decoder::Invalid session, reset decoder session");
    }
    else if(decodeStatus == kVTVideoDecoderBadDataErr)
    {
        NSLog(@"H264Decoder::decode failed status = %d(Bad data)", (int)decodeStatus);
    }
    else if(decodeStatus != noErr)
    {
        NSLog(@"H264Decoder::decode failed status = %d", (int)decodeStatus);
    }
    // Create了就得Release
    CFRelease(sampleBuffer);
    CFRelease(blockBuffer);
    return;
}
 
//解码回调函数
static void decodeOutputDataCallback(void *decompressionOutputRefCon, void *sourceFrameRefCon, OSStatus status, VTDecodeInfoFlags infoFlags, CVImageBufferRef pixelBuffer, CMTime presentationTimeStamp, CMTime presentationDuration)
{
    // retain再输出，外层去release；pixelBuffer就是解码后的数据
    CVPixelBufferRetain(pixelBuffer);
    Nextvc *decoder = (__bridge Nextvc *)decompressionOutputRefCon;
    
   
}
 

CMSampleBufferRef转换成YUV数据、YUV数据类型的变换：

/*
    1. CMSampleBufferRef 中提取yuv数据(Byte)
    2. 处理yuv数据
    3. yuv数据 转CVPixelBufferRef ，继续进行编码
 */
-(CVPixelBufferRef)processYUV422ToYUV420WithSampleBuffer:(CMSampleBufferRef)sampleBuffer
{
    // 1. 从CMSampleBufferRef中提取yuv数据
    // 获取yuv数据
    // 通过CMSampleBufferGetImageBuffer方法，获得CVImageBufferRef。
    // 这里面就包含了yuv420数据的指针
    CVImageBufferRef pixelBuffer_Before = CMSampleBufferGetImageBuffer(sampleBuffer);
    
    //表示开始操作数据
    CVPixelBufferLockBaseAddress(pixelBuffer_Before, 0);
    
    //图像宽度（像素）
    size_t pixelWidth = CVPixelBufferGetWidth(pixelBuffer_Before);
    //图像高度（像素）
    size_t pixelHeight = CVPixelBufferGetHeight(pixelBuffer_Before);
    //yuv中的y所占字节数
    size_t y_size = pixelWidth * pixelHeight;
    
    
    // 2. yuv中的u和v分别所占的字节数
    size_t uv_size = y_size / 4;
    
    uint8_t *yuv_frame = malloc(uv_size * 2 + y_size);
    
    //获取CVImageBufferRef中的y数据
    uint8_t *y_frame = CVPixelBufferGetBaseAddressOfPlane(pixelBuffer_Before, 0);
    memcpy(yuv_frame, y_frame, y_size);
    
    //获取CMVImageBufferRef中的uv数据
    uint8_t *uv_frame = CVPixelBufferGetBaseAddressOfPlane(pixelBuffer_Before, 1);
    memcpy(yuv_frame + y_size, uv_frame, uv_size * 2);
    
    CVPixelBufferUnlockBaseAddress(pixelBuffer_Before, 0);
    
    NSData *yuvData = [NSData dataWithBytesNoCopy:yuv_frame length:y_size + uv_size * 2];
    
    
    
    
    // 3. yuv 变成 转CVPixelBufferRef
 
    //现在要把NV12数据放入 CVPixelBufferRef中，因为 硬编码主要调用VTCompressionSessionEncodeFrame函数，此函数不接受yuv数据，但是接受CVPixelBufferRef类型。
    CVPixelBufferRef pixelBuf_After = NULL;
    //初始化pixelBuf，数据类型是kCVPixelFormatType_420YpCbCr8BiPlanarVideoRange，此类型数据格式同NV12格式相同。
    CVPixelBufferCreate(NULL,
                        pixelWidth, pixelHeight,
                        kCVPixelFormatType_420YpCbCr8BiPlanarVideoRange,
                        NULL,
                        &pixelBuf_After);
    
    // Lock address，锁定数据，应该是多线程防止重入操作。
    if(CVPixelBufferLockBaseAddress(pixelBuf_After, 0) != kCVReturnSuccess){
        NSLog(@"encode video lock base address failed");
        return NULL;
    }
    
    //将yuv数据填充到CVPixelBufferRef中
    uint8_t *yuv_frame_2 = (uint8_t *)yuvData.bytes;
    
    //处理y frame
    uint8_t *y_frame_2 = CVPixelBufferGetBaseAddressOfPlane(pixelBuf_After, 0);
    memcpy(y_frame_2, yuv_frame_2, y_size);
    
    uint8_t *uv_frame_2 = CVPixelBufferGetBaseAddressOfPlane(pixelBuf_After, 1);
    memcpy(uv_frame_2, yuv_frame_2 + y_size, uv_size * 2);
    
    
    CVPixelBufferUnlockBaseAddress(pixelBuf_After, 0);
    
    return pixelBuf_After;
}
```

**软硬编码对比：**

*   软编码：实现直接、简单，参数调整方便，升级易，但 CPU 负载重，性能较硬编码低，低码率下质量通常比硬编码要好一点。
*   硬编码：性能高，低码率下通常质量低于软编码器，但部分产品在 GPU 硬件平台移植了优秀的软编码算法（如 X264）的，质量基本等同于软编码。

苹果在 iOS 8.0 系统之前，没有开放系统的硬件编码解码功能，不过 Mac OS 系统一直有，被称为 Video ToolBox 的框架来处理硬件的编码和解码，终于在 iOS 8.0 后，苹果将该框架引入 iOS 系统。

**.H265 优点**

*   压缩比高，在相同图片质量情况下，比 JPEG 高两倍
*   能增加如图片的深度信息，透明通道等辅助图片。
*   支持存放多张图片，类似相册和集合。(实现多重曝光的效果)
*   支持多张图片实现 GIF 和 livePhoto 的动画效果。
*   无类似 JPEG 的最大像素限制
*   支持透明像素
*   分块加载机制
*   支持缩略图

**在 iOS 平台上做视频的解码，一般有三种方案：**

1.  软解码方案：ffmpeg  
    缺点：消耗 CPU 太大
2.  硬解码方案 1：采用私有接口 VideoToolBox  
    优点：CPU 消耗极低，解码效率极高  
    缺点：要使用私有接口 VideoToolBox
3.  硬解码方案 2：采用 AVPlayer＋httpserver＋HttpLiveStream 的组合方案  
    优点：CPU 消耗极低，解码效率极高  
    缺点：视频有延迟，不适合实时视频通讯
