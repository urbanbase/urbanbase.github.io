---
layout: post
title: "모바일에서 3D Viewer 구축하기_안드로이드 웹뷰 편"
date: 2019-09-29 10:00:00
categories: dev
tags: 3D Application
cover: "/assets/9_webview/9_thumbnail.png"

---
<img src="/assets/9_webview/9_thumbnail.png" width="" alt="Webview">
<br>

안녕하세요. 모바일 개발자 김부길입니다.<br> 
어반베이스에서는 AR Viewer SDK, Sketch Viewer 개발을 담당하고 있습니다.

<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//tHKMPEJlmEM' frameborder='0' allowfullscreen></iframe></div>

<br>
아직까지 대부분의 모바일 콘텐츠는 이미지나 영상으로 이루어져 있습니다. 그만큼 비게임 분야에서 모바일로 3D 콘텐츠를 보는 게 아직 흔한 일은 아니죠. 하지만 분명한 건, 모바일 콘텐츠의 패러다임이 2D에서 3D로 이동하고 있고, 이미 기술적으로는 모바일 기기에서 PC 수준의 3D 콘텐츠를 구현하는 것도 가능한 시대가 됐다는 겁니다. 

게임 개발자가 아닌 일반 앱 서비스 개발자들에게 모바일 3D 기술은 새롭게 개척해야 하는 분야입니다. 저 또한 어떻게 하면 유저들이 빠르고 편하게 3D 콘텐츠를 이용하는 앱 환경을 만들 수 있을까 많은 고민을 하는데요. 오늘은 제가 시도했던 방식들 중, 안드로이드 앱에서 3D 콘텐츠를 보여주기에 효과적인 WebView 방식을 소개해보고자 합니다. **Android WebView는 OpenGL 오픈소스 라이브러리나 Unity를 활용하는 것보다 기술 난이도가 낮아 상대적으로 쉽게 접근**할 수 있습니다. 
<br>
<br>

# Android WebView
WebView는 안드로이드 4.4 킷캣부터 <a href="http://www.chromium.org/Home" target="_blank" style="color: #0366d6;"> 크로미엄 프로젝트</a> 기반으로 새롭게 구현되었고, HTML5, CSS3, 자바스크립트 등 웹 표준 기술들을 지원합니다. 안드로이드 크롬 브라우저 버전 30 이후에서 지원하던 HTML5 기능들을 대부분 지원하게 됐다고 보시면 됩니다. V8 자바스크립트 엔진으로 속도가 향상되었고, 웹페이지를 개발할 수 있도록 크롬 개발자 도구를 통한 <a href="https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews?hl=ko" target="_blank" style="color: #0366d6;"> 리모트 디버깅</a>이 가능합니다.

BUT !! <br>
WebGL은 Lollipop(5.0)의 Webview 36 버전부터 지원이 됩니다. (Kitkat은 Webview 버전 30)

<img width='600px' src='/assets/9_webview/android_platform_market_share.png'/>

2019년 5월 기준, 안드로이드 Platform Version 점유율을 보면 KitKat 이하의 점유율은 매우 낮습니다. 이제는 WebGL을 지원하는 WebView를 맘놓고 사용할 수 있습니다.
<br>
<br>

# Android Webview의 속도 문제
WebView는 안드로이드 View를 상속 받은 컴포넌트이고, 안드로이드의 LifeCycle 안에서 작동합니다. 안드로이드의 LifeCycle을 타면서 onDraw() 함수에서 View를 그립니다. 아래 그림은 SurfaceView를 이용한 Chrome Browser의 구조인데요. WebView와 비교되는 Chrome Browser의 구조를 보면 이해가 좀 더 쉽습니다. 

![surfaceview](/assets/9_webview/surfaceview.png)
<br>**Source: https://sozu.tistory.com/35*

Activity와 별개로 다른 스레드가 SurfaceView에 그림을 그리고, Window를 통과해 View를 그려주는 방식입니다. 여기서 이런 의문점이 들 수 있습니다.

