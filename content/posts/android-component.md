---
title: "Android Component"
date: 2025-06-09
draft: false
categories: ["android"]
---

# 📦 Android 구성 요소 개요 (2025년 기준)

Android 앱은 시스템과 상호작용하기 위한 네 가지 주요 컴포넌트로 구성됩니다. 각각의 컴포넌트는 앱이 사용자와 상호작용하거나, 백그라운드에서 작업을 수행하거나, 다른 앱과 데이터를 공유하거나, 시스템 이벤트를 수신할 수 있도록 합니다.
이러한 컴포넌트는 앱의 진입점(entry point) 역할을 하며, 반드시 `AndroidManifest.xml`에 등록되어야 시스템이 인식하고 실행할 수 있습니다.

* **Activity**: 사용자 인터페이스(UI)를 구성하고 입력을 처리
* **Service**: 백그라운드에서 UI 없이 장시간 작업 처리
* **BroadcastReceiver**: 시스템 또는 앱의 이벤트를 수신
* **ContentProvider**: 앱 간 데이터를 안전하게 공유

---

## 🖥️ 1. Activity

### ✅ 정의

Activity는 사용자와 직접 상호작용하는 단일 UI 화면입니다. 사용자의 행동에 따라 앱의 상태를 전환하며, 생명주기(Lifecycle)를 통해 시스템 이벤트에 반응합니다.

### ✅ Manifest 예시

```xml
<activity
    android:name=".MainActivity"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

### ✅ 생명주기 콜백

* `onCreate()`: UI 초기화, 데이터 복원
* `onStart()`: 화면이 보이기 시작
* `onResume()`: 사용자 입력 가능
* `onPause()`: 포커스 상실
* `onStop()`: 완전히 숨겨짐
* `onDestroy()`: 메모리 해제 직전 호출

📘 [공식 Lifecycle 다이어그램 보기](https://developer.android.com/guide/components/activities/activity-lifecycle#lc)

---


## 🛠️ 2. Service

### ✅ 정의

Service는 사용자 인터페이스 없이 백그라운드에서 실행되는 컴포넌트입니다. 음악 재생, 위치 추적, 파일 다운로드 등 장기 작업에 사용됩니다.

### ✅ 유형

* **Started Service**: 명령을 받고 시작되며, 명시적으로 종료 필요 (`stopSelf()` 또는 `stopService()`)
* **Bound Service**: 다른 컴포넌트와 연결되어 데이터를 주고받음. 연결이 끊기면 서비스도 종료됨
* **Foreground Service**: 사용자에게 계속 보이는 알림과 함께 실행되며, 시스템 우선순위가 높음

### ✅ Android 14 변경사항

Foreground Service는 반드시 `android:foregroundServiceType`을 선언해야 하며, 선언하지 않으면 예외가 발생합니다.

```xml
<service
    android:name=".MyService"
    android:foregroundServiceType="mediaPlayback"
    android:exported="false" />
```

📘 [Foreground Services 공식 문서](https://developer.android.com/guide/components/foreground-services)

---

## 📡 3. BroadcastReceiver

### ✅ 정의

BroadcastReceiver는 시스템 또는 앱에서 전송하는 브로드캐스트 메시지를 수신하는 컴포넌트입니다. 예: 부팅 완료, 배터리 부족, 네트워크 변경 등.

### ✅ 등록 방식

* **정적 등록**: `AndroidManifest.xml`에 선언 → 앱 종료 상태에서도 수신 가능
* **동적 등록**: 코드에서 `registerReceiver()`로 등록 → 앱 실행 중에만 수신

### ✅ Android 12 이상 보안 정책

* `exported` 속성 명시 필수
* 코드에서는 `RECEIVER_EXPORTED`, `RECEIVER_NOT_EXPORTED` 상수 사용 권장

```xml
<receiver android:name=".BootReceiver" android:exported="false">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>
```

📘 [BroadcastReceiver 공식 문서](https://developer.android.com/guide/components/broadcasts)

---

## 📂 4. ContentProvider

### ✅ 정의

ContentProvider는 앱 간 데이터를 안전하게 공유할 수 있도록 돕는 컴포넌트입니다. `ContentResolver`를 통해 URI 기반으로 접근하며, SQLite, 파일, 네트워크 등 다양한 저장소와 연동 가능합니다.

### ✅ 사용 예시

```kotlin
val cursor = contentResolver.query(
    ContactsContract.Contacts.CONTENT_URI,
    null, null, null, null
)
```

### ✅ Manifest 등록

```xml
<provider
    android:name=".MyProvider"
    android:authorities="com.example.app.provider"
    android:exported="true"
    android:grantUriPermissions="true" />
```

### ✅ 보안 권장 사항

* `grantUriPermissions` 사용 시 `FLAG_GRANT_READ_URI_PERMISSION` 함께 설정
* `readPermission`, `writePermission`을 분리 선언해 최소 권한 원칙 적용

📘 [ContentProvider 공식 문서](https://developer.android.com/guide/topics/providers/content-providers)
