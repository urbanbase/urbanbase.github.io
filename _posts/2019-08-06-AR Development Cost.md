---
layout: post
title: "AR 개발 얼마면 되겠니?"
date: 2019-08-06 10:00:00
categories: tech
tags: AR
---


# AR의 시대

누구는 이미 왔다고 하고, 누구는 아직 멀었다고 합니다. 증강현실(AR, Augmented Reality) 이야기입니다. (저희는 전자에 한표 던지지만) 이렇게 갑론을박이 벌어지는 것 보면 아직 주류(mainstream)로 자리잡았다고 하기엔 무리가 있나봅니다.

<figure>
    <img src="/assets/8_AR/gartner.png">
    <figcaption><center> Gartner Hype Cycle 2018 </center>
    </figcaption>
</figure>

<a href="https://www.smartinsights.com/managing-digital-marketing/managing-marketing-technology/gartner-hype-cycle-2018-most-emerging-technologies-are-5-10-years-away" target="_blank" style="color: #0366d6;"> Gartner Hype Cycle 2018 </a>에 따르면,증강현실은 'Trough of Disillusionment' 단계로 2023~2028년쯤 널리 상용화 될 것으로 전망되고 있습니다.

한가지 분명한 건 **내가 직접 경험하지 않아도 경험한 것과 같은 효과를 주는 증강현실 기술**이 우리 생활 전반을 이전보다 훨씬 편리하게 그리고 효율적으로 만들 수 있는 잠재력을 가지고 있다는 겁니다. 이런 능력자를 포켓몬을 잡거나 머글 세계의 짐승을 잡을 때만 쓰는 건 좀 아쉽습니다. 

<img src="/assets/8_AR/harry porter ar.png">

<br>

그래서 소위 업계 탑티어라고 하는 기업들은 일찌감치 증강현실의 포텐을 알아보고 발빠르게 움직였습니다. 덕분에 이런 기업들을 이용하는 소비자들은 참~ 해피합니다.

<center>

피팅 한번 하겠다고 긴긴 줄 서는 대신

<img src="/assets/8_AR/past fitting.png"> 

<br>

요렇게?
<img src="/assets/8_AR/ar fitting by fx gear.png">
 AR Fitting <a href="http://www.fxmirror.net/ko/main" target="_blank" style="color: #0366d6;"> (FXMirror) </a> 

<br>
<br>
<br>

지웠다 그렸다 반복하는 메이크업 대신

<img src="/assets/8_AR/past makeup.png"> 
<br>

요렇게?

<img src="/assets/8_AR/ar makeup by sephora.png">
 AR Makeup <a href="https://www.techrepublic.com/article/how-sephora-is-leveraging-ar-and-ai-to-transform-retail-and-help-customers-buy-cosmetics/" target="_blank" style="color: #0366d6;"> (Sephora) </a>

<br>
<br>
<br>

보이지 않는 걸 보이는 것처럼 상상하는 대신

<img src="/assets/8_AR/past presentation.png"> 
<br>

요렇게?


<img src="/assets/8_AR/ar presentation by urbanbase.png">

AR Presentation <a href="https://arscale.io/" target="_blank" style="color: #0366d6;"> (Urbanbase) </a>

<br>
<br>
<br>

1cm를 우습게 본 나 자신을 자책하는 대신

<img src="/assets/8_AR/past homedesign.png"> 

<br>

요렇게?

<img src="/assets/8_AR/ar home by ikea.png">
AR HomeDesign <a href="https://www.ikea.com/ms/ko_KR/this-is-ikea/ikea-highlights/2017/ikea-place-app/index.html" target="_blank" style="color: #0366d6;"> (IKEA) </a> 


</center>

<br>

AR로 가볍게 하면 되기 때문이죠.

물론 이게 진짜인지 가짜인지 구분이 안갈 정도로 더 실감나게, 더 현실감있게 증강현실의 기술을 상향평준화 시키는 건 저희 같은 테크 회사들의 과제이긴 합니다만..

<br>

# AR 개발, 쉬울까 어려울까

AR에 뜻이 있는 개발자분들이 AR 개발을 '시작'하는 건 사실 크게 어렵지 않습니다. 나와 있는 AR SDK(Software Development Kit)만 수십개고, AR SDK계의 엘리트인 Apple ARKit와 Google ARCore는 아주 훌륭한 기능들이 계속 업그레드되며 나날이 진화하고 있기 때문입니다.

<img src="/assets/8_AR/arcorearkit.png">

