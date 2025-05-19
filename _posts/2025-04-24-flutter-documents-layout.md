---
title: "[Documents] Layout"
excerpt: "Flutter Layout 문서읽기"

categories:
  - documents
tags:
  - [flutter, layout]

permalink: /documents/Flutter-Layout/

toc: true
toc_sticky: true

date: 2025-04-26
last_modified_at: 2025-04-26
---

# [Flutter Layout 문서](https://docs.flutter.dev/get-started/fundamentals/layout)

# 목차

1. 레이아웃의 이해
2. 단일 위젯 레이아웃
3. Column과 Row
4. Devtool 및 Debugging Layout
5. Scroll Widget
6. 적응형 레이아웃

# 레이아웃의 이해

- 플러터 레이아웃 매커니즘의 핵심은 위젯임
- 레이아웃 모델, 행, 열처럼 보이는 것과 위젯을 배열, 제한 정렬하는 그리드처럼 보이지 않는 것도 모두 위젯임

## 제약 조건

- 위젯은 부모로부터 제약조건을 받음
- 제약조건은 최소, 최대 너비와 최소 및 최대 높이의 4가지 세트임
- 위젯은 이러한 제약 조건 내에서 어떤 크기가 되어야 하는지 정해지고, 너비와 높이를 부모에게 다시 전달함
- 부모 위젯은 원하는 크기와 정렬 방식을 고려하여 위젯의 위치를 설정함 (Center, Row, Column 등)

## Render Box

- Flutter에서 위젯은 기본 Render Box 객체에 의해 렌더링됨. 이러한 객체는 전달된 제약 조건을 처리하는 방법을 결정함

### 상자의 종류

- 최대한 크게 만들려는 상자
  > ex. Center, ListView에서 사용하는 상자
- 자녀와 같은 크기를 유지하려는 상자
  > ex. Transform, Opacity 등
- 특정 크기를 유지하려는 상자
  > ex. Image와 Text에서 사용하는 상자
