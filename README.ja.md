# Android Studio Empty Views Activity 雛形プロジェクトの差分比較

> [!NOTE]
> このドキュメントの英語版は [English (README.md)](README.md) をご覧ください。

このリポジトリは、Android Studioのバージョンごとに生成される雛形プロジェクト（**Empty Views Activity (Java)**）の差分や変更点を調査・追跡・比較するためのリポジトリです。

---

## 📌 調査対象バージョン一覧

| 雛形ディレクトリ | Android Studio バージョン | ビルド番号 | ビルド日時 |
|---|---|---|---|
| `Panda2JavaEmptyViewsActivity` | **Android Studio Panda 2** (2025.3.2) | `AI-253.30387.90.2532.14935130` | 2026年2月25日 |
| `Panda3JavaEmptyViewsActivity` | **Android Studio Panda 3** (2025.3.3 Patch 1) | `AI-253.31033.145.2533.15176040` | 2026年4月10日 |
| `Quail4JavaEmptyViewsActivity` | **Android Studio Quail 4** (2026.1.4 Patch 1) | `AI-261.26222.65.2614.16379836` | 2026年9月18日 |
| `Rabbit2JavaEmptyViewsActivity` | **Android Studio Rabbit 2** (2026.2.2 Canary 2) | `AI-262.10315.125.2622.16434108` | 2026年9月24日 |

---

## 📊 比較マトリックス

| 項目 / 設定 | Panda 2 (2025.3.2) | Panda 3 (2025.3.3 P1) | Quail 4 (2026.1.4 P1) | Rabbit 2 (2026.2.2 C2) |
|---|---|---|---|---|
| **Android Gradle Plugin (AGP)** | `9.1.1` | `9.1.1` | `9.4.1` | `9.5.0-alpha07` |
| **Gradle Wrapper** | `9.3.1` | `9.3.1` | `9.6.0` | `9.6.0` |
| **Gradle Daemon ツールチェーン** (`gradle-daemon-jvm.properties`) | JDK `21` | JDK `21` | JDK `25` | JDK `25` |
| **Configuration Cache** (`gradle.properties`) | 未指定 | 未指定 | `true` (有効) | `true` (有効) |
| **リリース最適化 DSL** (`app/build.gradle.kts`) | `isMinifyEnabled = false` | `isMinifyEnabled = false` | `optimization { enable = false }` | `optimization { enable = true; packageScope = ... }` |
| **Keep ルール配置** | `app/proguard-rules.pro` | `app/proguard-rules.pro` | `app/src/main/keepRules/rules.keep` | `app/src/main/keepRules/rules.keep` |
| **Activity 依存関係** (`libs.versions.toml`) | `activity = "1.13.0"` | `activity = "1.8.0"` | `activity-ktx = "1.13.0"` | `activity-ktx = "1.13.0"` |
| **AndroidX / Material のバージョン傾向** | 新しめ (1.8.0 / 1.14.0) | 保守的 (1.6.1 / 1.10.0) | 新しめ (1.8.0 / 1.14.0) | 保守的 (1.6.1 / 1.10.0) |
| **マニフェスト `windowSoftInputMode`** (`AndroidManifest.xml`) | 未指定 | 未指定 | `adjustResize` | `adjustResize` |
| **Edge-to-Edge 有効化メソッド** (`MainActivity.java`) | `EdgeToEdge.enable(this)` | `EdgeToEdge.enable(this)` | `EdgeToEdge.enable(this)` | `WindowCompat.enableEdgeToEdge(getWindow())` |
| **`settings.gradle.kts` の `include(":app")`** | 1行 | 1行 | 2行重複 (`include(":app")` x2) | 1行 |

---

## 🔍 主な変更点・差分の詳細

### 1. ビルドツールと Gradle 実行環境

- **Gradle バージョンの更新**:
  - Panda 2 および Panda 3 では **Gradle 9.3.1** が使用されています。
  - Quail 4 および Rabbit 2 では **Gradle 9.6.0** へアップデートされています。
- **Android Gradle Plugin (AGP)**:
  - `9.1.1` (Panda 2 & 3) ➔ `9.4.1` (Quail 4) ➔ `9.5.0-alpha07` (Rabbit 2 Canary) と段階的に更新されています。
- **Gradle Daemon 用 JVM ツールチェーン**:
  - `gradle/gradle-daemon-jvm.properties` の `toolchainVersion` が、Quail 4 以降 **21** から **25** (JDK 25) に引き上げられています。Foojay Disco API 経由でプラットフォームに応じた JDK 25 が取得・指定されます。
- **Configuration Cache のデフォルト有効化**:
  - Quail 4 および Rabbit 2 では、`gradle.properties` に `org.gradle.configuration-cache=true` がデフォルトで追記され、ビルド設定フェーズのキャッシュが標準で有効化されました。

---

### 2. ProGuard / R8 最適化設定 DSL と Keep ルール配置

Quail 4（AGP 9.4 以降）から、従来の ProGuard 設定方法から AGP 9 の新しい `optimization` DSL および規約ベースの Keep ルール配置へと刷新されました。

