---
title: 내 앱이 플레이스토어에서 업데이트 안된다고? Android 앱 업데이트 소유권 알아보기
date: 2024-01-29 16:05:00 +09:00
categories: [Develop]
tags: [안드로이드]
---

안녕하세요. 오늘은 Android 14에서 도입된 앱 업데이트 소유권에 대한 새로운 접근 방식을 살펴보겠습니다. Android 앱 개발자와 앱 마켓 운영자, Android 시스템 개발자에게 모두 중요한 변화로, 이에 대한 이해가 필수적입니다.

##  Update ownership 이란?

`update-owner`는 사용자 동의 없이 앱을 업데이트할 수 있는 Installer의 package name을 의미합니다. 이는 앱을 처음 설치할 때만 설정할 수 있으며, 설정 여부에 따라 동작이 달라집니다.

| 현재 상태 | 동작 |
| --- | --- |
| `update-owner` 설정 | `update-owner`가 아닌 Installer(스토어 앱)는 사용자 동의 후 업데이트 |
| `update-owner` 미설정 | 모든 Installer(스토어 앱)가 사용자 동의 없이 업데이트 가능 |

이 변화는 특히 구글 플레이스토어에 영향을 미치며, 2023년 4분기부터 `update-owner `컨셉이 적용되어 다른 애플리케이션에서 자동 업데이트가 불가능해졌습니다.

## 내 앱에 `update-owner` 적용하기

앱 개발 중  `update-owner`를 확인하고 적용하는 방법은 다음과 같습니다.

### `update-owner` 정의하기

`update-owner`를 설정은 `PckageInstaller` API를 사용하여 이루어집니다.

```java
PackageInstaller.SessionParams params = new PackageInstaller.SessionParams(PackageInstaller.SessionParams.MODE_FULL_INSTALL);
params.setRequestUpdateOwnership(ture);
getPackageManager().getPackageInstaller().createSession(params);
```

이 설정은 앱을 처음 설치할 때만 적용됩니다. 또한, `adb`를 사용하여 터미널에서 `update-owner`를 설정할 수도 있습니다.

```bash
$ adb install --update-ownership example.apk
Performing Streamed Install
Success

$ adb shell dumpsys package com.exapmle.application
Package [com.example.application] (fffffff):
  appId=12345
  updateOwnerPackageName=com.android.shell
```

이 명령어를 사용하면 `update-owner`가 설정되며, 이후 `adb shell dumpsys package` 명령으로 확인할 수 있습니다.
   
## `update-owner` 대응 방법

이번 기능의 경우 업데이트 경로를 제한해서  앱의 안전성과 신뢰성을 향상 시키기 위한 목적일 텐데요, 일부 앱의 경우에는 자신이 직접 설치가 필요할 경우도 있을 것 같습니다. 이럴 경우 어떻게 해야할지 알아보겠습니다.

###    `update-owner`가 설정되지 않도록 denylist등록하기

시스템 파티션에 preload되는 앱이 다른 앱을 설치할 때 `update-owner`가 설정되지 않도록 하려면, `AndroidManifest.xml`에 다음 속성을 포함해야 합니다.

```xml
<application …>
  <property android:name="android.app.PROPERTY_LEGACY_UPDATE_OWNERSHIP_DENYLIST"
            android:resource="@xml/legacyOwnershipDenylist" />
</application>
```

이 예에서는 `legacyOwnershipDenylist` 라는 xml  파일을 참조하도록 하였습니다.  `res/xml` 폴더에 `legacyOwnershipDenylist.xml` 파일을 생성합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <deny-ownership>com.example.app1</deny-ownership>
  <deny-ownership>com.example.app2</deny-ownership>
</resources>
```

### `PackageInstaller`로 앱 설치 요청 후 동의를 받아 설치하기

이미 `update-owner`가 설정된 앱을 `PackageInstaller`로 설치 요청하게 되면, 설치를 시도한 Installer 앱에게 user action이 필요하다는 result가 반환됩니다. 이 result를 받은 경우 아래 구현을 통해서 앱 설치 동의를 받도록 합니다.

```java
BroadcastReceiver receiver = new BroadcastReceiver() {
  @Override
  public voide onReceive(Context context, Intent intent) {
    int result = intent.getInteExtra(PackageInstaller.EXTRA_STATUS, PackageInstaller.STATUS_FAILURE);
    switch(result) {
      case PackageInstaller.STATUS_PENDING_USER_ACTION:
        startActivitygintent.getParcelableExtra(Intent.EXTRA_INTENT));
        break;
        ...
      }
    };
    ...
    context.registerReceiver(receiver, new IntentFilter("Action.TEST_INSTALL"), null, null);
    PendingIntent pendingIntent = PendingIntent.getBroadcast(context, sessionId, new Intent("action.TEST_INSTALL"), PendingIntent.FLAG_MUTABLE);
    sessing.commit(pendingIntent.getIntent.Sender());
