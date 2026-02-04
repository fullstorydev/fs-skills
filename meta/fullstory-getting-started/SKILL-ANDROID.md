---
name: fullstory-getting-started-android
version: v1
description: Android setup for Fullstory with agent-assisted integration. Prompts for Org ID and applies Gradle and initialization edits to the user's project.
parent_skill: fullstory-getting-started
platforms: [android]
related_skills:
  - fullstory-getting-started
  - fullstory-privacy-controls
  - mobile-instrumentation-orchestrator
---

# Fullstory Android — Agent-assisted setup

Use this skill when the user wants to **add Fullstory to an Android project** and the AI companion should **prompt for Org ID** and **apply the edits** to their Gradle and app code.

---

## Agent instructions

1. **Prompt the user for:**
   - **Fullstory Org ID** (required). Example: `ABCD-1234`. Tell them they can find it in Fullstory: Settings → General → Org ID.
   - **Server** — multi-choice: **US**, **EU**, or **Custom**. If **US**, use default (no `serverUrl` or `https://fullstory.com`). If **EU**, use `https://eu1.fullstory.com`. If **Custom**, ask for the server URL and use it.

2. **Locate the user’s Android project:**
   - Prefer project root where `settings.gradle` or `project/build.gradle` and `app/build.gradle` exist.
   - If unclear, ask: “Is your Android project open, and is this repo the app’s root (where `app/build.gradle` lives)?”

