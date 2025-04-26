---
title: "[Documents] Flutter Architeture"
excerpt: "Flutter architectural overview 문서읽기"

categories:
  - documents
tags:
  - [Flutter, Architecture]

permalink: /documents/Flutter-Architeture/

toc: true
toc_sticky: true

date: 2024-06-11
last_modified_at: 2025-04-24
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

> Flutter는 확장 가능한 계층형 시스템으로 설계되어 모든 부분은 선택적이고 교체 가능함.

## 1. 프레임워크

- 앱의 위젯 트리를 씬으로 합성

## 2. 엔진

- 화면의 새 프레임을 그려야 할 때마다 합성된 장면을 래스터화하는 역할을 함.
- Flutter 엔진의 핵심은 대부분 C++로 작성됨.

## 3. 엠베더

- Flutter 코드를 특정 플랫폼(Android, IOS, Mac, Windows)에서 애플리케이션의 전체 콘텐츠로 하거나, 기존 애플리케이션에 모듈로 통합할 수 있도록 함
- 현재 Android용 Java 및 C++, iOS 및 macOS용 Objective-C/Objective-C++, Windows 및 Linux용 C++

# Flutter의 반응형 사용자 인터페이스

## 1. 반응형 UI란

- 사용자 인터페이스의 초기 상태가 한 번 제공된 다음 이벤트에 대한 응답으로 **애플리케이션 상태가 변경될 때** 프레임워크가 런타임에 인터페이스를 업데이트하는 작업을 수행함

## 2. 기존의 UI

사용자 인터페이스의 초기 상태가 한 번 제공된 다음 이벤트에 대한 응답으로 **런타임 시** 별도로 업데이트됨

## 3. flutter의 반응형 UI 방식

- 변경사항들이 uI에 반영되도록 하기 위해서 컨트롤러를 통해 데이터 변경 사항을 모델에 푸시한 다음 모델이 컨트롤러를 통해 새 상태를 뷰에 푸시함 (MVC와 같은 접근 방식)
- UI 요소를 만들고 업데이트하는 것은 쉽게 동기화되지 않을 수 있는 두 개의 별도 단계라는 점 때문에 flutter는 사용자 인터페이스를 기본 상태에서 명시적으로 분리하는 접근방식을 취함.

  - 즉, React 스타일 API를 사용하여 UI만 생성한 후에, 프레임워크가 해당 구성 중 하나를 사용하여 사용자 인터페이스를 생성하거나나 업데이트함.

- Flutter에서 위젯은 객체 트리를 구성하는 데 사용되는 불변 클래스로 표시되고, 이러한 위젯은 레이아웃을 위한 별도의 개체 트리를 관리하거나, 트리 간 결합을 위해 별도의 개체 트리를 관리하는 데에 사용됨

- Flutter는 본질적으로 트리의 수정된 부분을 효율적으로 탐색하고, 객체 트리를 하위 레벨 객체 트리로 변환하고, 이러한 트리 간에 변경 사항을 전파하는 일련의 메커니즘으로 구성됨

## 4. UI 컨트롤에 대한 flutter 자체 구현으로 인한 장점

> 1.  무제한 확장성을 제공
>     -> 스위치 컨트롤의 변형을 원하는 개발자는 OS에서 제공하는 확장 지점에 국한되지 않고 임의의 방식으로 스위치 컨트롤을 만들 수 있음.
> 2.  Flutter 코드와 플랫폼 코드 간에 전환하지 않고도 Flutter가 전체 장면을 한 번에 합성할 수 있도록 하여 심각한 성능 병목 현상을 방지함.
> 3.  운영 체제 종속성에서 애플리케이션 동작을 분리함.
>     -> OS가 컨트롤 구현을 변경한 경우에도 애플리케이션은 모든 버전의 OS에서 동일하게 표현됨.

# 참고 개념

1. BSD 라이센스

- 버클리 소프트웨어 배포 라이선스라고도 하는 BSD 라이선스는 소프트웨어의 무료 사용, 수정 및 배포를 허용하는 일종의 오픈 소스 라이선스입니다.
- BSD 라이선스는 허가된 소프트웨어의 사용 및 배포에 최소한의 제한을 두는 허용적 라이선스입니다.
- BSD 라이선스의 주요 요구 사항은 소프트웨어 재배포 시 라이선스 사본과 책임 부인을 포함해야 한다는 것입니다. 많은 소프트웨어 개발자와 회사는 이 라이선스를 사용하여 소프트웨어의 권리를 유지하면서 광범위한 사용자가 작업에 액세스할 수 있도록 합니다.

  (출처: https://appmaster.io/ko/blog/bsd-raisenseuneun-mueosibnigga)

2. 불변 클래스(Immutable Class)
   > 예. String, Boolean, Integer, Float, Long (자바)

- Immutable Class는 변경이 불가능한 클래스
- 레퍼런스 타입의 객체이기 때문에 heap 영역이 생성됨
- 생성자, 접근메서드에 대한 방어 복사가 필요없음.
- 멀티쓰레드 환경에서 동기화 처리없이 객체를 공유할 수 있음.
- (Thread-safe) 불변이기 때문에 객체가 안전함.

3. 레스터화

- Rasterization은 벡터 그래픽스를 픽셀 기반 이미지로 변환하는 프로세스

4. MVC

- Model-View-Controller
- Model: 데이터와 비즈니스 로직을 관리하는 부분
- View: 사용자 인터페이스를 구성하는 부분으로 사용자에게 보여지는 화면을 담당함.
- Controller: Model과 View를 연결하는 부분으로 사용자의 입력을 처리하고 Model과 View를 업데이트함.
