## QA Automation Engineer 사전과제
---
#### 목표 및 요구사항
---
##### 1. 목표
1. Android Opensource Project를 최신 Android 환경에서 Build가 성공하도록 업데이트
2. 로그인 기능에 대한 Test 자동화 Code 작성
---
##### 2. 요구 사항
1. Build가 성공하도록 업데이트된 Project Source
2. 테스트 자동화 Source Code
3. README.md 문서에 다음 내용들 포함
    - Any assumptions - 추정한 부분들
    - Build가 성공되도록 어떤 변경점들을 주었는지
    - 툴과 언어 선택에 대한 이유
    - 실행 환경 및 설정에 대한 설명
    - 테스트 실행 방법에 대한 설명
    - 추후 개선/해결해야 하는 부분들에 대한 설명

---
#### Any assumptions
- Build 및 Source에 대한 적절한 변경점은 어디까지일까?
    1. Andorid Studio의 최신 버전, SDK, Lib 등을 설치하고 Source에 적극적으로 관여
    2. Source의 변형을 최소 ~ 전혀 하지 않은 상태에서 과제를 수행
- Test Tool 및 언어는  허용될 수 있을끼?
    1. 기존의 Python Code 및 lib를 적극적으로 사용할 수 있음
    2. 일부 Python Code 및 외부 Tool을 활용하여 해결
    3. Android Tool 및 java에 Dependency한 해결책
---
#### Source 변경점
1. ./Build.gradle
```diff
ext {
    configuration = [
            package          : "com.malmstein.yahnac",
-            buildToolsVersion: "23.0.3",
+            buildToolsVersion: "26.0.3",
            minSdk           : 16,
-            targetSdk        : 23,
-            compileSdk       : 23,
+            targetSdk        : 26,
+            compileSdk       : 26,
            versionCode      : 27,
            versionName      : "1.3.1"
    ]
}

buildscript {
    repositories {
        jcenter()
        maven {
            url 'https://maven.fabric.io/public'
        }
+        google()
    }

    dependencies {
-        classpath 'com.android.tools.build:gradle:2.1.3'
+        classpath 'com.android.tools.build:gradle:3.2.0'
        classpath 'com.google.gms:google-services:3.0.0'

    }
}

allprojects {
    repositories {
        mavenCentral()
        jcenter()
-        maven {
-            url "http://dl.bintray.com/novoda/maven"
-        }
+        google()
    }
}
```
2. ./gradle/wrapper/gradle-wrapper.properties
```diff
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
- distributionUrl=https\://services.gradle.org/distributions/gradle-2.14.1-all.zip
+ distributionUrl=https\://services.gradle.org/distributions/gradle-4.8-bin.zip
```
3. ./app/build.gradle
```diff
apply plugin: 'com.android.application'

def cfg = rootProject.ext.configuration;

android {
-    compileSdkVersion 23
-    buildToolsVersion '23.0.3'
+    compileSdkVersion 26
+    buildToolsVersion '26.0.3'

    defaultConfig {
        applicationId cfg.package
        minSdkVersion cfg.minSdk
        targetSdkVersion cfg.targetSdk
        versionCode cfg.versionCode
        versionName cfg.versionName

+       testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"

        Calendar instance = Calendar.getInstance()
        instance.setTimeInMillis(System.currentTimeMillis())

        buildConfigField "String", "BUILD_TIME_FORMATTED", "\"${instance.getTime()}\""
        buildConfigField "boolean", "ENABLE_CRASH_ANALYTICS", "!BuildConfig.DEBUG"
        buildConfigField "boolean", "ENABLE_USAGE_ANALYTICS", "!BuildConfig.DEBUG"
        buildConfigField "boolean", "ENABLE_APP_INVITES", "!BuildConfig.DEBUG"
        
+        testHandleProfiling = true
+        testFunctionalTest = true
        
    }

    compileOptions {
-        sourceCompatibility JavaVersion.VERSION_1_7
-        targetCompatibility JavaVersion.VERSION_1_7
+        sourceCompatibility JavaVersion.VERSION_1_8
+        targetCompatibility JavaVersion.VERSION_1_8
        
    }

    buildTypes {
        debug {
            debuggable true
            minifyEnabled false
            zipAlignEnabled true
        }
        release {
            debuggable false
            jniDebuggable false
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

    packagingOptions {
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/LICENSE-FIREBASE.txt'
        exclude 'META-INF/NOTICE'
    }

    testOptions {
        unitTests.returnDefaultValues = true
    }

    lintOptions {
        abortOnError false
    }

-    buildToolsVersion '23.0.3'
+    buildToolsVersion '26.0.3'
}

dependencies {
-    compile 'com.android.support:support-v13:23.4.0'
-    compile 'com.android.support:design:23.4.0'
-    compile 'com.android.support:appcompat-v7:23.4.0'
-    compile 'com.android.support:cardview-v7:23.4.0'
-    compile 'com.android.support:recyclerview-v7:23.4.0'
+    compile 'com.android.support:support-v13:26.0.1'
+    compile 'com.android.support:design:26.0.1'
+    compile 'com.android.support:appcompat-v7:26.0.1'
+    compile 'com.android.support:cardview-v7:26.0.1'
+    compile 'com.android.support:recyclerview-v7:26.0.1'
    compile 'com.google.firebase:firebase-core:9.4.0'
    compile 'com.google.firebase:firebase-crash:9.4.0'
    compile 'com.google.firebase:firebase-invites:9.4.0'
    compile 'com.novoda:simple-chrome-custom-tabs:0.1.3'
    compile 'com.novoda:notils:2.2.8'
    compile 'io.reactivex:rxandroid:0.24.0'
    compile 'org.jsoup:jsoup:1.8.1'
    compile 'com.firebase:firebase-client-android:2.4.1'
    compile 'com.squareup.okhttp:okhttp:2.3.0'
    testCompile 'junit:junit:4.12'
    testCompile 'org.mockito:mockito-all:1.9.5'
+    androidTestImplementation 'com.android.support.test:runner:1.0.1'
+    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.1'
}

apply plugin: 'com.google.gms.google-services'

```
---
#### 툴과 언어 선택에 대한 이유
Test App의 APK Build 후 Test 가능한 Tool 및 언어에 대한 BMT를 진행
1. Python + uiautomator2
    - Python으로 Sourcode 작성 후 실행하였으나 Click event가 발생하지 않음
    - uiautomator2의 inspector에서 resource-id가 잡히지 않거나 click event가 동작하지 않음
