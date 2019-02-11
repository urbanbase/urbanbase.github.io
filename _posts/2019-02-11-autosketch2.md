---
layout: post
title:  "TEST"
date:   2019-02-02 08:43:59
categories: tech
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---

<br>

# Auto Sketch
Auto sketch는 세계 유일의 실시간 도면 자동 변환 서비스입니다.

<br>

어반베이스는 특허받은 2D 도면의 3D 도면 변환 기술로 세계 최대의 3D 공간 DB를 구축했습니다. Auto desk CAD, SketchUp에 플러그인을 제공하여 확장성을 넓혀가고, Live sketch 기술을 고도화하여 사용자가 직접 그린 도면을 자유롭게 2D 또는 3D로 변환할 수 있는 서비스를 예정 중입니다.

<br>
<br>

Auto Sketch Demo <br>
[![Auto Sketch Demo](https://img.youtube.com/vi/sHmokPYTp2Q/0.jpg)](https://youtu.be/sHmokPYTp2Q)
<Br>
<br>
<br>
## 기술 설명
기존 목업을 실물로 제작하여 프로젝트를 진행할 경우, 목업 제작을 위한 **비용∙시간∙노동력** 등이 소모됩니다. <br>
<br>
하지만 `Auto Sketch`를 사용할 경우, 단순한 도면 파일 및 이미지 파일로도 실측 면적 기반의 건축물을 확인할 수 있습니다. 또한, 도면 이미지를 활용하여 **다양한 3D 확장자(\*.obj, \*.dae 등)의 3D 모델링 파일** 로 변환할 수 있습니다. <br>
`2D-3D Converting` 기술로 인하여 직접 3D 모델링 파일을 제작하거나, 2D CAD 도면 (*.dwg) 등을 공유하지 않아도 시각적으로 바로 확인할 수 있습니다. <br>
실물 모형 및 도면 파일은, 보안, 모형 및 파일 손상 등의 문제가 발생할 수 있지만 `Auto Sketch` 는 인터넷과 jpg 파일만 있으면 언제 어디서나 **3D 모델링 결과를 확인 할 수 있습니다.** <br>
<br>
이로써 **파일 용량 감소, 보안상의 문제 해결, 비용 절감, 편의성 증대**가 가능해집니다. <br>
<br>
### 요약
- 도면 또는 정형화된 이미지의 3D 도면 변환 엔진 개발
- AutoCAD, SketchUp 등 설계 프로그램에 설치할 수 있는 라이브러리, 모듈화, 플러그인 등 확장 프로그램화 <br>
<Br>
<Br>
## 프로세스
[![before]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (before).jpg)]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (before).jpg)
[![after]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (after).png)]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (after).png)

<br>
<Br>
기존에는 3D로 변환 시 치수 수정, 영역 분리, 개별 모델링 생성, 예외 처리 등 다소 복잡한 처리 과정을 거쳐야 했습니다. <br />
하지만 이 과정에서 `Auto Sketch`를 활용할 경우, 사용자가 이미지 파일을 **Drag and Drop** 하여 바로 결과를 확인할 수 있습니다. <br />
<br>
`Auto Sketch` 엔진은 이러한 복잡하고 불필요한 조작을 제거하여 작업 처리 시간 단축되는 장점이 있습니다.<br />또한, 각 구역의 특징을 분석하여 바닥재 및 벽재 등을 기본적으로 분석하여 제공해주며, `Computer Vision`, `Machine Learning`, `Auto Sketch` 알고리즘을 통하여 유의미한 의미를 가지는 3D 모델링 파일을 제공할 수 있습니다. <br>
<br>
<br>
## 응용분야
### 목업 제작
`Auto Sketch`를 이용하여 실물 목업 제작 시 보여질 모습을 3D로 미리 확인해 볼 수 있습니다.<br>
실물로 제작하지 않아도 치수 및 기호 오류 등을 확인할 수 있습니다. 또한 본인이 제작한 도면을 확인한 후 보완 및 수정이 용이 해 집니다.
<br>
### 응용 프로그램 빌드, 실행 및 테스트
`Auto Sketch`에서 도면 이미지를 불러와 `Computer Vision`, `Machine Learning` 등을 통하여 3D 모델링 파일로 불러올 수 있습니다.<br>
**저용량 이미지 파일 (.jpg, .png, .bmp) 등의 도면 이미지**만 입력하여 3D 모델링 결과를 손쉽게 확인할 수 있습니다.
### 실시간 다자간 온라인 화상 회의
`Auto Sketch`를 통해 실시간으로 도면 이미지를 3D 모델링 파일로 불러올 수 있습니다. 온라인 화상 회의를 통하여 공간적 한계가 사라지고 실물 목업 운반 등의 문제점을 해결 할 수 있습니다.<br>
또한, 해당 응용 프로그램을 Web으로 구현하여 도면 이미지 파일만 보유하여도 **3D 모델링 파일을 시각적으로 확인** 할 수 있습니다.<br>
<br>
<br>
<br>


