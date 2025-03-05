# FFmpeg 常用命令

+ 录屏
ffmpeg -video_size 1280x720 -framerate 30 -f avfoundation -pixel_format uyvy422 -i "0:0" -f flv output.flv

+ 录屏->推流
ffmpeg -video_size 1280x720 -framerate 30 -f avfoundation -pixel_format uyvy422 -i "0:0" -ar 44100 -f flv rtmp://127.0.0.1:1935/live
