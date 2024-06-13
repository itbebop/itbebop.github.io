---
title: "[Documents] Flutter Achiteture"
excerpt: "Flutter architectural overview 문서읽기"

categories:
  - documents
tags:
  - [tag1, tag2]

permalink: /documents/flutter-achitecture/

toc: true
toc_sticky: true

date: 2024-06-11
last_modified_at: 2024-06-13
---
# [Flutter 아키텍처 문서1](https://docs.flutter.dev/resources/architectural-overview)
# 목차
1. Flutter 아키텍처 레이어: flutter 구성 부분.
2. 반응형 사용자 인터페이스 : Flutter 사용자 인터페이스 개발의 핵심 개념.
3. 위젯 소개: Flutter 사용자 인터페이스의 기본 구성 요소.
4. 렌더링 프로세스 : Flutter가 UI 코드를 픽셀로 변환하는 방법.
5. 플랫폼 임베더 개요 : 모바일 및 데스크톱 OS에서 Flutter 앱을 실행할 수 있게 해주는 코드.
6. Flutter를 다른 코드와 통합 : Flutter 앱에서 사용할 수 있는 다양한 기술에 대한 정보.
7. 웹 지원 : 브라우저 환경에서 Flutter의 특징에 대한 특징들.

# Flutter 아키텍처 레이어
![image](https://github.com/itbebop/itbebop.github.io/assets/86880025/2c5d21f0-5944-4129-8e63-365990a427ad)

>Flutter는 확장 가능한 계층형 시스템으로 설계되어 모든 부분은 선택적이고 교체 가능함.
1. 프레임워크
2. 엔진
- 화면의 새 프레임을 그려야 할 때마다 합성된 장면을 래스터화하는 역할을 함.
- Flutter 엔진의 핵심은 대부분 C++로 작성됨.
3. 엠베더
- Flutter 코드를 특정 플랫폼(Android, IOS, Mac, Windows)에서 애플리케이션의 전체 콘텐츠로 하거나, 기존 애플리케이션에 모듈로 통합할 수 있도록 함
- 현재 Android용 Java 및 C++, iOS 및 macOS용 Objective-C/Objective-C++, Windows 및 Linux용 C++  

# 반응형 사용자 인터페이스
## 1) 반응형 UI란
- 인터페이스 상태로의 매핑을 제공하고 프레임워크가 애플리케이션 상태가 변경될 때 런타임에 인터페이스를 업데이트하는 작업을 수행
-  Facebook의 자체 React 프레임워크 작업 에서 영감을 얻었다고 함
## 2) 기존의 UI
사용자 인터페이스의 초기 상태가 한 번 설명된 다음 이벤트에 대한 응답으로 런타임 시 사용자 코드에 의해 별도로 업데이트됨

## 3) flutter의 반응형 UI 방식
- 변경사항들이 uI에 반영되도록 하기 위해서 컨트롤러를 통해 데이터 변경 사항을 모델에 푸시한 다음 모델이 컨트롤러를 통해 새 상태를 뷰에 푸시함 (MVC와 같은 접근 방식)
- UI 요소를 만들고 업데이트하는 것은 쉽게 동기화되지 않을 수 있는 두 개의 별도 단계라는 점 때문에 flutter는 사용자 인터페이스를 기본 상태에서 명시적으로 분리하는 접근방식을 취함. (이해X)
->  UI 설명만 생성하고 프레임워크는 해당 구성을 사용하여 적절하게 사용자 인터페이스를 생성 및/또는 업데이트함.

- Flutter에서 위젯은 객체 트리를 구성하는 데 사용되는 불변 클래스로 표시되는데, 
- 위젯은 레이아웃을 위한 별도의 개체 트리를 관리하는 데 사용되며, 그런 다음 합성을 위한 별도의 개체 트리를 관리하는 데 사용됨 
- Flutter의 핵심은 트리의 수정된 부분을 효율적으로 탐색하고, 개체 트리를 하위 수준 개체 트리로 변환하고, 이러한 트리 전체에 변경 사항을 전파하는 일련의 메커니즘.

# 위젯
## 위젯 개념
- 위젯은 Flutter 앱 사용자 인터페이스의 구성 요소이며 각 위젯은 사용자 인터페이스 일부에 대한 불변 선언임.
- 위젯은 구성을 기반으로 계층 구조를 형성하여 각 위젯은 상위 위젯 내에 중첩되며 상위 위젯으로부터 컨텍스트를 받을 수 있음.

## 위젯의 기능
- 효율적인 UI 업데이트: 앱은 프레임워크에 계층 구조의 위젯을 다른 위젯으로 바꾸도록 지시하여 이벤트(예: 사용자 상호 작용)에 대한 응답으로 사용자 인터페이스를 업데이트함. 
- 그런 다음 프레임워크는 새 위젯과 이전 위젯을 비교하고 사용자 인터페이스를 효율적으로 업데이트합니다.

##  UI 컨트롤에 대한 flutter 자체 구현으로 인한 장점
>1. 무제한 확장성을 제공 
 -> 스위치 컨트롤의 변형을 원하는 개발자는 OS에서 제공하는 확장 지점에 국한되지 않고 임의의 방식으로 스위치 컨트롤을 만들 수 있음.
>2. Flutter 코드와 플랫폼 코드 간에 전환하지 않고도 Flutter가 전체 장면을 한 번에 합성할 수 있도록 하여 심각한 성능 병목 현상을 방지함.
>3. 운영 체제 종속성에서 애플리케이션 동작을 분리함. 
-> OS가 컨트롤 구현을 변경한 경우에도 애플리케이션은 모든 버전의 OS에서 동일하게 표현됨.

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

# 참고 개념
1. BSD 라이센스
- 버클리 소프트웨어 배포 라이선스라고도 하는 BSD 라이선스는 소프트웨어의 무료 사용, 수정 및 배포를 허용하는 일종의 오픈 소스 라이선스입니다. 
- BSD 라이선스는 허가된 소프트웨어의 사용 및 배포에 최소한의 제한을 두는 허용적 라이선스입니다. 
- BSD 라이선스의 주요 요구 사항은 소프트웨어 재배포 시 라이선스 사본과 책임 부인을 포함해야 한다는 것입니다. 많은 소프트웨어 개발자와 회사는 이 라이선스를 사용하여 소프트웨어의 권리를 유지하면서 광범위한 사용자가 작업에 액세스할 수 있도록 합니다.

  (출처: https://appmaster.io/ko/blog/bsd-raisenseuneun-mueosibnigga)

2. 불변 클래스(Immutable Class)
>예. String, Boolean, Integer, Float, Long (자바)
- Immutable Class는 변경이 불가능한 클래스
- 레퍼런스 타입의 객체이기 때문에 heap 영역이 생성됨
- 생성자, 접근메서드에 대한 방어 복사가 필요없음.
- 멀티쓰레드 환경에서 동기화 처리없이 객체를 공유할 수 있음. 
- (Thread-safe) 불변이기 때문에 객체가 안전함.
 
