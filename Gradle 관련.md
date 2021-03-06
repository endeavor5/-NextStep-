

**프로젝트 소개**
-------

Google Developer Training 1.1 을 보다 AndroidManifest.xml과 Gradle에 관한 내용이 나왔습니다.

그래서 두 내용을 정리해보았습니다.

다음은 Gradle에 관한 내용입니다.

내용중에 Gradle과 상관없는 내용이 있을 수도 있습니다. 그런 내용은 제가 공부하면서 궁금한 점이 꼬리에 꼬리를 물었기때문에 같이 정리해서 그렇습니다.
그런 내용은 Skip 하셔도 됩니다!

프로젝트는 지속적으로 추가 될 예정입니다. 상단의 Star, Watching 버튼을 클릭하시면 구독 알림을 받으실 수 있습니다 :)

----------

[Google Developer Training 1.1 URL] 

https://google-developer-training.github.io/android-developer-fundamentals-course-concepts-v2/unit-1-get-started/lesson-1-build-your-first-app/1-1-c-your-first-android-app/1-1-c-your-first-android-app.html

https://codelabs.developers.google.com/codelabs/android-training-hello-world/index.html?index=..%2F..%2Fandroid-training#2


----------


**목차**
--

 - [프로젝트 소개](#프로젝트-소개)
 - [목차](#목차)
 - [전체 플로우](#전체-플로우)
  - [Gradle](#Gradle)
  - [빌드 프로세스](#빌드-프로세스)
  - [프로젝트 내에서의 Gradle 설정파일](#프로젝트-내에서의-Gradle-설정파일)
     - [01.settings.gradle 파일](#settings.gradle-파일)
     - [02.루트 프로젝트 디렉토리의 build.gradle 파일 (최상위 빌드 파일)](#02.루트-프로젝트-디렉토리의-build.gradle-파일-(최상위-빌드-파일))
     - [03.모듈내에서의 Gradle 관련 파일](#03.모듈내에서의-Gradle-관련-파일)
     - [04.Gradle 속성 파일](#04.Gradle-속성-파일)
       - [gradle.properties](#gradle.properties)      
       - [local.properties](#local.properties)      
  - [JCenter vs Maven Central](#JCenter-vs-Maven-Central)
  - [Dex 파일이란?](#Dex-파일이란?)
  - [JVM과 DVM의 차이점](#JVM과-DVM의-차이점)
  - [MultiDex란?](#MultiDex란?)
  - [Proguard란?](#Proguard란?)
  - [새로운 라이브러리를 추가하는 방법](#새로운-라이브러리를-추가하는-방법)
     - [01.jar파일일 경우 (간단한 방법)](#)
     - [02.jar나 다른 라이브러리일 경우](#02.jar나-다른-라이브러리일-경우)
  - [.jar형 라이브러리를 앱에 적용할 때 새로운 라이브러리를 gradle에 추가했는데
Cause: unable to find valid certification path to requested target 에러가 나는 경우](#ㄱ)


----------

**전체 플로우**
----------

**Gradle**
------

안드로이드 스튜디오는 Gradle을 이용하여 빌드 프로세스를 자동화한다. 

Gradle은 빌드 프로세스를 자동화하고 관리하는 한편, 개발자가 유연하게 맞춤형 빌드 구성을 정의할 수 있다. 

Gradle과 Gradle용 Android 플러그인은 안드로이드 스튜디오와 독립적으로 실행된다. 

때문에 빌드 도구를 별도로 업데이트 해야 한다. 

----------
**빌드 프로세스**
------

빌드 프로세스란 프로젝트를 APK로 변환해주는 과정을 말한다.

![BuildProcess](/image/BuildProcess.PNG)

1. 컴파일러는 소스코드를 DEX파일로 변환한다. 그리고 그 외의 모든 것을 컴파일된 리소스로 변환한다.

2. APK Packager는 DEX파일과 컴파일된 리소스를 APK로 만든다. 

3. 앱을 기기에 설치하려면 APK에 서명이 되어있어야 한다. 

4. 그러므로 APK Packager는 Debug/Release 키 저장소를 사용하여 APK에 서명한다. 

5. 빌드 프로세스가 끝나면 APK가 생성된다.


----------
**프로젝트 내에서 Gradle 관련 파일**
------

![Directory](/image/Directory.PNG)

위의 그림은 안드로이드 앱의 기본 프로젝트 구조이다. 

**01. settings.gradle 파일**

앱을 빌드할때 어떤 모듈을 포함할지 Gradle에 알려준다.

일반적인 경우엔 아래와 같이 포함한다.

```java
include ':app'
```

----------

**02. 루트 프로젝트 디렉토리의 build.gradle 파일 (최상위 빌트 파일)**

프로젝트의 모든 모듈에 적용되는 빌드 구성을 정의한다.

즉, 프로젝트의 모든 모듈에 공통되는 Gradle 저장소와 종속성을 정의한다.

```java
buildscript {
      repositories {
           google()
           jcenter()
      }

      dependencies {
           classpath 'com.android.tools.build:gradle:3.6.3'
      }
  }

  allprojects {
    repositories {
        google()
        jcenter()
    }
 }
```

**2-1. buildscript{} 구역 :** 

프로젝트의 모든 모듈에 공통되는 Gradle 레파지토리와(라이브러리 땡겨오는)

종속성을 정의하기 위한 블록, 구역이다.

프로젝트의 모든 모듈에 공통되는 구역이기 때문에 모듈의(ex. app) 개인 dependencies는 이 곳에 정의될 수 없다.


**2-2. repositories{} 구역 :** 

dependencies 구역에 쓰여있는 라이브러리들을 찾거나 다운로드 하기 위한 Gradle 레파지토리를 정의한다.

Gradle은 JCenter나 Maven Central, Ivy를 Gradle 레파지토리로 지원하고 있다.

또한 로컬 레파지토리나 자신의 소유의 서버 레파지토리도 정의할 수 있다.

참고로 안드로이드 스튜디오 3.0 이상이라면 Google의 Maven 저장소를 포함해야한다.


**2-3. dependencies{} 구역 :** 

프로젝트가 빌드하기 위해 필요한 Gradle 정보를 넣는 구역이다.

참고로 classpath 'com.android.tools.build:gradle:3.3.0'은 버전 3.3.0의 그래들을 위한 안드로이드 플러그인이다.

(플러그인 : 일반적인 소프트웨어의 일부로, 외부 기술의 일종이다. 즉, 쉽게 설치되고 사용될 수 있는 프로그램을 말한다.)


**2-4. allprojects{} 구역 :** 

프로젝트 내의 모든 모듈의 dependencies와 저장소들을 사용하기 위해 정의하는 구역이다.

디폴트 저장소는 jCenter이다.

----------


**03. 모듈내에서의 Gradle 관련 파일**

이 파일(build.gradle)이 위치하는 특정 모듈의 빌드 설정을 구성할 수 있으며, AndroidManifest 또는 최상위 build.gradle 파일에 있는 설정을 재정의할 수 있다.


```java
apply plugin: 'com.android.application' 

      android {
          compileSdkVersion 29
          buildToolsVersion "29.0.3"

      defaultConfig {
        applicationId 'com.test.emhwang'
        minSdkVersion 21
        targetSdkVersion 29
        versionCode 1
        versionName "1.0"
     }

      buildTypes {
         release {
             minifyEnabled true 
             proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
         }
      }

      productFlavors {
        free {
          applicationId 'com.test.emhwang.free'
        }
        
        paid {
          applicationId 'com.test.emhwang.paid'
       }
     }
     
     splits {
        density {
           enable false
           exclude "ldpi", "tvdpi", "xxxhdpi", "400dpi", "560dpi"
        }
      }
    }

    dependencies {
       implementation fileTree(dir: 'libs', include: ['*.jar'])
       implementation 'androidx.appcompat:appcompat:1.0.2'
       testImplementation 'junit:junit:4.12'
       androidTestImplementation 'androidx.test.ext:junit:1.1.1'
       androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
    }
```


**3-1. apply plugin :** 

Android 플러그인을 적용한다.

Gradle은 이것을 빌드하고 android{} 구역을 이용할 수 있도록 만든다.

**3-2. android{}구역 :** 

모듈의 Android 관련 설정을 구성하는 공간이다.

**3-3. compileSdkVersion :** 

Gradle이 앱을 컴파일 할 때 사용해야 할 Android API 레벨을 지정한다.

이 말인 즉슨, 앱은 이 API 레벨과 해당 API 레벨보다 낮은 API의 특징을 사용할 수 있다는 것이다.

**3-4. buildToolsVersion :** 

Gradle이 앱을 빌드할 때 사용해야 하는 컴파일러의 버전 및 SDK 빌드 도구를 지정한다.

SDK관리자를 사용하여 빌드 도구를 다운로드 할 수 있다.

(SDK : 안드로이드 소프트웨어를 개발하기 위한 도구를 모아놓은 키트)

(JDK : 자바 소프트웨어를 개발하기 위한 도구를 모아놓은 키트)

(=> 안드로이드를 개발하려면 SDK와 JDK 둘다 필요하다. (안드로이드는 자바 언어로 개발 되므로))

buildToolsVersion은 컴파일러의 버전, compileSdkVersion은 컴파일러가 컴파일 할 때의 버전이므로 compileSdkVersion과 buildToolsVersion은 서로 비슷하게 맞춰주는 것이 좋다.

----------
**3-5. defaultConfig{} 구역 :** 

디폴트 셋팅과 모든 빌드 변수의 값들을 캡슐화 해놓은 구역이다.

또한 이 구역은 AndroidManifest.xml에 있는 몇몇 속성들을 오버라이드 할 수 있다. (빌드 시스템에 의해서 동적으로 오버라이드 된다.)

**3-5-1. applicationId :** 

앱을 출시하기 위하여, 자신을 나타낼 수 있는 유니크한 값이다.

**3-5-2. minSdkVersion :** 

앱을 구동하기 위해 필요한 최소 API 레벨이다.

**3-5-3. targetSdkVersion :** 

앱을 테스트했었던 API 레벨이다. (기준)

**3-5-4. versionCode :** 

버전 코드

**3-5-5. versionName :** 

버전명 (사용자들이 친숙하게 느낄 수 있는 버전 코드)

**3-6. buildTypes{} 구역 :** 

빌드 타입을 구성할 수 있는 구역이다.

기본적으로 빌드 시스템은 debug와 release 두가지의 빌드 타입을 정의한다.


----------

**04. Gradle 속성 파일**

**4-1. gradle.properties :** 

프로젝트 범위의 Gradle 설정을 구성할 수 있다. 

```java
COMPILE_SDK_VERSION=android-29
BUILD_TOOL_VERSION=29.0.3
MIN_SDK_VERSION=21
TARGET_SDK_VERSION=29
APPLICATION_ID=com.test.emhwang
...

```

**4-2. local.properties :** 

빌드 시스템의 로컬 환경을 구성한다.

```java
sdk.dir=C\:\\Users\\cncn6\\AppData\\Local\\Android\\Sdk
```


----------

**JCenter vs Maven Central**
------
Bintray와 Maven은 다양한 라이브러리 저장소를 운영하는 서비스이다.

그리고 JCenter는 Bintray에, Maven Central은 Maven에 속하는 자바 레파지토리 저장소이다.

jcenter()는 mavenCentral()의 상위 개념이라고 볼 수 있는데, 왜냐하면 안드로이드 스튜디오 이전 버전은 mavenCentral()을 사용했었지만

현재는 jcenter()를 사용하고 있다.

이는 jcenter()가 mavenCentral()보다 성능 및 메모리 사용면에서 우수하기 때문이다.

jcenter()는 https프로토콜을 사용하므로 높은 보안을 유지하는데, mavenCentral()은 http프로토콜을 사용하여 상대적으로 낮은 보안을 유지한다.


----------

**Dex 파일이란?**
------

Dex파일이란 JAVA 코드로 작성되어 컴파일 된 클래스 파일을 DX(Android DX Tool)도구를 사용해 변환한 파일이다. 

이 과정에서 JAVA 바이트 코드들은 Dalvik 바이트 코드로 변환되며, 여러 클래스 파일에 들어있는 중복된 코드들을 재사용하기 때문에

.JAR파일에 비해 크기가 줄어든다. 

즉, DVM(Dalvik Virtual Machine)을 위한 실행파일이다. 


----------

**JVM과 DVM의 차이점**
------

JVM과 DVM은 엄밀히 말하면 다르다. 

Android는 자바를 사용하지만, Android SDK로는 자바소스를 Dalvik 바이트코드로 직접 컴파일할 수 없다. 

따라서 Android SDK에 DX라는 툴을 이용하여 자바 바이트코드를 Dalvik 바이트코드로 변환한다. 

즉, 여러개의 .class 파일들을 DX툴을 이용하여 하나의 .dex 파일로 변환하는 것이다. 

.dex파일은 DVM(Dalvik Virtual Machine)을 위한 실행파일이며, 즉 Android는 JVM을 사용하지 않는다 라는 것이다.


----------

**MultiDex란?**
------

안드로이드 앱을 구성하는 코드는 컴파일되어 Dex파일로 만들어진다. 

이때 Dex 포맷의 제한으로 인해 하나의 Dex 파일에는 최대 65536개의 메소드 참조만 저장될 수 있다. 

하지만 일반적으로 큰 규모의 앱을 작성하다보면 이러한 메소드 제한을 훌쩍 뛰어넘게 된다.

그러한 경우 빌드시 Dex에 대한 에러 메시지가 표시된다.

이럴때 MultiDex를 사용하면 Dex파일을 여러개로 나누어 이러한 문제를 피할 수 있다. 

하지만 MultiDex를 남발하면 안된다. 

------

물론 MultiDex를 사용하면 큰 어려움없이 큰 규모의 앱을 빌드할 수 있다. 

하지만 빌드과정에선 앱 내에서 사용하는 클래스를 여러개의 Dex파일로 나눠야 하며, 이를 위해 별도의 분석 작업이 추가되므로 빌드 속도가 느려지게 된다.

또한, 실제로 앱에서 사용하지 않는 클래스들도 모두 포함하므로 APK의 용량도 늘어나게 된다. 

즉, 개발자와 사용자 모두에게 좋지 않은 영향을 준다. 

이때 Proguard를 사용하면 기본적으로 사용하지 않는 클래스들을 제거해주므로, 메소드의 갯수를 줄일 수 있다. 


----------

**Proguard란?**
------

Proguard는 코드 난독화를 통해 디컴파일시 본인의 코드가 노출되는 것을 방지하며, 또한 불필요한 메소드를 제거하여 MultiDex를 피할 수 있다. 

그런데 난독화를 수행한다는 것은 클래스 내 파일 이름이나 라인 넘버등의 정보도 모두 제거하므로 디버그가 어렵다. 

그래서 보통 Debug 빌드일땐 난독화를 하지 않는다. 


```java
    buildTypes {
        debug {
            // Proguard 비활성화
            minifyEnabled false
            // 기본 Proguard 설정
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
        release {
            // Proguard 비활성화
            minifyEnabled true
            // 기본 Proguard 설정
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
```  

build.gradle (:app) 에서 보면 위와 같이 Proguard 코드를 확인할 수 있다. Debug 빌드일땐 Proguard 비활성화를 하여 

난독화 및 라인이 지워지는 것을 방지하고, Release 빌드일땐 Proguard 활성화를 하여 난독화를 진행한다. 

만약 프로젝트 상에서 라이브러리를 사용하는 경우, 라이브러리에 대한 예외처리를 따로 해줘야 한다. 

보통 해당 라이브러리의 홈페이지에 보면 Proguard 설정하는 법이 나와 있다. (Ex. Retrofit2, Glide ..)

이렇게 Proguard를 적용할때는 먼저 사용중인 라이브러리가 잘 작동되는지 하나하나 확인해봐야 한다. 

라이브러리 자체에 Proguard 설정이 없다면 프로젝트가 난독화 되면서 제대로 동작하지 않을 수 있기 때문이다. 

Proguard 설정 규칙에 대한 자세한 내용은 아래 URL를 참고하면 된다.

참고 URL : https://black-jin0427.tistory.com/m/249



----------

**새로운 라이브러리를 추가하는 방법**
------

**01. jar파일일 경우 (간단한 방법)**

libs 폴더에 jar파일을 넣는다 -> 우클릭해서 Add As library를 누른다.

**02. jar파일인 경우**

Jar dependency를 누르고 해당 경로를 선택한 후 추가한다. (이 방법보다 보통 간단한 방법을 사용한다.)

**03. jar파일 또는 다른 라이브러리일 경우**

File > Project Structure를 누른다 -> Dependencies 탭을 누른 후, + 버튼을 누른다.

**04. jcenter에 있는 라이브러리인 경우**

Library dependency를 누르고 검색창에 해당 라이브러리 명을 검색한 후 ok를 눌러서 Gradle에 추가한다.



이클립스에서 안드로이드 스튜디오로 넘어오면서 라이브러리 추가를 Gradle을 이용하여 jcenter에 있는 라이브러리를 불러와 사용하게 되었다.

경로 URL : https://bintray.com/bintray/jcenter

기존에는 위 사이트에서 필요한 라이브러리를 검색한 후 Gradle 파일에 입력하곤 했는데, 안드로이드 스튜디오에선 File > Project Structure > Library dependency를 이용하여 편하게 라이브러리를 추가할 수 있다.



----------
**.jar형 라이브러리를 앱에 적용할 때**
------

Project 단위에서 app 밑에 있는 libs 폴더안에 jar 파일을 넣고, (libs폴더가 없으면 생성하기)

오른쪽 버튼으로 Add as 시킨다.

그러면 app/build.gradle에 있는 dependencies에 

implementation files('libs/SDK_ANDROID_APMS_11888.jar') 이런 형태로 적용이 된다.

그런데 이렇게 하나하나 적용하면 끝이 없으니까

implementation fileTree(include: ['*.jar'], dir: 'libs') // libs 폴더안에 있는 모든 .jar 파일 적용

이런 식으로 하나로 통합해 주는 것이 좋다.




----------
**새로운 라이브러리를 gradle에 추가했는데 Cause: unable to find valid certification path to requested target 에러가 나는 경우**
------

유효한 인증서가 없기 때문이다.

이럴 때는 Configure build를 눌러서 에러로그를 자세히 킨 다음

Caused by : org.gradle.api.resource.ResourceException : Could not get resource '...'라고 하이퍼링크가 있을 것이다.

(ex. Caused by: org.gradle.api.resources.ResourceException: Could not get resource 'https://maven.fabric.io/public/io/fabric/tools/gradle/1.25.1/gradle-1.25.1.pom'.) // Firebase Crashlytics 추가하려다 발생

이럴 땐 이 하이퍼링크를 눌러서 그 사이트로 이동한다. (Chrome을 사용한다.)

(ex. https://maven.fabric.io/public/io/fabric/tools/gradle/1.25.1/gradle-1.25.1.pom)

창이 뜨면 주소창 맨 왼쪽에 '자물쇠' 모양이 있는데 그것을 누른다. 그러면 '인증서 : (유효)'가 있는데 이것을 누른다.

또 창이 뜨면 '자세히 탭'을 누르고 '파일에 복사'를 누른다.

그 다음 인증서 내보내기 마법사 창이 뜨는데, 다음과 다음을 누른다.

내보낼 파일 창에서 파일 이름을 지정해줘야 하는데 마음대로 지정해준다. (ex. mavenFabric.cer)

여기서 확장자는 무조건 .cer이어야 한다.

다시 다음을 누르고 종료시킨다.

그러면 아까 파일 이름을 지정해줄때 지정해놓았던 경로에 인증서가 저장되어 있을 것이다.


이제 안드로이드 스튜디오로 돌아와서 File > Settings 를 눌러서 셋팅창을 켠 후, 

Tools > Server Certificates 또는, 검색창에 Certificates라고 검색한다.

여기서 상단에 Accepted certificates라는 구역이 있는데, 여기서 '+ 버튼'을 누른다. 


(Accepted certificates 구역은 이 구역에 있는 인증서들이 현재 안드로이드 스튜디오에서 유효한 상태에 있는 인증서들이다.)

'+ 버튼'을 눌러서 뜬 창에서 아까 전의 인증서 경로를 찾은 후, apply 시키고 ok 버튼을 누른다.

이를 통해서 성공적으로 유효한 인증서를 등록할 수 있다.








