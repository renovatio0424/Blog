---
title: "[Android] Localization"

categories:
  - Android

tags:
  - Localization
  - Resource

author: Reno

toc: true
toc_label: "차례"
toc_icon: "list"

comments: true
---
최근에 번역을 도와주는 툴을 옮기면서 string을 정리하게 되었다.

정리하면서 알게 된 사실들을 기록하고 공유하려한다.

# **Localization 이란?**


> **Localization**
>
> 일을 실제 진행하는 곳의 특성에 맞추는 것을 말한다.
>
> [나무위키](https://namu.wiki/w/%ED%98%84%EC%A7%80%ED%99%94)

글로벌 서비스를 하게 되면 여러 파편화된 상황들이 많다.

예를 들어 각 국가별 string도 번역해야되고

각 국가별 사용하는 Device들도 천차만별 ..

이걸 어떻게 대응할것인가 ... ?

이에 대한 대응을 'Localization' 이다

# **Android Resource 접근 원리**

그렇다면 각 국가에 맞게, 각 디바이스에 맞게 어플을 새로 만든다??

너무나도 비효율적인 일이다.

왜냐하면 공통적으로 가지고 있는 것들이 있기 때문이다.

예를 들어 기능 같은 경우 지역별로 다른 경우도 있지만 같은 경우가 더 많다.

그러면 굳이 같은 기능인데 문구가 다르다고 기능을 복붙해야할 될까??

지원하는 국가, 디바이스가 많아지면 유지 보수는???

이렇게 하면 아무도 글로벌 서비스를 하지 않을 것이다.

그러면 실제 안드로이드는 어떻게 되어 있을까???

번역문구들은 string 리소스들이기 때문에 이들을 어떻게 제공하는지 확인해보았다.

[안드로이드 개발 공식 문서](https://developer.android.com/guide/topics/resources/providing-resources.html) 를 확인해보면

안드로이드는 res 디렉토리 안에 리소스 타입에 따라 디렉토리들을 가지고 있다.

이 타입 디렉토리를 어떻게 지정하냐에 따라 다르게 지원이 되는 것이다.

![스크린샷](/assets/images/post_image/resource_directory.png)

위 스크린샷을 보면 mipmap은 해상도에 따라 **'/타입-한정자/'** 의 형태로 디렉토리가 생성된것을 볼 수 있다.

이 한정자들이 적용되는 순서들이 정해져 있고, 한정자의 종류도 다양하다

# **리소스 참조 방법 2가지**

1. 코드로 접근하기
```java
R.string.hello
```

2. XML로 접근하기
```xml
@string/hello
```

# **Alias Resource (별칭 리소스)**
그리고 서비스에 맞추어서 **별칭 리소스** 를 구성하여 적용하는 것도 가능하다.

예를 들어 중국, 홍콩, 대만에서는 다른 앱아이콘을 사용하고 싶은 경우

중국, 홍콩, 대만 디렉토리에 아이콘을 각각 만들어서 넣는 것이 아니라

res/drawable/ 디렉토리에 해당 icon_cn 을 넣고

res/drawable-zh-rCN, res/drawable-zh-rHK, res/drawable-zh-TW 에

```xml
<?xml version="1.0" encoding="utf-8"?>
<bitmap xmlns:android="http://schemas.android.com/apk/res/android"
  android:src="@drawable/icon_cn" />
```

이 파일을 넣어두면 끝난다.

# **3줄 요약**

1. 안드로이드는 **Qualifier Rule** 을 통해 localization을 대응한다.
2. 리소스에 참조하는 방식은 **code, xml** 2가지 통해 접근한다.
3. **별칭 리소스** 를 통해 기본적으로 정의되지 않은 환경에 대응이 가능하다