“왜 onDraw() 함수를 쓰는 WebView가 SurfaceView를 쓰는 Chorme 브라우저보다 느릴까?”
<br>
<br>
<img width='700px' src='/assets/9_webview/Comparison_double_triple_buffering.svg'/>

**Source: https://en.wikipedia.org/wiki/Multiple_buffering#/media/File:Comparison_double_triple_buffering.svg*

단순하게 View는 Single Buffering, SurfaceView는 Double Buffering이라고 보시면 되는데요. 각각을 비유하자면, 도화지 1장에 그리고 지우고를 반복하는 경우와 도화지 2장 중 1장에는 그리고, 다 그려지면 다른 도화지에 최종 반영하는 것의 차이라고 볼 수 있습니다. 

여기서 핵심은, SurfaceView를 사용한 Chrome 브라우저는 View를 그릴때는 그리기만 하고 읽을 때는 읽기만 하여, View 출력의 효율을 높인다는 것인데요. 이러한 방식은 깜빡임을 방지할 수 있기 때문에 게임이나 카메라 Viewing에 사용되고 있습니다. (안드로이드에서 커스텀 카메라뷰나 AR Core를 이용한 AR 앱을 만들 때 SurfaceView를 많이 보게 될겁니다.)

그럼에도! WebView는 chromium의 지원 아래 계속해서 발전하고 있습니다.
<br>
<br>

# WebView 기능 활용해 3D Viewer 만들기
three.js 라는 그래픽 처리 자바스크립트 오픈소스를 활용하여 간단한 3D Viewer 앱을 만들어 보겠습니다.

## 1) 3D 모델 준비 
3D 모델을 직접 제작해도 되고, <a href="https://sketchfab.com/features/download" target="_blank" style="color: #0366d6;"> Sketchfab</a>이나 <a href="https://free3d.com/3d-models/" target="_blank" style="color: #0366d6;"> free3d</a>같은 사이트에서 무료로 3D 콘텐츠를 받을 수 있습니다. 유료, 상업 불가 라이센스도 있으니 잘 보고 다운받아 사용하시면 됩니다. 그리고 제작자에게 감사의 박수를!

## 2) 3D Viewer 기능을 하는 HTML 파일 만들기

**사용한 오픈소스 라이브러리:
https://github.com/hujiulong/vue-3d-model*<br>

위의 라이브러리는 three.js의 기능을 vue.js로 감싸서, HTML Body의 태그 입력만으로도 쉽게 3D Viewer를 만들도록 도와줍니다. 아래 첨부한 HTML 파일은 vue-3d-model 라이브러리를 응용하여 ProgressBar 및 반응형 대응이 되도록 응용하였습니다.

```html
<!DOCTYPE html>
 <html>

 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script src="https://unpkg.com/vue-3d-model/dist/vue-3d-model.umd.js"></script>
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>

  <style>
    /* ProgressBar 관련 */
    .loader {
     position: absolute;
     border: 8px solid #f3f3f3;
     border-radius: 50%;
     border-top: 8px solid #0CAEC2;
     width: 60px;
     height: 60px;
     margin-top: auto;
     margin-bottom: auto;
     vertical-align: middle;
     left: 0;
     right: 0;
     top: 0;
     bottom: 0;
     margin-left: auto;
     margin-right: auto;
     -webkit-animation: spin 1s linear infinite;
     /* Safari */
     animation: spin 1s linear infinite;
    }
 
    @-webkit-keyframes spin {
     0% {-webkit-transform: rotate(0deg);}
     100% {-webkit-transform: rotate(360deg);}
    }
 
    @keyframes spin {
     0% {transform: rotate(0deg);}
     100% {transform: rotate(360deg);}
    }
   </style>
 </head>

 <body style="background-color:#c8c8c8; margin:0px;  ">
  <div class="loader_wrapper" style="display:visible;">
   <div class="loader"></div>

   <div id="app" class="app">
    <model-gltf class="model-gltf" :background-color="bgColor" :background-alpha="bgAlpha" :scale="Scale"
     @on-load="onLoad" @on-progress="onProgress" :width=UbWindow.width :height=UbWindow.height :position="Position"
     :rotation="Rotation" src="./model/pony_cartoon/scene.gltf"></model-gltf>
   </div>
  </div>

  <script>
   new Vue({
    el: '#app',
    data: {
     UbWindow: {
      width: window.innerWidth,
      height: window.innerHeight
     },
     bgColor: '#c8c8c8',
     bgAlpha: 1,
     Scale: {
      x: 1,
      y: 1,
      z: 1
     },
     Position: {
      x: 0,
      y: 0,
      z: 0
     }
    },
    methods: {
     onLoad(event) {
      $('.loader').css('display', 'none');
      $('.app').fadeIn(1000);
     },
     onProgress(event) {
      console.log(event);
     }
    }
   });
  </script>
 </body>
```

