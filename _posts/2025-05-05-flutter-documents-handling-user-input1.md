---

title: "[Documents] Handling user input1(Buttons)"
excerpt: "Flutter Handling user input 문서읽기"

categories:
- documents

tags:
- [Flutter, Handling user input, buttons]

permalink: /documents/Handling user input1/

toc: true
toc_sticky: true

date: 2025-05-05
last_modified_at: 2025-05-05
---

[Flutter Handling user input 문서](https://docs.flutter.dev/get-started/fundamentals/user-input#selectabletext)

# 0. 목차

1. 버튼의 종류
(1) ElevatedButton
(2) FiledButton
(3) Tonal Button
(4) OutlinedButton
(5) TextButton
(6) IconButton
(7) FloatingActionButton

2. 반응형 버튼 만들기기

# 1. 버튼의 종류

![버튼 종류](https://docs.flutter.dev/assets/images/docs/fwe/user-input/material-buttons.png)

## 1. ElevatedButton
- 그림자를 넣어서 입체감있는 형태의 버튼

## 2. FiledButton
- 저장, 가입, 확인 등의 중요한, 기본 작업에 주로 사용하는 버튼

## 3. Tonal Button
- FiledButton과 OutlinedButton 사이의 버튼으로 OutlinedButton보다는 더욱 강조해야 할 때 사용

## 4. OutlinedButton
- 텍스트와 테두리가 있는 버튼.
- 중요한 작업을 포함할 수도 있지만 앱의 기본 작업에 사용되진 않음

## 5. TextButton
- 텍스트만 있는 버튼

## 6. IconButton
- 아이콘만 있는 버튼

## 7. FloatingActionButton
- 콘텐츠 위에 마우스를 올리면 나타나는 버튼

### FloatingActionButton을 BottomNavigtionbar에 넣는 방법

```dart
Scaffold(
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: const Icon(Icons.add),
  ),
  bottomNavigationBar : BottomAppBar(
    ...
  ),
  floatingActionButtonLocation:
    FloatingActionButtonLocation.centerDocked, // endDocked 등으로 변경 가능함
);
```

# 2. 반응형 버튼 만들기

## 상태 저장 및 상태 비저장 위젯

- 상태 비저장 위젯은 StatelessWidget의 하위 요소로서 변경되지 않음
  - 예. Icon, IconButton, Text 등
- 상태 저장 위젯은 StatefulWidget의 하위 요소로서 setState()함수로 변경할 수 있음
  - 예. Checkbox, Radio, Slider, InkWell, Form, TextFormField 등

## 상태 저장 위젯 만들기

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

/// 앱의 루트 위젯입니다.
class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      home: Scaffold(
        appBar: AppBar(title: const Text('Flutter Demo')),
        body: const Center(
          // FavoriteWidget을 앱의 메인 화면에 배치합니다.
          child: FavoriteWidget(),
        ),
      ),
    );
  }
}

/// 별 아이콘과 즐겨찾기 개수를 포함하는 사용자 정의 상태 저장 위젯입니다.
class FavoriteWidget extends StatefulWidget {
  const FavoriteWidget({super.key});

  @override
  State<FavoriteWidget> createState() => _FavoriteWidgetState();
}

/// FavoriteWidget의 실제 상태와 UI를 관리하는 클래스입니다.
class _FavoriteWidgetState extends State<FavoriteWidget> {
  // 현재 즐겨찾기 상태를 나타내는 변수 (true: 즐겨찾기됨, false: 해제됨)
  bool _isFavorited = true;

  // 즐겨찾기 개수를 저장하는 변수
  int _favoriteCount = 41;

  /// 별 아이콘을 탭할 때 호출되는 메서드입니다.
  /// 상태를 변경하고 UI를 갱신합니다.
  void _toggleFavorite() {
    setState(() {
      if (_isFavorited) {
        _favoriteCount -= 1;
        _isFavorited = false;
      } else {
        _favoriteCount += 1;
        _isFavorited = true;
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Row(
      mainAxisSize: MainAxisSize.min, // Row가 자식의 크기만큼만 차지하도록 설정
      children: [
        Container(
          padding: const EdgeInsets.all(0),
          child: IconButton(
            // 별 아이콘의 모양을 현재 상태에 따라 변경
            icon: (_isFavorited
                ? const Icon(Icons.star)
                : const Icon(Icons.star_border)),
            color: Colors.red[500],
            onPressed: _toggleFavorite, // 아이콘을 탭하면 _toggleFavorite 호출
          ),
        ),
        // 숫자가 40↔41로 바뀔 때 UI가 점프하지 않도록 SizedBox로 너비 고정
        SizedBox(
          width: 18,
          child: SizedBox(
            child: Text('$_favoriteCount'),
          ),
        ),
      ],
    );
  }
}

```

