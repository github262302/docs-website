---
title: FFmpeg使用指南
category: tutorial
date: 2025-04-01
tags: []
---

# FFmpeg 使用指南

## 导出音频

### 基础音频提取
```bash
ffmpeg -i input.mp4 -vn -acodec copy output.aac
```

**参数详解：**
| 参数 | 全称 | 作用 |
|-----|------|------|
| `-i` | input | 指定输入文件，后面紧跟输入文件名 |
| `-vn` | video none | 禁用视频流，只保留音频 |
| `-acodec` | audio codec | 指定音频编码器，`copy`表示直接复制不重新编码 |

### 导出为MP3格式
```bash
ffmpeg -i input.mp4 -vn -ar 44100 -ac 2 -b:a 192k output.mp3
```

**参数详解：**
| 参数 | 全称 | 作用 |
|-----|------|------|
| `-ar` | audio rate | 设置音频采样率，44100Hz是CD音质标准 |
| `-ac` | audio channels | 设置声道数，2表示立体声 |
| `-b:a` | bitrate audio | 设置音频比特率，192k是高质量MP3 |

### 导出为WAV无损
```bash
ffmpeg -i input.mp4 -vn -ar 48000 -ac 2 -acodec pcm_s16le output.wav
```

**参数详解：**
| 参数 | 作用 |
|-----|------|
| `-acodec pcm_s16le` | 使用PCM 16位小端编码，无损格式 |
| `48000` | 48kHz采样率，适合专业音频 |

---

## 剪切视频

### 按时间段剪切
```bash
ffmpeg -ss 00:01:30 -to 00:03:45 -i input.mp4 -c copy output.mp4
```

**参数详解：**
| 参数 | 作用 |
|-----|------|
| `-ss` | start seek，开始时间，格式 HH:MM:SS 或秒数 |
| `-to` | 结束时间，与 `-ss` 配合用 |
| `-c copy` | codec copy，音视频直接复制不重新编码（速度快） |

### 从开始剪指定长度
```bash
ffmpeg -ss 00:02:00 -t 60 -i input.mp4 -c copy output.mp4
```

**参数详解：**
| 参数 | 作用 |
|-----|------|
| `-t` | duration，持续时长，单位秒。这里是剪60秒 |
| `-ss 00:02:00` | 从第2分钟开始 |

### 精确剪切（重新编码，精度高但慢）
```bash
ffmpeg -ss 00:01:30 -to 00:03:45 -i input.mp4 -c:v libx264 -c:a aac output.mp4
```

**参数详解：**
| 参数 | 作用 |
|-----|------|
| `-c:v libx264` | 视频编码器H.264，重新编码确保关键帧精确 |
| `-c:a aac` | 音频编码器AAC |

---

## 常用组合示例

### 提取某段视频中的音频
```bash
ffmpeg -ss 00:01:00 -t 30 -i input.mp4 -vn -acodec copy output.aac
```
先`-ss`定位到1分钟，`-t 30`取30秒，`-vn`只保留音频。

### 批量导出音频
```bash
for f in *.mp4; do ffmpeg -i "$f" -vn -acodec copy "${f%.mp4}.aac"; done
```

---

## 注意事项

1. `-c copy` 剪切速度快但有精度误差（约几秒）
2. `-ss` 放在 `-i` 前是快速定位，放在 `-i` 后是精确解码
3. 精确剪切建议用重新编码模式