하지만 과거에 비해 AR 개발의 진입장벽이 많이 낮아졌다고 해도 국수 가락 뽑아내듯 AR 서비스를 개발할 수는 없습니다. 뽑아낸다고 해도, 시중에 나와 있는 AR 서비스들을 보시면 아시겠지만, AR이라고 하기에는 좀 부끄러운 퀄리티의 서비스들이 많습니다. 왜그럴까요. 곰곰히 생각해봤습니다.  

일단, 신생 기술인만큼(외국에 비해 국내의 AR 개발은 신생아 수준이죠) **AR에 정통한 개발자를 구하기가 어렵습니다**. 여기서 '정통'이라 함은 적어도 5년 이상은 AR 쪽으로 개발 커리어를 쌓았음을 의미합니다. 모바일 개발에 숙련된 개발자여야 함은 물론 운영체제별로 지원하는 파일포맷이 달라 iOS와 Android 각각에 대한 이해 또한 필수적입니다. 


<figure>
    <img src="/assets/8_AR/ar dev screen.png">
    <figcaption><center> AR 환경 설정 화면 </center>
    </figcaption>
</figure>



<br>더욱이 AR은 3D 모델을 현실로 이관하는 기술이기 때문에 **3D의 기본적인 지식 없이는 서비스의 퀄리티를 일정 수준 이상 높이는 데 한계**가 있을 수 밖에 없습니다. 저희 <a href="https://www.youtube.com/watch?v=TSb_-_2XyCM&list=PLpbBpSvS-4kexGkThqF8zG0-JaHomRkF2" target="_blank" style="color: #0366d6;"> AR Scale </a>을 리드하고 계신 우석님과 얼마 전에 점심을 먹으면서 AR 개발할 때 가장 중요한 몇가지만 뽑아달라고 했더니, "3D..3D..3D?!" 라고 하시더라고요. 그래픽 엔지니어가 아닌데도 3D를 잘 아는 모바일 개발자가 있다면 대단한 능력자겠죠. 그말은 즉슨, 채용 시장에서 찾아뵙기 힘들다는 뜻.

**개발 기간과 비용 또한 만만치 않습니다.** 관련하여 최근에 미국에서 유의미한 자료가 하나 나왔는데요. AR‧VR 앱을 전문적으로 개발하는 Jasoren이란 곳에서 **아마존 AR View 정도의 퀄리티로 AR 앱을 개발한다고 했을 때 소요되는 시간과 비용을 산출** 해 둔 자료 <a href="https://jasoren.com/how-much-does-it-cost-to-build-an-augmented-reality-app-like-amazon-ar-view/" target="_blank" style="color: #0366d6;"> How Much Does it Cost to Build an Augmented Reality App like Amazon AR View? </a> 입니다. 찾아보니 클라이언트만 140여개 정도 되는 꽤 큰 개발사로, 그간 AR 개발 및 세일즈 경험을 토대로 나온 자료가 아닐까 생각됩니다.

<img src="/assets/8_AR/how much.png">

<br>

일단 Jasoren은 AR 앱 개발 과정을 크게 기획 및 디자인, 개발, 테스팅 3단계로 나누었을 때 1800시간 정도 걸린다고 산출했습니다. 국내 법정근로시간(기준+연장)에 따라, 1주일에 52시간씩 일한다고 가정했을 때 **약 9개월에 가까운 시간**입니다. 비용은 어떨까요. 

최대 190,000달러, 현재 한화로 **약 2억 3천만 원 정도의 비용**이 발생하는 것으로 나타납니다.

하지만 개발 비용이 보통 (예상 개발 시간) X (개발자 시급)으로 산출된다고 가정했을 때, 해당 비용은 북미기준 개발자의 시급을 적용했기 때문에 국내 개발자의 인건비를 적용했을 때는 다소 차이가 있을 것으로 보입니다.

또한 원문을 읽어보시면 아시겠지만, "depending on"이라는 단어가 자주 등장합니다. 산출된 시간과 비용이 절대적인 것이 아니라 **어느 정도의 '퀄리티'를 원하느냐에 따라 달라질 수 있는 상대적인 수치임**을 강조하고 있는데요. 여기서는 그 퀄리티의 기준을 아마존 AR View로 잡았다는 것을 감안해서 보시면 좋을 듯 합니다. 만약 AR View 보다 낮은 수준의 앱을 원한다면 시간과 비용이 훨씬 줄어들테고, 그 이상이라면 더 높아질 수 있겠죠.

각각의 프로세스에 대해 조금 더 자세히 살펴보겠습니다.  

<br>
<br>

# AR 개발 3단계

<a style="color: #f12f22;"> **해당 시간과 비용은 절대적인 수치는 아니며, 내부 개발자의 시급, 개발 스콥 및 퀄리티 등 다양한 요소에 따라 가변하는 상대적인 수치임을 다시 한번 말씀 드립니다. 이 점 참고하시어 읽어주시기를 부탁 드립니다. </a>