```

result값에 `EXTRA_STATUS` 값이 `STATUS_PENDING_USER_ACTION`인지 여부를 확인하고, `startActivitygintent.getParcelableExtra(Intent.EXTRA_INTENT));` 를 호출 하면 PackageInstaller 에서 컨펌 팝업을 통해 사용자 동의를 구하고 `update-owner`가 삭제 됩니다.

### `PackageInstaller`로 앱 설치 이전 동의를 받아 설치하기

이번 방법은 Package Install 이전에 동의를 받아 설치하는 방법입니다. 

```java
PackageInstaller pi = getPackageManager().getPackageInstaller()
PackageInstaller.Session session = pi.openSession(pi.createSession(params));
BroadcastReceiver approvalReceiver = new BroadcastReceiver() {
  @Override
  public void onReceive(Context context, Intent intent) {
    int result = intent.getIntExtra(PackageInstaller.EXTRA_STATUS, PackageInstaller.STATUS_FAILURE);
    switch(result) {
      case PackageInstaller.STATUS_PENDING_USEAR_ACTION:
        startActivity(intent.getParcelableExtra(Intent.EXTRA_INTENT));
        break;
     case PackageInstaller.STATUS_SUCCESS:
        break;
     ,,,
  context.regeiterReceiver(approvalReceiver, new IntentFilter("action.TEST_APPROVAL"), Context.RECEIVER_EXPORTED);
  PackageInstaller.PreapprovalDetails preapprovalDetails = new PackageInstaller.PreapprobvalDetails.Builder()
    .setLabel("ExampleLabel")
    .setLocalge(Locale.US)
    .setPackageName("com.example.application")
    .build();
  PendingIntent pendingintent = PendingIntent.getBroadcast(getApplicationContext(), 12345, new Intent("action.TEXT_APPROVAL").setPackage("com.exampler.installer"), PendingIntent.FLAG_UPDATE_CURRENT | PendingIntent.FLAG_MUTABLE);
  session.requestUserPreapproval(approvalDetails, pendingIntent.getIntentSender());
```

`session.requestUserPreApproval()` 호출 하게 되면, 아래와 같이 반환됩니다.
- 사용자 컴펀이 필요하지 않은경우 `STATUS_SUCCESS`
- 사용자 컨펌이 필요한 경우, `STATUS_PENDING_USER_ACTION`

컴펌 팝업 요청 후 사용자 동의가 완료되면 상기 Receiver를 통해 다시 `STATUS_SUCCESS` 가 전달 되기 때문에, 모든 과정이 완료될 때 까지 unregisterReceiver를 하면 안됩니다.

## `update-owner` 확인하기

패키지에 `update-owner`가 설정되어 있는지 확인하는 방법은 두가지가 있습니다: 앱에서 직접확인하는 방법과 `adb` 통해서 확인하는 방법 입니다.

### 앱에서 확인하는 방법

먼저 확인하고자 하는 앱의 `AndroidManifest.xml` 파일에 아래 권한을 추가합니다.

```xml
<uses-permission android:name="android.permission.QUERY_ALL_PACKAGES"/>
```

코드 상에 `InstallSourceInfo.getUpdateOwnerPackageName() API를 호출해 확인합니다.

```java
String updateOwner = getPackageManager().getInstallSourceInfo("com.example.aplication").getUpdateOwnerPackageName();
```

### `adb` 를 통해 확인하는 방법

앱에 설정된 `update-oowner`는 `adb shell dumpsys` 명령을 통해서 확인 가능합니다.

```bash
$ adb shell dumpsys package com.example.application
  Package [com.example.application]
    appId=12345
    ....
    updateOwnerPackageName=com.android.vendig
```

`update-owner`가 설정되어 있지 않은 경우, 항목이 보이지 않거나, `null`로 설정됩니다.


## 맺음말

Android 14에서 도입된 이 새로운 업데이트 소유권 관리 방법은 앱의 안전성과 신뢰성을 높이는 중요한 변화입니다. 개발자와 사용자 모두에게 이점이 있는 이 기능을 적절히 활용하여 보다 안전한 앱 환경을 만들어 나가시길 바랍니다.


## 참조
- [안드로이드 오픈소스 프로젝트](https://source.android.com/docs/setup/create/app-ownership?hl=ko)