- 주어진 제약 조건에 따라 달라지는 상자 ([제약조건의 지해 참고](https://docs.flutter.dev/ui/layout/constraints))
  > ex. Row, Column(flex 상자)

## 단일 위젯 레이아웃

- Flutter에서 단일 위젯을 배치하려면 화면에서 위치를 변경할 수 있는 Center 위젯으로 Text나 Image와 같은 위젯을 감쌀 수 있음

```dart
Widget build(BuildContext context) {
  return Center(
    child: BorderedImage(),
  );
}

```

### Container

- Container는 레이아웃, 페인팅, 위지 지정, 크기 조정을 담당하는 여러 위젯으로 구성된 편리한 위젯임
- 레이아웃과 관련하여 위젯에 패딩과 여백을 추가하는 데 사용할 수 있고, 자체적으로 Padding 위젯도 있음

## Column과 Row

- Column과 Row는 위젯을 수직 및 수평으로 정렬하는 데 사용됨
- Column과 Row의 mainAxis는 주 축을 따라 정렬하는 방법을 정의하고, crossAxis는 교차 축을 따라 정렬하는 방법을 정의함

### Overflow

- 레이아웃이 기기에 비해 너무 큰 경우, Overflow가 발생함

![image](https://file.notion.so/f/f/3135856a-f5b4-4fc2-8ec4-db979cd3fd4c/f3b03060-3733-4912-8f84-2d1910908afa/image.png?table=block&id=1e1893ac-8819-80ab-bcf9-db9cfe5d8772&spaceId=3135856a-f5b4-4fc2-8ec4-db979cd3fd4c&expirationTimestamp=1745654400000&signature=eTyJK2JHLy4CCG0pPJmLHzfON_kKQTZIfcMOy5yfEDE&downloadName=image.png)

- Expanded 위젯을 사용하여 Column과 Row의 자식 위젯이 화면에 맞게 조정되도록 할 수 있음

```dart
Widget build(BuildContext context) {
  return const Row(
    children: [
      Expanded(
        child: BorderedImage(width: 150, height: 150),
      ),
      Expanded(
        child: BorderedImage(width: 150, height: 150),
      ),
      Expanded(
        child: BorderedImage(width: 150, height: 150),
      ),
    ],
  );
}
```

- Expanded 위젯은 Column과 Row의 자식 위젯이 화면에 맞게 조정되도록 할 수 있음

```dart
Widget build(BuildContext context) {
  return const Row(
    children: [
      Expanded(
        child: BorderedImage(width: 150, height: 150),
      ),
      Expanded(
        flex: 2,
        child: BorderedImage(width: 150, height: 150),
      ),
      Expanded(
        child: BorderedImage(width: 150, height: 150),
      ),
    ],
  );
}
```

![image](https://file.notion.so/f/f/3135856a-f5b4-4fc2-8ec4-db979cd3fd4c/0f94e703-41b7-4b52-8847-09ec3c2af974/image.png?table=block&id=1e1893ac-8819-8087-adec-e00601671612&spaceId=3135856a-f5b4-4fc2-8ec4-db979cd3fd4c&expirationTimestamp=1745654400000&signature=Pzm7ZNqrur3s-PwR96JVPSr2y_MqDBwy-nYcFFq84RE&downloadName=image.png)

# Devtool 및 Debugging Layout

- Render box의 제약 조건이 제한되지 않거나 무한하다면, 즉 최대 너비 또는 최대 높이가 double.infinity로 설정된 경우 box는 제대로 작동하지 않으며 디버그 모드에서 예외가 발생함

## 대표적인 예외

- RenderBox가 무제한 제약 조건을 갖는 가장 흔한 경우는 FlexBox(Row 또는 Column) 내부와 ListView와 같은 스크롤 가능한 위젯을 사용할 때 발생함

# Scrollable 위젯

## ListView

- ListView는 콘텐츠가 렌더 박스보다 길면 자동으로 스크롤을 제공하는 열 형태의 위젯으로 Column과 Row를 사용하는 방식과 유사함
- Column과 Row와는 달리 ListView는 자식 요소가 교차 축의 사용 가능한 모든 공간을 차지해야함

```dart
Widget build(BuildContext context) {
  return ListView(
    children: const [
      BorderedImage(),
      BorderedImage(),
      BorderedImage(),
    ],
  );
}
```

![image](https://file.notion.so/f/f/3135856a-f5b4-4fc2-8ec4-db979cd3fd4c/bad2816b-1f24-441b-94c6-fedb0b745905/image.png?table=block&id=1e1893ac-8819-8035-9177-f232ffb1cfe5&spaceId=3135856a-f5b4-4fc2-8ec4-db979cd3fd4c&expirationTimestamp=1745654400000&signature=RoReahrmLt2Yx6OcKbulUCaNx4itD59r63FPjNuT4-0&downloadName=image.png)

## ListView.builder

- ListView 목록 항목의 개수를 알 수 없거나 매우 많거나 무한할 때 사용함
- 빌더 생성자는 현재 화면에 표시되는 자식 항목만 빌드함

- 사용자 상호 작용이나 기타 요인에 따라 위젯의 고유한 특성을 변경해야 하는 경우.
- 해당 값이 변경되면 위젯을 다시 빌드하여 UI 부분을 업데이트해야 함.
- 개체를 변경할 때마다 State(예: 카운터 증가) 의 빌드 메서드를 다시 setState()호출하여 사용자 인터페이스를 업데이트하도록 프레임워크에 신호를 보내도록 호출해야 함.

# Adaptive layouts (적응형 레이아웃)

- Flutter는 모바일, 태블릿, 데스크톱 및 웹 앱을 만드는 데 사용되므로 화면 크기나 입력 장치 등에 따라 앱의 동작을 다르게 조정해야 할 가능성이 높음
- 이를 앱의 적응형, 반응형 레이아웃이라고 함

## 빌더 패턴

- 적응형 레이아웃을 만드는 데 가장 유용한 위젯 중 하나는 LayoutBuilder로서, 플러터에서 "빌더" 패턴을 사용하는 여러 위젯 중 하나임

  - ListView.builder: List의 항목을 지연 렌더링하는 데 사용됨
  - GridView.builder
  - Builder: BuildContext 위젯 코드에 접근하는 데 유용함
  - LayoutBuilder: 뷰포트 크기에 따라 반응형 레이아웃을 생성하는 데 사용됨
  - FutureBuilder

- LayoutBuilder다음 예에서는 뷰포트가 600픽셀 이하인지, 600픽셀보다 큰지에 따라 변경 사항이 반환됨

```dart
Widget build(BuildContext context) {
  return LayoutBuilder(
    builder: (BuildContext context, BoxConstraints constraints) {
      if (constraints.maxWidth <= 600) {
        return _MobileLayout();
      } else {
        return _DesktopLayout();
      }
    },
  );
}
```

![image](https://file.notion.so/f/f/3135856a-f5b4-4fc2-8ec4-db979cd3fd4c/2dc0cfd1-73a5-4fc6-a6e4-3777ce5f2e03/image.png?table=block&id=1e1893ac-8819-80bd-93f9-f6b63abd6607&spaceId=3135856a-f5b4-4fc2-8ec4-db979cd3fd4c&expirationTimestamp=1745654400000&signature=H2WrpnjprKkg9RH3cTczC9BvfP6v4xLz9CsZKi1I4_E&downloadName=image.png)

- 한편, ListView.builder 생성자의 itemBuilder 콜백에는 빌드 컨텍스트와 int가 전달되는데, 이 int는 현재 항목의 인덱스임

```dart
final List<ToDo> items = Repository.fetchTodos();

Widget build(BuildContext context) {
  return ListView.builder(
    itemCount: items.length,
    itemBuilder: (context, idx) {
      var item = items[idx];
      return Padding(
        padding: const EdgeInsets.all(8.0),
        child: Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Text(item.description),
            Text(item.isComplete),
          ],
        ),
      );
    },
  );
}
```

- 다음과 같이 특정 인덱스의 요소만 조작할 수도 있음

```dart
final List<ToDo> items = Repository.fetchTodos();

Widget build(BuildContext context) {
  return ListView.builder(
    itemCount: items.length,
    itemBuilder: (context, idx) {
      var item = items[idx];
      return Container(
        color: idx % 2 == 0 ? Colors.lightBlue : Colors.transparent,
        padding: const EdgeInsets.all(8.0),
        child: Row(
          mainAxisAlignment: MainAxisAlignment.spaceBetween,
          children: [
            Text(item.description),
            Text(item.isComplete),
          ],
        ),
      );
    },
  );
}
```
