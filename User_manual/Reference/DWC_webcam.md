---
title: Connecting a web camera to Duet Web Control
description: Duet 2 and Duet 3 mainboards do not support a directly-connected web camera. However, you can buy a suitable Wifi or Ethernet IP camera and then configure Duet Web Control to include an image from the camera on the Print page.
published: true
date: 2021-11-30T15:12:49.308Z
tags: 
editor: markdown
dateCreated: 2021-11-30T15:12:46.132Z
---

# Introduction

Duet 2 and Duet 3 mainboards do not support a directly-connected web camera. However,  you can buy a suitable Wifi or Ethernet IP camera and then configure Duet Web Control to include an image from the camera on the Print page.

**Important**: You must choose a camera that has the facility to return a static JPEG image in response to a HTTP request. Not all IP cameras have this facility. Sadly, none of the major browsers supports including a RTSP camera video stream in a web page.

# Generic setup instructions

* Connect the camera to your Wifi or Ethernet network according to the manufacturer's instructions.
* Identify your camera's IP address and the HTTP port and URL that return a static JPEG image. You may find [https://www.ispyconnect.com/sources.aspx](https://www.ispyconnect.com/sources.aspx) useful.
* In Duet Web Control, go to Settings, User Interface. At the bottom right is the Webcam Integration panel. Configure the URL there.

# Specific cameras

## Raspberry Pi with camera add-on

This is known to work. You can use OctoPrint to host a video camera feed. However there is a lighter weight solution. Here is a summary of the process of installing MotionEyeOS on a RaspberryPi with a camera attached.

* [motionEyeOS](https://github.com/ccrisan/motioneyeos/releases) for particular version Raspberry Pi (e.g. motioneyeos-raspberrypi-########.img.gz is for the original Raspberry Pi and the Raspberry Pi Zero).
* After [formatting an SD card](https://www.sdcard.org/downloads/formatter_4/), write the image to your MicroSD card using [balenaEtcher](https://etcher.io).
* Configure WiFi by adding a file to the MicroSD card. Copy, paste, and edit the following text, saving the file as **wpa_supplicant.conf** on the MicroSD card.

```
:update_config=1
:ctrl_interface=/var/run/wpa_supplicant
:network={
::scan_ssid=1
::ssid="HomeNetwork"
::vpsk="password"
:}
```

* Find the IP address of your Raspberry Pi using [https://github.com/adafruit/Adafruit-Pi-Finder/releases](https://github.com/adafruit/Adafruit-Pi-Finder/releases) app (e.g. 10.1.1.64).
* Visit that IP address in your web browser.
* Click on the Person icon (in the upper left side of the page) and enter **admin** for the username, and left the password field blank. Then click the Login button.
* Now would be a good time to use the menu button (in the upper left) to create your admin password, You also might even want to give your Pi a nice hostname by enabling the "advanced settings" option (e.g. 3dcam).

For more details on this setup process please visit [https://betrue3d.dk](https://betrue3d.dk/rpi-zero-wireless-video-for-duet-using-motioneye-os/)

## Raspberry Pi with UV4L

If using a Raspberry Pi cam, UV4L is a nice lightweight option, and has a built-in webserver for streaming. There’s a guide on [Github here](https://github.com/tomconn/RaspberryPiStreaming).

Roughly:
1. [Install UV4L](https://www.linux-projects.org/uv4l/installation/)
2. Also install the packages uv4l-server and uv4l-raspicam-extras
3. Set the duet webcam url to be 
**http://[raspberry pi ip address]:8080/stream/snapshot.jpeg?delay_s=0**

## Sannce I21AG

* Download and install the Sannce Cam app on your smartphone.
* Connect the camera to your WiFi network using the smartphone app.
* Use the app to configure an admin password and also a user-mode ID and password. The camera will reset and take a minute or two to connect again.
* Download and run the [Sannce search tool](http://www.sanncegroup.com/document/IPC/I21AD/tool/Search_tool4.2.exe). Select your camera from the list, then it will show the IP address and port number to use.
* Enter the following URL in the DWC webcam settings, substituting the IP address and port numbers you just found: 
**http://[ip-address:port]/snapshot.cgi**
* Check "Do not append extra HTTP qualifier" (I don't know whether this matters or not).
* You may wish to experiment with Webcam Update Interval - 0.1 seconds gives a decent video effect.
* Your browser will ask for a login ID and password for the camera. Give it the user-mode ID and password and tell it to remember them.
* To avoid your router possibly allocating a different IP address to the camera when it reconnects, if possible configure your router to make that IP address allocation permanent.
* An alternative method (which may not be supported by all browsers) to using snapshot.cgi, is to use 
**http://[ip-address:port]/videostream.cgi?loginuse=[YourUserName]&loginpas=[YourPassword]**
This embeds a video stream directly from the camera in your browser. 
* Note - if you go to **http://[ip-address:port]/index.htm** and click on Videostream mode, you can use the motors onboard the camera to focus on various parts of your print.

## WyzeCamV2

The following instructions were contributed by forum user Foden.

Purchased some of these beauties, [Amazon](https://www.amazon.ca/Wyze-Wireless-Camera-Android-Version/dp/B076H3SRXG/ref=sr_1_3?ie=UTF8&qid=1541290010&sr=8-3&keywords=wyze), for the house to replace my way more expensive - but dead - IP cameras. With the addition of a small/cheap 4-16GB SD card and an awesome hack from [Github](https://github.com/EliasKotlyar/Xiaomi-Dafang-Hacks), I can link them to my existing home PVR. I've used Sighthound for years https://www.sighthound.com/, but you don't need to have any software to use these cameras with with Duet 2. All you need to do is extract the folder 'firmware_mod' from the Dafang zip file to the root of your sd card. Edit the file \config\usb_eth_driver.conf.dist (note: it may be wpa_supplicant.conf.dist that you need to edit and rename) and add your SSID and password (only 2.4 Ghz). Rename \config\usb_eth_driver.conf.dist\ to \config\usb_eth_driver.conf. That sets up your Wi-Fi connectivity. Once you figure out the cameras IP just enter it into your browser **http://www.xxx.yyy.zzz**, you will be prompted for the user name 'root' and password 'ismart12' - all lowercase. You can, and should, change the password straight away. Monkey around with the settings but make sure under the camera controls you have RTSP h264 server running. Probably don't need to change much. Then go to your Duet W-Fi under Settings / User Interface enter this for the camera address **https://www.xxx.yyy.zzz/cgi-bin/currentpic.cgi** - replacing www.xxx.yyy.zzz with the camera IP. Set the web cam update interval to your liking, mine is set at 1 sec. Save your settings and when printing the job status window will update with an image from your camera based on the update interval you entered.

The cameras will also work, generally, in low to no light, I forgot where i got the info but think it just needs a file in the config folder called 'autonight.conf.sw' and all it contains is '-S'