---
title: "[dart] Constructor(생성자)1"
excerpt: "constructor 종류"

categories:
  - dart
tags:
  - [dart, constructor]

toc: true
toc_sticky: true

date: 2024-06-07
last_modified_at: 2024-06-07
---
# 생성자 종류
## 디폴트 생성자
- 생성자를 선언하지 않은 경우에 디폴트 생성자가 주어짐.
- 디폴트 생성자는 이름이 없고(unnamed), 인수를 갖지 않음
(no-argument).
- 자식 클래스의 디플트 생성자는, 부모 클래스의 디폴트 생성자를 호출함.

## 네임드 생성자
- 다수의 생성자를 구현하거나, 
- 생성자의 이름으로 의미를 부여하는 경우 사용
```
class Point {
  final double x;
  final double y;

  Point(this.x, this.y);

  // 명명된 생성자
  Point.origin()
      : x = xOrigin,
        y = yOrigin;
}
```
## 상수 생성자
- 클래스 내에 절대 바뀌지 않는 객체를 생성하는 경우에 이 객체를 컴파일 타임 상수로 만들 수 있음
- 인스턴스 변수에 final을, 생성자에 const를 붙여서 만들 수 있음
```
class ImmutablePoint {
  static const ImmutablePoint origin = ImmutablePoint(0, 0);

  final double x, y;

  const ImmutablePoint(this.x, this.y);
}
```
- 앱을 실행하는 동안 한번만 객체를 생성하면 다시 생성할 필요없이 다시 가져다가 쓸 수 있어 리소스를 아낄 수 있음.
- Q. 상수 생성자가 항상 상수를 생성하는 건 아님
(https://dart-ko.dev/language/classes#using-constructors)

## Factory 생성자
- 항상 새로운 인스턴스를 생성하지 않도록 factory 생성자 사용
- factory 생성자는 인스턴스를 캐시에서 반환하거나 서브타입의 인스턴스를 반환할 수 있음(Q. late?)
- 다음 예제에서 Logger factory 생성자는 캐시에서 객체를 반환하고, Logger.fromJson factory 생성자는 final 변수를 JSON 객체로 부터 초기화함.
```
class Logger {
  final String name;
  bool mute = false;

  // _cache는 맨 앞의 _ 덕분에 library-private입니다.
  static final Map<String, Logger> _cache = <String, Logger>{};

  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }

  factory Logger.fromJson(Map<String, Object> json) {
    return Logger(json['name'].toString());
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```

# 참고문서
https://dart-ko.dev/language/constructors#이니셜라이저-리스트