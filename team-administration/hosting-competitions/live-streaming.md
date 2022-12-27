# Live Streaming

For this article, we will be discussing live streaming through OBS. OBS will allow streaming to most major platforms with all of the same setups. It is assumed that a general understanding of OBS is had, and the basics can be found here:

{% embed url="https://www.youtube.com/watch?t=1041s&v=EuSUPpoi0Vs" %}

Ensure that the venue's network will both allow the ability to stream and has sufficient bandwidth to ensure a low latency stream. Most school networks are acceptable, but check the stream host's documentation for precise network bandwidth requirements.

## Cameras

Unlike traditional web live streams, robotics event live streams have multiple cameras, each of which are dozens of feet from each other. Typically, an event partner will place at least one camera at each field, and a wider event/awards camera. There are several approaches to connecting these cameras to the live stream computer.

### HDMI Cameras

Many cameras will offer an HDMI out signal, which can be an efficient way to achieve a low-latency stream. Before purchasing, ensure that the cameras that will be used have a clean HDMI out signal. Additionally,  several 50ft HDMI cords may be needed, which is the maximum length that can be reasonably gotten without signal degradation or the need for an active extension.

#### Recommended Setup: GoPro (Used by Team BCUZ)

GoPros provide a great option for live streaming robotics events, due to their cost and wide field of view. If GoPros are purchased, try to purchase a Hero 8 or newer. In this version, the USB C port can be directly connected to a computer and be used as a web camera, which can provide flexibility for additional cameras. In most cases, older models are acceptable.

Connect the micro-HDMI port to an external switcher or capture card. For most competition purposes, the Blackmagic ATEM Mini is an acceptable option. The ATEM Mini Pro is a slightly more expensive alternative but would allow streaming directly from the switcher without a dedicated streaming computer.

The GoPros may need to be modified to ensure they don't overheat during a long competition day. Overheating cameras can be identified by

* Remove the battery and leave the battery compartment open
* Disable H.264. In Video Compression, Select HVEC. The older encoding is not needed for our purposes and only increases temperatures
* Disable Wifi Configuration
* Set video mode to 1080p60
* Set field of view to the widest setting
* Disable video stabilization

_If the venue is particularly warm, invest in heat sinks to ensure that the cameras are properly cooled. Some Event Partners have found success in repurposing Raspberry Pi heatsinks. Target the area of the GoPro directly below the sensor, but experimentation with each particular model is warranted._

### Networked Cameras

