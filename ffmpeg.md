## FFmpeg

### 1. FFMPEG简介

FFmpeg的名称来自MPEG视频编码标准，前面的“FF”代表“Fast Forward”，FFmpeg是一套可以用来记录、转换数字音频、视频，并能将其转化为流的开源计算机程序。可以轻易地实现多种视频格式之间的相互转换。
FFmpeg的用户有Google，Facebook，Youtube，优酷，爱奇艺，土豆等

### 2. 基础知识

- Conainer/File：即特定格式的多媒体文件，比如mp4、flv、mkv等。

- Stream：表示时间轴上的一段连续数据，如一段声音数据、一段视频数据或一段字幕数据，可以是压缩的，也可以是非压缩的，压缩的数据需要关联特定的编解码器。

- Frame/Packet：通常，一个媒体流是由大量的数据帧组成的，对于压缩数据，帧对应着编解码器的最小处理单元，分属于不同媒体流的数据帧交错存储于容器之中。
    - 一般情况下：Frame对应压缩前的数据，Packet对应压缩后的数据。

- Codec(编解码器)：以帧为单位实现压缩数据和原始数据之间的相互转换的

- mux(复用)：把不同的流按照某种容器的规则放入容器，这种行为叫做mux

- demux(解复用)：把不同的流从某种容器中解析出来，这种行为叫demux

- 码率和帧率是视频文件的最重要的基本特征，对于他们的特有设置会决定视频质量。如果我们知道码率和时长那么可以很容易计算出输出文件的大小。

- 帧率：帧率也叫帧频率，帧率是视频文件中每一秒的帧数，肉眼想看到连续移动图像至少需要15帧。

- 码率：比特率(也叫码率，数据率)是一个确定整体视频/音频质量的参数，秒为单位处理的位数，码率和视频质量成正比，在视频文件中比特率用bps来表达。

![pic/Screen Shot 2022-02-09 at 7.51.48 PM.png]    
![pic/Screen Shot 2022-02-09 at 7.55.18 PM.png]    

### 3. ffmpeg的简单用法

#### 转码

```
ffmpeg -i out.ogv -vcodec h264 out.mp4
ffmpeg -i out.ogv -vcodec mpeg4 out.mp4
ffmpeg -i out.ogv -vcodec libxvid out.mp4
ffmpeg -i out.mp4 -vcodec wmv1 out.wmv
ffmpeg -i out.mp4 -vcodec wmv2 out.wmv
```
-i 后面是输入文件名。-vcodec 后面是编码格式，h264 最佳，但 Windows 系统默认不安装。如果是要插入 ppt 的视频，选择 wmv1 或 wmv2 基本上万无一失。

>附加选项：-r 指定帧率，-s 指定分辨率，-b 指定比特率；于此同时可以对声道进行转码，-acodec 指定音频编码，-ab 指定音频比特率，-ac 指定声道数，例如
```
ffmpeg -i out.ogv -s 640x480 -b 500k -vcodec h264 -r 29.97 -acodec libfaac -ab 48k -ac 2 out.mp4
```

#### 剪切

用 -ss 和 -t 选项， 从第 30 秒开始，向后截取 10 秒的视频，并保存：
```
ffmpeg -i input.wmv -ss 00:00:30.0 -c copy -t 00:00:10.0 output.wmv
ffmpeg -i input.wmv -ss 30 -c copy -t 10 output.wmv
```
达成相同效果，也可以用 -ss 和 -to 选项， 从第 30 秒截取到第 40 秒：
```
ffmpeg -i input.wmv -ss 30 -c copy -to 40 output.wmv
```
值得注意的是，ffmpeg 为了加速，会使用关键帧技术， 所以有时剪切出来的结果在起止时间上未必准确。 通常来说，把 -ss 选项放在 -i 之前，会使用关键帧技术； 把 -ss 选项放在 -i 之后，则不使用关键帧技术。 如果要使用关键帧技术又要保留时间戳，可以加上 -copyts 选项：
```
ffmpeg -ss 00:01:00 -i video.mp4 -to 00:02:00 -c copy -copyts cut.mp4
```

#### 合并

把两个视频文件合并成一个。

简单地使用 concat demuxer，示例：
```
$ cat mylist.txt
file '/path/to/file1'
file '/path/to/file2'
file '/path/to/file3'
 
$ ffmpeg -f concat -i mylist.txt -c copy output
```
更多时候，由于输入文件的多样性，需要转成中间格式再合成：
```
ffmpeg -i input1.avi -qscale:v 1 intermediate1.mpg
ffmpeg -i input2.avi -qscale:v 1 intermediate2.mpg
cat intermediate1.mpg intermediate2.mpg > intermediate_all.mpg
ffmpeg -i intermediate_all.mpg -qscale:v 2 output.avi
```

#### 调整播放速度

加速四倍：
```
ffmpeg -i TheOrigin.mp4 -vf  "setpts=0.25*PTS" UpTheOrigin.mp4
```
四倍慢速：
```
ffmpeg -i TheOrigin.mp4 -vf "setpts=4*PTS" DownTheOrigin.mp4
```

#### 帧率设置

使用-r选项
语法是：
```
ffmpeg -i input -r fps output
```
例如：
```
ffmpeg -i input.avi -r 30 output.mp4
```

#### 使用fps filter

另一个设置帧率是用fps filter，特别是在filterchains使用时非常有用。
例如：修改输入文件的帧率到25
```
ffmpeg -v clip.mpg -vf fps=fps=25 clip.webm
```

#### 比特率设置

比特率也是一个决定音视频总体质量的参数。他决定每个时间单位处理的bit数。

设置比特率：比特率决定处理1s的编码流需要多少bits，设置用-b选项。区分音视频用-b:a和-b:v

例如：设置整体1.5Mbit每秒
```
ffmpeg -i file.avi -b 1.5M file.mp4

ffmpeg -i input.avi -b:v 1500K output.mp4
```

#### CBR设置

CBR设置一般用作直播流，比如视频会议。为输出设置CBR,有三个参数必须设置为同一个值。`bitrate(-b option), minimal rate(-minrate), maximal rate(-maxrate)`。`maximal rate`需要设置`-bufsize`选项。

例如设置CBR为0.5Mbit/s。
```
ffmpeg -i in.avi -b 0.5M -minrate 0.5M -maxrate 0.5M -bufsize 1M output.mkv
```

#### 设置输出文件的最大size

用-fs选项。

例如设置输出文件的最大的size为10M
```
ffmpeg -i input.avi -fs 10MB output.mp4
```
#### 文件大小计算

文件的大小是是音视频流大小的和。

视频流的大小的方程式是(除以8是由bits到bytes的转换):
```
video_size = video_bitrate * time_in_seconds / 8;
```

如果音频是解压缩的，计算公式是：
```
audio_size = smpaling_rate * bit_depth * channels * time_in_second / 8;
```
例如：计算10分钟的视频， 1500kbits/s 视频比特率和 128kbits/s的音频比特率，用下面的计算方法：
```
file_size = video_size + audio_size;
file_size = (video_bitrate + audio_bitrate) * time_in_seconds / 8;
file_size = (1500 kbits/s + 128kbits/s) * 600s
file_size = 1628kbits/s * 600s
file_size = 976800kb = 976800000 b / 8 = 122100000 B / 1024 = 119238.28125KB
file_size = 119238.28125 KB / 1024 = 116.443634033203125MB = 116.44M
```
