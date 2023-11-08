
Before we start guys - please we offer mobile app development service @venbrinoDev
you can send us a dm 


[send a whatapps message now](http://wa.link/eyxjqu)

[our website](https://venbrinodevs.com/)




1. Make sure to initialize Firebase - 
2. Add dependency flutter pub add firebase_messaging
3. Configure your app for ios/macos to receive notification (https://firebase.flutter.dev/docs/messaging/apple-integration/)

## Usage

```dart

import 'package:firebase_core/firebase_core.dart';
import 'package:firebase_messaging/firebase_messaging.dart';
import 'package:flutter/material.dart';
import 'package:zylag/firebase_options.dart';

@pragma('vm:entry-point')
Future<void> _firebaseMessagingBackgroundHandler(RemoteMessage message) async {
  print("Handling a background message: ${message.messageId}");
}

//How to use
void main() async {
  await Firebase.initializeApp(
    options: DefaultFirebaseOptions.currentPlatform,
  );
  FirebaseMessaging.onBackgroundMessage(_firebaseMessagingBackgroundHandler);

  runApp(const MyApp());
}

///Wrap your main app with notification wrapper
class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return NotificationWrapper(
        onNotificationTapped: (RemoteMessage message) {
          debugPrint(
              "onNotificationTapped ---->>>>>> ${message.notification?.title}");
        },
        onForeGroundNotificationReceived: (RemoteMessage message) {
          debugPrint(
              "onForeGroundNotificationReceived ---->>>>>> ${message.notification?.title}");
        },
        child: const MaterialApp());
  }
}

class NotificationWrapper extends StatefulWidget {
  const NotificationWrapper(
      {Key? key,
      required this.child,
      this.onNotificationTapped,
      this.onForeGroundNotificationReceived})
      : super(key: key);

  final Widget child;

  final Function(RemoteMessage message)? onNotificationTapped;

  final Function(RemoteMessage message)? onForeGroundNotificationReceived;

  @override
  State<NotificationWrapper> createState() => _NotificationConfigState();
}

class _NotificationConfigState extends State<NotificationWrapper> {
  FirebaseMessaging messaging = FirebaseMessaging.instance;

  @override
  void initState() {
    super.initState();
    Future.microtask(
      () => startNotificationProcess(),
    );
  }

  void startNotificationProcess() async {
    await askForPermission();
    await setUpIOSNotification();
    onForeGroundNotification();
    setupInteractedMessage();
  }

  setUpIOSNotification() async {
    await FirebaseMessaging.instance
        .setForegroundNotificationPresentationOptions(
      alert: true, // Required to display a heads up notification
      badge: true,
      sound: true,
    );
  }

  ///Ask for user permission
  Future<void> askForPermission() async {
    NotificationSettings isPermissionGranted =
        await messaging.getNotificationSettings();

    if (isPermissionGranted.authorizationStatus !=
        AuthorizationStatus.authorized) {
      await messaging.requestPermission(
        alert: true,
        announcement: true,
        badge: true,
        carPlay: true,
        criticalAlert: false,
        provisional: false,
        sound: true,
      );
    }
  }

  ///ForeGround handler
  ///When your app is currently in use and notification is received
  onForeGroundNotification() {
    FirebaseMessaging.onMessage.listen((RemoteMessage message) {
      widget.onForeGroundNotificationReceived?.call(message);
    });
  }

  ///Handle notification when they are clicked
  Future<void> setupInteractedMessage() async {
    // Get any messages which caused the application to open from
    // A terminated state.
    RemoteMessage? initialMessage =
        await FirebaseMessaging.instance.getInitialMessage();

    // Handle if the app was open from notification
    if (initialMessage != null) {
      _handleMessage(initialMessage);
    }
    // Also handle any interaction when the app is in the background via a
    // Stream listener
    FirebaseMessaging.onMessageOpenedApp.listen(_handleMessage);
  }

  void _handleMessage(RemoteMessage message) {
    widget.onNotificationTapped?.call(message);
  }

  @override
  Widget build(BuildContext context) {
    return widget.child;
  }
}
```
                                                            
Thanks guys
                                                        
[send whatapps message now](http://wa.link/eyxjqu)

[our website](https://venbrinodevs.com/)

# Firebase-Notification-Wrapper
