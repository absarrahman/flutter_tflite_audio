# flutter_tflite_audio

This plugin allows you to use tflite to make audio/speech classifications. Can now support ios and android. The plugin can do the follow tasks:

1. Accept custom models and labels.
2. Run a stream and collect inference results over time
3. Can run the model multiple times, depending on the user's specifications
4. Can manually close the inference and/or recording at the user's discretion.

If there are any problems with the plugin, please do not hesistate to create an issue or request features on github.

![](audio_recognition_example.jpg)


## Limitations of this plugin 

1. This plugin relies on the use of taking in raw audio values as a tensor input. It's assumed that your model already uses mfcc. [For more information](https://www.tensorflow.org/api_docs/python/tf/raw_ops/Mfcc?hl=ja)
2. Can only accept two tensor inputs. float32[16000,1] for raw audio data and int32[1] as the sample rate
3. Can only accept monochannel. Not stereo.

## How to add tflite_audio as a dependency:
1. Add `tflite_audio` as a [dependency in your pubspec.yaml file]


## How to add tflite model and label to flutter:
1. Place your custom tflite model and labels into the asset folder. 
2. In pubsec.yaml, link your tflite model and label under 'assets'. For example:

```
  assets:
    - assets/conv_actions_frozen.tflite
    - assets/conv_actions_labels.txt

```

## How to use this plugin
Please look at the [example](https://github.com/Caldarie/flutter_tflite_audio/tree/master/example) on how to implement these futures.


1. Import the plugin. For example:

```
import 'package:tflite_audio/tflite_audio.dart';
```


2. To load your model:


```dart
 //1. call the the future loadModel()
 //2. assign the appropriate values to the arguments like below:
   TfliteAudio.loadModel(
        model: 'assets/conv_actions_frozen.tflite',
        label: 'assets/conv_actions_labels.txt',
        numThreads: 1,
        isAsset: true);
```


3. To get the results: 

```dart

//1. call the stream startAudioRecognition
//2. assign the appropriate values to the arguments
//3. listen to the stream for data
//4. onDone if you wish to do something after the stream closes
TfliteAudio.startAudioRecognition(
        sampleRate: 16000, 
        recordingLength: 16000, 
        bufferSize: 2200,
        // 1280,
        numOfInferences: 2)
          .listen(
            //Do something here to collect data
          )
          .onDone(
            //Do something here when stream closes
          );

```

4. To forcibly cancel the stream and recognition while executing

```dart
TfliteAudio.stopAudioRecognition();
```

5. For a rough guide on the parameters
  
  * numThreads -  Higher threads will reduce inferenceTime. However, cpu usage will be higher.
  
  * numOfInferences - determines the number of inferences per recording. Will also lengthen recording time as well.

  * sampleRate - determines the number of samples per second

  * recordingLength - determines the max length of the recording buffer. If the value is not below or equal to your tensor input, it will crash.

  * bufferSize - Make sure this value is equal or below your recording length. A very high value may not allow the recording enough time to capture your voice. A lower value will give more time, but it'll be more cpu intensive Remember that this value varies depending on your device.
    


## Android Installation & Permissions
Add the permissions below to your AndroidManifest. This could be found in  <YourApp>/android/app/src folder. For example:

```
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Edit the following below to your build.gradle. This could be found in <YourApp>/app/src/For example:

```
aaptOptions {
        noCompress 'tflite'
```

## iOS Installation & Permissions
1. Add the following key to Info.plist for iOS. This ould be found in <YourApp>/ios/Runner
```
<key>NSMicrophoneUsageDescription</key>
<string>Record audio for playback</string>
```

2. Change the deployment target to at least 12.0. This could be done by:

    a. Open your project workspace on xcode
  
    b. Select root runner on the left panel
  
    c. Under the info tab, change the iOS deployment target to 12.0
    

3. Open your podfile in your iOS folder and change platform ios to 12. Also make sure that use_frameworks! is under runner. For example

```
platform :ios, '12.0'
```

```ruby
target 'Runner' do
  use_frameworks! #Make sure you have this line
  use_modular_headers!

  flutter_install_all_ios_pods File.dirname(File.realpath(__FILE__))
end
```

## References

https://github.com/tensorflow/examples/tree/master/lite/examples/speech_commands