--------
<h3>
`Auto Sketch` 기술을 보유한 어반베이스는 3D 공간데이터 플랫폼입니다. <br>
전 세계의 모든 공간정보를 하나의 플랫폼 안에서 자유롭게 활용할 수 있도록 도면 변환 기술 및 AR, VR 뷰어 기술이 담긴 API와 ADK를 제공하고 있습니다.<br>
<br>
Auto Sketch : API, Engine <br>
<Br>
어반베이스 개발자 사이트 [자세히 보기][Urbanbase-dev]
</h3>
[Urbanbase-dev]: https://developer.urbanbase.com

<Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br>

- 기호 표시법
- 기호 표시법
    - 기호 표시법 2
    - 기호 표시법 2
        - 기호 표시법 3
        - 기호 표시법 3
    - 기호 표시법 2


<br>
엔터
<br>

*기울임*입니다.

**굵게**입니다.

<u>밑줄</u>입니다.

`하이라이트`입니다.

# 제목 1 
## 제목 2
### 제목 3
#### 제목 4
##### 제목 5
###### 제목 6

그림 넣는 법
[![railroad]({{ site.images | relative_url }}/rails.jpg)]({{ site.images | relative_url }}/rails.jpg)
[![그림의_제목]({{ site.images | relative_url }}/tech/studio_auto sketch (after).jpg경로와확장자)]({{ site.images | relative_url }}/경로와확장자)


코드 넣어보기 with js

{% highlight js %}
var a = 2;

{
    let a = 3;
    console.log( a );   // 3
}

console.log( a );       // 2
{% endhighlight %}



코드 넣어보기 with python

{% highlight python %}

import os

a = 3
b = 2

print (a + b)

{% endhighlight %}



링크 넣어보기 [클릭][Urbanbase]

[Urbanbase]: http://www.urbanbase.com


테이블 만들기

| 제목1  | 제목2 | 제목3 | 제목4 | 제목5 |
|--------|-------|-------|-------|-------|
| 1      | 2     | 3     | 4     | 5     |
| 6<br>7 | 8     | 9     | 10    | 11    |
|  ㅁㄴㅇㄹ       || ㅁ     | ㄴ    | ㅇ     |












=======
---
layout: post
title:  "TEST"
date:   2019-02-02 08:43:59
categories: tech
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---

<br>

# Auto Sketch
Auto sketch는 세계 유일의 실시간 도면 자동 변환 서비스입니다.

<br>

어반베이스는 특허받은 2D 도면의 3D 도면 변환 기술로 세계 최대의 3D 공간 DB를 구축했습니다. Auto desk CAD, SketchUp에 플러그인을 제공하여 확장성을 넓혀가고, Live sketch 기술을 고도화하여 사용자가 직접 그린 도면을 자유롭게 2D 또는 3D로 변환할 수 있는 서비스를 예정 중입니다.

<br>
<br>

