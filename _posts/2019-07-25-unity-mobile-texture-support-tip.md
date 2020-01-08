---
layout: post
date: 2019-07-25
---

#### Sprite Atlas Late Binding

- [Sprite Atlas Document](https://docs.unity3d.com/Manual/class-SpriteAtlas.html)

```csharp

U2D.SpriteAtlasManager.atlasRequested += (string tag, Action<SpriteAtlas> callback) =>{
    check SystemInfo.maxTextureSize

    if 2048
        // 일단 현재 세팅 그대로
        truecolor rgba 32bit
    else if 4096 (es3)
        max 4096
        astc rgba 4x4

    Late Binding

    var sa = Resources.Load<SpriteAtlas>(tag);
    callback(sa);
};

```

#### 4096 texture support target

- [iOS 7.0](https://ko.wikipedia.org/wiki/OpenGL_ES#OpenGL_ES_3.0)

- [ios 버전 점유율](https://developer.apple.com/kr/support/app-store/)

- [android 4.3 api 18](https://developer.android.com/guide/topics/graphics/opengl)
- [Opengl es 버전 별 디바이스](https://www.buildbox.com/forum/index.php?threads/atlasses-size-2048-or-4096.5711/)

#### tip posting

- [유니티 텍스쳐](https://mgun.tistory.com/2056)
- [안드로이드 ETC2 format 활용](https://illu.tistory.com/1080)
- [iOS PVRTC vs ASTC (es3)](https://gametorrahod.com/pvrtc-vs-astc-texture-compression-on-an-ios-device/)
- [Unity Texture Compression 1 Start](https://hrmrzizon.github.io/2017/03/11/unity-texture-compression-1-start/)
- [Max Texture Size Android](https://gamedev.stackexchange.com/questions/49963/max-texture-size-android-which-settings-for-2048x2048)
- [Unity Sprite Atlas Late Binding](http://tsubakit1.hateblo.jp/entry/2018/12/14/221116)
- [mobile chipset armv7s, armv7](http://kyejusung.com/2015/10/armv7s-armv7-arm64%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90/)
- [64bit ARM은 과연 의미가 있는가 (내용 추가, 스압 주의)](https://www.clien.net/service/board/lecture/5006904)
- [이미지 압축 아이콘 샘플](https://realfavicongenerator.net/blog/wp-content/uploads/2014/04/screenshot.png)
- [Texture Compression Crunched](https://forum.unity.com/threads/questions-about-compression-formats-etc2-crunch-etc.511560/)

#### watch

- [tsubakit - unity graphics blog](http://tsubakit1.hateblo.jp/)