<img src="/assets/8_AR/ar process.png"> 

## 디자인(Design)

1. 개발 범위 확정
- AR의 기본 기능에 더해 캡처나 영상 녹화 기능, 클라우드 기능, 소셜네트워크 기능 등 내부 개발팀의 역량에 따라 어떤 기능을 어디까지 구현할 것인지 논의합니다.
- iOS용, Android용 또는 둘다 할 것인지 결정합니다.
2. **UX/UI 디자이너 주도로 와이어프레임과 프로토타입 제작** 

평균적으로 디자인 단계에서는 120-200 시간, 비용은 최소 약 2200만원에서 최대 3700만원(19.08.05 환율 기준) 드는 것으로 나타납니다.

<br>

## 개발(Development)

1. 이커머스 앱 개발
: 상품 카테고리, 서치엔진, 결제시스템 등을 갖춘 일반적인 이커머스 앱을 개발하기 위해서 400-500 시간 정도가 소요됩니다.

2. AR 기능 탑재 
: AR SDK를 통해 바닥 인식(World recognition), 모델 감지(Object detection), 3D 모델 상호작용(3D interactions) 등의 기본적인 기능부터 주변 환경의 움직임을 트래킹(Motion tracking)하고, 조도를 측정(Light estimation)하는 등 좀 더 고도화된 기능까지 구축할 수 있습니다.
    
    <style>.embed-container { position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; } .embed-container iframe, .embed-container object, .embed-container embed { position: absolute; top: 0; left: 0; width: 100%; height: 100%; }</style><div class='embed-container'><iframe src='https://www.youtube.com/embed//-_uafeGlerw' frameborder='0' allowfullscreen></iframe></div>
    
3. 기타 기능 추가
: AR 앱의 'must-haves'는 아니지만 유저들이 경험할 수 있는 요소들을 좀 더 다양화하기 위해 추가적으로 기능을 개발할 수 있습니다.

개발 단계에서는 대략 1050-1300 시간이 걸리며, 비용은 최소 약 1억 2700만원에서 최대 1억 5800만원(19.08.05 환율 기준)까지 발생합니다.

<br>

## 테스팅(Testing)

1. Functional Testing
: 가장 큰 범주에서 초기 디자인 단계에서 계획했던 기능들이 빠짐없이 구현됐는지 체크합니다.
2. Unit Testing
: 앱의 기능을 가능한 작은 수준으로 쪼개 각각의 유닛이 제대로 작동하는지 점검합니다.
3. Integration Testing
: 유닛을 개별 테스트했을 때는 괜찮았다가 앱 안에서 하나의 기능으로 작동했을 때 문제를 일으킬 수 있습니다. 개별 유닛들이 문제없이 통합되었는지, 요소들 간 상충되는 부분은 없는지 검증합니다. 유닛 간의 궁합을 보는 작업이라고 할 수 있습니다. 
4. Bug Testing
: 이전 단계에서 발견된 버그들이 완벽하게 수정되었는지, 추가적으로 다른 요소들이 영향을 미치지는 않는지 최종적으로 확인합니다. 
5. Beta Testing
: 실제 유저들을 대상으로 테스팅을 진행하면 내부에서 미처 발견하지 못한 문제점을 찾을 수 있기 때문에 더욱 완성도 높은 서비스 런칭이 가능합니다.

테스팅 단계에서는 최대 300시간이 소요되고, 비용은 약 3700만원(19.08.05 환율 기준) 정도로 나옵니다. 

<br>

# +a

작년 초에 저희가 Urbanbase AR이라는 홈인테리어 증강현실 앱(AR View 정도의 퀄리티)을 선보였을 때만해도 미디어에서도 그렇고 업계에서도 그렇고 '정말 신기하다'고 꽤나 호응이 좋았습니다. 그만큼 증강현실 앱이 희소했다는 의미일텐데요. 

확실히 올해 들어오는 파트너십 문의들을 보면, 3D를 실제 공간에 띄어보는 증강현실의 기본 기능+a를 원하는 고객들이 늘어났습니다. AR 서비스에 자주 노출되면서 서비스에 대한 기대 수준이 높아졌다는 뜻이겠죠. 그리고 이러한 기대 수준은 AR 시대에 가까워질수록 점차 더 높아질 것이라 생각합니다. 저희(특히 모바일팀..)는 이 +a를 위해 지금보다 더 열심히 고민하고 개발을 해야겠습니다.

<img src="/assets/8_AR/passion.png">


