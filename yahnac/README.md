# 과제에 대한 가설
# 가설1: Android Studio의 최신 버전과 SDK, Lib 등을 설치하고 기존 source에 약간의 변형이 있을 수 있음
# 가설2: Andorid Studio의 버전만 최신 버전이고 나머지는 최대한 기존 source에 대한 변형을 최소화 해야 함

# 가설1에 대한 수행 결과
1. buildTollsVersion이 28.x를 넘어가면 source의 외부 lib 의존성에 문제가 발생함
2. 28.x 이상에서 rxjava 및 google-services등의 호환성 문제가 발생함
3. androidx로 migration할 경우 일부 lib는 제대로 변환되지 않음
   ex) android.*.design -> com.google.*.material 등으로 아얘 package가 달라짐
   ex) 일부 lib는 lib version으로 올릴 경우 class 기능이 나눠짐
   ex) network module은 제대로 동작하지 않음
4. java version과 lib가 끊임없이 충돌함
5. compile -> implementation, testcompile -> testImplementation으로 수정
6. ootProject.ext.configuration; -> ';' 삭제
7. JavaVersion.VERSION_1_7 --> JavaVersion.VERSION_1_8 수정
8. 
... 계속 새로운 문제가 발생함
... 가설1을 잠정적으로 실패로 간주함

# 가설2에 대한 수행 결과
1. gradle version 2.14.1이 java9+와 호환되지 않음
1.1. 안드로이드 스튜디오 최신 java 버전이 11임
1.2. 다른 java 버전 (java7, 8) 등으로 설정해봤지만 comfile 실패됨
1.3. 일단 gradle 버전은 최신 버전으로 업그레이드 진행함
   
2. Could not initialize class com.android.sdklib.repositoryv2.AndroidSdkHandler 에러
2.1. android sdk init에 문제가 발생함;
2.2. classpath 'com.android.tools.build:gradle:2.1.3' 부분이 문제인 것 같음
2.3. 버전을 3.X 버전으로 올려봄
   
3. This is an unexpected error. Please file a bug containing the idea.log file. 에러
3.1 Project의 java를 9 이상으로 설정하면 발생하는 문제?
3.2 Failed to find Build Tools revision 26.0.3 ?

4. The project is using an incompatible version (AGP 3.0.0) of the Android Gradle plugin. Minimum supported version is AGP 3.2.
4.1 'com.android.tools.build:gradle:3.2.0' -> 3.2.1

5. No cached version of com.android.tools.build:aapt2:3.2.0-4818971 available for offline mode.
5.1 gradle이 offline mode로 빠짐...
5.2 "http://dl.bintray.com/novoda/maven" 주소가 502 bad gateway로 호출됨
5.3 maven 참조를 변경해야 함
5.4 maven{}을 추석처리하고 mavenCentral(), mavenLocal()을 추가함
   
6. Build 성공
BUILD SUCCESSFUL in 3s
28 actionable tasks: 7 executed, 21 up-to-date

Build APK(s)
APK(s) generated successfully for 1 module:
Module 'yahnac02.app': locate or analyze the APK.

