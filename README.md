# React Native Live Streaming App with Video SDK
Live streaming has become one of the most popular ways to share information and connect with audiences in real-time. Whether you're a business owner, a musician or a teacher. Live streaming can be a powerful tool for engaging with your audience to share your message.

Choosing the right live stream platform is the most important decision, as it can impact the quality of your stream, the size of your audience and the overall success of your live stream. Here are a few factors to consider while choosing a live stream platform:

- Features: Look for live stream platforms that offer the features you need to create high-quality streams. For example, you might need a platform that supports multiple camera angles, allows you to share your screen, or allows multiple broadcasters.
- Integration: Integration of a live streaming platform should be simple and quick.
- Budget: Although live streaming platforms are expensive, it is important to choose a platform that fits within your budget.

# Why choose the Video SDK?
Video SDK is a perfect choice for those seeking a live streaming platform that offers the necessary features to create high-quality streams. The platform supports screen sharing, Real-time Messaging, allows broadcasters to invite audience members to the stage and supports 100k+ Participant, ensuring that your live streams are interactive and engaging. With Video SDK, you can also use your own custom designed layout template for live streaming .

In terms of integration, Video SDK offers a simple and quick integration process, allowing you to seamlessly integrate live streaming into your app. This ensures that you can enjoy the benefits of live streaming without any technical difficulties or lengthy implementation processes.

