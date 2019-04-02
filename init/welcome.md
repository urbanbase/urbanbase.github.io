---
layout: post
title: ""
date: 2019-04-02 08:43:59
categories: 
cover:  "/assets/instacode.png"
---


<!-- [![CTO IMAGE]({{ site.images | relative_url }}/assets/images/CTO IMAGE.jpg)]({{ site.images | relative_url }}/assets/images/CTO IMAGE.jpg) <br> -->
<img src="/assets/images/CTO IMAGE.jpg" alt="CTO IMAGE" style="margin: inherit;">

<br>

작년 여름 방현우 CTO가 어반베이스에 막 합류했을 때쯤, 인터뷰를 진행하던 기자분이 어떤 개발자상을 선호하냐고 물었습니다. <br>
“오타쿠 같은 개발자를 원한다”고 하더군요. 읭? 그땐 사실 좀 의아했습니다. <br>
오타쿠라니.. #왕고집 #마이웨이 #사회성부족 etc., 오타쿠에 대한 뭐 이런 진부한 편견을 가지고 있었거든요. <br> 그로부터 수개월이 지나 어느 정도 진열이 정비된 개발팀을 둘러보고 있노라니 방현우 CTO가 어떤 의미로 그런 말을 했는지 이제 좀 이해가 갑니다. 
<br>
<br>

**어반베이스 개발팀에는 오타쿠가 참 많습니다.** <br>
쉴 때 ~~왜 하필~~ 깃허브를 가지고 논다는 머신러닝 개발자도 있고요. 미국에서 열리는 AWS 행사에 꼭 가겠다며 영어회화 학원을 다니기 시작한 API 개발자는 새롭게 배운 표현들을 활용해 만든 한문장들을 ~~굳이~~ 자기 페북에 인증하며 열정을 불태우고 있는 중이죠. 데모용으로 만든 ~~병맛~~ 호러 게임들을 타운홀에서 VR로 시연하던 3D 그래픽 개발자의 얼굴에서 세상 다 가진 것 같은 미소를 보기도 했고요(제 기억에 이분은 잠을 4시간 이상 자본적이 없다고 했..). 회사 출퇴근 서비스앱 UX가 불편하다며 자체 개편 서비스를 만들고는 마주칠 때마다 피드백 달라고 ~~귀찮게~~ 하는 프론트엔드 개발자도 있습니다.
<br>
<br>

**그도 그럴 것이 우리가 다루는 기술과 서비스는 갓글링도 해결해주지 못하는 지대에 꽤 많이 걸쳐 있습니다.** <br> 그래서 어반베이스 개발팀에는 누가 시키지 않아도 자발적으로,남들은 잘 이해 못해도 스스로는 만족스러운, 별거 아닌 듯 보이지만 결국엔 큰 차이를 만들어내는 일들을 끊임없이 고민하고, 실험하며, 창조해내는 오타쿠들이 다수 모여 있죠. 이제 막 세상 밖으로 나온 어반베이스의 작품들은 이런 개발 오타쿠들의 손을 거쳐 한땀한땀 만들어지고 있습니다. 
<br>
<br>

**우리는 더 많은 개발 오타쿠들과 함께 하기를 원합니다.** <br> 
그들의 전문적인 견해를 개발 과정에 적극 반영할 오픈 마인드는 물론이고요, 그들의 배움을 열렬히 지지하고 지원할 준비도 이미 만땅 되어 있답니다. 개발팀별 기술 스택을 하단에 간략하게 곁들입니다. 보시고 “내가 이 구역의 오타쿠인데” 라는 생각이 드신다면 일단 가볍게 문을 두드려 주세요. 설득은 우리의 몫이니까요.  
<br>
<br>

## 어반베이스 ML&CV Team이 사용 중인 기술입니다.
- Python: 사용이 쉬운 멀티 패러다임 프로그래밍 언어로 OpenCV, Tensorflow, Pytorch, Django 등 다양하고 효율적인 패키지를 지원합니다.
- CV: <a href="https://urbanbase.github.io/2019/02/11/autosketch.html" target="_blank" style="color: #0366d6;"> Auto Sketch</a>에서 도면 분석 시, 2D⇢3D 전환을 위한 메타데이터 획득을 위해 사용합니다.
- ML: 사람이 처리해 결과를 얻기 어려운 부분에서 Tensorflow, Pytorch 등을 활용하고 있습니다.
- 딥러닝 프레임워크: Tensorflow와 MXnet를 기본으로 딥러닝 모델 학습을 수행하고 있습니다. 
- TensorFlow: 데이터 흐름 그래프를 사용하는 수치 연산용 오픈소스 소프트웨어 라이브러리로, TensorFlow 코드를 사용하여 데이터에 맞는 딥러닝 모델을 교육하고 있습니다.
- MXNet: 분산시스템 전문가들이 개발한 프레임워크로 분산 시스템(distributed system)을 지원하는 것이 특징입니다. MXNet의 여러가지 데이터 처리 툴을 이용하여 딥러닝 모델 교육 전, 데이터 전처리를 하는데 용이하게 사용하고 있습니다.
- Django: 보안이 우수하고 유지보수가 편리한 웹사이트를 신속하게 개발하도록 도움을 주는 Python 웹 프레임워크로, Django Rest Framwork(DRF)를 사용하여 <a href="https://urbanbase.github.io/2019/03/13/Taker.html" target="_blank" style="color: #0366d6;"> Space Taker</a>의 API를 개발하였습니다.
<br>
<br>
<br>

