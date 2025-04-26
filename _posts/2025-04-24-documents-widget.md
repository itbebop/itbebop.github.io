---

title: "[Documents] Widget"
excerpt: "Flutter Widget 문서읽기"

categories:

- documents
  tags:
- [Flutter, Widget]

permalink: /documents/Flutter-Widget/

toc: true
toc_sticky: true

date: 2025-04-24
last_modified_at: 2025-04-24

[Flutter Widget 문서1](https://docs.flutter.dev/get-started/fundamentals/widgets)

# 목차

1. 위젯의 개념
2. 위넷의 기능
3. 위젯의 구성
4. 위젯의 상태에 따른 분류
5. 상태 관리

# 위젯

## 위젯의 개념

- 위젯은 Flutter 앱 사용자 인터페이스의 구성 요소이며 각 위젯은 사용자 인터페이스 일부에 대한 불변 선언임.
- 위젯은 구성을 기반으로 계층 구조를 형성하여 각 위젯은 상위 위젯 내에 중첩되며 상위 위젯으로부터 컨텍스트를 받을 수 있음.

## 위젯의 기능

- 효율적인 UI 업데이트: 앱은 프레임워크에 계층 구조의 위젯을 다른 위젯으로 바꾸도록 지시하여 이벤트(예: 사용자 상호 작용)에 대한 응답으로 사용자 인터페이스를 업데이트함.
- 그런 다음 프레임워크는 새 위젯과 이전 위젯을 비교하고 사용자 인터페이스를 효율적으로 업데이트합니다.

## 위젯의 구성

- 위젯은 일반적으로 강력한 효과를 생성하기 위해 결합되는 여러 개의 작은 단일 목적 위젯으로 구성됨

## 위젯 상태

### stateless widget

- 변경 가능한 상태가 없음.
- 즉, 시간이 지남에 따라 변경되는 속성(예: 아이콘 또는 레이블)이 없음

### stateful widget

- 사용자 상호 작용이나 기타 요인에 따라 위젯의 고유한 특성을 변경해야 하는 경우.
- 해당 값이 변경되면 위젯을 다시 빌드하여 UI 부분을 업데이트해야 함.
- 개체를 변경할 때마다 State(예: 카운터 증가) 의 빌드 메서드를 다시 setState()호출하여 사용자 인터페이스를 업데이트하도록 프레임워크에 신호를 보내도록 호출해야 함.

## 상태관리

- build메서드로 모든 하위 위젯이 필요한 데이터로 인스턴스화되도록 보장할 수 있음.
- 그러나 위젯 트리가 깊어질수록 상태 정보를 트리 계층 구조 위아래로 전달하는 것이 번거로워짐.
- 따라서 InheritedWidget를 사용하여 공유 상위 항목에서 데이터를 가져오도록 함.

![image](https://github.com/itbebop/itbebop.github.io/assets/86880025/5945a423-4e61-4324-922b-d2bf0547c75a)

- ExamWidget이나 GradeWidget 객체가 StudentState로부터 데이터가 필요할 때마다 아래와 같이 접근할 수 있음

```
final studentState = StudentState.of(context);
```

- of(context)의 호출은 빌드 컨텍스트(현재 위젯 위치에 대한 핸들)를 사용하고 트리에서 유형 과 일치하는 가장 가까운 조상을 반환함(StudentState).
- 또한 상태 변경이 이를 사용하는 하위 위젯의 재빌드를 트리거해야 하는지 여부를 결정하기 위해 Flutter가 호출하는 메소드 InheritedWidget도 제공합니다 .updateShouldNotify()

- Flutter 자체는 애플리케이션 전체에 널리 퍼져 있는 색상 및 유형 스타일과 같은 속성을 포함하는 애플리케이션의 시각적 테마인 InheritedWidget와 같은 공유 상태를 위한 프레임워크의 일부로 광범위하게 사용됨.
- MaterialApp build() 메서드는 빌드 시 트리에 테마를 삽입한 다음 위젯이 계층 구조의 더 깊은 곳에서 .of() 메서드를 사용하여 관련 테마 데이터를 조회할 수 있음.
- 예시

```
Container(
  color: Theme.of(context).secondaryHeaderColor,
  child: Text(
    'Text with a background color',
    style: Theme.of(context).textTheme.titleLarge,
  ),
);
```

- 많은 Flutter 앱은 InheritedWidget에 대한 래퍼를 제공하는 공급자와 같은 유틸리티 패키지를 사용함.
- Flutter의 계층형 아키텍처는 flutter_hooks 패키지와 같이 상태를 UI로 변환하는 대체 접근 방식도 가능하게 함.
