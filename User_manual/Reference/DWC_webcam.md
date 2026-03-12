---
title: Connecting a web camera to Duet Web Control
description: Duet 2 and Duet 3 mainboards do not support a directly-connected web camera. However, you can buy a suitable Wifi or Ethernet IP camera and then configure Duet Web Control to include an image from the camera on the Print page.
published: true
date: 2026-03-12T13:18:47.048Z
tags: 
editor: markdown
dateCreated: 2021-11-30T15:12:46.132Z
---

# Introduction

Duet 2 and Duet 3 mainboards do not support a directly-connected web camera. However,  you can buy a suitable Wifi or Ethernet IP camera and then configure Duet Web Control to include an image from the camera on the Print page.

**Important**: You must choose a camera that has the facility to return a static JPEG image in response to a HTTP request. Not all IP cameras have this facility. Sadly, none of the major browsers supports including a RTSP camera video stream in a web page.

# IP cameras

## Generic setup instructions

* Connect the camera to your Wifi or Ethernet network according to the manufacturer's instructions.
* Identify your camera's IP address and the HTTP port and URL that return a static JPEG image. You may find [https://www.ispyconnect.com/sources.aspx](https://www.ispyconnect.com/sources.aspx) useful.
* In Duet Web Control, go to Settings, User Interface. At the bottom right is the Webcam Integration panel. Configure the URL there.

## Specific cameras

### Raspberry Pi with camera add-on

This is known to work. You can use OctoPrint to host a video camera feed. However there is a lighter weight solution. Here is a summary of the process of installing MotionEyeOS on a RaspberryPi with a camera attached.

