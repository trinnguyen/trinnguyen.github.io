---
id: 101
title: Run Xamarin project from command line
description: Simple shell scripts to get Xamarin project up and running on simulators or devices using MSBuild, mlaunch and adb
date: 2019-09-21T21:13:00+02:00
author: trinnguyen
layout: post
permalink: /xamarin/run-xamarin-project-from-command-line/
categories:
  - Xamarin
tags:
  - xamarin.ios
  - xamarin.android
  - msbuild
  - vscode
---

I feels pleasant working on Xamarin projects with VSCode in parallel with Jetbrains Rider IDE. It's neccessary to find a way to build and run Xamarin.iOS and Xamarin.Android apps using command line and generate those scripts as tasks in VSCode for quick development.

## Run Xamarin.Android project from command line

{% highlight shell %}
Install-Package Xamarin.1PasswordExtension
msbuild /t:Build && msbuild /t:Install && adb shell monkey -p APP_PACKAGE_NAME 1
{% endhighlight %}

### Notes
- It is required to ensure that devices or simulator is already running. Invoke *adb devices* should have some result. Simulator can be run using Android SDK tool *emulator*. Example: *./emulator -avd Pixel_2_29*

- There are built-in MSBuild targets that take care of everything, from building to deploying. Have a look at this docs: [https://docs.microsoft.com/en-us/xamarin/android/deploy-test/building-apps/build-process#build-targets](https://docs.microsoft.com/en-us/xamarin/android/deploy-test/building-apps/build-process#build-targets){:target="_blank"}

{% highlight shell %}
msbuild /t:Build
msbuild /t:Rebuild
msbuild /t:Clean
msbuild /t:Install
msbuild /t:Uninstall
msbuild /t:UpdateAndroidResources
{% endhighlight %}

- *adb shell monkey* is used to launch the installed app
- **UpdateAndroidResources** is useful when having trouble with the generated code in Resources.designer.cs or when editting resources file (i.e. strings.xml)

## Run Xamarin.iOS project from command line

*Example build and run Xamarin.iOS app on iPhone XR, iOS 12.4 simulator*

{% highlight shell %}
msbuild /t:Build && /Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mlaunch --launchsim=bin/iPhoneSimulator/Debug/YOUR_IOS_PROJECT_NAME.app --device::v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-12-4,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-XR
{% endhighlight %}

### Notes
- *msbuild /t:Build* for building the Xamarin.iOS app bundle, output file *YOUR_IOS_PROJECT_NAME.app* in *bin/iPhoneSimulator/Debug* folder. There is no need for generating IPA file during development

- *mlaunch* used for installing and launching the Xamarin.iOS app bundle on device or simulator. This is still closed source maintained by Xamarin team. But reading the help page would help using the tool
*/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mlaunch -h*

### Explain steps to work with mlaunch
- Locate *mlaunch* tool which usually locates at */Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mlaunch*

- Get list of simulators and save the *runtime* and *devicetype* value
{% highlight shell %}
mlaunch --listsim simulators.xml

# simulators.xml is a temporary file that store all available simulators. Select a simulator (i.e iPhone XR), and keep the associated value of *SimRuntime* and *SimDeviceType* for later usage,
{% endhighlight %}

- Construct argument and launch the app

{% highlight shell %}
mlaunch --launchsim=IOS_APP_PATH --device::v2:runtime=SIM_RUNTIME,devicetype=SIM_DEVICE_TYPE

# *IOS_APP_PATH* is the path to *YOUR_IOS_PROJECT_NAME.app* file generated after *msbuild* step. The *--device* argument is constructed using the *SIM_RUNTIME* and *SIM_DEVICE_TYPE* value from the above step.
{% endhighlight %}
    
- The message *Press enter to terminate the application message* will be shown when the app launch on simulator.
