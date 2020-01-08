---
layout: post
title: 유니티에서 Kotlin으로 안드로이드 플러그인 만들어 사용해 보기
---

[참고링크](https://qiita.com/manakam/items/b5108acc682ad71b5f30#unity-%E3%81%A7%E3%81%AE%E3%83%97%E3%83%A9%E3%82%B0%E3%82%A4%E3%83%B3%E5%91%BC%E3%81%B3%E5%87%BA%E3%81%97)

기존에 Java로 만들던 안드로이드 플러그인을 Kotlin으로 작성하고 .aar파일로 유니티에서 사용해보고 싶어졌다.

정보 검색 중 위의 페이지를 찾아서 한번 따라해 보았다. 작성하면서 세세한 부분의
설명은 적지 않으니 디테일 한 부분은 필요 시 마다 검색해보자.

### 개발환경

- Windows 10
- Unity 2017.2.0f3
- Android Studio 2.2.2

### .aar

진행하기 전에 .jar와 .aar의 차이점을 간단하게 찾아보니 기존은 .jar플러그인은 플러그인
파일과 별도로 AndroidManifest.xml를 같이 가져와야 사용 할 수 있었다.

그에 반해 .aar은 플러그인에서 사용하는 리소스와 리소스 Id를 담고 있는 R.xxx파일 등 플러그인에서 필요한 것을 모두 담고 있다. 물론 AndroidManifest도 포함되어 있다. 더 세부적인 내용을 알고 싶다면 구글에서 검색해보자.

### 프로젝트 생성

Start a New Android Studio Project 또는 File->New->New Project...를 통해 프로젝트를 생성한다.

<!--{:.text-center img}-->
![프로젝트 생성 1]({{ site.urlimg }}/assets/create-unity-android-plugin-with-kotlin/create_project_1.png "프로젝트 생성 1")<!--{: width="45%" }-->

![프로젝트 생성 2]({{ site.urlimg }}/assets/create-unity-android-plugin-with-kotlin/create_project_2.png "프로젝트 생성 2")

이어서 Add No Activity를 선택한다. 기타 SDK 버전 설정 항목은 구현하고자 하는 기능을 지원하는 버전 등 필요에 맞게 설정한다.

프로젝트가 생성 되면 기본적으로 있는 app은 놔두고 플러그인 제작 용도로 사용하기위한 모듈을 별도로 생성한다.

![모듈 생성 1]({{ site.urlimg }}/assets/create-unity-android-plugin-with-kotlin/create_module_1.png)

![모듈 생성 2]({{ site.urlimg }}/assets/create-unity-android-plugin-with-kotlin/create_module_2.png)

### Kotlin 환경 설정

Ctrl + Shift + A 또는 Help->Find Action에서 Plugins를 검색하여 플러그인 관리메뉴를 연다.

메뉴에서 Kotlin을 검색하여 설치한 뒤 Restart가 나오면 Android Studio를 재시작한다.

Kotlin을 설치 하였으니 이제 Gradle에 적용시키기 위해 Tools->Kotlin->Configure Kotlin in Project를 선택한다. Choose Configurator 창이 나오면 Android with Gradle
을 누르면 이어서 Configure Kotlin in Project 창이 나오는데 세팅되어 있는대로 진행을 하면 build.gradle에 내용이 작성된다.

gradle 파일이 변경되었으니 오른쪽위에 Sync Now가 나올텐데 눌러서 Sync를 진행 한다.

여기까지 진행하면 Android Studio에서 Kotlin을 사용할 수 있게 된다.

### 간단 플러그인 작성

클래스 작성 없이 단일 함수를 작성하여 사용하는 식으로 간단하게 구현.

이 경우 생성되는 코드 구조는 파일이름+Kt로 생성된다.

코드를 작성하기 전에 유니티 classes.jar를 가져와 참조를 시키자.

`유니티 설치 경로\Editor\Data\PlaybackEngines\AndroidPlayer\Variations\mono\Release\Classes\classes.jar`

해당 파일을 libs경로에 복사한 후 안드로이드 스튜디오에서 add를 하면 참조가 된다.

{% highlight kotlin %}
//fileName : AttachmentMail.kt
package mm.attachmentmail

import java.io.File
import android.content.Intent
import android.net.Uri
import com.unity3d.player.UnityPlayer

fun StartSelect(address : String, subject : String, body : String, mimeType : String, chooser : String, attachmentPath : String) {
    val intent = Intent()

    intent.setAction(Intent.ACTION_SEND)
    intent.setType(mimeType)
    intent.putExtra(Intent.EXTRA_EMAIL, arrayOf(address))
    intent.putExtra(Intent.EXTRA_SUBJECT, subject)
    intent.putExtra(Intent.EXTRA_TEXT, body)

    val file = File(attachmentPath)

    if (file.exists() && file.isFile()) {
        intent.putExtra(Intent.EXTRA_STREAM, Uri.fromFile(file))
    }

    UnityPlayer.currentActivity.getApplicationContext().startActivity(Intent.createChooser(intent, chooser))
}
{% endhighlight %}

{% highlight java %}
public final class AttachmentMailKt{
    public static final void StartSelect(){
        //code...
    }
}
{% endhighlight %}

이렇게 작성한 후 Gradle 탭에서 생성한 모듈이름\Tasks\build\assembleRelease를
실행하면 build\outputs\aar하위에 플러그인이 생성되는데
이대로 유니티에 임포트 한 후 빌드를 하면 에러가 하나 발생한다.

내용은 아까 참조 시킨 classes.jar가 중복되어 빌드가 진행이 되지
않게 되는데, 이를 해결하기 위해서는 안드로이드 스튜디오에서 classes.jar를
참조는 하지만 빌드 시에는 포함되지 않도록 세팅을 해주어야 한다.

모듈의 build.gradle을 열어 보면

```
dependencies {
    compile fileTree (dir : 'libs', include : [ '* .jar'])
}
```

이 부분에서 libs에 있는 .jar확장명을 가진 파일을 컴파일 하도록
하고 있는데, compile부분을

```
provided fileTree (dir : 'libs', include : [ '* .jar'])
```

provided를 변경하여 참조는 하되 빌드시에 포함되지 않도록 해주어야 한다.
포함되지 않은 경우에는 그만큼 결과물의 용량이 작기 때문에 확인 할 수 있다.
만약 이렇게 해도 포함되어 빌드 될 경우에는 아래 방법을 하나씩 해보면 될 것이다.

- menu\Build\clean Project 메뉴 실행
- build.gradle에 아래 내용 추가

```
// 이 부분은 증상해결에 영향이 있는지 정확히 확인되지 않음.
// 해결 방법 검색 중 나왔던 내용이라 별도로 확인해 봐야 한다.
configurations {
    provided
}
```

유니티에서 코틀린으로 작성한 플러그인을 동작시키기 위해서는 파일이 하나 더
필요한데 kotlin-runtime.jar이라는 녀석이 필요하다.
이 파일을 같이 유니티에 임포트 해주어야 플러그인이 동작 할 수 있다.
안드로이드 설치 위치\lib 하위에서 찾을 수 있다.

- [모듈이름-release].aar
- kotlin-runtime.jar

위의 두 파일을 유니티에 임포트하면 사용 할 준비는 끝난다.
특수 폴더의 제한은 이전 패치로 없어져서 편한 곳에 임포트 해도 되지만
그래도 별도 폴더로 관리하는 것이 편할 것 같다.

유니티에서 안드로이드기능을 호출하기 위해서는 AndroidJavaClass와 AndroidJavaObject를
통해 할 수 있는데 플러그인에서 static class로 만들었기 때문에 그에 맞게 호출하도록 한다.

위 두 클래스들은 사용 이후에 Dispose를 통해 해제해주어야 하기 때문에 되도록이면
using키워드를 통해 사용하는 것을 권장한다.

```csharp
using UnityEngine;

public static class Mailer
{
    private const string AddressCs = "qkrsogusl3@gmail.com";

    private const string Chooser = "우편물을 선택하세요.";
    private const string Subject = "문의 메일";
    private const string Body = "템플릿 글 \n\n듣고 싶은 것을 써주세요";

    private const string MimeType = "message/rfc822";

#if UNITY_EDITOR

#elif UNITY_ANDROID
    private const string ClassName = "com.qkrsogusl3.mylibrary.AttachmentMailKt";
    private const string MethodName = "StartSelect";
#endif

    public static void StartSelect(string attachmentPath)
    {
#if UNITY_EDITOR

#elif UNITY_ANDROID
        using (var ajc = new AndroidJavaClass(ClassName))
        {
            ajc.CallStatic(MethodName, AddressCs, Subject, Body, MimeType, Chooser, attachmentPath);
        }
#endif
    }
}
```

위 컴포넌트를 만들어 GameObject에 Attach한 후 빌드하여 실행해 보면 동작을 
확인 할 수 있다.

### 코드 저장소

[프로젝트 링크](https://github.com/qkrsogusl3/unity-kotlin-plugin.git)

참고링크를 보며 따라해 본 프로젝트의 링크.
