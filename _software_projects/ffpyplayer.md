---
title: "FFPyPlayer: Python media player/writer library"
excerpt: "[FFPyPlayer](https://github.com/matham/ffpyplayer) is a Python *(and Cython)* media player and writer, supporting media files, webcams, etc. It is open source and based on FFmpeg."
header:
  overlay_color: "#333"
---

[FFPyPlayer](https://github.com/matham/ffpyplayer) is a open source FFmpeg based Python media player that supports playing media files, USB cams, ethernet cams. It is written in Cython to get c-level performance with ease of use of Python. See the [documentation](https://matham.github.io/ffpyplayer/index.html) for full details.

[Kivy](kivy.org) uses FFPyPlayer as their video/audio backend on iOS and Android and supports it as a optional backend on all other platforms. 

## Background

Working in a neuroscience lab, we needed a lightweight and Pythonic way to read video from webcams and other media sources and to record them to disk. E.g. in one experiment, we needed to record animal behavior from 8 cameras simultaneously without losing any frames. Some requirements were:

* A Python interface.
* Playing webcams (USB, ethernet) and video files.
* Re-playing video should generate identical frame timestamps every time (for reliable animal behavior coding).
* Convert between image formats, e.g. `yuv420p` -> `RGBA`.
* Write to video files, with optional compression.

[FFmpeg](https://www.ffmpeg.org/), likely the most commonly used media library, provides a C-API that meets all these needs. It has a demo ffplay implementation to play media.

## FFPyPlayer

The player in FFPyPlayer is a python/cython port of ffplay implemented using OOP that meets all the requirements. It is high performance, including zero frame copying after decoding. 

The image utilities and media writer is an OOP interface to the FFmpeg low-level C-API. It currently only supports video and no audio.

FFPyPlayer supports:

* Direct show devices such as USB cams, on Windows. A list of available cameras can be queried.
* Web cameras such as ethernet cameras and any media sources supported by FFmpeg.
* Reading video sources with zero-copying of the image data.
* Filtering the media being played with any of the FFmpeg filters.
* Image methods to convert between image formats. E.g. `yuv420p` -> `RGBA`.
* Writing to a video file.


## Example usage

### Converting Images

```py
from ffpyplayer.pic import Image, SWScale
# create image
w, h = 500, 100
size = w * h * 3
buf = bytearray([int(x * 255 / size) for x in range(size)])

img = Image(plane_buffers=[buf], pix_fmt='rgb24', size=(w, h))
# create image converter
sws = SWScale(w, h, img.get_pixel_format(), ofmt='yuv420p')

# convert image
img2 = sws.scale(img)
img2.get_pixel_format()
'yuv420p'
planes = img2.to_bytearray()
map(len, planes)
[50000, 12500, 12500, 0]
```

### Simple transcoding example

```py
from ffpyplayer.player import MediaPlayer
from ffpyplayer.writer import MediaWriter
import time, weakref

# only video
ff_opts={'an':True, 'sync':'video'}
player = MediaPlayer(filename, ff_opts=ff_opts)
# wait for size to be initialized (todo: add timeout and check for quitting)
while player.get_metadata()['src_vid_size'] == (0, 0):
    time.sleep(0.01)

frame_size = player.get_metadata()['src_vid_size']
# use the same size as the inputs
out_opts = {'pix_fmt_in':'rgb24', 'width_in':frame_size[0],
            'height_in':frame_size[1], 'codec':'rawvideo',
            'frame_rate':(30, 1)}

writer = MediaWriter(filename_out, [out_opts])
while 1:
    frame, val = player.get_frame()
    if val == 'eof':
        break
    elif frame is None:
        time.sleep(0.01)
    else:
        img, t = frame
        writer.write_frame(img=img, pts=t, stream=0)
```