Auto Sketch Demo <br>
[![Auto Sketch Demo](https://img.youtube.com/vi/sHmokPYTp2Q/0.jpg)](https://youtu.be/sHmokPYTp2Q)
<Br>
<br>
<br>
## 기술 설명
기존 목업을 실물로 제작하여 프로젝트를 진행할 경우, 목업 제작을 위한 **비용∙시간∙노동력** 등이 소모됩니다. <br>
<br>
하지만 `Auto Sketch`를 사용할 경우, 단순한 도면 파일 및 이미지 파일로도 실측 면적 기반의 건축물을 확인할 수 있습니다. 또한, 도면 이미지를 활용하여 **다양한 3D 확장자(\*.obj, \*.dae 등)의 3D 모델링 파일** 로 변환할 수 있습니다. <br>
`2D-3D Converting` 기술로 인하여 직접 3D 모델링 파일을 제작하거나, 2D CAD 도면 (*.dwg) 등을 공유하지 않아도 시각적으로 바로 확인할 수 있습니다. <br>
실물 모형 및 도면 파일은, 보안, 모형 및 파일 손상 등의 문제가 발생할 수 있지만 `Auto Sketch` 는 인터넷과 jpg 파일만 있으면 언제 어디서나 **3D 모델링 결과를 확인 할 수 있습니다.** <br>
<br>
이로써 **파일 용량 감소, 보안상의 문제 해결, 비용 절감, 편의성 증대**가 가능해집니다. <br>
<br>
### 요약
- 도면 또는 정형화된 이미지의 3D 도면 변환 엔진 개발
- AutoCAD, SketchUp 등 설계 프로그램에 설치할 수 있는 라이브러리, 모듈화, 플러그인 등 확장 프로그램화 <br>
<Br>
<Br>
## 프로세스
[![before]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (before).jpg)]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (before).jpg)
[![after]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (after).png)]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (after).png)

<br>
<Br>
기존에는 3D로 변환 시 치수 수정, 영역 분리, 개별 모델링 생성, 예외 처리 등 다소 복잡한 처리 과정을 거쳐야 했습니다. <br />
하지만 이 과정에서 `Auto Sketch`를 활용할 경우, 사용자가 이미지 파일을 **Drag and Drop** 하여 바로 결과를 확인할 수 있습니다. <br />
<br>
`Auto Sketch` 엔진은 이러한 복잡하고 불필요한 조작을 제거하여 작업 처리 시간 단축되는 장점이 있습니다.<br />또한, 각 구역의 특징을 분석하여 바닥재 및 벽재 등을 기본적으로 분석하여 제공해주며, `Computer Vision`, `Machine Learning`, `Auto Sketch` 알고리즘을 통하여 유의미한 의미를 가지는 3D 모델링 파일을 제공할 수 있습니다. <br>
<br>
<br>
## 응용분야
### 목업 제작
`Auto Sketch`를 이용하여 실물 목업 제작 시 보여질 모습을 3D로 미리 확인해 볼 수 있습니다.<br>
실물로 제작하지 않아도 치수 및 기호 오류 등을 확인할 수 있습니다. 또한 본인이 제작한 도면을 확인한 후 보완 및 수정이 용이 해 집니다.
<br>
### 응용 프로그램 빌드, 실행 및 테스트
`Auto Sketch`에서 도면 이미지를 불러와 `Computer Vision`, `Machine Learning` 등을 통하여 3D 모델링 파일로 불러올 수 있습니다.<br>
**저용량 이미지 파일 (.jpg, .png, .bmp) 등의 도면 이미지**만 입력하여 3D 모델링 결과를 손쉽게 확인할 수 있습니다.
### 실시간 다자간 온라인 화상 회의
`Auto Sketch`를 통해 실시간으로 도면 이미지를 3D 모델링 파일로 불러올 수 있습니다. 온라인 화상 회의를 통하여 공간적 한계가 사라지고 실물 목업 운반 등의 문제점을 해결 할 수 있습니다.<br>
또한, 해당 응용 프로그램을 Web으로 구현하여 도면 이미지 파일만 보유하여도 **3D 모델링 파일을 시각적으로 확인** 할 수 있습니다.<br>
<br>
<br>
<br>


