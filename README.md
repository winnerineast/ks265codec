# *KSC265codec(v2.5.4.0)*

It should be noted that, the libraries used in our ios/andriod demos have expiration time.

## ENCODER
Usage: command line examples    
```
AppEncoder_x64 -i p_3840x2160_50.yuv -preset veryfast -latency offline -wdt 3840 -hgt 2160 -fr 50 -rc 1 -br 20000 -iper 128 -b test.265
AppEncoder_x64 -i p_3840x2160_50.yuv -preset veryfast -latency offline -wdt 3840 -hgt 2160 -fr 50 -rc 0 -qp 27 -iper 128 -b test.265
AppEncoder_x64 -i p_3840x2160_50.yuv -preset veryfast -latency offline -wdt 3840 -hgt 2160 -fr 50 -rc 3 -crf 24 -iper 128 -b test.265
```


### Basic parameters:

-preset [preset_value], 

which specifies the encoding speed by the character string [preset_value], among strings of "superfast", "veryfast", "fast", "medium", "slow", "veryslow" and "placebo".

-latency [latency_value],

which specifies the encoding latency by the character string [lactency_value], among strings of "zerolatency", "livestreaming", "offline".

-i [input_filename], 

which specifies the address of the input YUV file in 4:2:0 sampling format by a character string [input_filename].

-wdt [width], 

which specifies the image width of the input video by a positive integer value [width]. 

-hgt [height], 

which specifies the image height of the input video by a positive integer value [height].

-fr [framerate], 

which specifies the frame rate of the input video by a positive integer value [framerate].

-iper [intraperiod], 

which specifies the maximum distances between consecutive I pictures by a positive integer value [intraperiod].

-rc [rctype], 

which specifies the rate control type by the positive integer value [rctype] valuing among values 0(fixed qp), 1(cbr), 2(abr) and 3(crf). There are four cases:
* -br [bitrate] should be followed. If [rctype] equals to 1 or 2, a parameter -br [bitrate] should be followed and specifies the target encoding bit-rate by the positive value [bitrate] (kbps,kilo bit rate per second). 
* -qp [qp_value] should be followed. If [rctype] equals to 0, a parameter -qp [qp_value] should be followed and specifies the target encoding quantization parameter by the positive value [qp_value] ranging from 0 to 51. 
* -crf [crf_value] should be followed. If [rctype] equals to 3, a parameter -crf [crf_value] should be followed and specifies the target crf parameter by the positive value [crf_value] ranging from 0 to 51. 

-b [stream_filename], 

which specifies the address of the output stream file in HEVC/H.265 format by a character string [stream_filename]. Default: no stream is output.

### Optional parameters:

-v or -V [version],

which is utilized to print the version and copyright of the encoder.

-psnr [psnrcalc],

which specifies psnr calculation method by a non-negative value [psnrcalc], and
* 0 (as a default value) means disabling psnr calculation,
* 1 means enabling psnr calculation and outputing the overall psnr result. 
* 2 means enabling psnr calculation and outputing psnr info for each frame.

-o [reconstructYUV], 

which specifies the address of the reconstrcuted yuv file in 4:2:0 format by a character string [reconstructYUV]. Default: no reconstructed YUV file is output.

-frms [frame_no], 

which specifies the number of frames to be encoded for the input video by a positive integer value [frame_no]. Default: [frame_no] = -1, when all input frames are encoded.

-threads [thread_no], 

which specifies the number of threads used to encode the input video by a non-negative value [thread_no]. Default: [thread_no] = 0, when all available threads can be utilized.

-bframes[value1], -vbv-maxrate [value2] , -vbv-bufsize[value3],

which specifies similar meanings as similar values defined in x264



## DECODER
Usage: command line examples    
```
AppDecoder_x64.exe -b test.265 -o test.yuv -threads 2
```

### Basic parameters:

-v or -V [version]

which specifies the decoder version and copyright.

-b [bitstream],

which specifies input bit-stream file by a character string [bitstream].

### Optional parameters:

-o [output],

which specifies the decoded yuv file name by a character string [output].

-threads [threadnum],

which specifies the number of threads used for decoding process by a non-negative value [threadnum]. Default: [threadnum] = 0, when all available threads can be utilized.

## Performance of decoder

KSC265 decoder is compared with openHEVC in ffmpeg on ARM64@Andriod, ARM64@iOS and x86 platforms.

