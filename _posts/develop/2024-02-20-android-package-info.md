---
title: Android 어플리케이션 정보(Package Dump) 확인하기
date: 2024-02-19 09:35:00 +09:00
categories: [S/W]
tags: [Android]
---

안드로이드 어플리케이션을 개발하다보면 현재 설치된 어플리케이션의 상태를 확인해야 할 경우가 많습니다. 이 때 android에서 제공하는 dumpsys tool을 통해서 현재 package 상태를 모두 확인 할 수 있습니다

## Android 어플리케이션 정보(Package dump) 출력하기

package dump는 `adb dumpstate`명령어를 통해서 만들어진 전체 dump 파일을 확인하거나, 아래 명령을 통해서 특정 package의 dump만 출력합니다.

```sh
adb shell dumpsys package <패키지 이름>
```

dump에는 package와 관련된 아래와 같은 모든 정보가 포함됩니다.

|Items|	Description|
|---|---|
|Intent Resolver Table(Activity, Receiver, Service)	|App 실행과 관련된 MINE 타입, Scheme, Action 리스트|
|Domain verification Status|Android 앱과 관련된 웹 도메인의 소유권을 확인하는 기능|
|Permissions|Application에서 등록한 권한|
|Registerd ContentProviders, ContentProvider Authoririties|데이터 공유를 위한 ContentProvider와 ContentProvider 식별자|
|Key Set Manager|업데이트와 관련한 공개 키 세트|
|Packages, Hidden System Packages|설치된 Package의 정보|
|Dexopt state|실행 가능한 .dex 파일이 최적화되었는지 여부를 나타내는 상태|
|Compiler stats|애플리케이션 코드의 컴파일 과정과 성능에 대한 통계적 데이터 (실행 효율성과 최적화를 평가)|

## Intent Resolver Table(Activity, Receiver, Service)

Android에서 인텐트는 앱 구성 요소 간의 통신 수단으로 사용되며, 특정 작업을 실행하거나 데이터를 전달하는 데 사용됩니다. 인텐트를 구분하는 인텐트 필터로는 `MIME` 타입과 `Scheme`을 사용하게 됩니다.

### MIME 타입

`MIME(Multi-purpose Internet Mail Extensions)` 타입은 데이터의 형식을 지정합니다. MIME 타입은 "타입/서브타입" 형식으로 표현되며, `Full MIME` 타입과 `Wild MIME` 타입 두 가지 형태로 나뉩니다.

#### Full MIME Type:

`Full MIME Type`은 특정한 데이터 타입을 정확하게 지정합니다. 예를 들어, "image/jpeg"는 `JPEG` 형식의 이미지를 나타냅니다. 이 경우, 앱의 인텐트 필터가 "image/jpeg"에 대해서만 반응하도록 설정되어 있을 때, 해당 앱은 `JPEG` 이미지와 관련된 인텐트를 처리할 수 있습니다.

#### Wild MIME Type:

`Wild MIME Type`은 좀 더 일반적인 데이터 타입을 지정하는 데 사용되며, `""` 기호를 사용합니다. 예를 들어, "image/"는 모든 이미지 타입을 나타내며, "/json"은 모든 `JSON` 데이터를 나타냅니다. 이 경우, 인텐트 필터는 더 넓은 범위의 데이터 타입을 처리할 수 있습니다. "image/" 인텐트 필터를 가진 앱은 `JPEG`, `PNG`, `GIF` 등 다양한 이미지 형식을 처리할 수 있습니다.

### Scheme

`Scheme`은 인텐트의 `URI(Uniform Resource Identifier) `Scheme을 나타냅니다. 일반적인 스킴에는 "http", "https", "file" 등이 있습니다. 앱은 인텐트 필터를 사용하여 특정 스킴의 URI를 처리할 수 있습니다. 예를 들어, 웹 브라우저는 "http" 또는 "https" Scheme을 처리하도록 설정될 수 있습니다.

## Domain verification Status

도메인 검증 상태는 Android 앱과 관련된 웹 도메인의 소유권을 확인하는 기능과 관련이 있습니다. 이는 주로 Android 앱 링크(App Links) 기능과 연관되어 있으며, 앱 링크는 사용자가 웹 URL을 클릭했을 때 해당 URL이 특정 앱에 직접 연결되도록 하는 기능입니다.

## 퍼미션(Permissions)