#### `app/build.gradle.kts`
- **Panda 2 & Panda 3 (従来の ProGuard DSL)**:
  ```kotlin
  buildTypes {
      release {
          isMinifyEnabled = false
          proguardFiles(
              getDefaultProguardFile("proguard-android-optimize.txt"),
              "proguard-rules.pro"
          )
      }
  }
  ```
- **Quail 4 (新しい optimization DSL - 無効化)**:
  ```kotlin
  buildTypes {
      release {
          optimization {
              enable = false
          }
      }
  }
  ```
- **Rabbit 2 (新しい optimization DSL - スコープ付き最適化が有効)**:
  ```kotlin
  buildTypes {
      release {
          optimization {
              enable = true
              packageScope = setOf("androidx.**", "kotlin.**", "kotlinx.**")
          }
      }
  }
  ```

#### Keep ルールファイルの移行
- **Panda 2 & Panda 3**: `app/proguard-rules.pro` を利用。
- **Quail 4 & Rabbit 2**: `app/proguard-rules.pro` は削除され、**`app/src/main/keepRules/rules.keep`** へ移行されました。
  > AGP は `src/main/keepRules` 配下のすべてのルールファイルを自動的に収集して R8 に渡す規約となっています。

---

### 3. バージョンカタログ (`gradle/libs.versions.toml`)

#### Activity ライブラリの変更
- **Panda 2 & 3**:
  ```toml
  [versions]
  activity = "1.13.0" # (Panda 3 では 1.8.0)
  [libraries]
  activity = { group = "androidx.activity", name = "activity", version.ref = "activity" }
  ```
- **Quail 4 & Rabbit 2**:
  Kotlin 拡張を含むアーティファクト (`activity-ktx`) に変更されました：
  ```toml
  [versions]
  activityKtx = "1.13.0"
  [libraries]
  activity-ktx = { group = "androidx.activity", name = "activity-ktx", version.ref = "activityKtx" }
  ```

#### 依存ライブラリのバージョン傾向
- **Panda 2 & Quail 4** は最新寄りのライブラリバージョンを採用：
  - `appcompat`: `1.8.0`
  - `material`: `1.14.0`
  - `constraintlayout`: `2.2.2`
  - `androidx.test.ext:junit`: `1.3.0`
  - `androidx.test.espresso:espresso-core`: `3.7.0`
- **Panda 3 & Rabbit 2** は保守的・長期安定板を採用：
  - `appcompat`: `1.6.1`
  - `material`: `1.10.0`
  - `constraintlayout`: `2.1.4`
  - `androidx.test.ext:junit`: `1.1.5`
  - `androidx.test.espresso:espresso-core`: `3.5.1`

---

### 4. マニフェスト設定の変更 (`app/src/main/AndroidManifest.xml`)

**Quail 4** および **Rabbit 2** では、`MainActivity` に `android:windowSoftInputMode="adjustResize"` がデフォルトで付与されています：
```xml
<activity
    android:name=".MainActivity"
    android:exported="true"
    android:windowSoftInputMode="adjustResize">
```
> 近年の Edge-to-Edge フルスクリーン表示において、ソフトウェアキーボード（IME）表示時にレイアウトが正しくリサイズ・スクロールされるようにするための標準設定です。

---

### 5. Edge-to-Edge 実装メソッドの変更 (`MainActivity.java`)

- **Panda 2, Panda 3, Quail 4**:
  `androidx.activity.EdgeToEdge` を使用：
  ```java
  import androidx.activity.EdgeToEdge;
  ...
  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      EdgeToEdge.enable(this);
      setContentView(R.layout.activity_main);
      ...
  }
  ```
- **Rabbit 2 (Canary)**:
  `WindowCompat.enableEdgeToEdge` に変更：
  ```java
  import androidx.core.view.WindowCompat;
  ...
  @Override
  protected void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      WindowCompat.enableEdgeToEdge(getWindow());
      setContentView(R.layout.activity_main);
      ...
  }
  ```

---

### 6. プロジェクト設定 (`settings.gradle.kts`)

- **Quail 4**: テンプレート生成時の不具合により、`include(":app")` が2行重複して出力されています：
  ```kotlin
  rootProject.name = "Quail4JavaEmptyViewsActivity"
  include(":app")
  include(":app")
  ```
- **他のバージョン**: `include(":app")` は1行のみです。

---

## 📂 ディレクトリ構成

```text
android-studio-empty-views-activity-templates/
├── README.md                          # 英語比較レポート
├── README.ja.md                       # 日本語比較レポート
├── Panda2JavaEmptyViewsActivity/      # Android Studio Panda 2 (2025.3.2)
├── Panda3JavaEmptyViewsActivity/      # Android Studio Panda 3 (2025.3.3 P1)
├── Quail4JavaEmptyViewsActivity/      # Android Studio Quail 4 (2026.1.4 P1)
└── Rabbit2JavaEmptyViewsActivity/      # Android Studio Rabbit 2 (2026.2.2 C2)
```

