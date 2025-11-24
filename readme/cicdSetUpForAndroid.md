# How to setup CI/CD for Android with GitHub Actions
1) Quick prerequisites
   - Your repo has a working Gradle wrapper (./gradlew).
   - Android module is at app/ (adjust if yours is different).
   - Java 17 (most modern Android projects use this).
   - For release signing (optional): you have an app signing keystore.
1) Create the CI workflow
   - Create: .github/workflows/android-ci.yml
   - name: Android CI
```
on:
push:
branches:
- master
- develop
- feature/**    # any feature branch
pull_request:
branches:
- master
- develop
- feature/**

jobs:
build-test:
runs-on: ubuntu-latest

    permissions:
      contents: read

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "17"

      - name: Set up Android SDK
        uses: android-actions/setup-android@v3

      - name: Gradle cache
        uses: gradle/gradle-build-action@v3

      - name: Verify Gradle wrapper
        uses: gradle/wrapper-validation-action@v2

      - name: Lint
        run: ./gradlew lint --stacktrace

      - name: Unit tests
        run: ./gradlew testDebugUnitTest --stacktrace

      # If you have instrumented tests on emulator, you can add a separate job later

      - name: Assemble debug APK
        run: ./gradlew :app:assembleDebug

      - name: Upload debug APK
        uses: actions/upload-artifact@v4
        with:
          name: app-debug-apk
          path: app/build/outputs/apk/debug/*.apk
          if-no-files-found: error
```
- **What it does**
- triggers on push/PR to your three branch types
- runs lint + unit tests
- builds a debug APK and uploads it as a downloadable artifact
2) Add CD (release build on master)
   - Create a second workflow (keeps CI fast for feature/develop).
   - .github/workflows/android-release.yml
   - name: Android Release
```
on:
push:
branches:
- master
workflow_dispatch: {}   # allow manual runs too

jobs:
release:
runs-on: ubuntu-latest

    permissions:
      contents: write   # needed to create a GitHub Release

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "17"

      - name: Set up Android SDK
        uses: android-actions/setup-android@v3

      - name: Gradle cache
        uses: gradle/gradle-build-action@v3

      # --- Retrieve and create signing keystore from GitHub Secret ---
      - name: Decode keystore
        if: ${{ secrets.SIGNING_KEYSTORE_BASE64 != '' }}
        run: |
          echo "$SIGNING_KEYSTORE_BASE64" | base64 -d > keystore.jks
        env:
          SIGNING_KEYSTORE_BASE64: ${{ secrets.SIGNING_KEYSTORE_BASE64 }}

      # --- Build a signed release ---
      - name: Assemble Release
        env:
          SIGNING_KEY_ALIAS: ${{ secrets.SIGNING_KEY_ALIAS }}
          SIGNING_KEY_PASSWORD: ${{ secrets.SIGNING_KEY_PASSWORD }}
          SIGNING_STORE_PASSWORD: ${{ secrets.SIGNING_STORE_PASSWORD }}
        run: |
          ./gradlew \
            -Pci=true \
            -Pandroid.injected.signing.store.file=$PWD/keystore.jks \
            -Pandroid.injected.signing.store.password="$SIGNING_STORE_PASSWORD" \
            -Pandroid.injected.signing.key.alias="$SIGNING_KEY_ALIAS" \
            -Pandroid.injected.signing.key.password="$SIGNING_KEY_PASSWORD" \
            :app:clean :app:assembleRelease

      - name: Upload signed release artifact
        uses: actions/upload-artifact@v4
        with:
          name: app-release-apk
          path: app/build/outputs/apk/release/*.apk

      - name: Create GitHub Release
        id: gh_release
        uses: softprops/action-gh-release@v2
        with:
          tag_name: v${{ github.run_number }}
          name: "Release v${{ github.run_number }}"
          files: app/build/outputs/apk/release/*.apk
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```          
- If you prefer an AAB for Play Store: replace assembleRelease with :app:bundleRelease and publish the .aab.
3) Configure signing in your Gradle project
   - In app/build.gradle (KTS or Groovy), add a signing config that reads from injected properties so your local builds still work, and CI can supply secrets:
   - Groovy example
```
android {
   // ...
   signingConfigs {
   release {
   storeFile file(System.getProperty("android.injected.signing.store.file", "keystore.jks"))
   storePassword System.getProperty("android.injected.signing.store.password", "")
   keyAlias System.getProperty("android.injected.signing.key.alias", "")
   keyPassword System.getProperty("android.injected.signing.key.password", "")
   enableV3Signing true
   enableV4Signing true
   }
   }
   buildTypes {
   release {
   minifyEnabled true
   proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
   signingConfig signingConfigs.release
   }
   }
   }
```   
- (If you already have signing configs, just adjust how values are supplied.)
4) Add GitHub Secrets
   - In your repo → Settings → Secrets and variables → Actions → New repository secret:
   - SIGNING_KEYSTORE_BASE64 → base64 of your keystore.jks
   - (create with base64 -w0 keystore.jks > keystore.jks.b64 then paste)
   - SIGNING_KEY_ALIAS
   - SIGNING_KEY_PASSWORD
   - SIGNING_STORE_PASSWORD
   - Never commit the keystore or passwords to the repo.
5) (Optional) Deploy to Google Play – Internal testing
   - If you want real CD to the Play Console:
   - Add the Gradle Play Publisher plugin in your root/settings and app module:
   - app/build.gradle
   ```
   plugins {
   id 'com.github.triplet.play' version '3.10.1'
   }
   play {
   serviceAccountCredentials = file("play-service-account.json")
   track = "internal"
   defaultToAppBundles = true
   }
   ```
- In Play Console, create a Service Account and download the JSON key.
- Add a secret PLAY_SERVICE_ACCOUNT_JSON with the file’s base64.
- Update the release workflow to write the JSON and publish:
    - name: Restore Play credentials
    - run: echo "$PLAY_SERVICE_ACCOUNT_JSON" | base64 -d > play-service-account.json
      env:
    - PLAY_SERVICE_ACCOUNT_JSON: ${{ secrets.PLAY_SERVICE_ACCOUNT_JSON }}
    - name: Publish to Play (Internal)
    - run: ./gradlew :app:publishRelease
- Now each push to master builds and ships to the Internal track.
6) Nice extras (add when ready)
   - Static analysis: ./gradlew detekt ktlintFormat (if using Detekt/Ktlint)
   - Instrumentation tests on emulator (matrix by API level)
   - Code coverage: Jacoco + upload reports as artifacts
   - PR status checks required in GitHub Branch Protection
   Common gotchas
   If your default branch is main, change triggers accordingly.
   Module path differs? Replace :app: with your module path.
   NDK/CMakes? Add sdkmanager installs or ndkVersion in build.gradle.
   Large monorepo? Use Gradle Remote Build Cache for speed.
   If you paste the two workflow files as-is, you’ll have ✅ CI on master, develop, and feature/*, and ✅ a signed release artifact on master.