해당 어플리케이션이 단말에 설치한 퍼미션을 나타냅니다.

```sh
Permissions:
  Pemission [com.google.android.youtube.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION] (2be9617):
    sourcePackage=com.google.android.youtube
    uid=10267 gids=[] type=0 prot=signature
    perm=PermissionInfo{8b8b97a com.google.android.youtube.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION}
    flags=0x0
```

위 내용은 유튜브앱이 설치한 `permission`에 대한 상세 정보를 나타냅니다. 각 항목에 대한 설명은 다음과 같습니다:

### Permission

이것은 권한의 고유 이름을 나타냅니다. 
여기서 com.google.android.youtube.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION은 유튜브 앱이 사용하는 특정 권한의 이름입니다.

### sourcePackage

권한을 정의하고 있는 패키지의 이름입니다.
이 경우, `com.google.android.youtube`이 유튜브 앱의 패키지 이름입니다.

### uid

User ID, 앱을 실행하는 사용자의 고유 식별자입니다.
유튜브 앱의 UID는 10267입니다.

### gids

Group IDs, 해당 앱이 속하는 그룹의 식별자 목록입니다.
여기서는 비어 있는 것으로 표시됩니다([]).

### type

권한의 타입을 나타냅니다.
Android에서는 일반적으로 두 가지 주요 타입의 권한이 있습니다. 

- 일반(정상) 권한(normal permissions)
- 위험 권한(dangerous permissions). 

일반 권한은 앱에 자동으로 부여되며 사용자의 개입이 필요하지 않습니다. 반면, 위험 권한은 사용자가 명시적으로 승인해야 하는 권한입니다.

### prot