Moreover, Video SDK is [budget-friendly](https://www.videosdk.live/pricing), making it an affordable option for businesses of all sizes. You can enjoy the benefits of a feature-rich live streaming platform without breaking the bank, making it an ideal choice for startups and small businesses.

# 6 Steps to Build a Live Streaming React Native App

The below steps will give you all the information to quickly build interactive live streaming app. Please carefully follow along, and if you have any trouble, let us know right away on [Discord](https://discord.gg/f2WsNDN9S5), and we will be happy to help you.

## Prerequisites

Before proceeding, ensure that your development environment meets the following requirements:

- Video SDK Developer Account (Not having one? follow Video SDK Dashboard)
- Basic understanding of React Native
- Node.js v12+
- NPM v6+ (comes installed with newer Node versions)
- Android Studio or Xcode installed

> One should have a VideoSDK account to generate token. Visit VideoSDK dashboard to generate token

## App Architecture​
This App will contain two screens :

Join Screen : This screen allows SPEAKER to create studio or join predefined studio and VIEWER to join predefined studio.

Speaker Screen : This screen basically contains speaker list and some studio controls such as Enable / Disable Mic & Camera and Leave studio.

Viewer Screen : This screen basically contain live stream player in which viewer will play the stream.
![](https://www.videosdk.live/_next/image?url=https%3A%2F%2Fcdn.videosdk.live%2Fwebsite-resources%2Fdocs-resources%2Fils_app_arch.png&w=3120&q=75)


## Create App​

Create new react-native app by applying below commands.

```js
npx react-native init AppName
```

For react-native setup, you can follow Official Docs.

## Video SDK Installation​

Install the Video SDK by following below command. Do make sure you should be in your project directory before you run this command.
For NPM :

```js
npm install "@videosdk.live/react-native-sdk"
```

For Yarn :

```js
yarn add "@videosdk.live/react-native-sdk"
```

Project Structure :​

```js
root
   ├── node_modules
   ├── android
   ├── ios
   ├── App.js
   ├── api.js
   ├── index.jsCopy

```
## Android Setup

### Step 1: Add the required permission in the AndroidManifest.xml file.

```js
<manifest
  xmlns:android="http://schemas.android.com/apk/res/android"
  package="com.cool.app"
>
    <!-- Give all the required permissions to app -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <!-- Needed to communicate with already-paired Bluetooth devices. (Legacy up to Android 11) -->
    <uses-permission
        android:name="android.permission.BLUETOOTH"
        android:maxSdkVersion="30" />
    <uses-permission
        android:name="android.permission.BLUETOOTH_ADMIN"
        android:maxSdkVersion="30" />

    <!-- Needed to communicate with already-paired Bluetooth devices. (Android 12 upwards)-->
    <uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />

    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
    <uses-permission android:name="android.permission.WAKE_LOCK" />
​
  <application>
   <meta-data
      android:name="live.videosdk.rnfgservice.notification_channel_name"
      android:value="Meeting Notification"
     />
    <meta-data
    android:name="live.videosdk.rnfgservice.notification_channel_description"
    android:value="Whenever meeting started notification will appear."
    />
    <meta-data
    android:name="live.videosdk.rnfgservice.notification_color"
    android:resource="@color/red"
    />
    <service android:name="live.videosdk.rnfgservice.ForegroundService" android:foregroundServiceType="mediaProjection"></service>
    <service android:name="live.videosdk.rnfgservice.ForegroundServiceTask"></service>
  </application>
</manifest>
```

### Step 2: Link couple of internal library dependencies in android/app/build.gradle file

```js
dependencies {
    compile project(':rnfgservice') 
    compile project(':rnwebrtc') 
    compile project(':rnincallmanager')
  }

```
Include dependencies in android/settings.gradle

```js
include ':rnwebrtc'
project(':rnwebrtc').projectDir = new File(rootProject.projectDir, '../node_modules/@videosdk.live/react-native-webrtc/android')

include ':rnincallmanager'
project(':rnincallmanager').projectDir = new File(rootProject.projectDir, '../node_modules/@videosdk.live/react-native-incallmanager/android')

include ':rnfgservice'
project(':rnfgservice').projectDir = new File(rootProject.projectDir, '../node_modules/@videosdk.live/react-native-foreground-service/android')
```
Update MainApplication.java to use InCallManager and run some foreground services.

```js
import live.videosdk.rnfgservice.ForegroundServicePackage;
import live.videosdk.rnincallmanager.InCallManagerPackage;
import live.videosdk.rnwebrtc.WebRTCModulePackage;

public class MainApplication extends Application implements ReactApplication {
  private static List<ReactPackage> getPackages() {
      return Arrays.<ReactPackage>asList(
          /* Initialise foreground service, incall manager and webrtc module */
          new ForegroundServicePackage(),
          new InCallManagerPackage(),
          new WebRTCModulePackage(),
      );
  }
}
```
Some device might face WebRTC problem and to solve that, update your android/gradle.properties file with the following

```js
/* This one fixes a weird WebRTC runtime problem on some devices. */
android.enableDexingArtifactTransform.desugaring=false
```
If you use proguard , make the changes shown below in android/app/proguard-rules.pro file (this is optional)

```js
-keep class org.webrtc.** { *; }
```
### Step 3: Update colors.xml file with some new colours for internal dependencies.

```js
<resources>
    <item name="red" type="color">#FC0303</item>
    <integer-array name="androidcolors">
    <item>@color/red</item>
    </integer-array>
</resources>
```
## iOS Setup

### Step 1: Install react-native-incallmanager

```js
$ yarn add @videosdk.live/react-native-incallmanager
```

### Step 2: Make sure you are using CocoaPods 1.10 or higher. To update CocoaPods, you can simply install the gem again.

```js
$[sudo] gem install cocoapods
```

### Step 3: Manually linking (if react-native-incall-manager is not linked automatically)

- Drag node_modules/@videosdk.live/react-native-incall-manager/ios/RNInCallManager.xcodeproj under <your_xcode_project>/Libraries
- Select <your_xcode_project> --> Build Phases --> Link Binary With Libraries
- Drag Libraries/RNInCallManager.xcodeproj/Products/libRNInCallManager.a to Link Binary With Libraries
- Select <your_xcode_project> --> Build Settings In Header Search Paths, add $(SRCROOT)/../node_modules/@videosdk.live/react-native-incall-manager/ios/RNInCallManager

### Step 4: Change path of react-native-webrtc

```js
pod ‘react-native-webrtc’, :path => ‘../node_modules/@videosdk.live/react-native-webrtc’
```

### Step 5: Change your platform version

- You have change platform field of podfile to 11.0 or above it, as react-native-webrtc doesn’t support IOS < 11 platform :ios, ‘11.0’

### Step 6: After updating the version, you have to install pods

```js
Pod install
```

### Step 7: Then Add “libreact-native-webrtc.a” in Link Binary with libraries. In target of main project folder.

### Step 8: Now add the following permissions to info.plist (project folder/IOS/projectname/info.plist):

```js
<key>NSCameraUsageDescription</key>
<string>Camera permission description</string>
<key>NSMicrophoneUsageDescription</key>
<string>Microphone permission description</string>
```

## Register Service

Register Video SDK services in root index.js file for initialization service.

```js
import { register } from '@videosdk.live/react-native-sdk';
import { AppRegistry } from 'react-native';
import { name as appName } from './app.json';
import App from './src/App.js';
​
// Register the service
register();
AppRegistry.registerComponent(appName, () => App);
```

## Start Writing Your Code Now

### Step 1: Get started with API.js

Before jumping to anything else, we have write API to generate unique meetingId. You will require auth token, you can generate it using either by using videosdk-rtc-api-server-examples or generate it from the Video SDK Dashboard for developer.

```js
// Auth token we will use to generate a meeting and connect to it
export const authToken = "<Generated-from-dashbaord>";

// API call to create meeting
export const createMeeting = async ({ token }) => {
  const res = await fetch(`https://api.videosdk.live/v1/meetings`, {
    method: "POST",
    headers: {
      authorization: `${token}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ region: "sg001" }),
  });

  const { meetingId } = await res.json();
  return meetingId;
};

```

### Step 2: Wireframe App.js with all the components

To build up wireframe of App.js, we are going to use Video SDK Hooks and Context Providers. Video SDK provides MeetingProvider, MeetingConsumer, useMeeting and useParticipant hooks. Let's understand each of them.

First we will explore Context Provider and Consumer. Context is primarily used when some data needs to be accessible by many components at different nesting levels.

- MeetingProvider : It is Context Provider. It accepts value config and token as props. The Provider component accepts a value prop to be passed to consuming components that are descendants of this Provider. One Provider can be connected to many consumers. Providers can be nested to override values deeper within the tree.
- MeetingConsumer : It is Context Consumer. All consumers that are descendants of a Provider will re-render whenever the Provider’s value prop changes.
- useMeeting : It is meeting react hook API for meeting. It includes all the information related to meeting such as join, leave, enable/disable mic or webcam etc.
- useParticipant : It is participant hook API. useParticipant hook is responsible to handle all the events and props related to one particular participant such as name, webcamStream, micStream etc.

Meeting Context helps to listen on all the changes when participant joins meeting or changes mic or camera etc.

Let's get started with change couple of lines of code in App.js

```js
import React, { useState, useMemo, useRef, useEffect } from "react";
import {
  SafeAreaView,
  TouchableOpacity,
  Text,
  TextInput,
  View,
  FlatList,
  Clipboard,
} from "react-native";
import {
  MeetingProvider,
  useMeeting,
  useParticipant,
  MediaStream,
  RTCView,
  Constants,
} from "@videosdk.live/react-native-sdk";
import { createMeeting, authToken } from "./api";

// Responsible for either schedule new meeting or to join existing meeting as a host or as a viewer.
function JoinScreen({ getMeetingAndToken, setMode }) {
  return null;
}

// Responsible for managing participant video stream
function ParticipantView(props) {
  return null;
}

// Responsible for managing meeting controls such as toggle mic / webcam and leave
function Controls() {
  return null;
}

// Responsible for Speaker side view, which contains Meeting Controls(toggle mic/webcam & leave) and Participant list
function SpeakerView() {
  return null;
}

// Responsible for Viewer side view, which contains video player for streaming HLS and managing HLS state (HLS_STARTED, HLS_STOPPING, HLS_STARTING, etc.)
function ViewerView() {
  return null;
}

// Responsible for managing two view (Speaker & Viewer) based on provided mode (`CONFERENCE` & `VIEWER`)
function Container(props) {
  return null;
}

function App() {
  const [meetingId, setMeetingId] = useState(null);

  //State to handle the mode of the participant i.e. CONFERNCE or VIEWER
  const [mode, setMode] = useState("CONFERENCE");

  //Getting MeetingId from the API we created earlier

  const getMeetingAndToken = async (id) => {
    const meetingId =
      id == null ? await createMeeting({ token: authToken }) : id;
    setMeetingId(meetingId);
  };


  return authToken && meetingId ? (
    <MeetingProvider
      config={{
        meetingId,
        micEnabled: true,
        webcamEnabled: true,
        name: "C.V. Raman",
        //These will be the mode of the participant CONFERENCE or VIEWER
        mode: mode,
      }}
      token={authToken}
    >
      <Container />
    </MeetingProvider>
  ) : (
    <JoinScreen getMeetingAndToken={getMeetingAndToken} setMode={setMode} />
  );
}

export default App;
```

### Step 3: Implement Join Screen

Join screen will work as a medium to either schedule new meeting or to join existing meeting as a host or as a viewer.

These will have 3 buttons:

1. Join as Host : When this button is clicked, the person will join the entered meetingId as HOST.
2. Join as Viewer : When this button is clicked, the person will join the entered meetingId as VIEWER.
3. Create Studio Room : When this button is clicked, the person will join a new meeting as HOST.

```js
function JoinScreen({ getMeetingAndToken, setMode }) {
  const [meetingVal, setMeetingVal] = useState("");

  const JoinButton = ({ value, onPress }) => {
    return (
      <TouchableOpacity
        style={{
          backgroundColor: "#1178F8",
          padding: 12,
          marginVertical: 8,
          borderRadius: 6,
        }}
        onPress={onPress}
      >
        <Text style={{ color: "white", alignSelf: "center", fontSize: 18 }}>
          {value}
        </Text>
      </TouchableOpacity>
    );
  };
  return (
    <SafeAreaView
      style={{
        flex: 1,
        backgroundColor: "black",
        justifyContent: "center",
        paddingHorizontal: 6 * 10,
      }}
    >
      <TextInput
        value={meetingVal}
        onChangeText={setMeetingVal}
        placeholder={"XXXX-XXXX-XXXX"}
        placeholderTextColor={"grey"}
        style={{
          padding: 12,
          borderWidth: 1,
          borderColor: "white",
          borderRadius: 6,
          color: "white",
          marginBottom: 16,
        }}
      />
      <JoinButton
        onPress={() => {
          getMeetingAndToken(meetingVal);
        }}
        value={"Join as Host"}
      />
      <JoinButton
        onPress={() => {
          setMode("VIEWER");
          getMeetingAndToken(meetingVal);
        }}
        value={"Join as Viewer"}
      />
      <Text
        style={{
          alignSelf: "center",
          fontSize: 22,
          marginVertical: 16,
          fontStyle: "italic",
          color: "grey",
        }}
      >
        ---------- OR ----------
      </Text>

      <JoinButton
        onPress={() => {
          getMeetingAndToken();
        }}
        value={"Create Studio Room"}
      />
    </SafeAreaView>
  );
}
```

### Step 4: Implement Container Component

- Next step is to create a container that will manage Join screen, SpeakerView and ViewerView component based on mode.
- We will check the mode of the localParticipant, if its CONFERENCE we will show SpeakerView else we will show ViewerView.

```js
function Container() {
  const { join, changeWebcam, localParticipant } = useMeeting({
    onError: (error) => {
      console.log(error.message);
    },
  });

  return (
    <View style={{ flex: 1 }}>
      {localParticipant?.mode == Constants.modes.CONFERENCE ? (
        <SpeakerView />
      ) : localParticipant?.mode == Constants.modes.VIEWER ? (
        <ViewerView />
      ) : (
        <View
          style={{
            flex: 1,
            justifyContent: "center",
            alignItems: "center",
            backgroundColor: "black",
          }}
        >
          <Text style={{ fontSize: 20, color: "white" }}>
            Press Join button to enter studio.
          </Text>
          <Button
            btnStyle={{
              marginTop: 8,
              paddingHorizontal: 22,
              padding: 12,
              borderWidth: 1,
              borderColor: "white",
              borderRadius: 8,
            }}
            buttonText={"Join"}
            onPress={() => {
              join();
              setTimeout(() => {
                changeWebcam();
              }, 300);
            }}
          />
        </View>
      )}
    </View>
  );
}

// Common Component which will also be used in Controls Component
const Button = ({ onPress, buttonText, backgroundColor, btnStyle }) => {
  return (
    <TouchableOpacity
      onPress={onPress}
      style={{
        ...btnStyle,
        backgroundColor: backgroundColor,
        padding: 10,
        borderRadius: 8,
      }}
    >
      <Text style={{ color: "white", fontSize: 12 }}>{buttonText}</Text>
    </TouchableOpacity>
  );
};
```

### Step 5: Implement SpeakerView

Next step is to create SpeakerView and Controls components to manage features such as join, leave, mute and unmute.

1. We will get all the participants from useMeeting hook and filter them for the mode set to CONFERENCE so only Speakers are shown on the screen.

```js
function SpeakerView() {
  // Get the Participant Map and meetingId
  const { meetingId, participants } = useMeeting({});

  // For getting speaker participant, we will filter out `CONFERENCE` mode participant
  const speakers = useMemo(() => {
    const speakerParticipants = [...participants.values()].filter(
      (participant) => {
        return participant.mode == Constants.modes.CONFERENCE;
      }
    );
    return speakerParticipants;
  }, [participants]);

  return (
    <SafeAreaView style={{ backgroundColor: "black", flex: 1 }}>
      {/* Render Header for copy meetingId and leave meeting*/}
      <HeaderView />

      {/* Render Participant List */}
      {speakers.length > 0 ? (
        <FlatList
          data={speakers}
          renderItem={({ item }) => {
            return <ParticipantView participantId={item.id} />;
          }}
        />
      ) : null}

      {/* Render Controls */}
      <Controls />
    </SafeAreaView>
  );
}

function HeaderView() {
  const { meetingId, leave } = useMeeting();
  return (
    <View
      style={{
        flexDirection: "row",
        padding: 16,
        justifyContent: "space-evenly",
        alignItems: "center",
      }}
    >
      <Text style={{ fontSize: 24, color: "white" }}>{meetingId}</Text>
      <Button
        btnStyle={{
          borderWidth: 1,
          borderColor: "white",
        }}
        onPress={() => {
          Clipboard.setString(meetingId);
          alert("MeetingId copied successfully");
        }}
        buttonText={"Copy MeetingId"}
        backgroundColor={"transparent"}
      />
      <Button
        onPress={() => {
          leave();
        }}
        buttonText={"Leave"}
        backgroundColor={"#FF0000"}
      />
    </View>
  );
}

function Container(){
  ...

  const mMeeting = useMeeting({
    onMeetingJoined: () => {
      // We will pin the local participant if he joins in CONFERENCE mode
      if (mMeetingRef.current.localParticipant.mode == "CONFERENCE") {
        mMeetingRef.current.localParticipant.pin();
      }
    },
    ...
  });

  // We will create a ref to meeting object so that when used inside the
  // Callback functions, meeting state is maintained
  const mMeetingRef = useRef(mMeeting);
  useEffect(() => {
    mMeetingRef.current = mMeeting;
  }, [mMeeting]);

  return <>...</>;
}
```

2. We will be creating the ParticipantView to show the participants media. For which, will be using the webcamStream from the useParticipant hook to play the media of the participant.

```js
function ParticipantView({ participantId }) {
  const { webcamStream, webcamOn } = useParticipant(participantId);
  return webcamOn && webcamStream ? (
    <RTCView
      streamURL={new MediaStream([webcamStream.track]).toURL()}
      objectFit={"cover"}
      style={{
        height: 300,
        marginVertical: 8,
        marginHorizontal: 8,
      }}
    />
  ) : (
    <View
      style={{
        backgroundColor: "grey",
        height: 300,
        justifyContent: "center",
        alignItems: "center",
        marginVertical: 8,
        marginHorizontal: 8,
      }}
    >
      <Text style={{ fontSize: 16 }}>NO MEDIA</Text>
    </View>
  );
}
```

3. We will add the Controls component which will allow the speaker to toggle media and start / stop HLS.

```js
function Controls() {
  const { toggleWebcam, toggleMic, startHls, stopHls, hlsState } = useMeeting(
    {}
  );

  const _handleHLS = async () => {
    if (!hlsState || hlsState === "HLS_STOPPED") {
      startHls({
        layout: {
          type: "SPOTLIGHT",
          priority: "PIN",
          gridSize: 4,
        },
        theme: "DARK",
        orientation: "portrait",
      });
    } else if (hlsState === "HLS_STARTED" || hlsState === "HLS_PLAYABLE") {
      stopHls();
    }
  };

  return (
    <View
      style={{
        padding: 24,
        flexDirection: "row",
        justifyContent: "space-between",
      }}
    >
      <Button
        onPress={() => {
          toggleWebcam();
        }}
        buttonText={"Toggle Webcam"}
        backgroundColor={"#1178F8"}
      />
      <Button
        onPress={() => {
          toggleMic();
        }}
        buttonText={"Toggle Mic"}
        backgroundColor={"#1178F8"}
      />
      {hlsState === "HLS_STARTED" ||
      hlsState === "HLS_STOPPING" ||
      hlsState === "HLS_STARTING" ||
      hlsState === "HLS_PLAYABLE" ? (
        <Button
          onPress={() => {
            _handleHLS();
          }}
          buttonText={
            hlsState === "HLS_STARTED"
              ? `Live Starting`
              : hlsState === "HLS_STOPPING"
              ? `Live Stopping`
              : hlsState === "HLS_PLAYABLE"
              ? `Stop Live`
              : `Loading...`
          }
          backgroundColor={"#FF5D5D"}
        />
      ) : (
        <Button
          onPress={() => {
            _handleHLS();
          }}
          buttonText={`Go Live`}
          backgroundColor={"#1178F8"}
        />
      )}
    </View>
  );
}
```

### Step 6: Implement ViewerView

When HOST (`CONFERENCE` mode participant) starts the live streaming, the viewer will be able to see the live streaming.

To implement the player view, we are going to use react-native-video. It will be helpful to play the HLS stream.

Let's first add this package.

For NPM :

```js
npm install react-native-video
```

For Yarn :

```js
yarn add react-native-video
```

With react-native-video installed, we will get the hlsUrls and isHlsPlayable from the useMeeting hook which will be used to play the HLS in the player.

```js
//Add imports
// imports react-native-video
import Video from "react-native-video";

function ViewerView({}) {
  const { hlsState, hlsUrls } = useMeeting();

  return (
    <SafeAreaView style={{ flex: 1, backgroundColor: "black" }}>
      {hlsState == "HLS_PLAYABLE" ? (
        <>
          {/* Render Header for copy meetingId and leave meeting*/}
          <HeaderView />

          {/* Render VideoPlayer that will play `downstreamUrl`*/}
          <Video
            controls={true}
            source={{
              uri: hlsUrls.downstreamUrl,
            }}
            resizeMode={"stretch"}
            style={{
              flex: 1,
              backgroundColor: "black",
            }}
            onError={(e) => console.log("error", e)}
          />
        </>
      ) : (
        <SafeAreaView
          style={{ flex: 1, justifyContent: "center", alignItems: "center" }}
        >
          <Text style={{ fontSize: 20, color: "white" }}>
            HLS is not started yet or is stopped
          </Text>
        </SafeAreaView>
      )}
    </SafeAreaView>
  );
}
```

## Run Your Code Now

```js
//for android
npx react-native run-android

//for ios
npx react-native run-ios

```

> Stuck anywhere? Check out this example code on GitHub

![](https://www.videosdk.live/_next/image?url=https%3A%2F%2Fassets.videosdk.live%2Fstatic-assets%2Fghost%2F2023%2F05%2FUntitled-design.gif&w=2048&q=75)

# Conclusion

With this, we have successfully built the React Native Live Streaming app using the video SDK. If you wish to add functionalities like chat messaging, and screen sharing, you can always check out our [documentation](https://docs.videosdk.live/). If you face any difficulty with the implementation, You can connect with us on our [discord community](https://discord.gg/Gpmj6eCq5u).

# Resources

- [React Interactive Live Streaming with Video SDK - YouTube](https://www.youtube.com/watch?v=L1x7wtH-ok8)
- [Build a React Native Video Calling App with Video SDK](https://www.videosdk.live/blog/how-to-make-a-video-calling-app-using-react-native)
- [Build a React Native Android Video Calling App with 📞 Callkeep using 🔥 Firebase and Video SDK
](https://www.videosdk.live/blog/react-native-android-video-calling-app-with-callkeep)
- [React Native Group Video Calling App Tutorial - YouTube](https://youtu.be/pqg1y3eRyK4)
- [How to Build React Native IOS Video Call app using CallKeep using Firebase and Video SDK Part-2](https://www.videosdk.live/blog/react-native-ios-video-calling-app-with-callkeep/)