2. Android Studio + espresso
    - 자동완성되어야할 Source가 생성되지 않는 경우가 있음
    - Andorid Studio에서 recording 동작 후 play시 제대로 동작하지 않음
    - 1회 play 후 다시 실행시 lib dependency error가 발생함

- 최종선택
    - espresso를 선택
        - espresso의 경우 record & play시 간헐적으로 exception 발생하나 click event 동작함
        - exception이 발생하는 원인과 play시 error 원인을 해결하는게 주어진 시간내에 문제를 해결할 확률이 높아 보임
    - 문제 해결
        - android.support의 버전을 23.4.0에서 26.0.1로 올림
        - android.support.test.espresso의 버전을 3.0.2에서 3.0.1로 내림
        - esproesso script에 lib를 import
            ``` java
            import static org.hamcrest.core.AllOf.allOf;
            ```
        - 자동완성된 source를 적절하게 수정 및 간소화
        - source가 무조건 pass처리되지 않도록 테스트 성공 여부를 assert 걸어줌
 
---
#### 실행 환경 및 설정에 대한 설명
1. 테스트를 위해 Android Studio에서 "Pixel 4 API 26" 가상 단말을 생성
2. andorid service의 sdk를 23 > 26으로 변경
3. espresso의 버전을 3.0.2 > 3.0.1로 설정
4. Run/Debug Configurations에서 Android Instrumented Tests에 Test Source 추가
---
#### 테스트 실행 방법에 대한 설명
1. Android Studio 실행하여 "NewActivityTest.java" + "Pixel 4 API 26"을 선택한 후 테스트 Build 수행
---
#### 추후 개선/해결해야 하는 부분들에 대한 설명
1. 간헐적으로 test수행시 debugger가 실행되지 않는 문제를 해결해야 함
2. 동시성 테스트를 위한 Andorid 단말 연결 부분을 추가해야 함
2. 실단말에서 release Build를 테스트하기 위한 부분 추가해야 함
3. IDE에 Denpendency하지 않고 CI에 연결하여 스케쥴러에 의한 Regression Test 추가해야 함
4. 가능하다면 resourceId를 추가하고 외부 플렛폼에서도 테스트 가능하도록 개선해야 함
---