--------
<h3>
`Auto Sketch` 기술을 보유한 어반베이스는 3D 공간데이터 플랫폼입니다. <br>
전 세계의 모든 공간정보를 하나의 플랫폼 안에서 자유롭게 활용할 수 있도록 도면 변환 기술 및 AR, VR 뷰어 기술이 담긴 API와 ADK를 제공하고 있습니다.<br>
<br>
Auto Sketch : API, Engine <br>
<Br>
어반베이스 개발자 사이트 [자세히 보기][Urbanbase-dev]
</h3>
[Urbanbase-dev]: https://developer.urbanbase.com

<Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br>

- 기호 표시법
- 기호 표시법
    - 기호 표시법 2
    - 기호 표시법 2
        - 기호 표시법 3
        - 기호 표시법 3
    - 기호 표시법 2


<br>
엔터
<br>

*기울임*입니다.

**굵게**입니다.

<u>밑줄</u>입니다.

`하이라이트`입니다.

# 제목 1 
## 제목 2
### 제목 3
#### 제목 4
##### 제목 5
###### 제목 6

그림 넣는 법
[![railroad]({{ site.images | relative_url }}/rails.jpg)]({{ site.images | relative_url }}/rails.jpg)
[![그림의_제목]({{ site.images | relative_url }}/tech/studio_auto sketch (after).jpg경로와확장자)]({{ site.images | relative_url }}/경로와확장자)


코드 넣어보기 with js

{% highlight js %}
var a = 2;

{
    let a = 3;
    console.log( a );   // 3
}

console.log( a );       // 2
{% endhighlight %}



코드 넣어보기 with python

{% highlight python %}

import os

a = 3
b = 2

print (a + b)

{% endhighlight %}



링크 넣어보기 [클릭][Urbanbase]

[Urbanbase]: http://www.urbanbase.com


테이블 만들기

| 제목1  | 제목2 | 제목3 | 제목4 | 제목5 |
|--------|-------|-------|-------|-------|
| 1      | 2     | 3     | 4     | 5     |
| 6<br>7 | 8     | 9     | 10    | 11    |
|  ㅁㄴㅇㄹ       || ㅁ     | ㄴ    | ㅇ     |










>>>>>>> cc439b467012dc3af2cc03cac0dc923e39f1e262



=======
---
layout: post
title:  "TEST"
date:   2019-02-02 08:43:59
categories: tech
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---

<br>

# Auto Sketch
Auto sketch는 세계 유일의 실시간 도면 자동 변환 서비스입니다.

<br>

어반베이스는 특허받은 2D 도면의 3D 도면 변환 기술로 세계 최대의 3D 공간 DB를 구축했습니다. Auto desk CAD, SketchUp에 플러그인을 제공하여 확장성을 넓혀가고, Live sketch 기술을 고도화하여 사용자가 직접 그린 도면을 자유롭게 2D 또는 3D로 변환할 수 있는 서비스를 예정 중입니다.

<br>
<br>

Auto Sketch Demo <br>
[![Auto Sketch Demo](https://img.youtube.com/vi/sHmokPYTp2Q/0.jpg)](https://youtu.be/sHmokPYTp2Q)
<Br>
<br>
<br>
## 기술 설명
기존 목업을 실물로 제작하여 프로젝트를 진행할 경우, 목업 제작을 위한 **비용∙시간∙노동력** 등이 소모됩니다. <br>
<br>
하지만 `Auto Sketch`를 사용할 경우, 단순한 도면 파일 및 이미지 파일로도 실측 면적 기반의 건축물을 확인할 수 있습니다. 또한, 도면 이미지를 활용하여 **다양한 3D 확장자(\*.obj, \*.dae 등)의 3D 모델링 파일** 로 변환할 수 있습니다. <br>
`2D-3D Converting` 기술로 인하여 직접 3D 모델링 파일을 제작하거나, 2D CAD 도면 (*.dwg) 등을 공유하지 않아도 시각적으로 바로 확인할 수 있습니다. <br>
실물 모형 및 도면 파일은, 보안, 모형 및 파일 손상 등의 문제가 발생할 수 있지만 `Auto Sketch` 는 인터넷과 jpg 파일만 있으면 언제 어디서나 **3D 모델링 결과를 확인 할 수 있습니다.** <br>
<br>
이로써 **파일 용량 감소, 보안상의 문제 해결, 비용 절감, 편의성 증대**가 가능해집니다. <br>
<br>
### 요약
- 도면 또는 정형화된 이미지의 3D 도면 변환 엔진 개발
- AutoCAD, SketchUp 등 설계 프로그램에 설치할 수 있는 라이브러리, 모듈화, 플러그인 등 확장 프로그램화 <br>
<Br>
<Br>
## 프로세스
[![before]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (before).jpg)]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (before).jpg)
[![after]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (after).png)]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (after).png)

