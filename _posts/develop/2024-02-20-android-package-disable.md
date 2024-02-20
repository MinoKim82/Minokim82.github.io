---
title: Android에서 사용하지 않는 앱 비활성화하기
date: 2024-02-19 09:35:00 +09:00
categories: [S/W]
tags: [Android]
---

안녕하세요! 오늘은 새로 구매한 휴대전화에 설치된 필요하지 않은 앱들을 어떻게 관리할 수 있는지에 대해 알아보겠습니다. 구글 앱이나 제조사에서 기본적으로 설치한 앱들이 저장공간을 차지하는 것은 물론, 때로는 사용자에게 불필요할 수 있습니다. 이런 앱들을 삭제가 되지 않기 때문에 효율적으로 관리하는 방법은 앱을 비활성화 하여 사용하지 않도록 하는 것인데요, ADB를 통해 앱을 비활성화하거나 활성화하는 방법을 자세히 설명해드리겠습니다.

## ADB(Android Debug Bridge)란?

ADB는 Android 개발자들을 위해 설계된 다목적 커맨드 라인 도구입니다. 이 도구는 컴퓨터와 Android 기기 간에 다양한 작업을 수행할 수 있게 해줍니다. ADB는 Android SDK의 일부로 제공되며, USB 또는 네트워크를 통해 Android 기기와 직접적으로 통신할 수 있습니다. ADB의 주요 기능으로는 파일 시스템 접근, 앱 설치 및 제거, 로그 파일 추출, 기기 상태 정보 확인, 스크린 캡쳐, 다양한 시스템 명령 실행 등이 있습니다. 특히, 앱 개발 및 테스트 과정에서 발생하는 문제를 진단하고 해결하는 데 매우 중요한 도구로 활용됩니다.

## 시스템 앱 비활성화의 장점

일반적으로 Android 기기에 설치된 시스템 앱은 사용자가 직접 삭제할 수 없습니다. 설정 메뉴에서조차 비활성화 버튼이 보이지 않는 경우가 많죠. 이러한 상황에서 ADB를 사용하면 시스템 앱을 비활성화시킬 수 있어, 기기의 저장공간을 효율적으로 관리할 수 있습니다.

## 앱 비활성화 하기 

앱을 비활성화하는 방법은 아주 간단합니다. 아래와 같이 명령어를 입력하면 됩니다:

```sh
adb shell pm hide <package name>
```
여기서 <package name>은 비활성화하고자 하는 앱의 패키지 이름입니다. 

`com.example.app`앱을 비활성화 하려면 다음과 같이 입력합니다.

```sh
$ adb shell pm hide com.example.app
Package com.example.app new hidden state: true
```

`Package com.example.app new hidden state: true` 메시지가 표시되면 앱이 비활성화된 것입니다.

## 앱 활성화 하기

앱을 다시 사용하고 하는 경우에는 아래와 같이 입력합니다:

```sh
adb shell pm unhide <package name>
```
`com.example.app`앱을 활성화 하려면 다음과 같이 입력합니다.

```sh
$ adb shell pm unhide com.example.app
Package com.example.app new hidden state: false
```
`Package com.example.app new hidden state: false` 메시지가 표시되면 앱이 다시 활성화된 것입니다.

## 특정 유저에 대해서 앱 비활성화하는 방법

태블릿과 같이 여러 사람이 함께는 쓰는 단말에서는여러 프로필을 활용하시는 분들도 계실 건데요, 특정 프로필 마다 앱의 비활성화가 가능합니다. 아래와 같이 명령어를 입력하면 됩니다:

```sh
adb shell pm disable-user --user 0 <package name>
```

예를 들어, com.example.app 앱을 비활성화하려면 다음과 같이 입력합니다:

```sh
$ adb shell pm disable-user --user 0 com.example.app 
Success
```
`Success` 메시지가 표시되면 앱이 비활성화된 것입니다.

## 앱 활성화하는 방법

비활성화한 앱을 다시 사용하고 싶을 때는 다음과 같이 명령어를 입력합니다:

```sh
adb shell pm enable --user 0 <package name>
```

이 명령어로 비활성화된 앱을 다시 활성화할 수 있습니다.

## 앱의 활성화 여부 확인하기

앱이 현재 활성화 상태인지 확인하려면 adb shell dumpsys 명령을 사용하면 됩니다. 이 명령은 앱의 설치 정보와 함께 상태를 보여줍니다. 예를 들어:

```sh
adb shell dumpsys package com.example.app
```

이 명령을 실행하면 다음과 같은 정보가 표시됩니다:

```sh
Packages: 
  Package [com.example.app] (3d1fcc2):
    userId=10121
    ... 
    User 0: ceDataInode=122910 installed=true hidden=false suspended=false distractionFlags=0 stopped=false notLaunched=false enabled=3 instant=false virtual=false
```

여기서 `hidden`과 `enabled` 값은 해당 유저의 앱의 활성화 상태를 나타냅니다:

`hidden=false`: 앱이 활성화 상태
`hidden=true`: 앱이 비활성화 상태

`enabled=0` 또는 `enabled=1:` 앱이 활성화 상태
`enabled=2, 3, 4`: 앱이 비활성화 상태

## 맺음말

ADB를 사용하면 Android 기기에 설치된 불필요한 앱을 효율적으로 관리할 수 있습니다. 이를 통해 저장 공간을 절약하고 기기의 성능을 개선할 수 있죠. 오늘 제시된 방법을 통해 여러분의 Android 기기를 더 효과적으로 관리해 보세요. 궁금한 점이 있으시면 언제든지 댓글로 질문해 주세요!
