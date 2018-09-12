
# Fragment MP4

Fragment MP4 is a MP4 format which contains a series of segement which has audio/video samples, and non-Fragment MP4 usually packages all samples in one large file.

The main advantage of fragment MP4 is supporting adaptive bitrate.

## BOX
All MP4 files use an object oriented format that contains [boxes aka atoms](http://atomicparsley.sourceforge.net/mpeg-4files.html).

## MOOV
**moov** is a box which contains general metedata.

![Moov](https://docs.microsoft.com/en-us/iis/media/smooth-streaming/smooth-streaming-primer/_static/image1.png)

## Fragment
Each fragment contains **moof** and **mdat**

![Fragment](https://www.bogotobogo.com/VideoStreaming/images/iis8/SmoothStreamingWireFormat.png)
