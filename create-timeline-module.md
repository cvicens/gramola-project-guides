## Create the Timeline Module with Flutter
Flutter provides means to make your apps modular easily. Here we're going to create a module (or Flutter [package](https://flutter.io/developing-packages/#dart)) to deal with our brand new Timeline API and an example app to test our module before using it in our mobile app.

#### Creating a blank module

Open a new terminal and run the following command.

~~~shell
$ flutter create --template=package hello
Creating project hello...
...
Running "flutter packages get" in hello...                   9.1s
Wrote 11 files.

Your package code is in lib/hello.dart in the hello directory.
~~~

Now let's create an example app inside the hello package.

~~~shell
$ cd hello
$ flutter create example
Creating project example...
  example/ios/Runner.xcworkspace/contents.xcworkspacedata (created)
...
Wrote 65 files.

Running "flutter packages get" in example...                 8.6s
...
All done! In order to run your application, type:

  $ cd example
  $ flutter run

Your main program file is lib/main.dart in the example directory.
~~~

Now it's time to test our module from the example app.

##### Testing our blank module

Open the file './example/pubspec.yaml' and modify the 'dependencies' area to add our hello package dependency as in the next snippet.

> Note: make sure you're inside the 'hello' package folder.

~~~yaml
...
dependencies:
  flutter:
    sdk: flutter

  # The following adds the Cupertino Icons font to your application.
  # Use with the CupertinoIcons class for iOS style icons.
  cupertino_icons: ^0.1.2

  # Hello package
  hello:
    path: ../
    
dev_dependencies:
  flutter_test:
    sdk: flutter
~~~

Now let's modify function _incrementCounter() in file './lib/main.dart'.

> Add this import statement at the top:  **import 'package:hello/hello.dart';**

~~~java
void _incrementCounter() {
    setState(() {
      // This call to setState tells the Flutter framework that something has
      // changed in this State, which causes it to rerun the build method below
      // so that the display can reflect the updated values. If we changed
      // _counter without calling setState(), then the build method would not be
      // called again, and so nothing would appear to happen.
      _counter = _calculator.addOne(_counter);
    });
  }
~~~

Now make sure either of iOS/Android simulators is up and running. And run this command to list their devices IDs.

~~~shell
$ flutter devices
2 connected devices:

Android SDK built for x86 • emulator-5554                            • android-x86 • Android 8.0.0 (API 26) (emulator)
cv_i8                     • bce4eded7287eb6cc2a551d67b94f21fbbbae691 • ios         • iOS 11.3
~~~

Let's run our example app.

~~~shell
$ cd example
$ flutter run -d emulator-5554
Using hardware rendering with device Android SDK built for x86. If you get graphics artifacts, consider enabling software rendering with "--enable-software-rendering".
...
🔥  To hot reload changes while running, press "r". To hot restart (and rebuild state), press "R".
An Observatory debugger and profiler on Android SDK built for x86 is available at: http://127.0.0.1:8100/
For a more detailed help message, press "h". To quit, press "q".
~~~

![Hello package test 1]({% image_path hello-package-1.png %}){:width="300px"}

![Hello package test 2]({% image_path hello-package-2.png %}){:width="300px"}

#### Cloning the Timeline module and testing
This module has been conceived so that there's no need for configuration files, all needed information (as an instance of TimelineConfiguration) is received via the component constructor.

~~~javascript
EventTimelineComponent(TimelineConfiguration configuration)
~~~

Class TimelineConfiguration is as follows:

~~~javascript
class TimelineConfiguration {
  final String eventId;
  final String userId;
  final String timelineApi;
  final String imagesBaseUrl;

  const TimelineConfiguration({this.eventId, this.userId, this.timelineApi, this.imagesBaseUrl});
}
~~~

This way 

For the sake of simplicity we're going to clone an already working version of the timeline module, later we'll highlight the relevant parts of the code.

~~~shell
$ git clone --branch {{GRAMOLA_TIMELINE_VERSION}} {{GRAMOLA_TIMELINE_GIT_URL}}
~~~

As in our blank test, there's an example application we can use to test the module (Flutter package). Before running the test we have to make a little change in the code of the example app to point to the right Timeline and Files API endpoints.

> **Note:** If you open the code in a Dart editor you'll probably see errors, this is probably because packages referred to in 'pubspec.yaml' haven't been downloaded. These error will disapear when we execute the app with `flutter run` or if you run the following command.
> 
> ~~~shell
> $ flutter packages get
> Running "flutter packages get" in gramola-timeline...        0.6s
> Running "flutter packages get" in example...                 0.7s
> ~~~

Please open **'./example/lib/main.dart'** and change the code to match the actual URLs of Timeline and Files services.

> **Note:** Remember, to get the URLs of these services you can run `oc get route`.

Example app code after changes given these URLs:

