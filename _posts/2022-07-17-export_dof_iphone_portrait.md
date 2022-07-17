---
layout: post
title: Extracting Depth of Field JPG from iPhone portraits
description: Export DOF information captured from iPhone portrait mode as a JPG.
--- 

1. Placeholder TOC
{:toc}

## Overview
 Humans percieve depth using binocular vision i.e in the overlapping space that both the eyes observe, our brains can estimate/perceive how far the objects are based on the difference in postion that each eye sees the object at. This is [stereopsis](https://en.wikipedia.org/wiki/Stereopsis).

This effect can be replicated by using a dual camera system to understand how far objects in a scene are located relative to the observer i.e an estimate of which objects are closer to the camera and which ones are further away.[^1]

Modern day smartphones make use of this technique to identify and blur the background objects while keeping the subject in focus, resulting in a depth effect on the final image.

![Full width image](https://support.apple.com/library/content/dam/edam/applecare/images/en_US/ios/iphoto/ios15-iphone12-pro-camera-portrait-mode.png){:width="250px" align="center"}
{:.centered}

Portrait mode on an iPhone
{:.figcaption}

The depth information should be stored as an additional image in the JPG file[^3]. In case of HEIC images it would be stored in the metadata.

So now, the plan is to extract the depth information itself from the captured image. We'll use [Photopea](https://photopea.com) to quickly check if the exported image has depth information. For reference, Photopea shows the depth information as an additional layer.

![Full width image](/assets/posts/images/photopea_example.png)
{:.centered}

## What doesn't work

Images captured by the iPhone's camera app by default are saved as HEIC files. Converting the HEIC to a JPG online or using the Preview utility on macOS doesn't seem to preserve the depth information.

It doesn't work even after setting the sharing options while moving the HEIC image as "All Data".

## What works

### Directly capturing images as JPG's

You can change the camera app settings to save images as JPG's. Go to `Settings` > `Camera` > `Formats` > `Camera Capture` and set it to `Most Compatible`.

![Full width image](/assets/posts/images/camera_settings1.gif){:width="250px" align="center"}
{:.centered}

Changing the camera capture format
{:.figcaption}

### Copying images from Photos to Files

If you already have images captured in Portrait mode, you can export them to JPEG/JPG on your iPhone first by making a copy to the Files app.

To do this, simply long press on the picture in your Photos, select `copy`. Now open `Files`, go to your desired folder and long press to bring up the options. Select `paste` and the saved image should be in the JPEG format.

![Full width image](/assets/posts/images/jpg_depth_map2.gif){:width="250px" align="center"}
{:.centered}

Saving a photo as a JPEG
{:.figcaption}

## Final Result

The final image and its depth map visualized on Photopea:-

![Alt text](/assets/posts/images/portrait_example.jpg){:width="250px" align="center"}
{:.centered}

The original image
{:.figcaption}


![Alt text](/assets/posts/images/portrait_depth.png){:width="250px" align="center"}
{:.centered}

The depth map
{:.figcaption}

The depth map is a grayscale image where the brightness corresponds inversely to the distance from the camera. So the brightest objects are the closest to the camera.

Strictly speaking this would be called a disparity map where disparity is inversely related to distance from the camera. 

>A depth map describes at each pixel the distance to an object, in meters.

>A disparity map describes normalized shift values for use in comparing two images. The value for each pixel in the map is in units of 1/meters: (pixelShift / (pixelFocalLength * baselineInMeters)).[^4]

In an upcoming blog post let's check out how this info can be read directly using Swift and we'll look at some interesting applications of this.

[^1]:[Depth estimation basics and intuition by Daryl Tan - Medium](https://towardsdatascience.com/depth-estimation-1-basics-and-intuition-86f2c9538cd1)

[^3]:[Image Depth Maps Tutorial for iOS - Ray Wenderlich](https://www.raywenderlich.com/8246240-image-depth-maps-tutorial-for-ios-getting-started#toc-anchor-011)

[^4]:[`AVDepthData` Apple Developer Docs](https://developer.apple.com/documentation/avfoundation/avdepthdata)