<br>
<Br>
기존에는 3D로 변환 시 치수 수정, 영역 분리, 개별 모델링 생성, 예외 처리 등 다소 복잡한 처리 과정을 거쳐야 했습니다. <br />
하지만 이 과정에서 `Auto Sketch`를 활용할 경우, 사용자가 이미지 파일을 **Drag and Drop** 하여 바로 결과를 확인할 수 있습니다. <br />
<br>
`Auto Sketch` 엔진은 이러한 복잡하고 불필요한 조작을 제거하여 작업 처리 시간 단축되는 장점이 있습니다.<br />또한, 각 구역의 특징을 분석하여 바닥재 및 벽재 등을 기본적으로 분석하여 제공해주며, `Computer Vision`, `Machine Learning`, `Auto Sketch` 알고리즘을 통하여 유의미한 의미를 가지는 3D 모델링 파일을 제공할 수 있습니다. <br>
<br>
<br>
## 응용분야
### 목업 제작
`Auto Sketch`를 이용하여 실물 목업 제작 시 보여질 모습을 3D로 미리 확인해 볼 수 있습니다.<br>
실물로 제작하지 않아도 치수 및 기호 오류 등을 확인할 수 있습니다. 또한 본인이 제작한 도면을 확인한 후 보완 및 수정이 용이 해 집니다.
<br>
### 응용 프로그램 빌드, 실행 및 테스트
`Auto Sketch`에서 도면 이미지를 불러와 `Computer Vision`, `Machine Learning` 등을 통하여 3D 모델링 파일로 불러올 수 있습니다.<br>
**저용량 이미지 파일 (.jpg, .png, .bmp) 등의 도면 이미지**만 입력하여 3D 모델링 결과를 손쉽게 확인할 수 있습니다.
### 실시간 다자간 온라인 화상 회의
`Auto Sketch`를 통해 실시간으로 도면 이미지를 3D 모델링 파일로 불러올 수 있습니다. 온라인 화상 회의를 통하여 공간적 한계가 사라지고 실물 목업 운반 등의 문제점을 해결 할 수 있습니다.<br>
또한, 해당 응용 프로그램을 Web으로 구현하여 도면 이미지 파일만 보유하여도 **3D 모델링 파일을 시각적으로 확인** 할 수 있습니다.<br>
<br>
<br>
<br>


--------
<h3>
`Auto Sketch` 기술을 보유한 어반베이스는 3D 공간데이터 플랫폼입니다. <br>
전 세계의 모든 공간정보를 하나의 플랫폼 안에서 자유롭게 활용할 수 있도록 도면 변환 기술 및 AR, VR 뷰어 기술이 담긴 API와 ADK를 제공하고 있습니다.<br>
<br>
Auto Sketch : API, Engine <br>
<Br>
어반베이스 개발자 사이트 [자세히 보기][Urbanbase-dev]
</h3>
[Urbanbase-dev]: https://developer.urbanbase.com

<Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br>

- 기호 표시법
- 기호 표시법
    - 기호 표시법 2
    - 기호 표시법 2
        - 기호 표시법 3
        - 기호 표시법 3
    - 기호 표시법 2


<br>
엔터
<br>

*기울임*입니다.

**굵게**입니다.

<u>밑줄</u>입니다.

`하이라이트`입니다.

# 제목 1 
## 제목 2
### 제목 3
#### 제목 4
##### 제목 5
###### 제목 6

그림 넣는 법
[![railroad]({{ site.images | relative_url }}/rails.jpg)]({{ site.images | relative_url }}/rails.jpg)
[![그림의_제목]({{ site.images | relative_url }}/tech/studio_auto sketch (after).jpg경로와확장자)]({{ site.images | relative_url }}/경로와확장자)