| decoding  speed <br> ( ksc265inFFmpeg / openHEVCInFFmpeg) | iOS<br>(ipad mini2) | Andriod<br>(VIVOxplay5a) | PC<br>(E5-2690 v3  @ 2.60GHz) |
| ---------------------------------------- | ------------------- | ------------------------ | ----------------------------- |
| 1 thread                                 | 2.90                | 2.85                     | 2.11                          |
| full threads                             | 2.69                | 2.99                     | 3.89                          |

On average, as above table shows, KSC265 decoder can achieve more than 2/2.5 times the speed of openHEVC in ffmpeg on x86/ARM, and details can be found in the excels for decoding performance. Moreover, as following table shows, the decoding speed of KSC265 now can well support the 1080p@25fps applications.

| decoding  speed of ksc265inFFmpeg <br> (in frames per second) | iOS<br>(ipad mini2) | Andriod<br>(VIVOxplay5a) | PC<br>(E5-2690 v3  @ 2.60GHz) |
| ---------------------------------------- | ------------------- | ------------------------ | ----------------------------- |
| 1920x1080 @  1thread                     | 32.06               | 32.94                    | 177.19                        |
| 1280x720 @  1thread                      | 77.88               | 89.60                    | 346.24                        |
| 1920x1080 @  full threads                | 51.13               | 90.44                    | 939.25                        |
| 1280x720 @  full threads                 | 120.20              | 187.16                   | 1976.24                       |



## Performance of encoder

KSC265 encoder is compared with X265-v2.3,  X264 and vp9 on Win7@i5-4670 using following parameters:

```
x264.exe -o out.264 BQSquare_416x240_60.yuv --input-res 416x240 --preset [superfast|veryfast|slow|placebo] --fps [framerate] --profile high --aq-mode 0 --no-psy --psnr  --bitrate [btrNumber] --threads 1/0 --keyint [framerate * 10] --frames 1000000
AppEncoder_x64.exe -b out.265 -i BQSquare_416x240_60.yuv -preset [veryfast|slow|veryslow] -threads 1/0 -psnr 2 -rc 1 -br [btrNumber] -frms 1000000 -iper [framerate * 10]
x265.exe -o out.265 --input BQSquare_416x240_60.yuv --input-res 416x240 --preset [ultrafast|ultrafast|slow|veryslow] --fps [framerate] --aq-mode 0 --no-psy-rd --no-psy-rdoq  --psnr  --bitrate [btrNumber] --frame-threads [1|0] --no-wpp/--wpp --keyint [framerate * 10] --frames 1000000
vpxenc.exe --codec=vp9 --passes=1 --[rt|goog|best] --fps=[framerate]/1 --i420 --end-usage=vbr --target-bitrate=[btrNumber] --kf-max-dist=[framerate * 10] --cpu-used=8 --threads=[1|4] --psnr -w 416 -h 240 -o out.vp9 BQSquare_416x240_60.yuv --frame-parallel=0
```

Then on test sequences of JCTVC CLASS-A ~ CLASS-E, and one class of game videos@30fps, compared to x264(20151215), 265-v2.3 and vp9 in the speed form of encoded frames per second (fps), the average performance of KS265 can be summarized by the follows. 

|                          | KSC265  vs. X264        | KSC265 vs. X264        | KSC265 vs. X265         | KSC265 vs. X265        | KSC265 vs. vp9          | KSC265 vs. vp9        |
| ------------------------ | ----------------------- | ---------------------- | ----------------------- | ---------------------- | ----------------------- | --------------------- |
| full-thread  comparisons | Bitsaving @same quality | Speedup @same  quality | Bitsaving @same quality | Speedup @same  bitrate | Bitsaving @same quality | Speedup @same bitrate |
| RealTime+                | 47.1%                   | -9.8%                  | 28.6%                   | 123.5%                 | 36.5%                   | 111.0%                |
| RealTime                 | 45.0%                   | 2.3%                   | 34.2%                   | 84.4%                  | 41.1%                   | 73.5%                 |
| Transcode                | 37.9%                   | -5.0%                  | 11.8%                   | 199.1%                 | 19.8%                   | 300.2%                |
| Best Ratio               | 35.8%                   | 84.0%                  | 10.2%                   | 86.5%                  | 6.9%                    | 1015.9%               |

The details are described in the excel document.