3. **Look up the latest Gradle plugin version** before applying edits. Do **not** hardcode a version. Fetch the [Fullstory for Mobile Apps Release Notes](https://help.fullstory.com/hc/en-us/articles/4412766845591) and use the **most recent release** version number (e.g. `1.67.0`) for the Fullstory Android Gradle plugin classpath. Use that value wherever the edits below reference the plugin version.

4. **Apply the edits below** in order. Replace `YOUR_ORG_ID` and (if user chose EU or Custom) the server URL with the user’s values; use the version from step 3 for the Gradle plugin classpath. If the project uses **Kotlin DSL** and has a `libs.versions.toml` (version catalog), do the libs.versions.toml path in **step 1** before editing app-level Gradle.

5. **After applying:** Tell the user to sync Gradle and run the app, then point them to `fullstory-privacy-controls/SKILL-MOBILE.md` and `mobile-instrumentation-orchestrator` for next steps (privacy, identify, events).

---

## Edits to apply

### 1. Project-level Gradle (repository + plugin)

**Check first:** If the project uses Kotlin DSL and has `gradle/libs.versions.toml` (or `libs.versions.toml` in the root), do the **libs.versions.toml** path below so the plugin is available before editing app-level Gradle. Otherwise use the **buildscript** path.

---

**Path A — Project has `libs.versions.toml` (version catalog):**

- **File:** `gradle/libs.versions.toml` (or root `libs.versions.toml`)
- **Where:** Add to `[versions]` the latest Fullstory plugin version (from [Release Notes](https://help.fullstory.com/hc/en-us/articles/4412766845591)), e.g. `fullstory = "1.67.0"`. Add to `[plugins]`:

```toml
fullstory = { id = "com.fullstory", version.ref = "fullstory" }
```

- **File:** `settings.gradle.kts` (root)
- **Where:** In `settings.gradle` / `settings.gradle.kts`, add the Fullstory Maven repository in **both** `pluginManagement { repositories { ... } }` and `dependencyResolutionManagement { repositories { ... } }` so the plugin and any SDK dependencies can be resolved:

**In `pluginManagement { repositories { ... } }`:**

```kotlin
maven { url = uri("https://maven.fullstory.com") }
```

**In `dependencyResolutionManagement { repositories { ... } }`:**

```kotlin
maven { url = uri("https://maven.fullstory.com") }
```

- **File:** Root `build.gradle.kts` (top-level project, e.g. `build.gradle.kts` at project root)
- **Where:** Inside the `plugins { }` block, add the Fullstory plugin with `apply false` so it is available to app modules but not applied at root:

```kotlin
alias(libs.plugins.fullstory) apply false
```

Then in step 2, use `alias(libs.plugins.fullstory)` in `app/build.gradle.kts` and add the `fullstory { }` block.

---

**Path B — Project has a root `build.gradle` (or `build.gradle.kts`) with a `buildscript` block:**

- **File:** `project/build.gradle` (or root `build.gradle`)
- **Where:** Inside `buildscript { repositories { ... } }`, add the Fullstory Maven repository. Inside `buildscript { dependencies { ... } }`, add the Fullstory Gradle plugin classpath.

**Insert — `buildscript.repositories` (add one repository):**

```groovy
maven { url "https://maven.fullstory.com" }
```

**Insert — `buildscript.dependencies` (add one classpath):**

Use the **latest version** from the [Fullstory for Mobile Apps Release Notes](https://help.fullstory.com/hc/en-us/articles/4412766845591) (e.g. `1.67.0`). Do not hardcode; look up at apply time.

```groovy
classpath 'com.fullstory:gradle-plugin-local:<VERSION_FROM_RELEASE_NOTES>'
```

Replace `<VERSION_FROM_RELEASE_NOTES>` with the most recent release version (e.g. `1.67.0`).

---

### 2. App-level Gradle (plugin + Org ID + enabledVariants + logcatLevel)

- **File:** `app/build.gradle` (or `app/build.gradle.kts`)
- **Where:**
  - At the top, after other `apply plugin` lines (or in `plugins { }` for Kotlin DSL), add the Fullstory plugin.
  - Add a `fullstory { }` block with the user’s Org ID, `enabledVariants = "all"`, and `logcatLevel = "log"`. See [Android Configuration](https://developer.fullstory.com/mobile/android/configuration/): `enabledVariants` is a substring match to build flavor (narrow later if needed); `logcatLevel` controls which Logcat messages Fullstory ingests (e.g. for frustration signals) and can be adjusted as needed.

**Groovy — add after other plugins:**

```groovy
apply plugin: 'fullstory'
```

**Groovy — add a `fullstory` block (same file, top level):**

```groovy
fullstory {
    org "YOUR_ORG_ID"
    // Substring match to build variant (flavor + build type). Use "all" for all variants; narrow later if needed (e.g. 'debug|release').
    // See: https://developer.fullstory.com/mobile/android/configuration/#:~:text=app.%0A%0ADefault%3A%20false-,enabledVariants,-String
    enabledVariants "all"
    // Logcat level for Fullstory to ingest (e.g. error, warn, info, debug, log). Adjust as needed for frustration signals.
    // See: https://developer.fullstory.com/mobile/android/configuration/
    logcatLevel "log"
}
```

Replace `YOUR_ORG_ID` with the value provided by the user.

**Kotlin DSL — add in `plugins { }`:**

- If you used **libs.versions.toml** in step 1: `alias(libs.plugins.fullstory)`
- Otherwise: `id("fullstory")`

**Kotlin DSL — add block (same for both):**

```kotlin
fullstory {
    org = "YOUR_ORG_ID"
    // Substring match to build variant (flavor + build type). Use "all" for all variants; narrow later if needed.
    // See: https://developer.fullstory.com/mobile/android/configuration/#:~:text=app.%0A%0ADefault%3A%20false-,enabledVariants,-String
    enabledVariants = "all"
    // Logcat level for Fullstory to ingest (e.g. error, warn, info, debug, log). Adjust as needed for frustration signals.
    // See: https://developer.fullstory.com/mobile/android/configuration/
    logcatLevel = "log"
}
```

Replace `YOUR_ORG_ID` with the user’s value.

---

### 3. Manifest permission and Application subclass

**3a. AndroidManifest.xml — INTERNET permission**

- **File:** `app/src/main/AndroidManifest.xml` (or the main source set’s manifest)
- **Where:** Inside the root `<manifest>` element, ensure INTERNET and ACCESS_NETWORK_STATE are declared (required for Fullstory to send data and check connectivity).

**Insert if not present:**

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

**3b. Application subclass**

- **Check:** In the same AndroidManifest.xml, look at `<application android:name="..." ...>`. If `android:name` is already set, the app has a custom Application class — no change needed.
- **If `android:name` is not set:** Create an Application subclass and register it in the manifest.

**Create — `app/src/main/java/<package>/App.kt` (or `App.java`):**

**Kotlin:**

```kotlin
package com.example.app   // use the app’s actual package

import android.app.Application

class App : Application() {
    override fun onCreate() {
        super.onCreate()
        
    }
}
```

**Java:**

```java
package com.example.app;   // use the app’s actual package

import android.app.Application;

public class App extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        
    }
}
```

**Manifest — set the Application class:**

- **File:** `app/src/main/AndroidManifest.xml`
- **Where:** On the `<application>` element, set `android:name` to the new class. Use `.App` if the class is in the default application package, or the full class name (e.g. `com.example.app.App`).

```xml
<application
    android:name=".App"
    ...>
```

If the app uses a different package for the Application class, use the fully qualified name (e.g. `android:name="com.example.app.App"`).

---

### 4. Server (EU or Custom)

If the user chose **EU** or **Custom** in the Server prompt:

- **File:** Same as step 2 (`app/build.gradle` or `app/build.gradle.kts`)
- **Where:** Inside the `fullstory { }` block, add `serverUrl`. Use **EU** → `https://eu1.fullstory.com` (EU is currently limited to eu1). Use **Custom** → the URL the user provided.

**Groovy (add inside existing `fullstory { }`):**

```groovy
serverUrl "https://eu1.fullstory.com"   // EU; for Custom use the URL they provided
```

**Kotlin DSL (add inside existing `fullstory { }`):**

```kotlin
serverUrl = "https://eu1.fullstory.com"   // EU; for Custom use the URL they provided
```

Do **not** add `serverUrl` if the user chose **US** (default).

---

### 5. Application / consent-gated capture (if needed)

The Gradle plugin auto-initializes Fullstory by default. No Application code is required for a normal setup.

If the app uses **consent-gated capture** (e.g. `recordOnStart false` in the `fullstory { }` block), call **`FS.restart()`** after the user grants consent to begin capture. There is no `FS.start()` API. See `fullstory-user-consent/SKILL-MOBILE.md` for consent flows.

---

## Summary: what the agent does

| Step | File(s) | Action |
|------|--------|--------|
| 1 | `libs.versions.toml` + `settings.gradle.kts` + root `build.gradle.kts` (if version catalog), or `project/build.gradle` | If TOML: add version + plugin to libs.versions.toml, Maven repo in **both** pluginManagement and dependencyResolutionManagement in settings, and `alias(libs.plugins.fullstory) apply false` in root `plugins { }`. Otherwise: add Maven repo and Gradle plugin classpath. |
| 2 | `app/build.gradle` or `app/build.gradle.kts` | Apply Fullstory plugin (alias from TOML or id); add `fullstory { org, enabledVariants "all", logcatLevel "log" }` (and serverUrl if EU/Custom). |
| 3 | `app/src/main/AndroidManifest.xml` + optionally `App.kt`/`App.java` | Add INTERNET and ACCESS_NETWORK_STATE permissions if missing; check for Application subclass — if none, create App.kt/App.java extending Application and set `android:name` on `<application>`. |
| 4 | `app/build.gradle` | If user chose EU or Custom, add `serverUrl` inside `fullstory { }` (EU → `https://eu1.fullstory.com`) |
| 5 | — | No Application init needed by default; for consent-gated capture use `FS.restart()` after consent (see fullstory-user-consent) |

---

## Reference

- **Latest plugin/SDK version:** [Fullstory for Mobile Apps Release Notes](https://help.fullstory.com/hc/en-us/articles/4412766845591) — use the most recent release version for the Gradle plugin classpath; do not hardcode.
- **Android Configuration (serverUrl, enabledVariants, logcatLevel):** [Configuration](https://developer.fullstory.com/mobile/android/configuration/) — `serverUrl` for US/EU/Custom; `enabledVariants` is a substring match to build flavor; `logcatLevel` controls which Logcat messages Fullstory ingests (default `"log"`).
- **Official Android setup:** [Getting Started with Android Data Capture](https://help.fullstory.com/hc/en-us/articles/360040596093-Getting-Started-with-Android-Data-Capture)
- **After setup:** Use `fullstory-privacy-controls/SKILL-MOBILE.md` and `mobile-instrumentation-orchestrator/SKILL.md` for implementation order and API usage.
