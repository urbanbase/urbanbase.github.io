---
layout: post
title: "Geometry Shader Effect로 데이터 아름답게 표현하기"
date: 2019-07-02 16:00:00
categories: dev
tags: 3DGraphics UniteSeoul2019 KeijiroTakahashi GeometryShader
excerpt: "Unity의 Geometry Shader Effect 실무 적용기"
---


안녕하세요. 3D 그래픽스 엔지니어 문현목 입니다. 
<br>
온라인과 모바일 게임 회사에서 10년 넘게 근무하다가 비 게임 분야를 거쳐 현재 어반베이스에서 근무하고 있습니다. 플랫폼 회사로 성장해 나가는 과정을 함께 하고 있어서 너무 기쁘네요. 어반베이스에서는 3D 데이터를 사용자에게 시각적으로 표현해주는 일을 하고 있습니다. Unity를 이용해 3D Core Engine(UI를 통한 요청 사항을 처리해 3D 오브젝트에 반영)도 만들고 있고요. 이를 통해 3D Home Design, 3D Viewer를 개발합니다.
<center>
<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//7v4XXImNGFI' frameborder='0' allowfullscreen></iframe></div>
</center>
<br>
<br>

## Unite Seoul 2019

지난 5월 21일, 22일 양일간 Unite Seoul 2019에 참가했습니다. 유니티 코리아의 에반젤리스트(Evangelist)를 통해서 듣기로는 약 5천명이 참석했다고 하는데, 작년 대비 천명이 늘어난 수치라고 하네요. 

이틀간 세션 프로그램표입니다.

*첫째날
<img src="/assets/6_3D graphics/Schedule1.png">
<br>
*둘째날
<img src="/assets/6_3D graphics/Schedule2.png">

5개의 트랙으로 다양한 세션이 있었는데요. 제 몸을 복제해서 5개의 세션을 동시에 듣고 싶었으나 분신술을 할 수가 없어서.. 아쉽게도 팀원들과 세션을 나눠 들었습니다.
<center>
<img src="/assets/6_3D graphics/zzz.png">
</center>

<br>

제가 들었던 세션 중에서는,<br>
**Mark Schoennagel**의 **"Real-time Ray Tracing In Unity's High Definition Render Pipeline Using Nvidia RTX"** <br>
**Keijiro Takahashi**의 **"Unity 기술로 예술적 영감을 불어넣는 방법"** <br>
그리고 **Julien Frayer와 Thomas Iché**가 발표한 **"Visual Effect Graph로 실시간 VFX를 제작하는 방법"** 정도가 좋았습니다.<br>
어떤 것인지는 아래 youtube나 twitter 영상을 참고해 주세요.



2019 BMW 8 Series Coupe를 NVIDIA RTX를 이용하여 Ray Tracing으로 표현한 영상 <a href="https://www.youtube.com/watch?v=IY8FJxS4Lbs" target="_blank" style="color: #0366d6;"> (링크) </a>
<center>
<style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed/IY8FJxS4Lbs' frameborder='0' allowfullscreen></iframe></div></center>

<br>
Unity Visual Effect Graph를 이용하여 표현한 Spaceship 데모 <a href="https://www.youtube.com/watch?v=rqMcPZoEc3U" target="_blank" style="color: #0366d6;"> (링크) </a>
<center><style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed/rqMcPZoEc3U' frameborder='0' allowfullscreen></iframe></div></center>



<br>
RealSense 뎁스 카메라와 유니티 VFX Graph로 표현한 작업 <a href="https://github.com/keijiro/Rsvfx" target="_blank" style="color: #0366d6;"> (링크) </a>

<!-- https://twitter.com/_kzr/status/1098900394002964486 -->

<img src="https://camo.githubusercontent.com/2bc3c65923f8e3db7131ab50e1254164ac25c558/68747470733a2f2f692e696d6775722e636f6d2f4b304338304c662e676966" alt="gif" data-canonical-src="https://i.imgur.com/K0C80Lf.gif" style="max-width:100%;">
<img src="https://camo.githubusercontent.com/262d2fa83c11137e79d46a8df3b22e72480e6505/68747470733a2f2f692e696d6775722e636f6d2f6a4278494930742e676966" alt="gif" data-canonical-src="https://i.imgur.com/jBxII0t.gif" style="max-width:100%;">



