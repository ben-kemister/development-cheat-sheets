---
title: Camera Module (Raspberry Pi)
tags:
- raspberry_pi
- camera
- timelapse
---

Adding a camera to a Raspberry Pi opens up a  new category of potential Raspberry Pi projects.
Popular projects include time-lapse photography, face recognition, DIY CCTV, pet and 3D printer monitors, car cams and more.
<!--more-->

## Using or accessing the Camera

[Picamera](https://picamera.readthedocs.io/en/latest/index.html) is a great Python package which provides an interface
to the Raspberry Pi camera module for Python 2.7 (or above) or Python 3.2 (or above).

## Timelapse

### Links

* [Raspberry Pi Zero W as a headless time-lapse camera](https://www.jeffgeerling.com/blog/2017/raspberry-pi-zero-w-headless-time-lapse-camera)
    * [Raspberry Pi Time-Lapse App - Github](https://github.com/geerlingguy/pi-timelapse/tree/master)
* [How to Shoot Time-Lapse Videos with Raspberry Pi](https://www.tomshardware.com/how-to/raspberry-pi-time-lapse-video)
* [Raspberry Pi Time-Lapse in Four Easy Steps](https://pimylifeup.com/raspberry-pi-time-lapse/)

## Links

* [About the Camera Modules - Raspberry Pi Documentation](https://www.raspberrypi.com/documentation/accessories/camera.html#about-the-camera-modules)
* [Cameras Compared for Raspberry Pi](https://core-electronics.com.au/guides/raspberry-pi/camera-raspberry-pi/#Over)
* [Raspberry Pi - High Quality Camera Product Brief](https://core-electronics.com.au/attachments/uploads/hq-camera-product-brief.pdf)
* [Adjust Raspberry Pi camera focus to fix the blurry issue](https://zpjiang.me/2020/05/28/picamera-adjust-focus/)

## Camera & Photography Terms

### [ISO](https://photographylife.com/what-is-iso-in-photography)

In very basic terms, ISO is simply a camera setting that will brighten or darken a photo. 
As you increase your ISO number, your photos will grow progressively brighter. 
For that reason, ISO can help you capture images in darker environments.

A photo taken at too high of an ISO will show a lot of grain, also known as noise, and might not be usable. 
So, brightening a photo via ISO is always a trade-off. 
You should only raise your ISO when you are unable to brighten the photo via shutter speed or aperture.

### [Shutter Speed](https://photographylife.com/what-is-shutter-speed-in-photography)

Shutter speed is the length of time the camera shutter is open, exposing light onto the camera sensor. 
Essentially, it’s how long your camera spends taking a photo.

When you use a long shutter speed (also known as a “slow” shutter speed), you end up exposing your sensor for a significant period of time. 
The first big effect of it is motion blur. 
If your shutter speed is long, moving subjects in your photo will appear blurred.

The other big effect of shutter speed is on exposure, which relates to the brightness of an image. 
If you use a long shutter speed, your camera sensor gathers a lot of light, and the resulting photo will be quite bright.

### [Dynamic Range Compression](https://rawpedia.rawtherapee.com/Dynamic_Range_Compression)

_Dynamic range_ is the ratio of the largest to the smallest value of a measured signal. 
In photography it commonly refers to the ratio of the brightest element of a scene to the darkest. 
An outdoor scene on a very foggy day commonly has very little difference between the brightest and darkest elements, 
which is known as a low dynamic range scene. In contrast, an indoor scene with a visible sunny sky through a window is known as a high dynamic range scene.

The dynamic range of a scene can easily exceed the dynamic range of the "sensor" that captures the scene.
Photography and image processing needs to deal with mapping high dynamic ranges to lower ones. 
This is done by either discarding some of the data outside the range or by using compression.