Protection level, 권한의 보호 수준을 나타냅니다.
`signature`는 이 권한이 앱이 설치된 동일한 서명을 가진 앱에 의해서만 사용될 수 있음을 의미합니다. 자세한 내용은 [여기](https://developer.android.com/reference/android/content/pm/PermissionInfo#PROTECTION_FLAG_SYSTEM)를 참조해주세요.

|타입|상수|값|비고|
|---|---|---|---|
|**normal**|PROTECTION_NORMAL|0||
|**Dangerous**|PROTECTION_DANGEROUS|1||
|**Signature**|PROTECTION_SIGNATURE|2||	
|**SignatureOrSystem**|PROTECTION_SIGNATURE\|PROTECTION_FLAG_PRIVILEGED(PROTECTION_FLAG_SYSTEM)|2\|0x10|PROTECTION_FLAG_SYSTEM was deprecated in API level 23.|

### perm

PermissionInfo, 권한에 대한 추가 정보를 포함하는 객체입니다.
PermissionInfo{8b8b97a com.google.android.youtube.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION}는 이 권한에 대한 자세한 정보를 담고 있습니다.

### flags

권한에 설정된 플래그 값입니다.
0x0은 특별한 플래그가 설정되지 않았음을 나타냅니다.

## ContentProviders

`Registered ContentProviders`와 `ContentProvider Authorities`는 애플리케이션 간 데이터 공유 및 접근을 관리하는 중요한 구성 요소입니다.

### Registered ContentProviders

어플리케이션이 등록한 ContentProvider의 목록을 나타냅니다.

### ContentProvider Authority

ContentProvider에 접근을 위한 Authority 목록을 나타냅니다.

## Packages(Hidden System Packages)

Package의 설치 정보를 나타냅니다.

```sh
Packages:
  Package [com.google.android.youtube] (6b7d559):
    appId=10267
    pkg=Package{b1d251e com.google.android.youtube}
    codePath=/data/app/~~u8EWDDGnuXkFENCePu7QGg==/com.google.android.youtube-BOiL4HRBgp25BzgQsxLuoA==
    resourcePath=/data/app/~~u8EWDDGnuXkFENCePu7QGg==/com.google.android.youtube-BOiL4HRBgp25BzgQsxLuoA==
    legacyNativeLibraryDir=/data/app/~~u8EWDDGnuXkFENCePu7QGg==/com.google.android.youtube-BOiL4HRBgp25BzgQsxLuoA==/lib
    extractNativeLibs=false
    primaryCpuAbi=arm64-v8a
    secondaryCpuAbi=null
    cpuAbiOverride=null
    versionCode=1544019392 minSdk=26 targetSdk=34
    minExtensionVersions=[]
    versionName=19.03.35
    usesNonSdkApi=false
    splits=[base, config.arm64_v8a, config.en, config.xxhdpi]
    apkSigningVersion=3
    flags=[ SYSTEM HAS_CODE ALLOW_CLEAR_USER_DATA UPDATED_SYSTEM_APP ALLOW_BACKUP KILL_AFTER_RESTORE RESTORE_ANY_VERSION LARGE_HEAP ]
    privateFlags=[ PRIVATE_FLAG_ACTIVITIES_RESIZE_MODE_RESIZEABLE_VIA_SDK_VERSION ALLOW_AUDIO_PLAYBACK_CAPTURE PRIVATE_FLAG_REQUEST_LEGACY_EXTERNAL_STORAGE HAS_DOMAIN_URLS PARTIALLY_DIRECT_BOOT_AWARE PRODUCT PRIVATE_FLAG_ALLOW_NATIVE_HEAP_POINTER_TAGGING ]
    forceQueryable=false
    queriesPackages=[com.google.android.apps.youtube.music, com.google.android.apps.youtube.gaming, com.google.android.apps.youtube.kids, com.google.android.apps.youtube.unplugged, com.google.android.apps.youtube.creator, com.google.android.apps.youtube.mango, com.google.android.apps.maps, com.google.android.apps.meetings, com.google.android.gm, com.google.android.apps.tachyon]
    queriesIntents=[Intent { act=android.intent.action.TTS_SERVICE }, Intent { act=com.android.vending.billing.InAppBillingService.BIND }, Intent { act=android.media.action.IMAGE_CAPTURE }, Intent { act=android.intent.action.PICK dat=content://*/... typ=image/* }, Intent { act=android.intent.action.GET_CONTENT dat=content://*/... typ=image/* }, Intent { act=android.intent.action.VIEW }, Intent { act=android.support.customtabs.action.CustomTabsService }, Intent { act=android.speech.RecognitionService }, Intent { act=android.intent.action.SEND dat=content://*/... typ=text/plain }, Intent { act=android.intent.action.VIEW cat=[android.intent.category.BROWSABLE] dat=http://www.example.com/... }]
    dataDir=/data/user/0/com.google.android.youtube
    supportsScreens=[small, medium, large, xlarge, resizeable, anyDensity]
    usesOptionalLibraries:
      org.apache.http.legacy
      android.ext.adservices
      androidx.window.extensions
      androidx.window.sidecar
    usesLibraryFiles:
      /system/framework/org.apache.http.legacy.jar
    timeStamp=2024-01-24 15:15:22
    lastUpdateTime=2024-01-24 15:15:22
    installerPackageName=com.android.vending
    installerPackageUid=10248
    initiatingPackageName=com.android.vending
    originatingPackageName=null
    category=2
    packageSource=0
    appMetadataFilePath=/data/app/~~u8EWDDGnuXkFENCePu7QGg==/com.google.android.youtube-BOiL4HRBgp25BzgQsxLuoA==/app.metadata
    signatures=PackageSignatures{fec21ff version:3, signatures:[fa41615], past signatures:[7d3bce25 flags: 17, fa41615 flags: 17]}
    installPermissionsFixed=true
    pkgFlags=[ SYSTEM HAS_CODE ALLOW_CLEAR_USER_DATA UPDATED_SYSTEM_APP ALLOW_BACKUP KILL_AFTER_RESTORE RESTORE_ANY_VERSION LARGE_HEAP ]
    privatePkgFlags=[ PRIVATE_FLAG_ACTIVITIES_RESIZE_MODE_RESIZEABLE_VIA_SDK_VERSION ALLOW_AUDIO_PLAYBACK_CAPTURE PRIVATE_FLAG_REQUEST_LEGACY_EXTERNAL_STORAGE HAS_DOMAIN_URLS PARTIALLY_DIRECT_BOOT_AWARE PRODUCT PRIVATE_FLAG_ALLOW_NATIVE_HEAP_POINTER_TAGGING ]
    apexModuleName=null
    declared permissions:
      com.google.android.youtube.DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION: prot=signature
    requested permissions:
      android.permission.READ_MEDIA_VISUAL_USER_SELECTED
    install permissions:
      android.permission.FOREGROUND_SERVICE_CAMERA: granted=true
    User 0: ceDataInode=6574 installed=true hidden=false suspended=false distractionFlags=0 stopped=false notLaunched=false enabled=0 instant=false virtual=false
      installReason=0
      firstInstallTime=2022-01-01 09:00:00
      uninstallReason=0
      lastDisabledCaller: com.android.vending
      gids=[3003]
      runtime permissions:
        ....
        android.permission.POST_NOTIFICATIONS: granted=false, flags=[ USER_SENSITIVE_WHEN_GRANTED|USER_SENSITIVE_WHEN_DENIED]
      enabledComponents:
        ...
        com.google.android.youtube.ManageNetworkUsageActivity
```

위 내용은 youtube package 내용 중 일부 입니다. 각 항목의 의미는 아래와 같습니다.

### appId

앱의 고유 식별자(UID)입니다. 이 경우 10267입니다.

### pkg

앱의 패키지 정보를 나타냅니다.

### codePath 및 resourcePath

앱의 코드와 리소스가 저장된 경로입니다.

### legacyNativeLibraryDir

네이티브 라이브러리가 저장된 경로입니다.

### extractNativeLibs

네이티브 라이브러리 추출 여부를 나타냅니다. 여기서는 `false`로 설정되어 있습니다.

### primaryCpuAbi 및 secondaryCpuAbi

앱이 지원하는 주요 및 보조 CPU 아키텍처입니다.

### versionCode, minSdk, targetSdk

앱의 버전 코드, 최소 지원 SDK 버전, 대상 SDK 버전입니다.

### versionName

앱의 버전 이름입니다.

### usesNonSdkApi

비표준 SDK API 사용 여부입니다.

### splits

앱의 APK 분할 정보입니다.

### apkSigningVersion

APK 서명 버전입니다.

### flags 및 privateFlags

앱에 설정된 다양한 플래그입니다. 플래그의 의미는 `AOSP Settings` 에서 확인할 수 있습니다.
일부 중요 flag 값은 아래와 같습니다.

- `SYSTEM`: 이 플래그는 앱이 시스템 앱임을 나타냅니다. 시스템 앱은 Android 운영 체제의 일부로, 일반적으로 사용자에 의해 제거되거나 변경될 수 없습니다.
- `HAS_CODE`: 앱이 실행 가능한 코드를 포함하고 있음을 나타냅니다. 이것은 대부분의 앱에 해당되는 일반적인 속성입니다.
- `ALLOW_CLEAR_USER_DATA`: 이 플래그는 사용자가 앱의 데이터를 설정에서 '데이터 지우기' 옵션을 통해 초기화할 수 있음을 나타냅니다.
- `UPDATED_SYSTEM_APP`: 이 앱은 원래 시스템에 포함되어 있던 시스템 앱이지만, 업데이트를 통해 변경되었음을 나타냅니다.
- `ALLOW_BACKUP`: 이 플래그는 앱 데이터가 Android의 백업 및 복원 기능을 사용할 수 있음을 나타냅니다.
- `KILL_AFTER_RESTORE`: 백업 데이터가 복원된 후 앱을 종료해야 함을 나타냅니다.
- `RESTORE_ANY_VERSION`: 앱의 어떠한 버전의 데이터도 복원할 수 있음을 나타냅니다.
- `LARGE_HEAP`: 이 플래그는 앱이 필요에 따라 표준보다 더 큰 힙 메모리를 요청할 수 있음을 나타냅니다.

### forceQueryable

다른 앱에 의해 쿼리 가능 여부입니다.

### queriesPackages 및 queriesIntents

앱이 쿼리하는 다른 패키지 및 인텐트입니다.

### dataDir

앱 데이터 디렉토리의 경로입니다.

### supportsScreens

앱이 지원하는 화면 크기입니다.

### usesOptionalLibraries 및 usesLibraryFiles

앱이 사용하는 선택적 라이브러리와 라이브러리 파일입니다.

### timeStamp 및 lastUpdateTime

앱 설치 및 마지막 업데이트 시간입니다.

### installerPackageName 및 installerPackageUid

앱을 설치한 패키지 및 UID입니다.

### initiatingPackageName 및 originatingPackageName

앱 설치를 시작한 패키지 및 원래 패키지입니다.

### category 및 packageSource

앱의 카테고리 및 패키지 소스입니다.

### appMetadataFilePath

앱 메타데이터 파일의 경로입니다.

### signatures

앱 서명 정보입니다.
이 정보는 앱이 어떻게 서명되었는지와 관련된 세부사항을 포함하고 있습니다. 여기에 나타난 각 부분의 의미는 다음과 같습니다:

- PackageSignatures{fec21ff version:3}: 
  - PackageSignatures는 앱의 서명 정보를 포함하는 객체입니다.
  - `fec21ff`는 서명 정보의 고유 식별자 또는 해시 값입니다.
  - version:3는 사용된 APK 서명 방식의 버전을 나타냅니다. 이 경우, 버전 3는 Android 9.0(Pie) 이상에서 도입된 최신 APK 서명 방식을 의미합니다.

- signatures:[fa41615]:
  - 이 부분은 현재 앱에 사용된 서명의 실제 값입니다. 
  - `fa41615`는 서명의 고유 식별자 또는 해시 값입니다.

- past signatures:[7d3bce25 flags: 17, fa41615 flags: 17]:
  - `past signatures`는 앱이 과거에 사용했던 서명들을 나타냅니다.
각 서명 값 (예: `7d3bce25` 및 `fa41615`) 뒤에 오는 flags는 해당 서명에 적용된 특정 플래그를 나타냅니다. 여기서 flags: 17는 특정 내부 플래그 값을 의미하며, 이는 서명의 사용 방식이나 특성에 대한 추가 정보를 제공합니다.

### installPermissionsFixed

설치 권한 고정 여부입니다.

### pkgFlags 및 privatePkgFlags

패키지 플래그입니다.

### apexModuleName

APEX 모듈 이름입니다.

### declared permissions 및 requested permissions

앱이 선언하고 요청한 권한입니다.

### install permissions

설치된 권한입니다.

### User 0
`User 0` 정보는 Android 시스템에서 특정 사용자(이 경우, 사용자 0)에 대한 앱의 상태 및 설정을 나타냅니다. 여기에 포함된 각 항목의 의미는 다음과 같습니다:

- `ceDataInode=6574`: 이것은 암호화된 사용자 데이터(CE: Crypted Encryption)의 파일 시스템 inode 번호를 나타냅니다. 6574는 이 데이터의 고유 식별자입니다.
- `installed=true`: 앱이 현재 사용자에게 설치되어 있음을 나타냅니다.
- hidden=false: 앱이 숨겨져 있지 않음을 나타냅니다. 즉, 사용자가 앱을 볼 수 있습니다.
- `suspended=false`: 앱이 일시 중단 상태가 아니라는 것을 나타냅니다. 일시 중단된 앱은 사용자에게 보이지 않고 실행되지 않습니다.
- `distractionFlags=0`: 앱에 적용된 '주의 분산 방지' 플래그의 값입니다. 이 경우 0은 주의 분산 방지 기능이 적용되지 않았음을 의미합니다.
- `stopped=false`: 앱이 정지 상태가 아님을 나타냅니다. 정지 상태의 앱은 백그라운드에서 활동을 하지 않습니다.
- `notLaunched=false`: 앱이 최소 한 번 이상 실행되었음을 나타냅니다.
- `enabled=0`: 앱의 활성화 상태를 나타냅니다. '0'은 일반적으로 앱이 활성화되어 사용 가능함을 의미합니다.
  - COMPONENT_ENABLED_STATE_DEFAULT: 0
  - COMPONENT_ENABLED_STATE_ENABLED: 1
  - COMPONENT_ENABLED_STATE_DISABLED: 2
  - COMPONENT_ENABLED_STATE_DISABLED_USER: 3
- `instant=false`: 이 앱이 '인스턴트 앱'(즉, 설치 없이 사용할 수 있는 앱)이 아님을 나타냅니다.
- `virtual=false`: 앱이 가상 환경에서 실행되지 않음을 나타냅니다.

### installReason 및 uninstallReason

설치 및 제거 이유입니다.

### lastDisabledCaller

마지막으로 비활성화한 호출자입니다.

### gids

앱에 할당된 그룹 ID입니다.

### runtime permissions

런타임 권한입니다.

### enabledComponents

활성화된 컴포넌트입니다.

## 맺음말

오늘은 Android 어플리케이션의 Package Dump를 확인하는 방법에 대해 알아보았습니다. 이 정보를 통해 개발 중인 앱의 상태와 성능을 정밀하게 파악하고 최적화할 수 있습니다. 앱 개발에 있어서 이러한 도구의 활용은 효율성을 높이고, 문제 해결을 가속화하는 데 큰 도움이 됩니다.