이 중에서도 Keijiro Takahashi의 세션이 개인적으로 가장 인상 깊었는데요. 이번 콘텐츠에서는 이 세션을 통해 새롭게 알게 된 Geometry Shader에 대한 소개와 함께 실무 적용기를 한번 정리해봤습니다.

<br>
<br>
<br>

## 케이지로 다카하시 (Keijiro Takahashi)

Unity Japan의 에반젤리스트로 일본 게임 산업에 10년 이상 있었고, 콘솔과 모바일 게임 개발 방법론에서 풍부한 경험을 가지고 있습니다. Unity에는 2012년에 합류했다고 하는데요, Unity로 훌륭한 게임을 만들 수 있도록 돕고, 강의를 하고, 책을 쓰고 있습니다.
<br>
*\*Source: <https://unity3d.com/kr/community/evangelists>*

<!-- <https://twitter.com/_kzr/status/1127888862460534784?lang=bg> -->
<br>
Unite Shanghai 2019에서 보여준 비파 연주를 VFX Graph와 RealSense로 표현한 영상 <a href="https://twitter.com/_kzr/status/1127888862460534784?lang=bg" target="_blank" style="color: #0366d6;"> (보러가기) </a>
<img src="/assets/6_3D graphics/UniteShanghaiVFX.png">




상하이 음악 아카데미에서 비파 선생님으로 계시는 분과 라이브 퍼포먼스를 했고, 실시간으로 이펙트를 표현했다고 하더라고요. Intel에서 나오는 RealSense라는 뎁스 카메라로부터 정보를 얻어서 VFX Graph에 입력, 파티클을 생성하도록 표현한 작업이라고 설명했습니다.

<br>
<img src="/assets/6_3D graphics/RealSenseAttribVFX.png">
<br>

**\*VFX Graph에 대한 Unity Blog:** <br>
<https://blogs.unity3d.com/kr/2018/11/27/creating-explosive-visuals-with-the-visual-effect-graph/>
<br>
<br>
**\*인텔 RealSense 뎁스 카메라를 유니티 VFX Graph에 연결하는 예제:**<br>
<https://github.com/keijiro/Rsvfx>

<br>

덧붙여, 기온이나 날씨 같은 기상 정보는 물론 인구 밀도, 공기질 정보(AQI) 등의 데이터를 비주얼라이제이션 하는 방법도 생각해 볼 수 있을 것이라고 하며, 두 가지 케이스를 이야기 했는데요. 한 가지는 Geometry Shader Effect, 다른 한가지는 Deep Learning을 통한 pix2pix VFX 였습니다. 이번 글에서는 Geometry Shader Effect에 대한 내용만 다뤄볼게요.

<br>
<br>

## Geometry Shader Effect

**\*Keijiro의 Standard Geometry Shader github :**
<https://github.com/keijiro/StandardGeometryShader>

Keijiro는 Geometry shader에 효과를 주었다고 했는데요. Vertex Shader나 Pixel(Fragment) Shader는 많이 들어보셨겠지만 Geometry shader는 좀 생소하실 수 있을 것 같습니다. Geometry Shader Effect를 이해하기 위해서, Graphics Pipeline을 통해 Geometry Shader의 개념에 대해 먼저 소개하려고 합니다. 

<br>
<br>

### Graphics Pipeline

3D 그래픽스에서는 3D 모델 데이터를 스크린 화면에 렌더링하기까지의 과정을 '렌더링 파이프라인'이라고 합니다. 아래는 위키에 나와있는 그래픽스 파이프라인에 대한 이미지 인데요. 독일인이 작성한 자료라서 영어랑 스펠링에 차이가 좀 있긴 하지만, 감안하셔서 참고하시면 좋을 듯 합니다. 

