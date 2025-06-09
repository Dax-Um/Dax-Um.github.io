---
title: "Activity"
date: 2025-06-09
draft: false
categories: ["android"]
---


# 🖥️ Android Activity 완벽 가이드 (2025년 기준)

Android 앱의 핵심 구성 요소인 **Activity**는 사용자와 직접 상호작용하는 단일 UI 화면을 나타냅니다. Activity는 앱이 사용자에게 보여주는 **각각의 화면**을 담당하며, 생명주기를 통해 시스템 이벤트에 반응하고 사용자 경험을 관리합니다.

---

## 📌 Activity란?

* 앱의 **UI 단위 구성 요소**이며, 사용자에게 제공되는 하나의 화면을 의미합니다.
* 앱 내의 여러 Activity는 사용자 흐름에 따라 서로 전환되며, 각각의 Activity는 독립된 생명주기를 가집니다.
* 일반적으로 `MainActivity`가 진입점이 되며, 이후 필요한 Activity를 `Intent`를 통해 실행합니다.

### 대표적인 Activity 예시

* 로그인 화면 (`LoginActivity`)
* 홈 화면 (`HomeActivity`)
* 상세 정보 화면 (`DetailActivity`)

---

## 🧬 생명주기 구조

> 📌 **실무에서는?** Jetpack 아키텍처 컴포넌트를 활용해 생명주기 콜백을 직접 사용하는 비중은 줄고, 대부분 `repeatOnLifecycle`, `ViewModel`, `LifecycleObserver`를 사용해 간접적으로 관리합니다. 하지만 생명주기 구조를 이해하는 것은 여전히 중요하며, 정확한 리소스 해제, 센서 관리, 화면 상태 복원 등에 필수적입니다.

Activity의 생명주기는 단순히 시스템 콜백을 받는 것 이상의 의미를 가지며, **적절한 리소스 관리**, **데이터 저장/복원**, **사용자 경험 최적화**를 위해 중요한 역할을 합니다.

다음은 각 생명주기 단계에서 일반적으로 수행하는 작업과 앱 최적화를 위한 권장 구현입니다:

Activity는 다음과 같은 생명주기 콜백을 통해 시스템 상태 변화에 대응합니다:

| 메서드           | 설명                          |
| ------------- | --------------------------- |
| `onCreate()`  | 최초 생성 시 호출. UI 초기화 및 리소스 설정 |
| `onStart()`   | Activity가 사용자에게 표시되기 직전 호출  |
| `onResume()`  | 사용자와 상호작용 가능한 상태. 포커스 있음    |
| `onPause()`   | 포커스를 잃었지만 여전히 화면에 존재할 수 있음  |
| `onStop()`    | 완전히 화면에서 사라짐. UI 비활성화       |
| `onDestroy()` | Activity 종료 및 리소스 해제 단계     |

### 🔁 생명주기 흐름 예시 및 구현 가이드 (상세 예시 포함)

> ✅ 실무에서는 대부분 아래와 같은 직접적인 콜백 구현보다는 ViewModel + repeatOnLifecycle 구조를 사용합니다. 하지만 센서, 카메라, 오디오 등 명확한 시점 제어가 필요한 경우에는 여전히 생명주기 콜백을 적절히 활용합니다.

| 콜백 메서드        | 일반적 구현 내용                                             | 앱 최적화를 위한 팁                                                                                     |
| ------------- | ----------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `onCreate()`  | View Binding, 초기 데이터 설정, ViewModel 연결                 | 네트워크 초기 요청이나 Room 쿼리 같은 무거운 작업은 `lifecycleScope.launch {}`로 비동기 처리하거나 Splash Screen과 결합하여 UX 향상 |
| `onStart()`   | UI 업데이트, Google Analytics 또는 Firebase Analytics 로그 전송 | 사용자에게 표시되기 직전이므로 카메라 프리뷰 준비, 위치 권한 체크 등을 이 시점에 배치                                               |
| `onResume()`  | 카메라 시작, 센서 활성화, 광고 로딩 재개                              | 이 시점은 사용자와 직접 상호작용 가능한 상태이므로, 앱 주요 기능을 본격적으로 활성화                                                |
| `onPause()`   | 센서 정지, MediaPlayer 정지, 저장되지 않은 임시 데이터 보존              | 리소스 소모 높은 기능을 최소화하고, 잠재적 사용자 데이터 유실을 방지                                                         |
| `onStop()`    | 알림 중지, Room DB에 캐시 저장, 카메라 해제                         | 사용자가 화면을 완전히 벗어난 시점이므로, 데이터 동기화나 서버 전송 처리 등에 적합                                                 |
| `onDestroy()` | View 정리, 리스너 및 콜백 제거, 서비스 언바인딩                        | `BroadcastReceiver` 등록 해제, 내부 참조 null 처리 등을 통해 메모리 누수 방지                                        |