* [motionEyeOS](https://github.com/ccrisan/motioneyeos/releases){target=_blank} for particular version Raspberry Pi (e.g. motioneyeos-raspberrypi-########.img.gz is for the original Raspberry Pi and the Raspberry Pi Zero).
* After [formatting an SD card](https://www.sdcard.org/downloads/formatter_4/){target=_blank}, write the image to your MicroSD card using [balenaEtcher](https://etcher.io){target=_blank}.
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

* Find the IP address of your Raspberry Pi using [https://github.com/adafruit/Adafruit-Pi-Finder/releases](https://github.com/adafruit/Adafruit-Pi-Finder/releases){target=_blank} app (e.g. 10.1.1.64).
* Visit that IP address in your web browser.
* Click on the Person icon (in the upper left side of the page) and enter **admin** for the username, and left the password field blank. Then click the Login button.
* Now would be a good time to use the menu button (in the upper left) to create your admin password, You also might even want to give your Pi a nice hostname by enabling the "advanced settings" option (e.g. 3dcam).

For more details on this setup process please visit [https://betrue3d.dk](https://betrue3d.dk/rpi-zero-wireless-video-for-duet-using-motioneye-os/){target=_blank}

### Raspberry Pi with UV4L

If using a Raspberry Pi cam, UV4L is a nice lightweight option, and has a built-in webserver for streaming. There’s a guide on [Github here](https://github.com/tomconn/RaspberryPiStreaming).

Roughly:
1. [Install UV4L](https://www.linux-projects.org/uv4l/installation/){target=_blank}
2. Also install the packages uv4l-server and uv4l-raspicam-extras
3. Set the duet webcam url to be 
`http://[raspberry pi ip address]:8080/stream/snapshot.jpeg?delay_s=0`

### Sannce I21AG

* Download and install the Sannce Cam app on your smartphone.
* Connect the camera to your WiFi network using the smartphone app.
* Use the app to configure an admin password and also a user-mode ID and password. The camera will reset and take a minute or two to connect again.
* Download and run the [Sannce search tool](https://support.sannce.com/hc/en-us/categories/900000044686-DOWNLOAD){target=_blank}. Select your camera from the list, then it will show the IP address and port number to use.
* Enter the following URL in the DWC webcam settings, substituting the IP address and port numbers you just found: 
`http://[ip-address:port]/snapshot.cgi`
* Check "Do not append extra HTTP qualifier" (I don't know whether this matters or not).
* You may wish to experiment with Webcam Update Interval - 0.1 seconds gives a decent video effect.
* Your browser will ask for a login ID and password for the camera. Give it the user-mode ID and password and tell it to remember them.
* To avoid your router possibly allocating a different IP address to the camera when it reconnects, if possible configure your router to make that IP address allocation permanent.
* An alternative method (which may not be supported by all browsers) to using snapshot.cgi, is to use 
`http://[ip-address:port]/videostream.cgi?loginuse=[YourUserName]&loginpas=[YourPassword]`
This embeds a video stream directly from the camera in your browser. 
* Note - if you go to `http://[ip-address:port]/index.htm` and click on Videostream mode, you can use the motors onboard the camera to focus on various parts of your print.

### WyzeCamV2

The following instructions were contributed by forum user Foden.

Purchased some of these beauties, [Amazon (Canada)](https://www.amazon.ca/Wyze-Wireless-Camera-Android-Version/dp/B076H3SRXG/){target=_blank}, for the house to replace my way more expensive - but dead - IP cameras. With the addition of a small/cheap 4-16GB SD card and an awesome hack from [Github](https://github.com/EliasKotlyar/Xiaomi-Dafang-Hacks){target=_blank}, I can link them to my existing home PVR. I've used Sighthound for years https://www.sighthound.com/, but you don't need to have any software to use these cameras with with Duet 2. All you need to do is extract the folder 'firmware_mod' from the Dafang zip file to the root of your sd card. Edit the file \config\usb_eth_driver.conf.dist (note: it may be wpa_supplicant.conf.dist that you need to edit and rename) and add your SSID and password (only 2.4 Ghz). Rename \config\usb_eth_driver.conf.dist\ to \config\usb_eth_driver.conf. That sets up your Wi-Fi connectivity. Once you figure out the cameras IP just enter it into your browser **http://www.xxx.yyy.zzz**, you will be prompted for the user name 'root' and password 'ismart12' - all lowercase. You can, and should, change the password straight away. Monkey around with the settings but make sure under the camera controls you have RTSP h264 server running. Probably don't need to change much. Then go to your Duet W-Fi under Settings / User Interface enter this for the camera address **https://www.xxx.yyy.zzz/cgi-bin/currentpic.cgi** - replacing www.xxx.yyy.zzz with the camera IP. Set the web cam update interval to your liking, mine is set at 1 sec. Save your settings and when printing the job status window will update with an image from your camera based on the update interval you entered.

The cameras will also work, generally, in low to no light, I forgot where i got the info but think it just needs a file in the config folder called 'autonight.conf.sw' and all it contains is '-S'

# Cameras connected to a Raspberry Pi Running DSF

> In **RRF 3.5 and later**, use the Spyglass camera plugin for cameras connected to the RPi via the CSI connector, and Motion for USB-connected cameras.
> 
> For **RRF 3.4 and earlier**, use the Motion plugin for all cameras.
{.is-info}

## Spyglass

### Description

[Spyglass](https://github.com/mainsail-crew/spyglass) is a modern webcam server for CSI-connected cameras on the Raspberry Pi. It requires Bookworm or newer to operate. Duet3D provides a [plugin](https://github.com/Duet3D/SpyglassWebcamServerPlugin) which bundles this application for easy installation in SBC mode.


### Usage

Once this plugin has been installed and started using the Plugins page on DWC, it can be configured in DWC. To achieve this, go to the `Settings` -> `General` page and make the following changes:

- Set `Webcam URL` to `http://[HOSTNAME]:8080/stream`
- Set `Webcam update interval (in ms)` to `0`
- Go to the `Job` -> `Webcam` page to see your live stream

For snapshots it is also possible to use `http://[HOSTNAME]:8080/snapshot` instead.

## Motion

### Description

From RRF 3.4, the Duet3D-provided Raspberry Pi images for Duet Software Framework come with the [motion](https://github.com/Motion-Project/motion){target=_blank} streaming service installed. A compatible camera can be connected to the Rapsberry Pi using the Raspberry Pi camera connectors or USB, and can then be used through DWC and controlled through the [Motion Webcam Plugin](https://github.com/Duet3D/MotionWebcamServerPlugin/releases){target=_blank}.

Motion is a program that monitors the video signal from one or more cameras and is able to detect if a significant part of the picture has changed. Or in other words, it can detect motion. It is generally used in security cameras. It can also stream the camera view, using a built-in webserver, which is what DWC uses.

### Camera check

When you connect a camera to the Raspberry Pi, it is set up as a device in the /dev folder as a 'video' device. You can check what devices are connected by sending `v4l2-ctl --list-devices` in the Raspberry Pi's terminal.
```
bcm2835-codec-decode (platform:bcm2835-codec):
	/dev/video10
	/dev/video11
	/dev/video12

mmal service 16.1 (platform:bcm2835-v4l2):
	/dev/video0

HP Webcam 3100: HP Webcam 3100 (usb-3f980000.usb-1.5):
	/dev/video1
	/dev/video2
```
In the above, /dev/video0 is a Raspberry Pi Camera, and /dev/video1 is a USB webcam.

### Installation

* Download the .zip file of the plugin without extracting it, using the version that matches the firmware version of your Duet, from [Motion Webcam Plugin](https://github.com/Duet3D/MotionWebcamServerPlugin/releases){target=_blank}. 
* Install the plugin in DWC by going to Settings > Plugins > External plugins, then select 'Install plugins' and upload the .zip file.
* Once installed, click the 'Start' button to start the Plugin.

This plugin runs the Motion webcam streaming service as a DSF plugin. 

More information on configuration parameters can be found on the [Motion documentation pages](https://motion-project.github.io/motion_config.html){target=_blank}.

### Configuration

### Tabs {.tabset}

#### Single camera

##### Motion settings

The Motion configuration file `motion.conf` is created in `0:/sys/motion.conf`, and can be edited in DWC. The default configuration uses the default `/dev/video0` device node. Depending on your choice of camera (see 'Camera check' section above), you may need to change this in `motion.conf`.

The standard configuration streams video using the built-in webserver, and is not set to capture images or video. However, the stream usually runs at 1 frame per second, with a resolution of 1280x720. If you would prefer a higher frame rate and better quality, at the expense of making the Raspberry Pi work a bit harder, you can add the following to motion.conf.

Turn off the motion detection (reduces RPi CPU utilisation by 5-10%):
```
# When Motion is started, pause the motion detection. Default: off
pause on
```
Increase the stream frame rate (usually 1fps!):
```
# Limit the framerate of the stream in frames per second (default 1, max 100)
stream_maxrate 25
```
Adjust the video size by editing the following:
```
# Image width in pixels.
width 1280

# Image height in pixels.
height 720
```
After you make changes to motion.conf, you will need to restart the Motion Plugin to make the settings take effect. Go to Settings > Plugins > External plugins, click 'Stop' to stop the plugin, wait a couple of seconds, then click 'Start' again. Check that your changes have taken effect, by checking the Job > Webcam page.

Reported in 'top' (run from RPi terminal), Motion's CPU utilisation on a RPi4 is around 40-50% for 1280x720 @ 25fps, and around 30% for 800x600 @ 25fps, when the stream is being displayed. It drops to below 10% CPU utilisation when not being displayed.

##### Setup in DWC

To configure this service in DWC, go to the `Settings` -> `General` page and make the following changes:

- Click 'Enable Webcam'
- Set `Webcam URL` to `http://[HOSTNAME]:8081/0/stream` 
- Set `Webcam update interval (in ms)` to `0`
- If you have made changes to motion.conf, restart Motion by going to Settings > Plugins > External plugins, click 'Stop' to stop the plugin, wait a couple of seconds, then click 'Start' again.
- Go to the `Job` -> `Webcam` page to see your live stream

#### Multiple cameras

It is easiest to set up one camera first, to check everything is working, before setting up multiple cameras.

##### Motion settings

To use more than one camera, Motion splits the configuration into a main configuraton named `motion.conf` and a configuration for each camera. For this example, they are called `camera1.conf` and `camera2.conf`. There are all created and stored in `0:/sys/`, and can be edited in DWC.

`motion.conf` stores all parameters that are used by all cameras. This includes the webserver address for all the cameras, the location of the `camera#.conf` configuration files (in the same folder as `motion.conf` in this example), and picture settings that apply to all streams. An example could be:

motion.conf
```
pause on
daemon off
setup_mode off
log_level 3
emulate_motion off
picture_output off
movie_output off

width 800
height 600
framerate 25
stream_maxrate = 25
stream_localhost off

webcontrol_port 8080
webcontrol_localhost off
webcontrol_parms 0
camera /opt/dsf/sd/sys/camera1.conf
camera /opt/dsf/sd/sys/camera2.conf

```
The individual `camera#.conf` files contain information specific to the camera, mainly the device node the stream uses, eg `/dev/video0` or `/dev/video1`. Create these in the /sys folder with `motion.conf` An example could be:

Camera1.conf
```
camera_name CAM-1
camera_id 001
video_device /dev/video0
text_left CAM-1
stream_port 8081
```
Camera2.conf
```
camera_name CAM-2
camera_id 002
video_device /dev/video1
text_left CAM-2
stream_port 8082
```
After you make changes to motion.conf or camera#.conf, you will need to restart the Motion Plugin to make the settings take effect. Go to Settings > Plugins > External plugins, click 'Stop' to stop the plugin, wait a couple of seconds, then click 'Start' again. Check that your changes have taken effect, by checking the Job > Webcam page.

##### Setup in DWC

To configure this service for multiple cameras in DWC, go to the `Settings` -> `General` page and make the following changes:

- Click 'Enable Webcam'
- Set `Webcam URL` to `http://[HOSTNAME]:8080/stream` 
- Set `Webcam update interval (in ms)` to `0`
- Click 'Embed webcam image in an iframe'
- If you have made changes to motion.conf or camera#.conf, restart Motion by going to Settings > Plugins > External plugins, click 'Stop' to stop the plugin, wait a couple of seconds, then click 'Start' again.
- Go to the `Job` -> `Webcam` page to see your live stream

### Build instructions

(Not generally needed if you are using the 'Release' version of the plugin that matches your RRF version.)

Create a ZIP file of every file but `README.md` in this directory and make sure `plugin.json` is at the root level. Once created, the ZIP can be installed as a third-party plugin.

### Logging

Unfortunately the motion service outputs info and warning log messages even if the log level is initially set to not output these, so by default this plugin's configuration suppresses all the log messages and only sends them to the `duetpluginservice` journal log.

#### Checking the journald log (recommended way)

To view the log of the motion service, open a Linux console (or connect over SSH) and run

```
journalctl -u duetpluginservice -f
```

Then restart the Motion Webcam Server plugin and look for potential errors.


#### Modify plugin manifest to see all messages in DWC

To see all the output messages from the motion service directly in DWC, open `plugin.json` and set `sbcOutputRedirected` from `false` to `true`. Then build the plugin again and overwrite the existing installation.
Once the plugin is restarted, all the log messages are written to the DWC console.

#### Increasing the log level

If you need further details, you can reset `log_level` in `motion.conf` from `3` (critical) to `6` (notice). Note that a restart of the plugin is required whenever the config file is modified.