<p><a href="https://commons.wikimedia.org/wiki/File:3D-Pipeline.png#/media/File:3D-Pipeline.png"><img src="https://upload.wikimedia.org/wikipedia/commons/5/54/3D-Pipeline.png" alt="Programmable 3D-Pipeline."></a><br>By <a href="https://de.wikipedia.org/wiki/Benutzer:Martin_Wantke" class="extiw" title="de:Benutzer:Martin Wantke">Martin Wantke</a> - <span class="int-own-work" lang="en">Own work</span>, Public Domain, <a href="https://commons.wikimedia.org/w/index.php?curid=35922943">Link</a></p>

<br>

Vertex-Shader와 3D Coordinate(좌표) 정보로 Animation, Transformation 표현 -> Hull-Shader, Domain-Shader로 Tessellation이 이루어지며 -> Geometry Shader 처리 후 -> Perspective Transformation 처리(3D에서 2D로 변환, Z-buffer 처리) -> Clipping, Backface Culling이 된 다음 -> Triangle Rasterization -> Pixel-Shading이 되고 (텍스쳐의 픽셀화) -> 이후 Z-Test, Alpha-Blending을 하며 Antialiasing 처리 -> Postprocessing 의 과정이네요.

- Z-buffer: 깊이 버퍼, 어떤 것이 그려지고 어떤 것이 가려지는 것을 처리하기 위한 정보
- Clipping: 화면 밖에 있는 것은 그릴 필요 없으므로 잘라짐
- Backface Culling: 카메라를 마주본 표면(front face)만 그리고 안쪽 뒷면은 그리지 않음
- Triangle Rasterization: 아래 이미지 참고
- Alpha-Blending: 투명도 값에 따라 섞어주는 계산
- Antialiasing: 픽셀이 계단처럼 끊어지지 않고 부드럽게 그려지기 위해 처리하는 기법으로 해당 <a href="https://namu.wiki/w/%EC%95%88%ED%8B%B0%EC%97%90%EC%9D%BC%EB%A6%AC%EC%96%B4%EC%8B%B1" target="_blank" style="color: #0366d6;">링크</a>에서 다양한 AA기법 확인 가능



<img src="https://www.ntu.edu.sg/home/ehchua/programming/opengl/images/Graphics3D_Pipe.png">

**Source : <https://www.ntu.edu.sg/home/ehchua/programming/opengl/cg_basicstheory.html>*



**Vertex Shader**는 GPU에서 실행되는 프로그램 중 하나입니다. 3D 데이터는 각각의 정점들이 모여 형태를 구성하는데, Vertex-Shader 프로그램에서 이 Vertex 정보를 어떻게 그릴지 결정해줍니다. 정점의 정보에는 위치(x,y,z 좌표), 컬러, 텍스쳐 UV 좌표, Normal 값이 들어가는데요. Vertex Shader에서 이러한 정점 정보를 변화시켜서 위치를 옮기거나 텍스쳐를 바꾸거나 색상을 바꾸는 등의 작업을 처리할 수 있습니다. 

**Geometry Shader**는 Vertex Shader에서 할 수 없는 점이나 선, 삼각형 등 도형을 생성할 수 있는 기능이 있습니다. 정점 3개가 모여 하나의 삼각형을 이루고 이 삼각형이 Geometry Shader에 들어와서 정점을 없애거나 더 많은 도형을 만들 수 있습니다. Rasterization을 거쳐 Pixel Shader로 넘어갑니다. 

**Pixel(Fragment) Shader**는 화면에서 각 픽셀이 어떠한 컬러로 그려질지 계산합니다. 같은 색을 출력하거나 텍스쳐로부터 색을 읽어오고, 빛을 적용하고, 굴곡을 표현하는 범프 맵핑, 그림자, 반사광, 투명처리 등을 계산할 수 있습니다.

**Geometry Shader Effect**는 Geometry Shader에서 점이나 선, 삼각형 등 도형을 생성할 수 있기 때문에 이러한 부분을 효과로 표현하게 됩니다.