콜백마다 책임을 명확히 분리하면 생명주기 변화에 따른 크래시를 방지할 수 있고, 배터리와 메모리 사용을 최소화하여 앱의 전반적인 성능과 사용자 경험을 개선할 수 있습니다.

예를 들어, onPause 시점에 동영상 재생을 일시 정지하지 않으면 앱이 백그라운드에 있음에도 리소스를 계속 소비하여 배터리를 낭비할 수 있습니다. 또는 onDestroy에서 리스너나 콜백을 해제하지 않으면 메모리 누수(leak)가 발생해 장기적으로 앱 안정성이 떨어질 수 있습니다.

Jetpack의 LifecycleObserver 또는 LifecycleOwner를 사용하는 구조화된 방식도 함께 고려해보세요. 실무에서는 다음과 같은 코드로 생명주기를 간접적으로 관리합니다:

```kotlin
lifecycleScope.launch {
    repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewModel.uiState.collect { state ->
            render(state)
        }
    }
}
```

이 구조는 Activity의 `onStart`, `onStop`을 자동으로 감지하여 Flow 또는 LiveData 관찰을 중단/재개합니다. 복잡한 생명주기 관리를 단순화시킬 수 있어 실무에서 매우 널리 사용됩니다.

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    Log.d("Lifecycle", "onCreate 호출")
}

override fun onStart() {
    super.onStart()
    Log.d("Lifecycle", "onStart 호출")
}

override fun onResume() {
    super.onResume()
    Log.d("Lifecycle", "onResume 호출")
}
```

📘 [공식 Lifecycle 다이어그램 보기](https://developer.android.com/guide/components/activities/activity-lifecycle#lc)

---

## 🧾 AndroidManifest.xml 등록 예시

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

* `android:exported="true"`는 Android 12(API 31) 이상에서 **필수**입니다.
* `MAIN` + `LAUNCHER`는 앱 실행 시 가장 먼저 실행되는 액티비티임을 의미합니다.

---

## 🧱 Edge-to-Edge UI 대응 (Android 14+)

Android 14부터는 시스템 UI (상태바, 내비게이션 바 등)까지 콘텐츠가 확장되는 **Edge-to-Edge** 구성이 기본입니다. `WindowInsets` 처리를 통해 레이아웃의 경계 패딩을 수동으로 조정해야 합니다.

### ✅ View 기반 대응

```kotlin
WindowCompat.setDecorFitsSystemWindows(window, false)

WindowInsetsControllerCompat(window, window.decorView).isAppearanceLightStatusBars = true

ViewCompat.setOnApplyWindowInsetsListener(rootView) { view, insets ->
    val bars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
    view.setPadding(bars.left, bars.top, bars.right, bars.bottom)
    insets
}
```

### ✅ Jetpack Compose 기반 대응

```kotlin
WindowCompat.setDecorFitsSystemWindows(window, false)

Scaffold(
    topBar = {
        TopAppBar(
            title = { Text("Edge-to-Edge 대응") },
            modifier = Modifier.windowInsetsPadding(WindowInsets.statusBars)
        )
    },
    content = { innerPadding ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(innerPadding)
                .windowInsetsPadding(WindowInsets.navigationBars)
        ) {
            Text("Edge-to-Edge 적용된 화면")
        }
    }
)
```

📘 [Inset 공식 가이드](https://developer.android.com/develop/ui/views/layout/insets-controller#compose)

---

## 🎯 Activity에서 Intent로 화면 전환하기

Activity 간 화면 이동은 `Intent` 객체를 생성하여 수행합니다. 다음은 주요 상황별 예시입니다:

### ✅ 기본 화면 전환

```kotlin
val intent = Intent(this, DetailActivity::class.java)
startActivity(intent)
```

### ✅ 데이터 전달하기

```kotlin
val intent = Intent(this, DetailActivity::class.java)
intent.putExtra("itemId", 123)
intent.putExtra("itemName", "Notebook")
startActivity(intent)
```

```kotlin
// DetailActivity.kt
val itemId = intent.getIntExtra("itemId", -1)
val itemName = intent.getStringExtra("itemName")
```

### ✅ Activity 종료 후 결과 받기 (startActivityForResult → registerForActivityResult)

```kotlin
val launcher = registerForActivityResult(ActivityResultContracts.StartActivityForResult()) { result ->
    if (result.resultCode == Activity.RESULT_OK) {
        val data = result.data?.getStringExtra("result")
        Log.d("ActivityResult", "결과: $data")
    }
}