Another approach is to use a [PoE](https://en.wikipedia.org/wiki/Power_over_Ethernet) network camera, which are commonly produced as security cameras. This approach is significantly more flexible than HDMI cameras, but careful considerations must be made to ensure access to a low-latency stream from the cameras.

#### Recommended Setup: Amcrest PoE Camera (Used by Nathan Nolte)

Camera: [https://amcrest.com/amcrest-1080p-poe-video-security-ip-camera-pt.html](https://amcrest.com/amcrest-1080p-poe-video-security-ip-camera-pt.html)

To obtain a low-latency stream from the cameras, we will use a tool known as OBS-gstreamer: [https://obsproject.com/forum/resources/obs-gstreamer.696/](https://obsproject.com/forum/resources/obs-gstreamer.696/)&#x20;

[https://gstreamer.freedesktop.org/download/](https://obsproject.com/forum/resources/obs-gstreamer.696/)

Here are some troubleshooting tips:

Once everything is installed, it may still not get the GStreamer Source to show up in OBS if Windows isn't finding the GStreamer framework. This may be caused by the framework not being in the Windows PATH environment variable. The fix I found was launching OBS with a batch file that mounts GStreamer to PATH when run. The steps for this fix are as follows:

Create a new file called start\_obs.bat. Make sure that the file extension is .bat. Add the following code to the file:

```
cd /D "%~dp0"
cd gstreamer
SET GSTREAMER_1_0_ROOT_MINGW_X86_64=%cd%
cd bin
SET PATH=%cd%
SET GST_PLUGIN_PATH=%cd%
cd..
cd..
cd bin
cd 64bit
start obs64.exe
```

Save the file. and play around with the format of the RTSP stream for a while before it works. Try this syntax `uridecodebin uri=rtsp://admin:password@192.168.1.200 ! queue ! video`.

All the checkboxes are cleared in the GStreamer source properties except for the very last one (Clear image data after end-of-stream error)

_Note: thanks so much to Nathan Nolte for his advice with this setup!_

#### Recommended setup: HuddleCam HD Pro IP (Used by WV Robotics Alliance)
Another option for networked cameras is [NewTek's NDI](https://ndi.tv), which is a low-latency network video protocol.

The [PTZOptics HuddleCamHD Pro IP](https://huddlecamhd.com/ndi-webcam/) is one NDI camera which is well-suited for use in VRC event livestreams. It can be powered via PoE or an included power adapter.

The Pro IP is an "EPTZ" camera, meaning it has a 4K sensor but outputs 1080p video, and the user can control the size and location of the 1080p crop within the 4K image. Pan and zoom is configurable via the included IR remote control, or over the network via software. The included remote can individually control up to 4 cameras, which is quite convenient when you have a camera on each field.

The [obs-ndi](https://obsproject.com/forum/resources/obs-ndi-newtek-ndi™-integration-into-obs-studio.528/) plugin allows for NDI video sources to be added natively into OBS Studio. For best results, enable hardware acceleration and set the latency to "Low (experimental)".

Additionally, the free [NDI Tools](https://ndi.tv/tools/) suite includes several pieces of software which are very handy for configuring and using NDI cameras. In particular, "NDI Studio Monitor" is nice for viewing video feeds and controlling the cameras' pan and zoom, and "NDI Webcam" can turn up to 4 NDI cameras into "virtual webcams" which is handy as an alternate way to get video into OBS or any other application.

[Another variant](https://huddlecamhd.com/pro/) of the HuddleCamHD Pro is also available which outputs video over USB and HDMI.

### USB Webcams
Another option is to use a USB webcam on the end of a long USB extension cable. While this approach has its downsides, the cost to set up USB webcams could be as low as around $100 per field, which may be significantly less than HDMI or IP cameras.

When selecting a webcam, prioritize the widest possible field of view (FOV). The wider the field of view, the closer the camera can be placed while still showing the entire field. Many popular webcams have a relatively narrow field of view, which is fine for their intended purpose (placing on top of a computer monitor) but suboptimal for use as a VRC field camera. However, there are some webcams available with a suitably wide field of view. Webcams intended for use in conference rooms may also be a good option, as they also tend to have a wider field of view.

The [Aukey PC-LM1E](https://www.aukey.com/products/aukey-overview-full-hd-video-1080p-webcam) is one webcam which is well-suited to use as a VRC field camera. Some Logitech models, such as the [C930](https://www.logitech.com/en-us/products/webcams/c930s-pro-hd-webcam.960-001403.html), look like they would also work well.

For mounting the cameras, light stands provide a relatively inexpensive means to get the camera high enough. 6-7 foot light stands are plentiful on Amazon, and this may be enough height for a webcam with a wide field of view, and/or if you have enough room to put the camera farther from the field horizontally. Otherwise, taller light stands are available, albeit at increased cost. Make sure both the webcam and light stand have a 1/4" screw mount (this is also the standard-sized screw mount used on most tripods).

Since most webcams come with relatively short integrated cables, a USB extension cable will likely be needed. Many models are avialable, but one good option is [this cable](https://www.monoprice.com/product?p_id=6149) from Monoprice. It's available in lengths ranging from 16 to 82 feet, but the 49 and 65-foot lengths are both good choices for this application. 

With multiple USB cameras connected to the streaming computer at once, some care may need to be taken to ensure there is enough USB bandwidth for all cameras to be running at the same time. As a rule of thumb, no more than two cameras should be sharing the same USB port on the computer, and ideally every camera would be connected directly to a dedicated port on the computer (rather than through a hub).

## Audio

Audio is one of the most important aspects of the stream, and distinguishes a functional stream from an excellent one. Event Partners will likely need to purchase a USB Audio Interface to connect the venue's microphones to the live stream. To minimize copyright strikes on the streams, be sure to only capture the Emcee's microphones and Tournament Manager's Audio, and exclude any music that may be playing in the venue. It is best practice to mix in stream-friendly music through OBS. It is also possible to pipe Tournament Manager Audio into OBS instead of capturing it from the venue's audio mixer.

## TM Source

Below is a recommended plugin for OBS (Win 64) in order to automate much of the work for linking TM and OBS. This is a third-party plugin and is not officially supported by RECF or Vex however it was designed by an employee of the company that designed TM and is one of the best community products to link the two platforms.

{% embed url="https://gitlab.com/dwabtech/vextm-obs-source" %}
OBS plugin by Dave Flowerday
{% endembed %}

It is recommended to follow the installation and setup instructions given by Mr. Flowerday.

## TM Switcher

Below is a recommended tool that can be used to automate many of the aspects of a robotics live stream, including field switching, showing saved match scores, and automatically recording matches.

{% embed url="https://github.com/MayorMonty/tm-obs-switcher" %}

### Contributing Teams to this Article:

* [BCUZ](https://www.instagram.com/bcuz.robotics/?hl=en) (Clemson University)
* [WV Robotics Alliance](https://www.wvrobot.org)