<br>
<br>

### Geometry Shader Effect 이용해 2D 도면 -> 3D로 표현하기

어반베이스는 **2D도면을 몇 초 만에 3D 공간으로 변환하는 기술**을 보유하고 있는데요. Geometry Shader에 대한 이야기를 듣다 보니, 데이터 시각화하는 부분에서 2D 도면 데이터에 높이값을 적용하여 3D로 변환하는 작업이 가능할 것 같다는 생각이 들었습니다. 문이나 창 같은 부분은 표현하지 못하겠지만 벽에 대하여 일정한 높이로 Geometry를 변형하여 2D를 3D로 표현하면 무언가 재미있는 결과가 나올 것 같았습니다. 

Keijiro의 Github 중 <a href="https://www.desmos.com/calculator" target="_blank" style="color: #0366d6;">Standard Geometry Shader</a>를 참고해 실제 적용을 해보았습니다. Shader 코드도 있기 때문에 Geometry shader에서 어떤 작업을 하는지 볼 수 있습니다. 

Assets/StandardGeometry.cginc 파일을 클릭하면, 117 줄부터 void Geometry( ... ) 함수가 있습니다. 내부에서 어떤 일을 하는지 보면 Vertex inputs라고 주석이 있습니다. wp는 world position 이고요. 입력 받은 삼각형의 세 개 정점 위치를 변수에 넣고, uv 좌표도 변수에 넣습니다. Extrusion amount라고 되어있는데,삼각형을 얼마나 수직으로 늘려줄지 그 늘어나는 길이를 계산하는 부분입니다. 로컬 시간에 2 PI(π)를 곱해 cos 함수에 넣습니다. 

<img src="http://tananyag.geomatech.hu/files/00/01/17/94/material-1179486.png?v=1497014242" width="600">

> *"cos 함수는 반지름이 1인 원에서 0°에서 360° (2 π)로 움직이는 점의 x 좌표값입니다. 0°에서 1이고, 90°가 될 때 0이고, 180°가 될 때 -1이 되고, 270°가 될 때 0이 되고 360°가 되면 다시 1이 됩니다. 물결 같은 파동 그래프라고 볼 수 있고, 1에서 -1까지 범위로 진동을 한다고 볼 수 있습니다. 참고로 sin 함수는 y좌표입니다."*

다시 함수로 돌아와서 extrude를 얼마나 할지는 cos함수로 진동하는 값을 구하고 이 값에 추가로 sin함수를 곱하여 ext값을 계산합니다. 어떤 움직임이 될지는 해당 <a href="https://www.desmos.com/calculator" target="_blank" style="color: #0366d6;">사이트</a>에서 실시간으로 그래프를 볼 수 있습니다.

<center> 
y = (0.4 - cos (x * 6.28) * 0.41) * (1 + 0.3 * sin(832 + x * 88) )
</center>
<br>

<img src="/assets/6_3D graphics/extWave.png">

이런 모양의 그래프가 되네요. 

코드로 다시 옵니다. Extrusion points가 있습니다. ConstructNormal은 세 점을 이용하여 삼각형에 수직인 법선 벡터(Normal vector)를 구하는 함수입니다. 법선 벡터의 위에서 구한 extrusion amount를 곱하여 offset으로 사용하네요. 각각의 세 점에 offset을 더하여 새로운 점을 저장해두고, 제일 윗쪽의 삼각형(Cap triangle)을 구하고, 옆면(Side faces)의 버텍스 정보를 구해서 outStream에 넣습니다. 

Git으로 받은 StandardGeometryShader 프로젝트를 Unity에서 열어 Simple 씬을 열어봅니다. 플레이를 해보면 다음과 같이 나옵니다.

<img src="/assets/6_3D graphics/SphereGeometry.png">

이제 우리 도면을 적용해보면 어떤 모습일지 궁금해지네요. 테스트로 사용하는 3D 도면 파일을 Unity에 가져와서 벽 영역에 해당하는 부분을 Sphere 오브젝트의 MeshFilter 콤포넌트에 적용해봅니다.

