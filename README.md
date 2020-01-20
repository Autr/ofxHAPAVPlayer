# ofxHAPAVPlayer

MacOS only, OF 0.10.0 / 0.11.0

## Fork Notes

I forked this over [ofxHapPlayer](https://github.com/bangnoise/ofxHapPlayer) as it will handle other AVFoundation media types. The player is threaded, and the results much faster than the default ofVideoPlayer on MacOS, with real-time scrubbing of H264 encoded videos.

As per the original README, I'm slowly adding in missing methods as I put it to use. For a _methodless_ implementation, using ofParameters to control playback, speed, timeline, check out [ofxFlex](https://github.com/Autr/ofxFlex).


**XCode**

The addon will work out-the-box with the latest project generator. To make the release app portable to other machines, you must link HapInAVFoundation.framework inside the build, by adding this line to the Build Phases > Run Script:

```
install_name_tool -change @rpath/HapInAVFoundation.framework/Versions/A/HapInAVFoundation @executable_path/../Frameworks/HapInAVFoundation.framework/Versions/A/HapInAVFoundation “$TARGET_BUILD_DIR/$PRODUCT_NAME.app/Contents/MacOS/$PRODUCT_NAME”;
```

**QT Creator**

Currently `.mm` files must be added manually to the .qbs project file to use this addon. For a portable release version, it makes more sense to use XCode (script + code signing etc).


## Original README

A 64bit AVFoundation video player supporting the HAP codec in openFrameworks.

Please note this is a work in progress. Most of the basic functionality of an ofVideoPlayer has been implemented, eg., getWidth, getHeight, play, pause, setFrame, getFrame etc but there are still some gaps - notably no getPixels and no set/getVolume.

Emphasis has been put on making the player truly block-free on loading - it's aimed at installations and real-time AV software that needs fast, non-block loading and can handle a *lot* of video.

On Mac OSX it is intended to become a replacement for ofxThreadedVideo: https://github.com/gameoverhack/ofxThreadedVideo

On my MacBook Pro (Early 2013, El Capitan 10.11.5) I can get approximately 200+ 100 x 100 videos or 15+ 1920 x 1080 videos playing smoothly whilst still loading a new video and seeking to a random frame every 40 milliseconds.

To encode your video with HAP download the codec from: https://github.com/vidvox/hap-qt-codec/releases/

This addon was originally initiated by Joshua Batty: https://github.com/JoshuaBatty/ and is based on examples from https://github.com/Vidvox/hap-in-avfoundation

Known issues:

* When running applications with instances of ofxHAPAVPlayer inside XCode both Activity Monitor and XCode report memory (ever) increasing on (re)loading and construction/destruction. This appears to be a bug in Xcode as applications run outside of Xcode do not have this 'psuedo' memory leak. eg., try running the examples inside Xcode and then run them just as an application outside of Xcode.

* Seeking to an exact frame may fail depending on video/codec. This means that stepping through frame-by-frame on a paused video will most likely not work as expected - I've implemented some code that will skip ahead or back frames if a video fails to go to the correct frame, but this is still not 'expected' behaiviour. Seeking to a frame while a playing video is fine - it just might not go to *exactly* the frame you request.

ofxHAPAVPlayer can be added to your project by using the project generator.
Once the project is generated you will need to add the following entries to the *Runpath Search Paths* in your *Build Settings*:

> @executable_path/../Frameworks
> @loader_path/../Frameworks

Only tested in 10.11.5 but should work in 10.10.+
