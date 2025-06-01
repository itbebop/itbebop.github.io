---
title: "[Packages] GetX"
excerpt: "GetX 패키지 소개 및 사용법"

categories:
  - packages
tags:
  - [flutter, state management, getx]

permalink: /packages/state management/

toc: true
toc_sticky: true

date: 2025-06-01
last_modified_at: 2025-06-01
---

[GetX 패키지](https://pub.dev/packages/get) 문서 읽기

---
title: "[Packages] GetX1"
excerpt: "GetX 패키지 소개 및 사용법"

categories:
  - packages
tags:
  - [flutter, state management, getx]

permalink: /packages/state management/

toc: true
toc_sticky: true

date: 2025-06-01
last_modified_at: 2025-06-01
---

[GetX 패키지](https://pub.dev/packages/get) 문서 읽기

# 0. 목차

1. GetX 소개 
2. ProviderScope와 Provider
3. Riverpod 사용방법
4. ref.read/ ref.watch/ ref.listen
5. 알아두면 좋은 자주 쓰는 기능들

아래는 GetX에 대한 소개와 사용법을 요청하신 문체(서술어를 “임”, “함” 등으로 통일)로 정리한 내용임.

---

# 1. GetX 소개

- **GetX**는 Flutter에서 상태 관리, 라우트(화면 이동) 관리, 의존성 주입(Dependency Injection)을 한 번에 제공하는 라이브러리
- 코드를 간결하게 만들고, 생산성과 성능, 유지보수성을 높여줌.

---
# 2. 단순 산태 관리(Simple State Management)와 반응형 상태 관리(Reactive State Management)
- GetX는 두 가지 상태 관리 방식을 제공함.
  - **단순 상태 관리**: 간단한 상태를 관리할 때 사용
  - **반응형 상태 관리**: UI가 상태 변화에 자동으로 반응하도록 함
- **단순 상태 관리**는 `GetBuilder`를 사용하여 상태를 업데이트함.
```dart
import 'package:flutter/material.dart';
import 'package:get/get.dart';
void main() => runApp(GetMaterialApp(home: Home()));  
class Home extends StatelessWidget {
  var count = 0;
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('GetX 단순 상태 관리 예시')),
      body: Center(
        child: GetBuilder<Home>(
          init: this,
          builder: (_) => Text('카운트: $count'),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          count++;
          update(); // 상태 업데이트
        },
        child: Icon(Icons.add),
      ),
    );
  }
}
```
- 위 코드에서 `GetBuilder`를 사용하여 상태를 관리함.  
  `update()` 메서드를 호출하면 UI가 갱신됨.
- **반응형 상태 관리**는 `Obx` 위젯을 사용하여 상태 변화를 자동으로 감지하고 UI를 갱신함.
```dart
import 'package:flutter/material.dart';
import 'package:get/get.dart';
void main() => runApp(GetMaterialApp(home: Home()));
class Home extends StatelessWidget {
  var count = 0.obs; // 반응형 변수 선언
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('GetX 반응형 상태 관리 예시')),
      body: Center(
        child: Obx(() => Text('카운트: $count')),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => count++,
        child: Icon(Icons.add),
      ),
    );
  }
}
```
- 위 코드에서 `count`는 반응형 변수임.  
  `Obx` 위젯으로 감싸서, `count` 값이 바뀔 때마다 UI가 자동으로 갱신됨.
  
- GetX는 상태 관리 외에도 라우트 관리, 의존성 주입 등 다양한 기능을 제공함.
- **라우트 관리**: 화면 이동을 간단하게 처리할 수 있음.
```dart
import 'package:flutter/material.dart';
import 'package:get/get.dart';
void main() => runApp(GetMaterialApp(home: Home()));
class Home extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('GetX 라우트 관리 예시')),
      body: Center(
        child: ElevatedButton(
          onPressed: () => Get.to(NextPage()),
          child: Text('다음 페이지로 이동'),
        ),
      ),
    );
  }
}
class NextPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('다음 페이지')),
      body: Center(
        child: ElevatedButton(
          onPressed: () => Get.back(),
          child: Text('이전 페이지로 돌아가기'),
        ),
      ),
    );
  }
}
```
- 위 코드에서 `Get.to()`로 다음 페이지로 이동하고, `Get.back()`으로 이전 페이지로 돌아갈 수 있음.
- **의존성 주입**: 컨트롤러를 전역으로 등록하고, 어디서든 접근할 수 있음.
```dart
import 'package:flutter/material.dart';
import 'package:get/get.dart';
void main() => runApp(GetMaterialApp(home: Home()));
class Home extends StatelessWidget {
  final CounterController c = Get.put(CounterController());

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('GetX 의존성 주입 예시')),
      body: Center(
        child: Obx(() => Text('카운트: ${c.count}')),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: c.increment,
        child: Icon(Icons.add),
      ),
    );
  }
}
class CounterController extends GetxController {
  var count = 0.obs;

  void increment() => count++;
}
```
- 위 코드에서 `Get.put()`으로 컨트롤러를 등록하고, `Obx`로 감싸서 UI가 자동으로 갱신됨.
- GetX는 보일러플레이트 코드가 적고, 직관적인 API로 빠른 개발이 가능함.
 

---

## 3. GetX 상태 관리 예시

### 3.1. 반응형 변수 사용

```dart
import 'package:flutter/material.dart';
import 'package:get/get.dart';

void main() => runApp(GetMaterialApp(home: Home()));

class Home extends StatelessWidget {
  var count = 0.obs; // 반응형 변수 선언

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('GetX 예시')),
      body: Center(
        child: Obx(() => Text('카운트: $count')),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => count++,
        child: Icon(Icons.add),
      ),
    );
  }
}
```

위 코드에서 `count`는 반응형 변수임.  
`Obx` 위젯으로 감싸서, `count` 값이 바뀔 때마다 UI가 자동으로 갱신됨.

---

### 3.2. 컨트롤러로 상태 관리

컨트롤러(Controller)를 별도로 만들어서 상태를 관리함.

```dart
// counter_controller.dart
import 'package:get/get.dart';

class CounterController extends GetxController {
  var count = 0.obs;

  void increment() => count++;
}
```

```dart
// main.dart
import 'package:flutter/material.dart';
import 'package:get/get.dart';
import 'counter_controller.dart';

void main() => runApp(GetMaterialApp(home: Home()));

class Home extends StatelessWidget {
  final CounterController c = Get.put(CounterController());

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('GetX 컨트롤러 예시')),
      body: Center(
        child: Obx(() => Text('카운트: ${c.count}')),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: c.increment,
        child: Icon(Icons.add),
      ),
    );
  }
}
```

컨트롤러를 `Get.put()`으로 등록하고, 위젯에서 `Get.find()` 없이 바로 접근할 수 있음.  
`Obx`로 감싸서, 컨트롤러의 반응형 변수 값이 바뀌면 UI가 자동으로 갱신됨.

---

## 4. 라우트(화면 이동) 관리

GetX는 화면 이동도 간단하게 처리할 수 있음.

```dart
// 다음 화면으로 이동
Get.to(NextPage());

// 이전 화면으로 돌아가기
Get.back();

// 이름을 이용한 라우트 등록 및 이동
GetMaterialApp(
  initialRoute: '/',
  getPages: [
    GetPage(name: '/', page: () => Home()),
    GetPage(name: '/next', page: () => NextPage()),
  ],
);
// 이름으로 이동
Get.toNamed('/next');
```

---

## 5. 의존성 주입(Dependency Injection)

컨트롤러를 전역으로 등록하고, 어디서든 접근할 수 있음.

```dart
Get.put(CounterController()); // 앱 시작 시 등록

// 다른 위젯에서 접근
final c = Get.find();
```

---

## 6. 요약

- **상태 관리**: 반응형 변수와 컨트롤러로 상태를 효율적으로 관리함.
- **라우트 관리**: 간단한 코드로 화면 이동을 처리함.
- **의존성 주입**: 컨트롤러를 전역으로 등록하고 어디서든 접근 가능함.
- **코드 간결**: 보일러플레이트 코드가 적고, 직관적인 API로 빠른 개발이 가능함.

---
Perplexity로부터의 답변: pplx.ai/share