---
title: Cordova Build Work Process With Svelte & Pixi.js
categories: Game
tags: Game Dev
excerpt: Cordova Build
---

<!-- prettier-ignore -->
* content
{:toc}

## Cordova Build 작업 과정 정리

Svelte 와 Pixi.js 로 작업한 심플한 게임을 Cordova 를 이용하여 iOS/Android Build 로 만들어 본 과정을 정리하였습니다.

## 1. Get Started

- cordova cli

```bash
npm install -g cordova
cordova create hello com.example.hello HelloWorld
cd hello

cordova platform add browser
cordova platform add ios
cordova platform add android

cordova run browser

cordova platform ls
cordova plugin ls
cordova requirements

cordova build
cordova prepare

cordova platform update android --save
cordova platform update ios --save
```

## 2. Requirements

### Android

- Android Studio
- Android SDK - Android Studio 설치 및 SDK Path 설정
- Java Development Kit (JDK) - 별도 설치하지 않고 Android Studio 에 포함된 OpenJDK 사용
- Gradle - 별도 설치하거나 Android Studio 설치버전 사용
- JAVA_HOME, ANDROID_HOME, ANDROID_SDK_ROOT 변수 설정

### iOS

- Xcode

```bash
xcode-select --install
```

- ios-deploy

```bash
npm install -g ios-deploy
```

## 3. 추가 작업 및 이슈

### iOS 기본 UIWebView 문제

- WebGL 지원은 되지만 프레임이 안나옴 (30이하)
- AppStore 에서 더이상 UIWebView 허용안함
- WKWebView 로 변경하기 위해 plugin 설치

```bash
cordova plugin add cordova-plugin-wkwebview-engine
```

### WKWebView 이슈