코드 넣어보기 with js

{% highlight js %}
var a = 2;

{
    let a = 3;
    console.log( a );   // 3
}

console.log( a );       // 2
{% endhighlight %}



코드 넣어보기 with python

{% highlight python %}

import os

a = 3
b = 2

print (a + b)

{% endhighlight %}



링크 넣어보기 [클릭][Urbanbase]

[Urbanbase]: http://www.urbanbase.com


테이블 만들기

| 제목1  | 제목2 | 제목3 | 제목4 | 제목5 |
|--------|-------|-------|-------|-------|
| 1      | 2     | 3     | 4     | 5     |
| 6<br>7 | 8     | 9     | 10    | 11    |
|  ㅁㄴㅇㄹ       || ㅁ     | ㄴ    | ㅇ     |












=======
---
layout: post
title:  "TEST"
date:   2019-02-02 08:43:59
categories: tech
tags:	jekyll welcome
cover:  "/assets/instacode.png"
---

<br>

# Auto Sketch
Auto sketch는 세계 유일의 실시간 도면 자동 변환 서비스입니다.

<br>

어반베이스는 특허받은 2D 도면의 3D 도면 변환 기술로 세계 최대의 3D 공간 DB를 구축했습니다. Auto desk CAD, SketchUp에 플러그인을 제공하여 확장성을 넓혀가고, Live sketch 기술을 고도화하여 사용자가 직접 그린 도면을 자유롭게 2D 또는 3D로 변환할 수 있는 서비스를 예정 중입니다.

<br>
<br>

Auto Sketch Demo <br>
[![Auto Sketch Demo](https://img.youtube.com/vi/sHmokPYTp2Q/0.jpg)](https://youtu.be/sHmokPYTp2Q)
<Br>
<br>
<br>
## 기술 설명
기존 목업을 실물로 제작하여 프로젝트를 진행할 경우, 목업 제작을 위한 **비용∙시간∙노동력** 등이 소모됩니다. <br>
<br>
하지만 `Auto Sketch`를 사용할 경우, 단순한 도면 파일 및 이미지 파일로도 실측 면적 기반의 건축물을 확인할 수 있습니다. 또한, 도면 이미지를 활용하여 **다양한 3D 확장자(\*.obj, \*.dae 등)의 3D 모델링 파일** 로 변환할 수 있습니다. <br>
`2D-3D Converting` 기술로 인하여 직접 3D 모델링 파일을 제작하거나, 2D CAD 도면 (*.dwg) 등을 공유하지 않아도 시각적으로 바로 확인할 수 있습니다. <br>
실물 모형 및 도면 파일은, 보안, 모형 및 파일 손상 등의 문제가 발생할 수 있지만 `Auto Sketch` 는 인터넷과 jpg 파일만 있으면 언제 어디서나 **3D 모델링 결과를 확인 할 수 있습니다.** <br>
<br>
이로써 **파일 용량 감소, 보안상의 문제 해결, 비용 절감, 편의성 증대**가 가능해집니다. <br>
<br>
### 요약
- 도면 또는 정형화된 이미지의 3D 도면 변환 엔진 개발
- AutoCAD, SketchUp 등 설계 프로그램에 설치할 수 있는 라이브러리, 모듈화, 플러그인 등 확장 프로그램화 <br>
<Br>
<Br>
## 프로세스
[![before]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (before).jpg)]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (before).jpg)
[![after]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (after).png)]({{ site.images | relative_url }}/assets/tech(demo)/studio_auto sketch (after).png)