val intent = Intent(this, EditActivity::class.java)
launcher.launch(intent)
```

```kotlin
// EditActivity.kt
val resultIntent = Intent()
resultIntent.putExtra("result", "저장 완료")
setResult(Activity.RESULT_OK, resultIntent)
finish()
```

### ✅ Intent Flags 예시

* `Intent.FLAG_ACTIVITY_NEW_TASK`: 새로운 태스크에서 Activity 시작 (ex. Service에서 Activity 실행)
* `Intent.FLAG_ACTIVITY_CLEAR_TOP`: 스택 상에 같은 Activity가 있을 경우 해당 Activity 위의 Activity들을 제거하고 재사용

```kotlin
val intent = Intent(this, HomeActivity::class.java)
intent.flags = Intent.FLAG_ACTIVITY_CLEAR_TOP or Intent.FLAG_ACTIVITY_SINGLE_TOP
startActivity(intent)
```

Activity 간 화면 전환은 `Intent`를 통해 수행합니다. 데이터 전달도 가능합니다.

```kotlin
val intent = Intent(this, DetailActivity::class.java)
intent.putExtra("itemId", 123)
startActivity(intent)
```

전달받는 쪽에서는 다음과 같이 처리합니다:

```kotlin
val itemId = intent.getIntExtra("itemId", -1)
```

---

## ✅ Activity에서 상태 저장하기

Android는 회전, 다크모드 변경 등 다양한 구성 변경(Configuration Change) 시 Activity를 자동으로 재생성합니다. 이때, 앱 상태를 복구하려면 `onSaveInstanceState()`와 `ViewModel`을 적절히 활용해야 합니다.

### 🔁 Bundle 기반 임시 저장

```kotlin
override fun onSaveInstanceState(outState: Bundle) {
    outState.putString("inputText", editText.text.toString())
    super.onSaveInstanceState(outState)
}

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    val restored = savedInstanceState?.getString("inputText")
    editText.setText(restored)
}
```

### ✅ ViewModel을 통한 상태 보존 (권장)

ViewModel은 Activity보다 **긴 생명주기**를 가지며, 구성 변경에도 데이터를 유지합니다.

```kotlin
class MyViewModel : ViewModel() {
    val inputText = MutableStateFlow("")
}
```

```kotlin
val viewModel: MyViewModel by viewModels()

lifecycleScope.launch {
    viewModel.inputText.collect { text ->
        editText.setText(text)
    }
}

editText.doAfterTextChanged {
    viewModel.inputText.value = it.toString()
}
```

### 🤔 언제 어떤 방식 사용?

| 상황       | Bundle    | ViewModel           |
| -------- | --------- | ------------------- |
| 임시 입력 값  | ✅         | ✅                   |
| 대용량 데이터  | ❌ (제한 있음) | ✅                   |
| 화면 회전 대응 | ✅         | ✅                   |
| 앱 재시작 대응 | ❌ (불가)    | ❌ (Room 등 외부 저장 필요) |

화면 회전 등 구성 변경(Configuration Change) 시 상태 유지를 위해 다음 콜백을 활용합니다.

```kotlin
override fun onSaveInstanceState(outState: Bundle) {
    outState.putString("input", editText.text.toString())
    super.onSaveInstanceState(outState)
}

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    val savedText = savedInstanceState?.getString("input")
}
```

---

## ✅ 베스트 프랙티스

* **Activity는 가볍게 유지하고**, 실제 UI 로직은 Fragment, Composable에 위임하는 것이 유지 보수에 유리합니다.

* **상태 관리**는 ViewModel + StateFlow / LiveData 조합으로 처리하고, 화면 구성과 상태 흐름을 분리합니다.

* **구성 변경 대응**은 ViewModel로 하고, onSaveInstanceState는 보조 수단으로만 사용합니다.

* `onDestroy()`에서는 메모리 누수를 막기 위해 Listener, Receiver, Callback 등 모든 참조를 해제해야 합니다.

* 화면 간 전환 시 Intent를 통해 필요한 최소한의 데이터만 넘기고, 복잡한 데이터는 ViewModel이나 Repository 공유 구조를 활용합니다.

* Jetpack Navigation을 도입하면 back stack 관리가 간단해지고, deep link, SafeArgs 등 다양한 기능을 손쉽게 사용할 수 있습니다.

* 화면 로직은 `Activity`보다 `Fragment`, `Composable`에 위임하여 **책임 분리**

* 상태 관리는 `ViewModel` + `StateFlow` / `LiveData` 조합으로 처리

* `onDestroy()`에서는 **UI 리소스만 정리**하고, 앱 전역 데이터는 ViewModel이나 Repository 계층에 유지

---

## 📎 참고 문서

* [Activity 공식 가이드](https://developer.android.com/guide/components/activities/intro-activities)
* [Activity 생명주기](https://developer.android.com/guide/components/activities/activity-lifecycle)
* [Edge-to-Edge Inset 공식 가이드](https://developer.android.com/develop/ui/views/layout/insets-controller)