- iOS 9 이상 필요
- 로컬파일에 ajax 요청할 수 없음 (XMLHttpRequest, file:// url)
- 쿠키 수락 설정 불가
- 고급 캐시 설정 사용 불가
- 앱 종료시 HTML5 LocalStorage 삭제됨

### WKWebView 로컬 파일 로딩 실패

- plugin 설치로 해결

```bash
cordova plugin add cordova-plugin-wkwebview-file-xhr
cordova plugin add cordova-plugin-wkwebviewxhrfix
```

### WKWebView navigation error

```xml
<allow-navigation href="about:*" />
```

### WKWebView Status Bar 및 iPhone notch 위치 안맞음

- CSS 수정

```css
padding-top: constant(safe-area-inset-top);
padding-left: constant(safe-area-inset-left);
padding-right: constant(safe-area-inset-right);

padding-top: env(safe-area-inset-top);
padding-left: env(safe-area-inset-left);
padding-right: env(safe-area-inset-right);
```

### WKWebView orientationchange 화면 크기 버그

- orientationchange event 시 window.innerWidth window.innerHeight 값이 이상한 문제
- 동시에 호출되는 resize event 에서도 값이 이상함
- 500ms 단위로 3초동안 resize 반복 호출하는 방법으로 임시 처리

```jsx
let resizeTimer = [];

const reserveResize = () => {
  resizeTimer.forEach((timer) => {
    clearTimeout(timer);
  });
  resizeTimer = [];

  for (let i = 500; i < 3000; i += 500) {
    resizeTimer.push(
      setTimeout(() => {
        let time = i;
        resize(time);
      }, i)
    );
  }
};
```

### Hide Status Bar

- config.xml

```xml
<preference name="Fullscreen" value="true" />
```

- iOS - edit info.plist

  - "Status bar is initially hidden" to "YES"
  - "View controller-based status bar appearance" to "NO" - UIViewControllerBasedStatusBarAppearance

  ```xml
  <platform name="ios">
  	<config-file parent="UIStatusBarHidden" target="*-Info.plist">
  		<true />
  	</config-file>
  	<config-file parent="UIViewControllerBasedStatusBarAppearance" target="*-Info.plist">
  		<false />
  	</config-file>
  </platform>
  ```

### Prevent Scrolling

- config.xml

```xml
Cordova 2.3.0+ <preference name="UIWebViewBounce" value="false" />
Cordova 2.6.0+ <preference name="DisallowOverscroll" value="true" />
```

- 여전히 작은 scroll 발생 - 3픽셀 정도
- svelte window bind scrollY 해봤으나 해결 안됨
- 결국 div 로 감싸고 position: fixed 로 해결

### iOS BackupWebStorage warning

- iCloud에 저장하는 용량이 크면 AppStore Reject 된다는 내용
- 백업 로컬에 저장하도록 변경
- config.xml

```xml
<platform name="ios">
	<preference name="BackupWebStorage" value="local"/>
</platform>
```

### AdMob

- cordova-plugin-admob-free 사용
- Android

```bash
cordova plugin add cordova-plugin-admob-free --save --variable ADMOB_APP_ID="ca-app-pub-XXXXXXXXXXXXXXXX~XXXXXXXXXX"
```

- iOS - edit info.plist or config.xml

  - GADIsAdManagerApp - YES
  - GADApplicationIdentifier - ADMOB_APP_ID iOS

  ```xml
  <platform name="ios">
    <config-file target="*-Info.plist" parent="GADApplicationIdentifier">
      <string>ca-app-pub-XXXXXXXXXXXXXXXX~XXXXXXXXXX</string>
    </config-file>
  </platform>
  ```

### Android 4.4 문제

- babel transpile svelte - rollup-plugin-babel

```jsx
babel({
	extensions: ['.js', '.mjs', '.html', '.svelte'],
	runtimeHelpers: true,
	include: ['src/**', 'node_modules/svelte/**'],
```

- @rollup/plugin-babel 일 경우

```jsx
babel({
	babelHelpers: 'runtime',
```

- CanvasRenderer - pixi.js-legacy
- Canvas Flickering - Add pixi Empty background
- AudioContext / 해결 안됨
- svelte transition error - css polyfill

```jsx
(function () {
  if (!(CSSStyleSheet && CSSStyleSheet.prototype.insertRule)) {
    return;
  }

  var style = document.createElement('style');
  var shouldPrefixKeyframes = false;

  document.body.appendChild(style);

  try {
    style.sheet.insertRule('@keyframes _ {}');
  } catch (err) {
    shouldPrefixKeyframes = true;
  }

  document.body.removeChild(style);

  if (!shouldPrefixKeyframes) {
    return;
  }

  var originalInsertRule = CSSStyleSheet.prototype.insertRule;

  CSSStyleSheet.prototype.insertRule = function (rule, index) {
    if (rule.indexOf('@keyframes') === 0) {
      rule = rule.replace('@keyframes', '@-webkit-keyframes');

      try {
        originalInsertRule.call(this, rule, index);
      } catch (err) {}
    } else {
      originalInsertRule.call(this, rule, index);
    }
  };
})();
```

### Cordova build.config

```json
{
  "android": {
    "debug": {
      "keystore": "build/keystore/debug.keystore",
      "storePassword": "android",
      "alias": "androiddebugkey",
      "password": "android",
      "keystoreType": ""
    },
    "release": {
      "keystore": "build/keystore/release.jks",
      "storePassword": "",
      "alias": "",
      "password": "",
      "keystoreType": ""
    }
  }
}
```

### Cordova App Name

- config.xml

```xml
<name short="short name">Full App Name</name>
```

- Android - res/values/strings.xml 의 app_name 을 수정해야함
- cordova-plugin-app-name 사용

```bash
cordova plugin add cordova-plugin-app-name --variable APP_NAME="Look <Ma> I Got Spaces and Stuff!"
```

- iOS - config.xml 수정

```xml
<platform name="ios">
	<edit-config file="*-Info.plist" mode="merge" target="CFBundleDisplayName">
	  <string>App Name</string>
	</edit-config>
</platform>
```

### iOS - AppStore Invalid Binary

- ITMS-90809: Deprecated API Usage - UIWebView 의존성 제거
  - cordova-admob-free framework 강제 update
  - config.xml

```xml
<platform name="ios">
	<preference name="WKWebViewOnly" value="true" />
</platform>
```

- ITMS-90683: Missing Purpose String in Info.plist
  - cordova-admob-free bluetooth framework 제거

### iOS Add export compliance keys

```xml
<platform name="ios">
  <config-file parent="ITSAppUsesNonExemptEncryption" target="*-Info.plist">
		<false />
	</config-file>
</platform>
```

### Local Storage

- WKWebView 에서 localstorage 삭제되는 문제
- cordova-plugin-nativestorage

### Firebase

- cordova-plugin-firebasex
- Firebase 설정파일 다운로드 후 root directory에 추가 - google-services.json, GoogleService-info.plist
- Messaging 일단 사용 안함

```jsx
FirebasePlugin.setAutoInitEnabled(false, function () {
  log('Firebase Auto init has been disabled ');
  FirebasePlugin.unregister();
});
```

- iOS - xcode project 가 아니라 xcode workspace 사용
- 불필요한 기능들 때문에 빌드 사이즈 커짐
- Android 빌드 오류 발생
- 플러그인 변경 - cordova-plugin-firebase-analytics
- resource file 설정 - config.xml

```xml
<platform name="android">
	<resource-file src="google-services.json" target="app/google-services.json" />
</platform>
<platform name="ios">
	<resource-file src="GoogleService-Info.plist" />
</platform>
```

- cordova-android 9.0.0
- AndroidX 설정 - config.xml

```xml
<preference name="AndroidXEnabled" value="true" />
```

- cordova-plugin-androidx-adapter
- Google Play Service 버전 통일 - cordova-android-play-services-gradle-release
- Android 5.0 이하 지원 안되는 문제 발생
  - because Cordova has increased the minimum SDK version to 22, we no longer support or test with Android 5.0 or lower.
- cordova-android 8.1.0 사용
  - cordova-plugin-androidx-adapter 는 계속 사용

### Android Build

- 2020.03 부터 Android Target SDK 29 필수
  - Target SDK Version 28 → 29
  - platforms\android\build.gradle
  - platforms\android\project.properties
  - platforms\android\CordovaLib\project.properties
- App Bundle 로 빌드하기

```bash
cordova build android --release -- --packageType=bundle
```

### Sound

- howler.js
