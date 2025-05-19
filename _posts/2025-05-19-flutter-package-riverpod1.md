---
title: "[Packages] Riverpod1"
excerpt: "Riverpod 장점과 기본 개념"

categories:
  - packages
tags:
  - [flutter, layout]

permalink: /packages/Flutter-Layout/

toc: true
toc_sticky: true

date: 2025-05-19
last_modified_at: 2025-05-19
---

# [참고 문서](https://codewithandrea.com/articles/flutter-state-management-riverpod/)

# 0. 목차

1. Riverpod이란? (Provider와 비교)
2. ProviderScope와 Provider
3. Riverpod 사용방법
4. ref.read/ ref.watch/ ref.listen
5. 알아두면 좋은 자주 쓰는 기능들

# 1. Riverpod이란?

- Flutter 패키지 중 하나인 Provider 패키지의 진화한 버전으로, 상태 관리를 위한 라이브러리임

## Provider와의 차이점

- Provider는 위젯 트리(InheritedWidget)를 기반으로 하여 상태를 관리하므로 위젯 트리의 영향을 받을 수 밖에 없음
- 아래의 이미지와 같은 구조에서 SigninPage에서는 DatabaseProvider를 찾지 못해서 ProviderNotFoundException이 발생함

![alt text](https://file.notion.so/f/f/3135856a-f5b4-4fc2-8ec4-db979cd3fd4c/001fac00-8539-48ca-9545-810b44d6a99a/image.png?table=block&id=1f8893ac-8819-8099-b689-c348792ef02a&spaceId=3135856a-f5b4-4fc2-8ec4-db979cd3fd4c&expirationTimestamp=1747677600000&signature=x6lriwLpSozReVlEUUnhFa2gSqJCVA3xFntMokITHS8&downloadName=image.png)

- 반면 Riverpod은 위젯 트리와 독립적으로 상태를 관리할 수 있도록 설계되어, 위젯 트리의 영향을 받지 않고 필요할 때만 Provider와 위젯을 다시 빌드하기가 용이함

## Provider를 전역으로 관리함으로써 발생할 수 있는 문제들

- 추가 예정

# 2. ProviderScope와 Provider

## ProviderScope

- ProviderScope는 Provider를 전역으로 관리하기 위한 위젯으로, Provider를 사용하기 위해서는 반드시 ProviderScope로 감싸야 함
- ProviderScope는 모든 Provider의 상태를 저장하는 역할을 하며, 내부적으로 ProviderContainer를 생성함 ([리스너 등록](https://codewithandrea.com/articles/riverpod-initialize-listener-app-startup/) 참고 )

## Provider

- Provider는 Riverpod에서 상태를 관리하기 위한 기본 단위로, 상태를 생성하고 제공하는 역할을 함
- 싱글톤, 서비스 로케이터, 종속성 주입, InheritedWidget 등의 디자인 패턴을 완전히 대체함

### Provider의 종류

1. `Provider`
2. `StateProvider`(legacy)

- 카운터 값과 같이 변경될 수 있는 간단한 상태 객체를 저장하는 데 적합함

```dart
// 선언
final counterStateProvider = StateProvider<int>((ref) {
  return 0;
});

// 호출
class CounterWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    // 1. watch the provider and rebuild when the value changes
    final counter = ref.watch(counterStateProvider);
    return ElevatedButton(
      // 2. use the value
      child: Text('Value: $counter'),
      // 3. change the state inside a button callback
      onPressed: () => ref.read(counterStateProvider.notifier).state++,
    );
  }
}
```

3. `StateNotifierProvider`(legacy)

- 이벤트나 사용자 상호작용에 따라 변경될 수 있는 상태를 관리하는 데 적합함
- 참고 [StateNotifier](https://pub.dev/documentation/flutter_riverpod/latest/flutter_riverpod/StateNotifier-class.html)

```dart
import 'dart:async';

class Clock extends StateNotifier<DateTime> {
  // 1. initialize with current time
  Clock() : super(DateTime.now()) {
    // 2. create a timer that fires every second
    _timer = Timer.periodic(Duration(seconds: 1), (_) {
      // 3. update the state with the current time
      state = DateTime.now();
    });
  }

  late final Timer _timer;

  // 4. cancel the timer when finished
  @override
  void dispose() {
    _timer.cancel();
    super.dispose();
  }
}
```

4. `FutureProvider`

- 비동기 작업을 처리하는 데 적합함

```dart
final weatherFutureProvider = FutureProvider.autoDispose<Weather>((ref) {
  // get repository from the provider below
  final weatherRepository = ref.watch(weatherRepositoryProvider);
  // call method that returns a Future<Weather>
  return weatherRepository.getWeather(city: 'London');
});

// example weather repository provider
final weatherRepositoryProvider = Provider<WeatherRepository>((ref) {
  return WeatherRepository(); // declared elsewhere
});


Widget build(BuildContext context, WidgetRef ref) {
  // watch the FutureProvider and get an AsyncValue<Weather>
  final weatherAsync = ref.watch(weatherFutureProvider);
  // use pattern matching to map the state to the UI
  return weatherAsync.when(
    loading: () => const CircularProgressIndicator(),
    error: (err, stack) => Text('Error: $err'),
    data: (weather) => Text(weather.toString()),
  );
}
```

- 비동기 작업(예: 네트워크 요청)을 수행하고 캐시된 값을 제공
- 비동기 작업의 오류 및 로딩 상태를 처리
- 여러 비동기 값을 다른 값으로 결합
- 데이터 다시 가져오기 및 새로 고침(끌어오기-새로 고침 작업에 유용함)

5. `StreamProvider`

- 실시간 API의 Stream 결과를 모니터링하고 UI를 반응형으로 다시 구축하는 데 사용

```dart
final authStateChangesProvider = StreamProvider.autoDispose<User?>((ref) {
  // get FirebaseAuth from the provider below
  final firebaseAuth = ref.watch(firebaseAuthProvider);
  // call a method that returns a Stream<User?>
  return firebaseAuth.authStateChanges();
});

// provider to access the FirebaseAuth instance
final firebaseAuthProvider = Provider<FirebaseAuth>((ref) {
  return FirebaseAuth.instance;
});

Widget build(BuildContext context, WidgetRef ref) {
  // watch the StreamProvider and get an AsyncValue<User?>
  final authStateAsync = ref.watch(authStateChangesProvider);
  // use pattern matching to map the state to the UI
  return authStateAsync.when(
    data: (user) => user != null ? HomePage() : SignInPage(),
    loading: () => const CircularProgressIndicator(),
    error: (err, stack) => Text('Error: $err'),
  );
}
```

6. `ChangeNotifierProvider`(legacy)

- Flutter SDK의 일부인 ChangeNotifier를 사용하여 상태가 변경되면 리스너에 알림

```dart
class AuthController extends ChangeNotifier {
  // mutable state
  User? user;
  // computed state
  bool get isSignedIn => user != null;

  Future<void> signOut() {
    // update state
    user = null;
    // and notify any listeners
    notifyListeners();
  }
}

final authControllerProvider = ChangeNotifierProvider<AuthController>((ref) {
  return AuthController();
});


Widget build(BuildContext context, WidgetRef ref) {
  return ElevatedButton(
    onPressed: () => ref.read(authControllerProvider).signOut(),
    child: const Text('Logout'),
  );
}
```

- **변경 불가능한 상태** 와 **단방향 데이터 흐름**이라는 원칙에 위배되기가 쉬워서 ChangeNotifier보다는 StateNotifier를 사용하는 것이 좋음

7. `NotifierProvider` **(Riverpod 2.0의 새로운 기능)**

8. `AsyncNotifierProvider` **(Riverpod 2.0의 새로운 기능)**

- 참고: [새로운 Flutter Riverpod Generator와 함께 Notifier 및 AsyncNotifier를 사용하는 방법](https://codewithandrea.com/articles/flutter-riverpod-async-notifier/)

# 3. Riverpod 사용방법

## 실제 사용 예시

```dart
// helloWorldProvider
final helloWorldProvider = Provider<String>((ref) {
  return 'Hello world';
});
```

- helloWorldProvider: Provider를 전역 변수로 선언
- Provider<String>: String 타입의 상태를 관리하는 Provider
- ref: provider를 사용하기 위해서는 반드시 ref를 매개변수로 받아야 함

## (1) ConsumerWidget 사용

- 가장 일반적인 방법
- `StatelessWidget` 대신 `ConsumerWidget`를 사용하면 `build` 메서드는 provider를 사용할 수 있는 [`WidgetRef`](https://pub.dev/documentation/flutter_riverpod/latest/flutter_riverpod/WidgetRef-class.html) 타입의 **ref** 객체(유형 )를 받음

```dart
final helloWorldProvider = Provider<String>((_) => 'Hello world');

// 1. widget class now extends [ConsumerWidget]
class HelloWorldWidget extends ConsumerWidget {
  @override
  // 2. build method has an extra [WidgetRef] argument
  Widget build(BuildContext context, WidgetRef ref) {
    // 3. use ref.watch() to get the value of the provider
    final helloWorld = ref.watch(helloWorldProvider);
    return Text(helloWorld);
  }
}
```

## (2) Consumer 사용

```dart
final helloWorldProvider = Provider<String>((_) => 'Hello world');

class HelloWorldWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // 1. Add a Consumer
    return Consumer(
      // 2. specify the builder and obtain a WidgetRef
      builder: (_, WidgetRef ref, __) {
        // 3. use ref.watch() to get the value of the provider
        final helloWorld = ref.watch(helloWorldProvider);
        return Text(helloWorld);
      },
    );
  }
}
```

- 위젯을 위와 같이 Consumer로 감싸면 Provider를 사용할 수 있음
- **ref** 객체는 Consumer의 인수 중 하나이며, 이를 통해 Provider를 사용할 수 있음

```dart
// Consumer를 사용하는 예시
final helloWorldProvider = Provider<String>((_) => 'Hello world');

class HelloWorldWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      // 1. Add a Consumer
      body: Consumer(
        // 2. specify the builder and obtain a WidgetRef
        builder: (_, WidgetRef ref, __) {
          // 3. use ref.watch() to get the value of the provider
          final helloWorld = ref.watch(helloWorldProvider);
          return Text(helloWorld);
        },
      ),
    );
  }
}
```

- Text위젯만을 Consumer로 감싼 경우, provider의 상태가 변경되면 Text 위젯만 다시 빌드됨
- Scaffold와 AppBar는 다시 빌드되지 않음
- 큰 class나 복잡한 layout에서 원하는 부분만을 Consumer로 감싸면 성능을 최적화하거나, 가독성이 좋은 코드 구성이 가능함

## (3) [ConsumerStatefulWidget, ConsumerState 사용](https://codewithandrea.com/articles/flutter-state-management-riverpod/#3-using-consumerstatefulwidget-&-consumerstate)

```dart
final helloWorldProvider = Provider<String>((_) => 'Hello world');

// 1. extend [ConsumerStatefulWidget]
class HelloWorldWidget extends ConsumerStatefulWidget {
  @override
  ConsumerState<HelloWorldWidget> createState() => _HelloWorldWidgetState();
}

// 2. extend [ConsumerState]
class _HelloWorldWidgetState extends ConsumerState<HelloWorldWidget> {
  @override
  void initState() {
    super.initState();
    // 3. if needed, we can read the provider inside initState
    final helloWorld = ref.read(helloWorldProvider);
    print(helloWorld); // "Hello world"
  }

  @override
  Widget build(BuildContext context) {
    // 4. use ref.watch() to get the value of the provider
    final helloWorld = ref.watch(helloWorldProvider);
    return Text(helloWorld);
  }
}
```

- StatelessWidget이 아닌 StatefulWidget 위젯의 경우 ConsumerStatefulWidget을 사용하여 Provider를 사용할 수 있음

- `ConsumerState`에서 하위 클래스의 모든 위젯 라이프사이클 메서드 내에서 `ref` 객체에 접근할 수 있습니다 .

- 이는 `ConsumerState`가 `WidgetRef`를 **속성으로** 선언하기 때문인데 , 이는 Flutter `State` 클래스가 `BuildContext`를 **속성으로** 선언하여 모든 위젯 라이프사이클 메서드에 직접 접근할 수 있는 것과 유사합니다.

❔ref.watch()는 build 메서드에서만 사용해야 하고 ref.read()는 build 메서드 외부에서도 사용 가능한가

## (4) [HookConsumerWidget 사용](https://pub.dev/packages/hooks_riverpod)

- 추가 예정

# 4. ref.read/ ref.watch/ ref.listen

- ref.watch()는 Provider의 상태를 구독하고, 상태가 변경될 때마다 위젯을 다시 빌드함
- ref.read()는 Provider의 상태를 읽기만 하고, 상태가 변경되어도 위젯을 다시 빌드하지 않음
- onPressed와 같은 콜백 함수에서는 ref.watch가 아닌 ref.read()를 사용하여야 함

```dart
final counterStateProvider = StateProvider<int>((_) => 0);

class CounterWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    // 1. watch the provider and rebuild when the value changes
    final counter = ref.watch(counterStateProvider);
    return ElevatedButton(
      // 2. use the value
      child: Text('Value: $counter'),
      // 3. change the state inside a button callback
      onPressed: () => ref.read(counterStateProvider.notifier).state++,
    );
  }
}
```

- ref.listen와 ref.read, ref.watch의 차이점은?
  - ref.listen은 Provider의 상태가 변경될 때마다 콜백 함수를 실행할 수 있음
  - ref.read는 Provider의 상태를 읽기만 하고, 상태가 변경되어도 위젯을 다시 빌드하지 않음
  - ref.watch는 Provider의 상태를 구독하고, 상태가 변경될 때마다 위젯을 다시 빌드함

```dart
final counterStateProvider = StateProvider<int>((_) => 0);

class CounterWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    // if we use a StateProvider<T>, the type of the previous and current
    // values is StateController<T>
    ref.listen<StateController<int>>(counterStateProvider.state, (previous, current) {
      // note: this callback executes when the provider value changes,
      // not when the build method is called
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(content: Text('Value is ${current.state}')),
      );
    });
    // watch the provider and rebuild when the value changes
    final counter = ref.watch(counterStateProvider);
    return ElevatedButton(
      // use the value
      child: Text('Value: $counter'),
      // change the state inside a button callback
      onPressed: () => ref.read(counterStateProvider.notifier).state++,
    );
  }
}
```

- 참고 [Riverpod Provider 간 결합](https://codewithandrea.com/articles/flutter-state-management-riverpod/#combining-providers-with-riverpod)`ref.read`[](https://codewithandrea.com/articles/flutter-state-management-riverpod/#combining-providers-with-riverpod)

# 5. 알아두면 좋은 자주 쓰는 기능들

## (1) autoDispose 수정자

- `FutureProvider`또는 `StreamProvider`를 사용하여 작업하는 경우 provider를 더 이상 사용되지 않으면 모든 리스너를 삭제해야 함
- provider에 autoDispose를 추가하여 자동으로 삭제할 수 있음

```dart
final authStateChangesProvider = StreamProvider.autoDispose<User?>((ref) {
  // get FirebaseAuth from another provider
  final firebaseAuth = ref.watch(firebaseAuthProvider);
  // call method that returns a Stream<User?>
  return firebaseAuth.authStateChanges();
});
```

## (2) [The family modifier](https://codewithandrea.com/articles/flutter-state-management-riverpod/#the-family-modifier)

- 추가예정

## (3) [Riverpod provider 간 결합](https://codewithandrea.com/articles/flutter-state-management-riverpod/#combining-providers-with-riverpod)

- 추가예정

## (4) [Riverpod로 테스트하기](https://codewithandrea.com/articles/flutter-state-management-riverpod/#testing-with-riverpod)

- 추가예정