* **Timeline:**  {{TIMELINE_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}
* **Files:**  {{FILES_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}

~~~javascript
new MaterialPageRoute(
  builder: (context) => new EventTimelineComponent(
    new TimelineConfiguration(
      eventId: _eventIdFieldController.text, 
      userId: _userIdFieldController.text, 
      timelineApi: 'http://{{TIMELINE_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}/api/timeline',
      imagesBaseUrl: 'http://{{FILES_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}/api/files'
    )
  )
),
~~~

Now, let's run the example app to test the Timeline module.

~~~shell
$ cd example
$ flutter devices
1 connected devices:

Android SDK built for x86 • emulator-5554 • android-x86 • Android 8.0.0 (API 26) (emulator)
$ flutter run -d emulator-5554
~~~

Example app UI to enter Event ID and User ID.

![Timeline package test 1]({% image_path timeline-package-1.png %}){:width="300px"}

Result for **Event ID: 1** and **User ID: trever**

![Timeline package test 2]({% image_path timeline-package-2.png %}){:width="300px"}

So, so far we have created a module/package capable of using the Timeline API, then it's time to move on.

#### Adding the new module to our mobile app (phase 2)
Now it's time to try the same module from the real mobile app itself. As we did before, for the sake of saving time we have prepared the code for 'phase 2' in a branch named, phase_2, no surprises here I hope ;-)
The code is [here]({{MOBILE_APP_GIT_URL}}) and phase 2 branch can be found [here]({{MOBILE_APP_GIT_URL}}/tree/phase_2).

Let's clone our clode and check out branch 'phase_2' in a new terminal.

> **Note:** You can always use the same folder for the different branches of a git repo, but during the labs, it maybe easier to compare versions if in different folders.

~~~shell
$ mkdir gramola_phase_2 ; cd gramola_phase_2
$ git clone {{MOBILE_APP_GIT_URL}}
$ cd {{MOBILE_APP_REPO_NAME}}
$ git checkout phase_2
$ git branch
  master
* phase_2
~~~

Before we run our code, we have to point our code to the right API URLs. To do so, please open file './data/connections.json' and change URLs to match the routes in our Openshift project.

Let's have a look to our routes.

~~~shell
$ oc get route
NAME      HOST/PORT                                        PATH      SERVICES   PORT      TERMINATION   WILDCARD
{{EVENTS_SERVICE_NAME}}    {{EVENTS_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}             {{EVENTS_SERVICE_NAME}}     8080                    None
{{FILES_SERVICE_NAME}}     {{FILES_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}              {{FILES_SERVICE_NAME}}      8080                    None
{{TIMELINE_SERVICE_NAME}}     {{TIMELINE_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}              {{TIMELINE_SERVICE_NAME}}      8080                    None
~~~

According to the routes update **' ./data/connections.json'** with these values accordingly:

~~~json
{
    "eventsApi" : "http://{{EVENTS_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}/api/events",
    "imagesApi" : "http://{{FILES_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}/api/files",
    "timelineApi" : "http://{{TIMELINE_SERVICE_NAME}}-{{PROJECT_NAME}}.{{BASE_APPS_HOST}}/api/timeline"
}
~~~

> Login API: this can be "dummy" or a real URL that checks user/password, for the sake of simplicity set to "dummy" or just delete it, as in the previous example (phase_1).

Now let's run our app, later we'll explain the code.

~~~shell
$ flutter devices
2 connected devices:

Android SDK built for x86 • emulator-5554  • android-x86 • Android 8.0.0 (API 26) (emulator)
iPhone X                  • 2E729265-625D-488A-B41B-BAFEB523F2B4     • ios         • iOS 11.2 (simulator)

$ flutter run -d emulator-5554
Running "flutter packages get" in gramola-app...             0.7s
...
🔥  To hot reload changes while running, press "r". To hot restart (and rebuild state), press "R".
An Observatory debugger and profiler on Android SDK built for x86 is available at: http://127.0.0.1:8100/
For a more detailed help message, press "h". To quit, press "q".
~~~

We're going to check everything remains the same until we get to the Timeline feature.

Login Screen (login API is mocked up even though the code is ready to invoke a real one). Type any user/password, the mocked up user will be 'trever'.

![Login Screen]({% image_path phase-2-login.png %}){:width="300px"}

Events Screen by default shows all events. 

![Events Screen]({% image_path phase-2-all-events.png %}){:width="300px"}

To filter by city please tap lower right corner button.

![Choose city]({% image_path phase-2-choose-city.png %}){:width="300px"}

Result when filtering by city == 'MADRID'.

![Choose city]({% image_path phase-2-city-Madrid.png %}){:width="300px"}

Now if you tap on an event you should jump to the new package, as in the next picture.

![Choose city]({% image_path phase-2-timeline-1.png %}){:width="300px"}

> **Note:** As you can see, the package behaves in the same way and theme colors match the theme of the app.

Well done! You've completed all labs.