## 어반베이스 3D Team이 사용 중인 기술입니다.
- Unity Engine: Unity Technologies에서 개발한 크로스 플랫폼 게임 엔진으로 Direct3D, OpenGL, OpenGL ES, WebGL, Metal, Vulkan 등 다양한 그래픽 API를 지원합니다. 현재 Unity 2018.3 버전을 사용하여 개발 중이며, 2019 버전으로 판올림을 준비하고 있습니다.
- C#: 유니티 엔진의 기본 개발 언어인 C#은 기본 로직뿐만 아니라 쉐이더(shader) 코드까지 수정 가능한 유연한 툴입니다. 스크립팅 IDE로는 기본적으로 MonoDevelop과 Visual Studio를 사용할 수 있습니다. 
- Three.js: 웹에서 3D 그래픽을 다룰 수 있도록 WebGL의 기능을 JavaScript 로 만든 라이브러리입니다. JavaScript를 다룰 수 있는 개발자 중에서도 기하학을 이해하는 개발자들이 수월하게 개발할 수 있습니다.
- 절차지향 모델링: 모델링 정보가 정적인 상태의 파일을 부르지 않고, 프로그램 런타임 중에 구조를 생성하는 모델링 방식입니다. 모델링 구조 변경이 자유롭고, 일정 패턴을 반복적으로 생성할 때 공수가 적게 들기 때문에 <a href="https://urbanbase.github.io/2019/02/15/livesketch.html" target="_blank" style="color: #0366d6;"> Live Sketch </a> 개발 시 중요하게 사용하고 있습니다.
<br>
<br>
<br>

## 어반베이스 Mobile Team이 사용 중인 기술입니다.
- Swift: iOS 개발 환경에서 기존의 objective-c 보다 클래스 모듈화, 디자인 패턴 적용 등에 용이해 사용하고 있습니다.
- ARKit/SceneKit: Apple의 AR 프레임워크로 트레킹부터 3D 렌더링까지 아이폰 환경에 최적화된 증강현실 기능을 구현할 수 있도록 합니다. 
- ARCore/Sceneform: Google의 AR 프레임워크로 트레킹부터 3D 렌더링까지 안드로이드 환경에 최적화된 증강현실 기능을 구현할 수 있도록 합니다.
- Kotlin: 안드로이드 공식 언어로 추가되었고, Swift에 많은 영향을 끼쳤다고 알려져 있습니다. Java 대비 개발효율이 높다고 판단되어 주 언어로 사용 중입니다.
- Java: 안드로이드 개발에 보편적으로 사용된 언어이기 때문에 Java를 사용하는 기존 개발사 대상 배포를 위해 부분적으로 사용 중입니다.
<br>
<br>
<br>

## 어반베이스 API Team이 사용 중인 기술입니다.
- node.js: 생산성을 높이기 위해 채택하여 개발 중이며, 싱글쓰레드로 동작하기 때문에 일반적인 웹 서비스에 적합한 서버를 구성하기 좋습니다.
- Meteor: 개발환경을 쉽게 구축할 수 있고, 자바스크립트로만 구성되어 있기 때문에 풀스택 개발자가 빠르게 서비스를 개발할 수 있어 채택하고 있습니다. 
- Mongodb: 문서-지향 데이터베이스이며, Json 형태로 구성하고 있어 JavaScript 기반의 node.js, meteor와 잘 맞아 사용하고 있습니다. 
- AWS: 마이크로 서비스를 위한 서버리스 아키텍처를 채택하여, Amazon API Gateway, AWS Lambda, Amazon S3, Amazon Cognito 등 현재 AWS에서 제공하는 다양한 서비스를 활용하고 있습니다.
- Database: 클라우드에서 관계형 데이터베이스를 간편하게 설정, 운영 및 확장할 수 있는 장점을 활용하고자 현재 Amazon RDS(PostgreSQL)를 사용하고 있습니다.
- Serverless: 대부분의 API는 AWS 인프라로 동작되는데, Serverless를 통해 AWS와 연동이 잘돼 사용하고 있습니다. 
<br>
<br>
<br>

## 어반베이스 UI, UX Team이 사용 중인 기술입니다.
- Vanilla JS: 속도를 보장하면서 라이브러리를 사용하지 않기 때문에 용량이 적고 빠른 개발을 할 수 있습니다.
- Vue.js & ReactJS: 최신 F/E 프레임워크를 이용해 효율적인 디자인 패턴을 구현하고, 스피드한 개발 및 모듈화가 가능해 채택하고 있습니다.
