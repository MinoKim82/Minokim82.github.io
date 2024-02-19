---
title: Android에서 자신의 버전 가져오기
date: 2024-02-19 09:35:00 +09:00
categories: [Develop]
tags: [Android]
---

새로운 버전으로 자동 업데이트를 요청하거나, 버전 정보를 화면에 표시할 때, 자신의 버전을 가져와야 할 때가 있다. 오늘은 안드로이드에서 어떻게 버전 을 가져오는지 알아 보겠습니다.

## Version Code 및 Version Name 확인

```java
int versionCode = 0;
String versionName = "";
try {
    PackageInfo packageInfo = context.getPackageManager().getPackageInfo(context.getPackageName(), 0);
    versionName = packageInfo.versionName;
    versionCode = packageInfo.versionCode;
} catch (PackageManager.NameNotFoundException e) {
    versionName = "0.0.00.0";
}
```

버전을 확인하는 방법은 간단합니다. `PackageInfo` 객체에서 `versionCode`와 `versionName`을 가져오면 됩니다. `versionCode`는 `int` 타입으로, `versionName`은 `String` 타입으로 가져옵니다.

## versionCode와 versionName의 차이

**versionCode**와는 앱에서 사용되는 고유한 식별자로, 일반적으로 정수 또는 문자열 형태로 부여됩니다. 버전 코드는 보통 앱 내부에서 사용되며, 앱의 업데이트나 배포를 위한 과정에서 사용됩니다.


**versionName**은 사용자가 볼 수 있는 버전 이름으로, 일반적으로 "1.0", "2.1" 등과 같이 표기됩니다. 버전 네임은 보통 앱의 릴리즈 노트나 마케팅 자료 등에서 사용됩니다.

따라서, 버전 코드와 버전 네임은 서로 다른 목적으로 사용되지만, 둘 다 앱의 버전을 구분하기 위한 역할을 합니다.