<br>
<Br>
기존에는 3D로 변환 시 치수 수정, 영역 분리, 개별 모델링 생성, 예외 처리 등 다소 복잡한 처리 과정을 거쳐야 했습니다. <br />
하지만 이 과정에서 `Auto Sketch`를 활용할 경우, 사용자가 이미지 파일을 **Drag and Drop** 하여 바로 결과를 확인할 수 있습니다. <br />
<br>
`Auto Sketch` 엔진은 이러한 복잡하고 불필요한 조작을 제거하여 작업 처리 시간 단축되는 장점이 있습니다.<br />또한, 각 구역의 특징을 분석하여 바닥재 및 벽재 등을 기본적으로 분석하여 제공해주며, `Computer Vision`, `Machine Learning`, `Auto Sketch` 알고리즘을 통하여 유의미한 의미를 가지는 3D 모델링 파일을 제공할 수 있습니다. <br>
<br>
<br>
## 응용분야
### 목업 제작
`Auto Sketch`를 이용하여 실물 목업 제작 시 보여질 모습을 3D로 미리 확인해 볼 수 있습니다.<br>
실물로 제작하지 않아도 치수 및 기호 오류 등을 확인할 수 있습니다. 또한 본인이 제작한 도면을 확인한 후 보완 및 수정이 용이 해 집니다.
<br>
### 응용 프로그램 빌드, 실행 및 테스트
`Auto Sketch`에서 도면 이미지를 불러와 `Computer Vision`, `Machine Learning` 등을 통하여 3D 모델링 파일로 불러올 수 있습니다.<br>
**저용량 이미지 파일 (.jpg, .png, .bmp) 등의 도면 이미지**만 입력하여 3D 모델링 결과를 손쉽게 확인할 수 있습니다.
### 실시간 다자간 온라인 화상 회의
`Auto Sketch`를 통해 실시간으로 도면 이미지를 3D 모델링 파일로 불러올 수 있습니다. 온라인 화상 회의를 통하여 공간적 한계가 사라지고 실물 목업 운반 등의 문제점을 해결 할 수 있습니다.<br>
또한, 해당 응용 프로그램을 Web으로 구현하여 도면 이미지 파일만 보유하여도 **3D 모델링 파일을 시각적으로 확인** 할 수 있습니다.<br>
<br>
<br>
<br>


--------
<h3>
`Auto Sketch` 기술을 보유한 어반베이스는 3D 공간데이터 플랫폼입니다. <br>
전 세계의 모든 공간정보를 하나의 플랫폼 안에서 자유롭게 활용할 수 있도록 도면 변환 기술 및 AR, VR 뷰어 기술이 담긴 API와 ADK를 제공하고 있습니다.<br>
<br>
Auto Sketch : API, Engine <br>
<Br>
어반베이스 개발자 사이트 [자세히 보기][Urbanbase-dev]
</h3>
[Urbanbase-dev]: https://developer.urbanbase.com

<Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br><Br>

- 기호 표시법
- 기호 표시법
    - 기호 표시법 2
    - 기호 표시법 2
        - 기호 표시법 3
        - 기호 표시법 3
    - 기호 표시법 2


<br>
엔터
<br>

*기울임*입니다.

**굵게**입니다.

<u>밑줄</u>입니다.

`하이라이트`입니다.

# 제목 1 
## 제목 2
### 제목 3
#### 제목 4
##### 제목 5
###### 제목 6

그림 넣는 법
[![railroad]({{ site.images | relative_url }}/rails.jpg)]({{ site.images | relative_url }}/rails.jpg)
[![그림의_제목]({{ site.images | relative_url }}/tech/studio_auto sketch (after).jpg경로와확장자)]({{ site.images | relative_url }}/경로와확장자)


코드 넣어보기 with js

{% highlight js %}
var a = 2;

{
    let a = 3;
    console.log( a );   // 3
}

console.log( a );       // 2
{% endhighlight %}



코드 넣어보기 with python

{% highlight python %}

import os

a = 3
b = 2

print (a + b)

{% endhighlight %}



링크 넣어보기 [클릭][Urbanbase]

[Urbanbase]: http://www.urbanbase.com


테이블 만들기

| 제목1  | 제목2 | 제목3 | 제목4 | 제목5 |
|--------|-------|-------|-------|-------|
| 1      | 2     | 3     | 4     | 5     |
| 6<br>7 | 8     | 9     | 10    | 11    |
|  ㅁㄴㅇㄹ       || ㅁ     | ㄴ    | ㅇ     |










>>>>>>> cc439b467012dc3af2cc03cac0dc923e39f1e262


>>>>>>> 6b982285648b17df396c533d2cd2bf3872ea80d5
