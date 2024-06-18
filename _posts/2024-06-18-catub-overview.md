---
title: "[Catub]App overview"
excerpt: "첫 App project"

categories:
  - catub
tags:
  - [proeject, android, catub, github]

# permalink: /project/catub/

toc: true
toc_sticky: true

date: 2024-06-17
last_modified_at: 2024-06-18

published: true
---


# 기획
## 기획 배경
- flutter를 배우면서 github에 기록한 TIL을 간편하게 스마트폰으로 확인할 필요성.
- 스마트폰의 웹브라우저나 github 앱은 depth가 많아서 원하는 내용을 확인하기 번거로움.

## 기획 의도
- 빠르게 스마트폰으로 github에 정리한 파일(markdown형식)을 조회할 수 있는 앱을 만들고자 함.
- 즐겨찾기 기능으로 다시 보고 싶은 내용만 바로 다시 볼 수 있도록 함.
- 우선 초기 버전으로 인증이 필요없이 조회만 가능하도록 빠르게 앱을 만드는 것을 목표로 함.

## 추후 계획
- 마크다운 형식 파일 외에 source나 이미지 등 여러 파일도 조회할 수 있도록 함
- 조회뿐 아니라 github 인증 기능을 추가하여 파일의 작성,수정,삭제도 가능하도록함


## 화면

### 초기화면
- 검색 창에는 사용자 계정 or 사용자 계정/repository 로 검색할 수 있음.
  - 사용자 계정을 검색하는 경우에는 아래 Repository리스트화면처럼 profile과 repository리스트가 나옴.
  - 사용자 계정/repository로 검색하는 경우에는 해당 Repository의 Contents리스트가 나옴.

  ![image](https://github.com/itbebop/flutter_git_blog/assets/86880025/d49b9fb6-54d0-4a49-aae0-f4e1e6743daa)


### Repository리스트 
>최초 github 계정의 사용자 정보와 repository리스트 확인할 수 있는 화면

  ![image](https://github.com/itbebop/flutter_git_blog/assets/86880025/3ebbb266-381c-498b-814d-eb404a9f3b26)



### Repository Contents리스트
>해당 repository의 Contents들을 확인할 수 있는 화면

![image](https://github.com/itbebop/flutter_git_blog/assets/86880025/0de07d40-952d-4c6c-b504-baa44ec0c258)

### Contents 디테일
>선택한 Contents의 내용을 볼 수 있는 화면

![image](https://github.com/itbebop/flutter_git_blog/assets/86880025/c9499adb-d876-4468-aa50-165805709dbe)

### 즐겨찾기
>즐겨찾기한 파일의 목록을 볼 수 있는 화면
- 즐겨찾기의 삭제는 현재 글을 조회해서 표시되어있는 즐겨찾기를 해제하는 경우 즐겨찾기 목록에서 삭제하는 방식
- 추후에 즐겨찾기를 폴더별로 관리 기능 및 목록에서 바로 삭제, 이동 등이 가능하도록 수정 예정

![image](https://github.com/itbebop/flutter_git_blog/assets/86880025/1287b462-6eab-47e4-990c-ed9a939e22e5)

