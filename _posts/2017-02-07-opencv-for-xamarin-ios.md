---
id: 22
title: OpenCV for Xamarin.iOS
description: Getting OpenCV worked on Xamarin.iOS
date: 2017-02-07T15:57:44+00:00
author: trinnguyen
layout: post
permalink: /opencv-for-xamarin-ios/
categories:
  - Xamarin
tags:
  - objective-sharpie
  - opencv
  - xamarin
  - xamarin.ios
---
Updated: 3 years ago I&#8217;ve worked on a Xamarin cross-platforms applications via iOS, Android, Windows Phone 8 which required using OpenCV for face detector and recognition. Even OpenCV has official frameworks for both iOS (ObjC) but it is pretty hard to make fully binding on Xamarin.iOS because most parts of framework are written on C/C++.

Below tutorial is a workaround solution, but effectively, for getting native OpenCV for Xamarin.iOS.

## Overview {#getting-opencv-for-xamarinios}

The idea is creating iOS Static library (CocoaTouch library) as a wrapper for specific functions (by requirements). This wrapper is written on Objective-C and implemented by communicating with OpenCV C++ classes.

Let&#8217;s go through a sample spec: detect faces from any image

* **iOS Static Library**: Simple library with one function: Detect faces on an image with OpenCV, export .a file for using on Xamarin

* **Xamarin.iOS Binding project**: Create a C# wrapper for static library, iOS Application: demo how binding and function of static library, detect faces on an image and draw them with red rectangle on image

## Step by step: OpenCV on Xamarin.iOS {#step-by-step-opencv-on-xamarinios}

### **First, create iOS Static Library**

Should have an Objective-C library for wrapping all features that will work with OpenCV:

  * Download **opencv2.framework** from OpenCV site (http://opencv.org/downloads.html)
  * XCode: Create iOS Static Library project with a Objective-C class that contains required functions (can be multiple classes for management), named **FaceDetector**

{% highlight objc %}

@interface OpenCVUtils : NSObject
+ (cv::Mat)cvMatFromUIImage:(UIImage *)image;
+ (UIImage *)UIImageFromCVMat:(cv::Mat)cvMat;
+ (CGRect) cgRectFromCVRect:(cv::Rect) cvRect;
@end
{% endhighlight %}

{% highlight objc %}
- (id) initWithFaceCascade:(NSString*) filePath;
- (NSArray*) detectFaces:(UIImage*) image;
@end
{% endhighlight %}

  * Build iOS Static Library and get **libFaceDetector.a** file

### Second, Create Xamarin.iOS Binding

  * Xamarin Studio: New Solution -> iOS -> Unified API -> iOS Binding Project
  * Drag **libFaceDetector.a** to Binding project
  * Back to **opencv2.framework** file, open it, go to opencv2.framework -> Versions -> A -> opencv2. Rename this file to opencv2.a. Drag this to Binding project. (This binary file is required as reference from libFaceDetector.a)
  * Open **opencv2.linkwith.cs**, add **LinkerFlags** and **Frameworks**

{% highlight csharp %}
[assembly: LinkWith ("opencv2.a", LinkTarget.ArmV7 | LinkTarget.ArmV7s | LinkTarget.Simulator, ForceLoad = true, LinkerFlags="-lc++", Frameworks="Accelerate AssetsLibrary AVFoundation CoreGraphics CoreImage CoreMedia CoreVideo QuartzCore UIKit Foundation")]
{% endhighlight %}

  * **ApiDefinition.cs** : Define interfaces for working with libFaceDetector.a . Can create manually if experienced with Xamarin.iOS Binding Metadata or using Objective Sharpie (command line tool)

{% highlight objc %}
interface FaceDetector {
 //- (id) initWithFaceCascade:(NSString*) filePath;
 [Export ("initWithFaceCascade:")]
 IntPtr Constructor (string filePath);

 //- (NSArray*) detectFaces:(UIImage*) image;
 [Export ("detectFaces:")]
 NSArray DetectFaces(UIImage image);
}
{% endhighlight %}

  * Create Xamarin.iOS project, add reference to above Xamarin.iOS Binding project, try use C# wrapper classes
  
{% highlight csharp %}

UIImage srcImage = UIImage.FromBundle("lena1");
NSArray arrFaces = _faceDetector.DetectFaces(srcImage);

{% endhighlight %}

### Source code

Get the full source code: <a href="https://github.com/trinnguyen/xamarin.ios-opencv" target="_blank">https://github.com/trinnguyen/xamarin.ios-opencv</a>
