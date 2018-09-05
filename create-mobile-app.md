## Create a Mobile App with Flutter
As we advanced in the introduction of these labs our aim is to build a mobile app using [Flutter](https://flutter.io), so before we get dirty with the code let's explain a little more about this framework.

#### What's Flutter?
In short, Flutter is a multiplatform framework (iOS and Android) that takes a different approach to build apps for multiple platforms. Although it's not correct defining something saying what it's no, let's start by saying that It's not based on Cordova or any other HMTL/Javascript to native bridge, it doesn't use Javascript to interact with native components as React Native does and it doesn't generate native code (Objective-C, Swift, Android, ...) from another language.

**So, how does it work?**

In simple/rough terms, you code your app using [Dart](https://dart.io), and Flutter takes your code and compiles it (along with the UI components) and generates a binary for iOS or Android.

Go [here](https://hackernoon.com/whats-revolutionary-about-flutter-946915b09514) for a nice article about what Flutter is.

#### What's Dart?
Dart is a OO language, similar to others, Java, C++, C#, TypeScript, ... it supports asynchronous programming through language features and APIs that use Future and Stream objects, and more importantly Dart can be:

* Ahead-of-time (AOT) compiled to fast, predictable, native code,  or 
* Just-in-time (JIT) compiled for fast development cycles and debugging.

More on Dart and why Flutter uses it [here](https://hackernoon.com/why-flutter-uses-dart-dd635a054ebf).

#### Creating a blank app and testing it
Before we dive into our app's code let's create a blank application and test if everything works properly.

~~~shell
$ flutter create blank_app
...
Run "flutter doctor" for information about installing additional components.

All done! In order to run your application, type:

  $ cd blank_app
  $ flutter run

Your main program file is lib/main.dart in the gramola_app directory.
~~~

In order to run our blank app you have to either run an emulator or connect a real device to your computer. In order to see which devices (real/virtual) are ready to deploy run this command.

~~~shell
$ flutter devices
2 connected devices:

Android SDK built for x86 • emulator-5554                        • android-x86 • Android 8.0.0 (API 26) (emulator)
iPhone X                  • 2E729265-625D-488A-B41B-BAFEB523F2B4 • ios         • iOS 11.2 (simulator)
~~~

As you can see, we have two devices, one is an iOS simulator running iOS 11.2 and the other one is an Android virtual device (AVD) runnint API 26.

Now it's time to run our blank app, let's do it in the Android virtual device (as you can see the flag -d allows us select a given device).

~~~shell
$ flutter run -d emulator-5554
Using hardware rendering with device Android SDK built for x86. If you get graphics artifacts, consider enabling software rendering with "--enable-software-rendering".
...
🔥  To hot reload changes while running, press "r". To hot restart (and rebuild state), press "R".
An Observatory debugger and profiler on Android SDK built for x86 is available at: http://127.0.0.1:8100/
For a more detailed help message, press "h". To quit, press "q".
~~~

If everything worked as expected you should see something similar to this picture.

![Flutter blank app]({% image_path blank-app-android.png %}){:width="300px"}

As you can see 'flutter run' allows us to hot reload changes, open the IDE you chose during the Flutter installation process and try to change something in 'lib/main.dart' and type 'r' in the terminal window you should see the change happening immediatly. (Below the UI after changing the title from home: new 'Flutter Demo Home Page' to 'Gramola App')

![Flutter blank app]({% image_path blank-app-android-change.png %}){:width="300px"}

#### Clonning mobile app phase 1 code
If you remember from the project introduction the plan was to develop an app (phase 1) which would eventually need a new feature (phase 2).
To make it easier we have prepared the code for 'phase 1' in a branch named, guess how? phase_1
The code is [here]({{MOBILE_APP_GIT_URL}}) and phase 1 branch can be found [here]({{MOBILE_APP_GIT_URL}}/tree/phase_1).

Let's clone our clode and check out branch 'phase_1' in a new terminal.

~~~shell
$ mkdir gramola_phase_1 ; cd gramola_phase_1
$ git clone {{MOBILE_APP_GIT_URL}}
$ cd {{MOBILE_APP_REPO_NAME}}
$ git checkout phase_1
$ git branch
  master
* phase_1
~~~

Before we run our code, we have to point our code to the right API URLs. To do so, please open file './data/connections.json' and change URLs to match the routes in our Openshift project.

Let's have a look to our routes.

~~~shell
$ oc get route
NAME      HOST/PORT                                        PATH      SERVICES   PORT      TERMINATION   WILDCARD
{{EVENTS_SERVICE_NAME}}    {{EVENTS_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}             {{EVENTS_SERVICE_NAME}}     8080                    None
{{FILES_SERVICE_NAME}}     {{FILES_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}              {{FILES_SERVICE_NAME}}      8080                    None
~~~

According to the routes update ' ./data/connections.json' with these values accordingly:


~~~json
{
    "loginApi" : "http://demo2703148.mockable.io/api/login",
    "eventsApi" : "http://{{EVENTS_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}/api/events",
    "imagesApi" : "http://{{FILES_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}/api/files"
}
~~~

> Login API: this can be "dummy" or a real URL that checks user/password, for the sake of simplicity set to "dummy" or just delete it, as in the previous example.

Now let's run our app, later will explain the code.

~~~shell
$ flutter devices
2 connected devices:

Android SDK built for x86 • emulator-5554                            • android-x86 • Android 8.0.0 (API 26) (emulator)
iPhone X                  • 2E729265-625D-488A-B41B-BAFEB523F2B4     • ios         • iOS 11.2 (simulator)

$ flutter run -d emulator-5554
Running "flutter packages get" in gramola-app...             0.7s
...
🔥  To hot reload changes while running, press "r". To hot restart (and rebuild state), press "R".
An Observatory debugger and profiler on Android SDK built for x86 is available at: http://127.0.0.1:8100/
For a more detailed help message, press "h". To quit, press "q".
~~~

Login Screen (login API is mocked up even though the code is ready to invoke a real one). Type any user/password, the mocked up user will be 'trever'.

![Login Screen]({% image_path phase-1-login.png %}){:width="300px"}

Events Screen by default shows all events. 

![Events Screen]({% image_path phase-1-all-events.png %}){:width="300px"}

To filter by city please tap lower right corner button.

![Choose city]({% image_path phase-1-choose-city.png %}){:width="300px"}

Result when filtering by city == 'MADRID'.

![Choose city]({% image_path phase-1-city-Madrid.png %}){:width="300px"}

Well done! You've completed all labs.
