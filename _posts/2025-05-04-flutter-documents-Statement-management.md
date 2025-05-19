---

title: "[Documents] State management"
excerpt: "Flutter State management 문서읽기"

categories:
- documents

tags:
- [Flutter, State management]

permalink: /documents/Flutter-State management/

toc: true
toc_sticky: true

date: 2025-05-04
last_modified_at: 2025-05-04
---

[Flutter State management 문서](https://docs.flutter.dev/get-started/fundamentals/state-management)

# 0. 목차

1. 상태 관리의 개념
2. StatefulWidget: 가장 간단한 상태 관리 방법
3. 위젯 간의 상태공유 방법
4. Listenable: 공유상태 변경 알림
5. MVVM 패턴: 상태객체 구성방법



# 1. 상태 관리의 개념

- Flutter 앱의 상태는 UI를 표시하거나 시스템 리소스를 관리하는 데 사용되는 모든 객체를 의미
- 상태관리는 이러한 객체에 가장 효과적으로 접근하고 위젯들 간 공유되도록 앱을 구성하는 방법

# 2. StatefulWidget

- 상태를 관리하는 가장 간단한 방법은 [StatefulWidget](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html)을 사용하는 것 

```dart
class MyCounter extends StatefulWidget {
  const MyCounter({super.key});

  @override
  State<MyCounter> createState() => _MyCounterState();
}

class _MyCounterState extends State<MyCounter> {
  int count = 0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('Count: $count'),
        TextButton(
          onPressed: () {
            setState(() {
              count++;
            });
          },
          child: Text('Increment'),
        )
      ],
    );
  }
}
```
## 상태관리의 두 가지 측면
- 캡슐화
  - MyCounter를 사용하는 위젯은 기본 count 변수를 볼 수 없으며 이에 액세스하거나 변경할 수 없음
- 객체 수명 주기
  - _MyCounterState객체와 해당 count변수는 처음 MyCounter가 빌드될 때 생성되어 화면에서 제거될 때까지 존재함
  - [임시 상태(ephemeral state)](https://docs.flutter.dev/data-and-backend/state-mgmt/ephemeral-vs-app)라고 함

## 임시 상태와 애플리케이션 상태

- **임시 상태**는 애플리케이션의 일부가 아닌 개별 위젯의 상태를 나타냄
- **애플리케이션 상태**(공유 상태라고도 함)는 애플리케이션의 모든 부분에서 공유되는 상태를 나타냄
- 애플리케이션 상태의 예로는 
  - 사용자 기본 설정
  - 로그인 정보 
  - 소셜 네트워킹 앱의 알림
  - 전자상거래 앱의 쇼핑 카트
  - 뉴스 앱에서 기사 읽음/읽지 않음 상태 등
- 특정 변수가 임시 상태의 변수인지 앱 상태의 변수인지 명확한 기준은 없고, 애플리케이션의 기능과 구조에 따라 달라짐

# 3. 위젯 간 상태 공유 방법

- 앱의 위젯 간에 상태를 공유하는 방법은 다음과 같음
  - 위젯 생성자 사용
  - InheritedWidget 사용
  - 콜백 사용

## (1) 위젯 생성자 사용

```dart
class MyCounter extends StatelessWidget {
  final int count;
  const MyCounter({super.key, required this.count});

  @override
  Widget build(BuildContext context) {
    return Text('$count');
  }
}
```

```dart
Column(
  children: [
    MyCounter(
      count: count,
    ),
    MyCounter(
      count: count,
    ),
    TextButton(
      child: Text('Increment'),
      onPressed: () {
        setState(() {
          count++;
        });
      },
    )
  ],
)
```

- 위젯을 사용하기 위해 무엇을 제공해야 하는지 명확하게 알 수 있는 장점이 있음
- 즉, 위젯 생성자를 통해 앱의 공유 데이터를 전달하면 코드를 읽는 누구에게나 공유 종속성이 있음을 명확하게 알 수 있음 
- 이는 종속성 주입 이라고 하는 일반적인 디자인 패턴임

## (2) InheritedWidget 사용

### InheritedWidget 장점

- 위젯 트리를 따라 데이터를 전달하는 것은 부모 → 자식 → 손자… 식으로 계속 데이터를 넘겨줘야 하여 특히 위젯 구조가 복잡해질수록 이런 방식은 번거롭고, 코드가 지저분해짐
- InheritedWidget은 부모 위젯이 가진 데이터를 자식 위젯들이 쉽게 접근할 수 있도록 해주는 Flutter의 기본 위젯으로, InheritedWidget을 사용하면, 위젯 트리 어디서든 쉽게 데이터를 꺼내 쓸 수 있음

### InheritedWidget 구현

#### InheritedWidget 상속 클래스

InheritedWidget을 상속받아 새로운 클래스를 만듬

공유하고 싶은 데이터를 이 클래스의 필드로 선언

#### of() 메서드 만들기

자식 위젯에서 쉽게 데이터를 가져올 수 있도록 static 메서드 of()를 만듬

내부적으로 context.dependOnInheritedWidgetOfExactType<클래스명>()을 사용해 가장 가까운 조상 InheritedWidget을 찾아 반환

#### updateShouldNotify() 구현

데이터가 바뀌었을 때 자식 위젯들을 다시 빌드할지 결정

보통 이전 데이터와 새 데이터가 다르면 true를 반환

```dart
import 'package:flutter/material.dart';

// 1. InheritedWidget을 상속받아 새로운 클래스를 만듬
//    공유하고 싶은 데이터를 이 클래스의 필드로 선언

class MyState extends InheritedWidget {
  // 공유할 데이터를 필드로 선언
  final String data;

  // 생성자에서 데이터와 child(자식 위젯)를 받음
  const MyState({
    Key? key,
    required this.data,
    required Widget child,
  }) : super(key: key, child: child);

  // 2. of() 메서드 만들기
  //    자식 위젯에서 쉽게 데이터를 가져올 수 있도록 static 메서드를 만듬
  //    context.dependOnInheritedWidgetOfExactType<클래스명>()을 사용해
  //    가장 가까운 조상 MyState 위젯을 찾아 반환
  static MyState of(BuildContext context) {
    final MyState? result = context.dependOnInheritedWidgetOfExactType<MyState>();
    assert(result != null, 'MyState 위젯이 위젯 트리에 없습니다.');
    return result!;
  }

  // 3. updateShouldNotify() 구현
  //    데이터가 바뀌었을 때 자식 위젯들을 다시 빌드할지 결정
  //    이전 데이터와 새 데이터가 다르면 true를 반환
  @override
  bool updateShouldNotify(MyState oldWidget) {
    return data != oldWidget.data;
  }
}

// 실제 사용 예시
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // MyState를 위젯 트리의 상단에 배치하고, 데이터를 전달
    return MyState(
      data: '공유할 데이터입니다!',
      child: MaterialApp(
        home: HomeScreen(),
      ),
    );
  }
}

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // of() 메서드를 사용해 MyState의 데이터를 가져옴
    String sharedData = MyState.of(context).data;

    return Scaffold(
      appBar: AppBar(title: Text('InheritedWidget 예제')),
      body: Center(
        child: Text(
          sharedData, // 가져온 데이터를 화면에 출력
          style: TextStyle(fontSize: 24),
        ),
      ),
    );
  }
}

```

## (3) 콜백 사용

- 콜백 함수란, 어떤 이벤트(예: 버튼 클릭, 값 변경)가 발생했을 때 실행되는 함수

- 위젯이 자신의 내부 상태가 바뀌었음을 부모나 다른 위젯에 알려야 할 때, 콜백 함수를 파라미터로 받아서 사용할 수 있음

- ValueChanged<T>는 값이 바뀔 때 실행되는 콜백 함수의 타입을 정의한 것으로, 예를 들어 값이 바뀌면 onChanged(새로운값)처럼 호출됨

```dart
import 'package:flutter/material.dart';

// 1. MyCounter 위젯은 값을 변경할 때 알릴 수 있도록 콜백 함수를 파라미터로 받음
class MyCounter extends StatefulWidget {
  // ValueChanged<int> 타입의 onChanged 콜백을 받음
  final ValueChanged<int> onChanged;

  const MyCounter({super.key, required this.onChanged});

  @override
  State<MyCounter> createState() => _MyCounterState();
}

class _MyCounterState extends State<MyCounter> {
  int count = 0; // 내부에서 관리하는 카운트 값

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('현재 값: $count'),
        // 2. 버튼을 누르면 count 값을 1 증가시키고, onChanged 콜백을 호출해 부모에게 알림
        TextButton(
          onPressed: () {
            setState(() {
              count++;
            });
            // 콜백 함수 호출: 부모 위젯에게 새로운 count 값을 전달
            widget.onChanged(count);
          },
          child: const Text('증가'),
        ),
      ],
    );
  }
}

// 3. MyCounter를 사용하는 부모 위젯 예시
class ParentWidget extends StatelessWidget {
  const ParentWidget({super.key});

  // MyCounter에서 값이 바뀔 때 호출되는 콜백 함수
  void handleCounterChanged(int newValue) {
    print('MyCounter에서 전달된 새 값: $newValue');
    // 여기서 setState 등을 이용해 부모 위젯의 상태를 변경할 수도 있음
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('콜백 예제')),
      body: Center(
        // 4. onChanged에 콜백 함수 전달
        child: MyCounter(
          onChanged: handleCounterChanged,
        ),
      ),
    );
  }
}
```

# 4. Listenable
## Listenable 개념
- Listenable은 값이 바뀌었을 때 여러 위젯(리스너)에게 알릴 수 있는 추상 클래스
- 대표적으로 ChangeNotifier와 ValueNotifier가 자주 사용됨
- 이들을 활용하면 상태가 바뀔 때마다 UI를 자동으로 업데이트할 수 있음

## Listenable 구현

### ChangeNotifier

- 여러 값이나 복잡한 상태를 관리할 때 사용함

- 값이 바뀔 때마다 notifyListeners()를 호출하여 이 ChangeNotifier를 구독 중인 위젯들이 자동으로 다시 빌드됨

- UI에서는 ListenableBuilder를 사용해 특정 부분만 효율적으로 다시 그릴 수 있음

### ValueNotifier

- 단일 값(예: 숫자, 문자열 등)을 관리할 때 더 간단하게 쓸 수 있음

- 값이 바뀌면 자동으로 리스너들에게 알림

- ValueListenableBuilder를 사용하면 값이 바뀔 때마다 UI를 쉽게 업데이트할 수 있음


## Listenable 사용 예시

### 1. ChangeNotifier + ListenableBuilder

```dart
import 'package:flutter/material.dart';

// 1. ChangeNotifier를 상속받아 상태 관리 클래스 생성
class CounterNotifier extends ChangeNotifier {
  int _count = 0;

  int get count => _count;

  // 값을 변경할 때마다 notifyListeners()를 호출해 리스너(위젯)들에게 알림
  void increment() {
    _count++;
    notifyListeners(); // 이 메서드를 호출하면 구독 중인 위젯들이 다시 빌드됨
  }
}

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    // CounterNotifier를 생성해서 하위 위젯에 전달
    final counterNotifier = CounterNotifier();

    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: const Text('ChangeNotifier 예제')),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              // 2. ListenableBuilder로 CounterNotifier를 구독
              ListenableBuilder(
                listenable: counterNotifier,
                builder: (context, child) {
                  // count 값이 바뀔 때마다 Text 위젯만 다시 빌드됨
                  return Text('카운터: ${counterNotifier.count}', style: const TextStyle(fontSize: 24));
                },
              ),
              const SizedBox(height: 20),
              // 3. 버튼을 누르면 카운터 증가
              TextButton(
                onPressed: () {
                  counterNotifier.increment();
                },
                child: const Text('증가'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

```

### 2. ValueNotifier + ListenableBuilder

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    // 1. ValueNotifier로 간단한 값 상태 관리
    final ValueNotifier<int> counterNotifier = ValueNotifier<int>(0);

    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: const Text('ValueNotifier 예제')),
        body: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              // 2. ValueListenableBuilder로 ValueNotifier를 구독
              ValueListenableBuilder<int>(
                valueListenable: counterNotifier,
                builder: (context, value, child) {
                  // value 값이 바뀔 때마다 Text 위젯만 다시 빌드됨
                  return Text('카운터: $value', style: const TextStyle(fontSize: 24));
                },
              ),
              const SizedBox(height: 20),
              // 3. 버튼을 누르면 카운터 증가
              TextButton(
                onPressed: () {
                  counterNotifier.value++;
                  // value 값을 바꾸면 자동으로 리스너들에게 알림
                },
                child: const Text('증가'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

```

# 5. MVVM 패턴
## 왜 MVVM이 상태관리와 연결되는가?
### Flutter에서 상태란?
- 앱의 데이터(예: 입력값, 체크박스 상태, 서버에서 받아온 리스트 등)는 시간이 지나면서 변할 수 있음
- 이런 "상태"가 바뀌면 UI도 함께 바뀌어야 하죠. 이걸 효율적으로 관리하는 것이 상태관리임

### MVVM의 구조
- MVVM은 앱을 Model(데이터/비즈니스 로직), ViewModel(상태와 로직 관리), View(UI)로 분리함

- Model은 데이터와 데이터를 다루는 로직을 담당하고,

- ViewModel은 Model의 데이터를 가공해 View에 전달하고, 상태가 바뀌면 View에 알림

- View는 ViewModel이 제공하는 데이터를 화면에 표시하고, 사용자 입력을 ViewModel로 전달

### Flutter에서 MVVM의 역할
- Flutter의 상태관리 라이브러리(Provider, Riverpod, BLoC 등)도 결국 ViewModel 역할을 하며, 상태(데이터)와 UI를 분리해서 관리함

- ViewModel이 상태를 가지고 있다가 notifyListeners() 등으로 UI에 변경을 알리면, UI는 자동으로 다시 그려짐

- 이 구조 덕분에 UI 코드와 비즈니스 로직(상태 관리)이 분리되어, 코드가 더 깔끔하고 유지보수가 쉬워짐

###상태관리와 MVVM의 연결고리
- 즉, "상태관리"라는 주제에서 MVVM이 언급되는 이유는,

- MVVM이 상태(데이터)와 UI의 관계를 체계적으로 정리해주는 대표적인 설계 패턴이기 때문

- Flutter에서 상태를 잘 관리하려면, 자연스럽게 MVVM 같은 패턴을 참고하게 됨

- MVVM은 앱을 Model(데이터/비즈니스 로직), ViewModel(상태와 로직 관리), View(UI)로 분리함

## MVVM 구성 요소
### Model
역할: 데이터 구조 정의 및 저수준 작업(API 통신, 데이터 캐싱) 처리.

특징: Flutter 프레임워크에 의존하지 않아 순수 Dart로 작성되며, 테스트가 용이함

```dart
// 데이터 구조 정의
class CounterData {
  CounterData(this.count);
  final int count;
}

// 모델 클래스 (HTTP 통신 담당)
class CounterModel {
  Future<CounterData> loadCountFromServer() async {
    final response = await get(Uri.parse('https://api.example.com/count'));
    if (response.statusCode != 200) throw Exception('Failed to load');
    return CounterData(int.parse(response.body));
  }

  Future<CounterData> updateCountOnServer(int newCount) async {
    // 서버에 카운트 업데이트 로직
  }
}
```

### ViewModel
역할: View와 Model 간의 중개자. 비즈니스 로직 처리 및 상태 변경 알림.

특징: ChangeNotifier를 상속받아 notifyListeners()로 View에 변경 사항을 알림림

```dart
import 'package:flutter/foundation.dart';

class CounterViewModel extends ChangeNotifier {
  final CounterModel model;
  int? count;
  String? errorMessage;

  CounterViewModel(this.model);

  // 초기화 로직
  Future<void> init() async {
    try {
      count = (await model.loadCountFromServer()).count;
    } catch (e) {
      errorMessage = '초기화 실패';
    }
    notifyListeners(); // 변경 사항 알림
  }

  // 카운트 증가 로직
  Future<void> increment() async {
    try {
      final newCount = (count ?? 0) + 1;
      await model.updateCountOnServer(newCount);
      count = newCount;
    } catch (e) {
      errorMessage = '업데이트 실패';
    }
    notifyListeners(); // 변경 사항 알림
  }
}
```

### View
역할: UI 표시 및 사용자 입력 처리.

특징: ListenableBuilder로 ViewModel을 구독해 자동 리빌드됨

```dart
class CounterView extends StatelessWidget {
  const CounterView({super.key});

  @override
  Widget build(BuildContext context) {
    final viewModel = Provider.of<CounterViewModel>(context);

    return ListenableBuilder(
      listenable: viewModel,
      builder: (context, _) {
        return Column(
          children: [
            if (viewModel.errorMessage != null)
              Text('오류: ${viewModel.errorMessage}', style: TextStyle(color: Colors.red)),
            Text('현재 카운트: ${viewModel.count ?? 0}'),
            TextButton(
              onPressed: viewModel.increment,
              child: const Text('증가'),
            ),
          ],
        );
      },
    );
  }
}
```
MVVM의 장점
관심사 분리: UI, 로직, 데이터가 분리되어 유지보수가 쉽음

테스트 용이: Model과 ViewModel은 Flutter 없이 테스트 가능함

재사용성: ViewModel은 다른 플랫폼(예: 웹)에서도 재사용할 수 있음

안정성: 오류 처리를 ViewModel에서 중앙 관리해 UI가 안정적임

활용 팁
복잡한 앱: Model을 Repository와 DataSource로 세분화해 확장성 있게 구성함

상태 관리: Provider 외에 Riverpod, BLoC 등을 활용할 수 있음

데이터 흐름: View → ViewModel → UseCase → Repository → DataSource 순으로 호출되며, 데이터는 반대 방향으로 전달됨