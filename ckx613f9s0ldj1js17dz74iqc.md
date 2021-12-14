## Managing App Lifecycle in Flutter.


When building a mobile application, there are several important factors to consider in order to maximize the app's performance. One of these is handling the application's lifecycle state. Application Lifecycle essentially monitors or manages the state that an application can be in. For example, suppose you wish to start or stop services while an app is running in the background/foreground.

In Flutter, we can monitor these different states in other to carry out certain functionalities based on the current state of the app using the `didChangeAppLifecycleState()` method under the `WidgetsBindingObserver` class. This method takes in the `AppLifecycleState` object as an argument, which is an enum containing the following states:


- **detached **- The application is still hosted on a flutter engine, but it is no longer connected to any host views. It can either be in the progress of attaching a view when the engine was first initialized, or after the view is been destroyed due to a Navigator pop.
- **paused **- The application is presently not visible to the user, does not respond to user interaction, and is operating in the background.
- **resumed **- The application is visible to the user and responds to user interaction.
- **inactive **- The application is in an inactive state and is not responding to user interaction. Apps in this state may be paused at any time. 

Now that we have got an idea of what AppLifecycle is, let's create a project to show how we can work with the AppLifecycle state. 

## Project Setup
### Step 1:

Create a new Flutter project by running the command below in your terminal:


    flutter create applifecyle_manager


### Step 2:

Let’s create a  stateful widget `MyHomePage` and add the `WidgetsBindingObserver` class as a mixin to it as shown below. 


>  Mixins allows us to reuse code from another class without inheriting it since Dart doesn’t support multiple inheritance. We make use of the `with` keyword for denoting mixins.


    class MyHomePage extends StatefulWidget {
      const MyHomePage({Key? key}) : super(key: key);
      @override
      State<MyHomePage> createState() => _MyHomePageState();
    }
    class _MyHomePageState extends State<MyHomePage> with WidgetsBindingObserver {
      @override
      Widget build(BuildContext context) {
      //...
    }


### Step 3:

Using a stateful widget gives us access to the `initState` and `dispose` method. The `WidgetsBindingObserver` will be added in the `initState` method and removed in the `dispose` method, as shown below.


    class _MyHomePageState extends State<MyHomePage> with WidgetsBindingObserver {
      @override
      void initState() {
        WidgetsBinding.instance!.addObserver(this);
        super.initState();
      }
      @override
      void dispose() {
        WidgetsBinding.instance!.removeObserver(this);
        super.dispose();
      }
    
      @override
      Widget build(BuildContext context) {
      //...
    }


### Step 4:

Here, we will access the `didChangeAppLifecycleState()` method from our `WidgetsBindingObserver`. This method exposes us to the previously described states and we can perform appropriate actions based on the state of our app. For the sake of simplicity, we will only print the app state to the terminal in this project.


    class _MyHomePageState extends State<MyHomePage> with WidgetsBindingObserver {
      //...
      @override
      void didChangeAppLifecycleState(AppLifecycleState state) {
        debugPrint('state = $state');
      }

Now when we run our app on our device and interact with it by either minimizing, closing, or reopening it, we can observe as our app lifecycle changes as shown below.


![This tells us the state our app.](https://paper-attachments.dropbox.com/s_A883BECE0E155A497CD307699B2739C7B5C225F9A3E2D41628464C87D4964DE2_1639362494257_Screenshot+2021-12-13+032742.png)


We’ve successfully set up the app lifecycle state for `MyHomePage` screen. However, if we have an app with several routes, you may want to implement the app lifecycle only once at the root of our app widget to avoid code repetitions for each screen. 

Let's start by creating a new dart file called `app_lifecycle.dart`. Within this file, we'll create a stateful widget `AppLifecycleManager` and repeat the procedures for adding `WidgetsBindingObserver` class and `didChangeAppLifecycleState` method. 

The `AppLifecycleManager` widget should look like this:


    import 'package:flutter/material.dart';
    
    class AppLifecycleManager extends StatefulWidget {
      final Widget? child;
      const AppLifecycleManager({Key? key, this.child}) : super(key: key);
      @override
      _AppLifecycleManagerState createState() => _AppLifecycleManagerState();
    }
    class _AppLifecycleManagerState extends State<AppLifecycleManager>
        with WidgetsBindingObserver {
      @override
      void initState() {
        WidgetsBinding.instance!.addObserver(this);
        super.initState();
      }
      @override
      void dispose() {
        WidgetsBinding.instance!.removeObserver(this);
        super.dispose();
      }
      @override
      void didChangeAppLifecycleState(AppLifecycleState state) {
        debugPrint('state = $state');
      }
      @override
      Widget build(BuildContext context) {
        return Container(
          child: widget.child,
        );
      }
    }

Then we can use the `AppLifecycleManager` widget to wrap our MaterialApp widget in our `main.dart` file as shown below. 


    import 'package:flutter/material.dart';
    import 'app_lifeycle.dart';
    
    void main() {
      runApp(const MyApp());
    }
    class MyApp extends StatelessWidget {
      const MyApp({Key? key}) : super(key: key);
      @override
      Widget build(BuildContext context) {
        return AppLifecycleManager( //--> added the AppLifecycleManager
          child: MaterialApp(
            title: 'Flutter Demo',
            theme: ThemeData(
              primarySwatch: Colors.blueGrey,
            ),
            home: const MyHomePage(title: 'AppLifecycle Manager'),
          ),
        );
      }
    }

Now we can access the app lifecycle states across every routes on our app.


## Conclusion

App lifecycle is essential in our application to enhance the user experience by performing appropriate actions based on the state of our app, and we’ve learnt what an app lifecycle is, the various states an app can be in, and how we can access the app lifecycle states in Flutter.

Check out the full code on GitHub  [here](https://github.com/iloveteajay/applifecycle_manager) .

Thank you for reading and I hope you find this article useful and informative. 😊 