위의 HTML 파일을 서버에 올려서 사용해도 되고, Android 로컬 저장소인 Assets 폴더에 넣어서 사용해도 됩니다. 저는 후자를 택했습니다. script 태그의 자바스크립트 파일도 다운받은 후 안드로이드 로컬 저장소에 넣어서 인터넷 접속이 안되더라도 앱이 구동되도록 하였습니다.

## 3) 위에서 만든 3D 뷰어 HTML 파일 Android WebView에 올리기

자바 액티비티에 WebView를 정의하고 url을 호출합니다.

```java
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    wv_main.webChromeClient = WebChromeClient()
    wv_main.webViewClient = WebViewClient()

    if (Build.VERSION.SDK_INT >= 19) {
        wv_main.setLayerType(View.LAYER_TYPE_HARDWARE, null)
    }
    else {
        wv_main.setLayerType(View.LAYER_TYPE_SOFTWARE, null)
    } 
    // 하드웨어 가속을 통해서 성능을 올린다. 기기에 따라서 차이는 있겠지만 평균적으로 4.4 이상이라면 가속이 유리하다.

    val settings = wv_main.settings
    settings.setJavaScriptEnabled(true)
    settings.cacheMode = WebSettings.LOAD_CACHE_ELSE_NETWORK
    settings.domStorageEnabled = true
    settings.allowUniversalAccessFromFileURLs = true
    settings.allowFileAccess = true
    wv_main.loadUrl("file:///android_asset/3d_viewer.html")
}
```

<br>

<table>
<tr>
    <th>함수명</th>
    <th>기능</th>
