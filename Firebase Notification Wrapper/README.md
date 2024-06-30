
Before we start guys - please we offer mobile app development service @venbrinoDev
you can send us a dm 



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


UNIT TEST FLUTTER BLOC AND REPOSITORY

```dart
class ApiRepository {
  final ApiService apiService;

  ApiRepository(this.apiService);

  Future<List<Data>> fetchData() async {
    final response = await apiService.getData();
    return response.map((item) => Data.fromJson(item)).toList();
  }
}



import 'package:flutter_test/flutter_test.dart';
import 'package:mockito/mockito.dart';
import 'package:mockito/annotations.dart';

// Import the actual files
import 'api_repository.dart';
import 'api_service.dart';
import 'data.dart';

// Generate a MockApiService
@GenerateMocks([ApiService])
void main() {
  late ApiRepository apiRepository;
  late MockApiService mockApiService;

  setUp(() {
    mockApiService = MockApiService();
    apiRepository = ApiRepository(mockApiService);
  });

  test('fetchData returns a list of Data', () async {
    // Arrange
    when(mockApiService.getData()).thenAnswer(
      (_) async => [
        {'id': 1, 'name': 'Data 1'},
        {'id': 2, 'name': 'Data 2'},
      ],
    );

    // Act
    final result = await apiRepository.fetchData();

    // Assert
    expect(result, isA<List<Data>>());
    expect(result.length, 2);
    expect(result[0].name, 'Data 1');
  });
}




import 'package:bloc/bloc.dart';
import 'package:equatable/equatable.dart';
import 'api_repository.dart';
import 'data.dart';

// Bloc Events
abstract class DataEvent extends Equatable {
  const DataEvent();
}

class FetchData extends DataEvent {
  @override
  List<Object> get props => [];
}

// Bloc States
abstract class DataState extends Equatable {
  const DataState();
}

class DataInitial extends DataState {
  @override
  List<Object> get props => [];
}

class DataLoadInProgress extends DataState {
  @override
  List<Object> get props => [];
}

class DataLoadSuccess extends DataState {
  final List<Data> data;

  const DataLoadSuccess(this.data);

  @override
  List<Object> get props => [data];
}

class DataLoadFailure extends DataState {
  @override
  List<Object> get props => [];
}

// Bloc Implementation
class DataBloc extends Bloc<DataEvent, DataState> {
  final ApiRepository apiRepository;

  DataBloc(this.apiRepository) : super(DataInitial());

  @override
  Stream<DataState> mapEventToState(DataEvent event) async* {
    if (event is FetchData) {
      yield DataLoadInProgress();
      try {
        final data = await apiRepository.fetchData();
        yield DataLoadSuccess(data);
      } catch (_) {
        yield DataLoadFailure();
      }
    }
  }
}


import 'package:bloc_test/bloc_test.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:mockito/mockito.dart';
import 'package:mockito/annotations.dart';

// Import the actual files
import 'data_bloc.dart';
import 'api_repository.dart';
import 'data.dart';

// Generate a MockApiRepository
@GenerateMocks([ApiRepository])
void main() {
  late DataBloc dataBloc;
  late MockApiRepository mockApiRepository;

  setUp(() {
    mockApiRepository = MockApiRepository();
    dataBloc = DataBloc(mockApiRepository);
  });

  tearDown(() {
    dataBloc.close();
  });

  group('DataBloc', () {
    blocTest<DataBloc, DataState>(
      'emits [DataLoadInProgress, DataLoadSuccess] when FetchData is added and fetchData succeeds',
      build: () {
        when(mockApiRepository.fetchData()).thenAnswer(
          (_) async => [
            Data(id: 1, name: 'Data 1'),
            Data(id: 2, name: 'Data 2'),
          ],
        );
        return dataBloc;
      },
      act: (bloc) => bloc.add(FetchData()),
      expect: () => [
        DataLoadInProgress(),
        DataLoadSuccess([
          Data(id: 1, name: 'Data 1'),
          Data(id: 2, name: 'Data 2'),
        ]),
      ],
    );

    blocTest<DataBloc, DataState>(
      'emits [DataLoadInProgress, DataLoadFailure] when FetchData is added and fetchData fails',
      build: () {
        when(mockApiRepository.fetchData()).thenThrow(Exception('Error'));
        return dataBloc;
      },
      act: (bloc) => bloc.add(FetchData()),
      expect: () => [
        DataLoadInProgress(),
        DataLoadFailure(),
      ],
    );
  });
}
```
                                                          

# Firebase-Notification-Wrapper
