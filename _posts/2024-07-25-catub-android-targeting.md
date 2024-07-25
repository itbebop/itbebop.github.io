---
title: "[Catub]Android targetting"
excerpt: "첫 App project"

categories:
  - catub
tags:
  - [proeject, android, catub, github, android targetting]

# permalink: /categories/project/catub/

toc: true
toc_sticky: true

date: 2024-07-25
last_modified_at: 2024-07-25

published: true
---


# 1. 메일 수신(7월9일)💥
![Pasted image 20240725094046](https://github.com/user-attachments/assets/9f214c48-5494-4bd3-945e-d2469738a8df)



# [Google Play Console 고객센터](https://support.google.com/googleplay/android-developer/answer/11926878?hl=ko)

## Google Play 앱의 대상 API 수준 요구사항

2024년 8월 31일부터 다음과 같습니다.

- 새 앱과 앱 업데이트는 Google Play에 제출하려면 Android 14(API 수준 34)를 타겟팅해야 합니다(단, Android 13 이상을 타겟팅해야 하는 Wear OS 및 Android TV 앱은 제외).
- 기존 앱이 Android 13(API 수준 33) 이상을 타겟팅해야 앱의 대상 API 수준보다 상위 버전의 Android OS를 실행하는 기기에서 신규 사용자가 계속 사용할 수 있습니다. Android 12(API 수준 31) 이하(Wear OS의 경우 Android 10 이하, TV OS의 경우 Android 11 이하)를 타겟팅하는 앱은 앱의 대상 API 수준과 같거나 하위 버전의 Android OS를 실행하는 기기에서만 사용할 수 있습니다.

앱을 업데이트하기 위해 시간이 더 필요한 경우 2024년 11월 1일까지 연장을 요청할 수 있습니다. 올해 말에는 Play Console에서 앱의 기한 연장 요청 양식을 이용할 수 있게 됩니다.

새로운 Android 버전이 출시될 때마다 전반적인 Android 플랫폼 사용자 환경, 보안, 성능이 개선됩니다. ==각 앱은 매니페스트 파일에 `targetSdkVersion`(대상 API 수준이라고도 함)을 지정합니다.== 대상 API 수준은 앱이 다양한 Android 버전에서 실행되는 방식을 나타냅니다.

최신 API 수준을 타겟팅하도록 앱을 구성하면 사용자가 보안, 개인 정보 보호, 성능 개선의 혜택을 받을 수 있으며, 이전 Android 버전(지정된 `minSdkVersion`까지)에서도 앱을 실행할 수 있습니다.

Android 및 Google Play 사용자에게 안전한 환경을 제공하기 위해 Google Play의 모든 앱은 아래 나열된 대상 API 수준 요구사항을 충족해야 합니다.

# 2. 조치내역✨

## 경로
	app>build.gradle

## 수정사항

- 변경 전


```
    defaultConfig {

        applicationId "com.itbebop.catub"

        minSdkVersion flutter.minSdkVersion

        targetSdkVersion 28

        versionCode flutterVersionCode.toInteger()

        versionName flutterVersionName

    }
```

- 변경 후

```
    defaultConfig {

        applicationId "com.itbebop.catub"

        minSdkVersion flutter.minSdkVersion

        targetSdkVersion 34 // 28 -> 34로 변경

        versionCode flutterVersionCode.toInteger()

        versionName flutterVersionName

    }
```

# 3. 정리🧹

- flutter 공식문서를 보니 targetSdk의 기본값은 flutter.targetSdkVersion이라고 하고, 
- flutter.targetSdkVersion는 flutter sdk의 설치 경로(flutter\packages\flutter_tools\gradle\src\main\groovy\flutter.groovy)에서 가져오는 거 같은데
- 위 파일을 확인해보니 아래와 같이 되어있음
```
    static int compileSdkVersion = 34
    static int minSdkVersion = 19
    static int targetSdkVersion = 33
```
- 어차피 33이었어도 최신 버전인 34가 아니라서 문제가  되었을 듯함
- 기본값으로 관리를 하는 경우에는 flutter upgrade를 하여 flutter 버전과 연동하여 관리가 할 수 있을 것 같음
- 발생한 에러 수정과정에서 기본값을 지우고 직접 버전을 넣은 거 같은데 다음 앱 출시에서는 기본값으로 관리해보자
- 수정해서 다시 제출하더라도 배포/승인이 되어야 경고문구 없어진다고 함

# 4. 참고 자료📜

## [flutter 안드로이드 타켓팅 대응 (블로그)](https://blog.lovizu.com/entry/flutter-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-%ED%83%80%EC%BC%93%ED%8C%85-%EB%8C%80%EC%9D%91)

## [관련 flutter 공식문서](https://docs.flutter.dev/deployment/android)

| Property                   | Purpose                                                                                                                                                                | Default Value                        |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- |
| `compileSdk`         | 앱이 컴파일되는 Android API 레벨입니다. 이는 사용 가능한 가장 높은 버전이어야 합니다. 이 속성을 로 설정하면 앱이 에 특정한 API를 사용하지 않는 한 `31`API 또는 이전 버전을 실행하는 기기에서 앱을 실행합니다 .`30``31`                        |                            |
| `defaultConfig`      |                                                                                                                                                                   |                            |
| `.applicationId`     | 앱을 식별하는 최종 고유 [애플리케이션 ID입니다 .](https://developer.android.com/studio/build/application-id)                                                                         |                            |
| `.minSdk`            | 앱이 실행되도록 설계한 최소 [Android API 레벨입니다 .](https://developer.android.com/studio/publish/versioning#minsdk)                                                             | `flutter.minSdkVersion`    |
| `.targetSdk`         | 앱 실행을 테스트한 Android API 레벨입니다. 앱은 이 레벨까지의 모든 Android API 레벨에서 실행되어야 합니다.                                                                                           | `flutter.targetSdkVersion` |
| `.versionCode`       | [내부 버전 번호를](https://developer.android.com/studio/publish/versioning) 설정하는 양의 정수 . 이 번호는 어느 버전이 다른 버전보다 최신인지만 결정합니다. 숫자가 클수록 최신 버전을 나타냅니다. 앱 사용자는 이 값을 절대 보지 못합니다. |                            |
| `.versionName`       | 앱이 버전 번호로 표시하는 문자열입니다. 이 속성을 원시 문자열 또는 문자열 리소스에 대한 참조로 설정합니다.                                                                                                     |                            |
| `.buildToolsVersion` | Gradle 플러그인은 프로젝트에서 사용하는 Android 빌드 도구의 기본 버전을 지정합니다. 빌드 도구의 다른 버전을 지정하려면 이 값을 변경합니다.                                                                             |                            |