</tr>
        <tr>
        <td> setJavaScriptEnabled </td>
        <td> 자바스크립트 활성화  </td>
        </tr>
        <tr>
        <td> setAppCacheEnabled </td>
        <td> 앱 내부 캐시 사용 여부  </td>
        </tr>
        <tr>
        <td>setCacheMode</td>
        <td>
        웹 캐시 설정 모드<br>
        1. LOAD_CACHE_ELSE_NETWORK: 기간이 만료돼 캐시를 사용할 수 없을 경우 네트워크를 사용합니다. <br>
        2. LOAD_CACHE_ONLY: 네트워크를 사용하지 않고 캐시를 불러옵니다. <br>
        3. LOAD_DEFAULT: 기본적인 모드로 캐시를 사용하고 만료된 경우 네트워크를 사용해 로드합니다.<br>
        4. LOAD_NO_CACHE: 캐시모드를 사용하지 않고 네트워크를 통해서만 호출합니다.  
        </td>
        </tr>
        <tr>
        <td> setDomStorageEnabled </td>
        <td> Web 저장공간 사용 여부 (Local Storage)<br>
        '하루동안 보지 않기' 기능에 사용됩니다.
        </td>
        </tr>
        <tr>
        <td>allowUniversalAccessFromFileURLs</td>
        <td>
        - asset 폴더에 html 파일과, 3D 모델 파일들을 넣어놓았다면 해당 함수를 호출해야 에러가 나지 않습니다. 
        <br>
        <br>
        - http 문서에서 참조하는 태그(img, link, script)를 사용할 경우, W3C에서 권고하는 동일 출처 정책(Same-Origin Policy)을 따라야 합니다. <br>
        즉, 동일 도메인 내에서만 허용되는 것을 권고하기 때문에 asset 폴더의 html이 다른 폴더의 파일을 참조하면 권고 사항을 지키지 않았다고 에러가 나죠.
        <br>
        <br>
        - 참고로 http 문서에서 header(Access-Control-Allow-Origin: * 를 보신적이 있다면, 이게 바로 동일 출처 정책을 풀어주는 것입니다.
        </td>
        </tr>
        <tr>
        <td>loadUrl</td>
        <td>url 로드</td>
        </tr>
</table>

<br>

위의 함수로도 3D Viewer를 만드는 데 충분하지만, WebView의 다른 유용한 함수들도 정리해 보겠습니다.

<br>

<table>
<tr>
<th>함수명</th>
<th>기능</th>
</tr>
<tr>
<td>setWebViewClient()</td>
<td>WebView의 함수로, 각종 알림 및 요청을 받을 수 있습니다.</td>
</tr>

<tr>
<td>setWebChromeClient</td>
<td>WebView의 함수로, 자바스크립트 다이얼로그, favicon 등의 이벤트를 받을 수 있습니다.</td>
</tr>

<tr>
<td>setLoadWithOverviewMode</td>
<td>WebSettings의 함수로, 컨텐츠가 웹뷰보다 클 경우 스크린 크기에 맞게 조정합니다.</td>
</tr>

<tr>
<td>setUseWideViewPort(boolean)</td>
<td>WebSettings의 함수로, html 뷰 포트 메타태그를 지원합니다.</td>
</tr>
</table>

<br>
덧붙이자면, 간단한 HTML 페이지인 경우 setWebViewClient() 만으로 충분하지만, 자바스크립트 다이얼로그, favicon 등의 기능이 들어간 페이지라면 setWebChromeClient() 로 이벤트를 받을 수 있습니다.
<br>

<img width='200px' src='/assets/9_webview/webview.gif'>
<center><WebView를 이용한 3D Viewer></center>

<br>

# Android WebView 처리 속도가 만족스럽지 않다면
Chrome Custom Tab을 사용해 보시기를 추천합니다. Chrome Custom Tab을 통해 앱 안에서 동작하듯이 Chrome 브라우저를 사용할 수 있습니다.

1. 툴바 색상
2. Enter, Exit animation
3. 툴바의 커스텀 액션
4. overflow menu (삼땡 버튼) & 하단 툴바

**Source: https://developer.chrome.com/multidevice/android/customtabs*
<br>

위와 같은 사항들을 어느 정도 커스터마이징 할 수 있습니다만, WebView 처럼 완전한 커스터마이징은 불가능합니다. 위에서 설명 드렸듯이, Chrome 브라우저는 surfaceView를 활용한 비동기적인 화면 그리기로 속도가 매우 빠른 편이기 때문에 3D 콘텐츠를 그리는 작업을 쉽게 보여주기 위해서 Chrome Custom Tab을 이용할 수 있습니다.

앱 상단바의 색상을 맞출 수 있고, 기본적인 뒤로가기 버튼을 커스터마이징 할 수 있고, 주소 검색란을 숨길 수 있습니다. 완벽한 커스터마이징은 되지 않지만, 앱 내에서 동작한다는 느낌을 줍니다.

단, 고려해야 할 부분도 있습니다.

1. 현재 url 정보를 숨길 수 없기 때문에 완성도가 떨어져 보이는 아쉬움이 있다는 것과
2. 아이콘은 24dp 이하의 크기만 사용 가능하다는 점입니다.

**Source: https://sirubomber.tistory.com/38*

<img width='200px' src='/assets/9_webview/chromeclient.gif'> 
<center> <Chrome Custom Tab을 이용한 3D Viewer> </center>

비교적 가벼운 3D 콘텐츠를 사용했기 때문에 WebView와 Chrome Custom Tab의 모델 로딩 속도 차이가 그리 많이 나지는 않아 보이기는 합니다. 하지만 로드가 많이 걸리는 3D 파일을 사용하신다면, WebView와 Chrome Custom Tab의 속도 차이가 많이 나는 걸 체감하실 수 있습니다.