<img src="/assets/6_3D graphics/TooMuch.png">

주황색 아웃라인으로 나오는 부분이 2D 벽 영역이 되고, 이것을 Geometry Shader에서 위와 같은 함수를 처리하여 표면에 수직인 방향으로 Extrusion 되는 것을 확인할 수 있습니다. 그런데 Y축 방향으로의 Extusion만 처리해주고 X,Z 축 방향의 확장은 막아보고 싶습니다. 그리고 움직임이 너무 복잡한 것 같으니 단순한 진동형태로 바꿔보고 싶습니다.

- 요구사항: 

1. Y 축방향으로만 확장
2. 단순한 진동형태로 변경

> *"빨간, 파랑, 녹색 화살표가 보이는데 이것은 좌표 축을 보여주는 것이고, RGB 순서로 XYZ 축이라고 보면 됩니다. 상(上)방향 벡터가 녹색이고 Y축이라는 것을 알 수 있습니다. Unity 에서는 이러한 왼손 좌표계를 사용하는 것이고 다른 엔진이나 라이브러리에서는 좌표축이 다를 수 있습니다."*

단순한 진동 형태로의 변경은 extrusion amount를 계산하는 부분을 수정하면 되고, Y축으로 확장을 제약하는 것은 세 점을 넣어서 법선 벡터를 구하는 부분에서 필터링을 할 수 있습니다.

그래프를 그려주는 <a href="https://www.desmos.com/calculator" target="_blank" style="color: #0366d6;">사이트</a>로 가서 식을 변경해 보겠습니다. Sin 함수를 곱해주는 부분을 빼보았습니다.

<center> 
y = (0.4 - cos (x * 6.28) * 0.41)
</center>

이 그래프를 보면 다음과 같습니다.

<img src="/assets/6_3D graphics/extWaveSimple.png">

좀더 단순한 형태의 그래프로 바뀌었습니다. cos 함수 그래프를 약간 변형한 것이라고 보면 됩니다. 쉐이더 코드 부분도 sin 함수 부분을 주석처리하여 cos 함수로만 extrusion amount를 구하도록 변경 합니다.

cos 함수만 이용해 extrusion amount를 구한 결과입니다.

<img src="/assets/6_3D graphics/SimpleButAllDirection.png">

ConstructNormal 함수를 보면 세 점을 이용하여 외적(cross product)을 통해 삼각형의 법선 벡터를 구하고 이 벡터의 크기를 1로 바꾸는 normalize 처리를 합니다. 여기서 Normal 방향을 녹색 화살표 방향(Y축)으로만 Normal 값이 나올 수 있도록 normalize 결과 벡터의 x성분과 z성분을 0으로 초기화 합니다. y성분만 남게 됩니다. 

Y축 방향의 법선 벡터만 구해주는 ConstructNormal을 적용한 결과입니다.

<img src="/assets/6_3D graphics/OnlyUpDirection.png">

아래는 플레이 한 것을 캡쳐한 것입니다. Y축 방향으로만 확장되었다가 줄어드는 것을 확인할 수 있습니다. 양수 방향과 음수 방향으로 모두 확장됩니다.

<img src="/assets/6_3D graphics/captureGIF.gif">

ComputeNormal 함수에서 normalize 된 벡터의 y 성분에서 음수의 경우 0으로 만들면 양수 방향으로만 확장되는 것을 확인할 수 있습니다.

Geometry Shader를 이용하여 2D 평면을 추가로 Triangle을 생성, Geometry를 만드는 것을 보았는데요. Deferred Rendering만 지원하고 Forward rendering은 지원하지 않는다는 것, lightmap,shadowmask,motion vectors 등을 지원하지 않고, XR(VR과 AR을 합쳐서 XR이라 표현)에서는 테스트가 불가하다는 것 등의 한계점이 있었지만 Geometry Shader를 통해 추가로 Triangle을 GPU에서 생성할 수 있다는 것을 확인할 수 있었습니다. 
