---
layout: post
title: "롯데하이마트 앱은 AR을 어떻게 도입하게 됐을까?"
date: 2020-12-17 10:00:00
categories: dev
tags: AR Application
cover: "/assets/20_12_Himart_ARViewer/20_12_thumbnail.png"
---

<img src="/assets/20_12_Himart_ARViewer/20_12_thumbnail.png" width="" alt="Himart_ARViewer">

안녕하세요. 어반베이스 어플리케이션팀 팀장 이우석입니다. 
저희팀은 AR뷰어(AR Viewer)를 개발하고 있는데요, AR Viewer는 Android와 iOS에서 AR(증강현실)을 쉽게 적용하고 사용할 수 있도록 만든 어반베이스의 SDK(Software Development Kit)입니다. 요즘 코로나로 인해 비대면/언택트 기술 도입에 대한 관심이 그 어느 때보다 높은 만큼 롯데하이마트가 어반베이스의 AR Viewer를 사용해 자사 앱에 AR 서비스를 적용한 과정을 소개하려 합니다.

---

## 개발 과정

롯데하이마트 AR 프로젝트는 두달 반 정도 걸렸고, 아래와 같은 단계로 업무를 나누어 진행했습니다. 

<div style="text-align : center;">
<img width="650" src="https://ub-mobile.s3.ap-northeast-2.amazonaws.com/github_blog/arviewer_process.jpg" alt="arviewer_process">
</div>

가장 먼저 **요구사항을 정의** 하였습니다.

프로젝트 기획서를 검토하면서 어떤 AR 기능을 넣을 것인지 화면은 어떻게 구성할 것인지 이야기합니다. 그 후에는 어떤 기능이 개발돼야 하고, 어느 정도 일정이 필요한지 산정합니다. 이렇게 논의를 진행하다보면 항상 궁금해 하시는 부분이 있습니다.


## <center><b><q>그럼 저희는 어떤 부분을 개발하면 되나요?</q></b></center>

이 과정이 **역할분담**입니다. 
AR Viewer는 여타 SDK처럼 UI를 제외한 기능을 제공하고 있습니다. 따라서 UI 개발은 앱 파트에서 개발을 해야 하는데요.

<div style="text-align : center;">
<img width="450" src="https://ub-mobile.s3.ap-northeast-2.amazonaws.com/github_blog/airbnb-himart-caption.jpg" alt="airbnb-himart-caption">
</div>


왼쪽에 에어비앤비 앱은 숙소를 검색하는 화면에서 Google Map SDK 를 사용하고 있습니다. 구글맵은 지도를 특정 위치에 포커싱하는 기능이나 지도 확대와 같은 기본 기능들만 지원해주고 그 외의 UI(녹색 테두리)는 에어비앤비 브랜드 톤에 맞게 구현을 했습니다.

오른쪽을 보시면, 롯데하이마트도 마찬가지로 제품 로드, 회전, 확대 등의 기본적인 AR 기능은 어반베이스의 AR Viewer SDK에서 지원받고, 이외 UI(하늘색 테두리)는 하이마트 측에서 구현했습니다.

```swift
@IBAction func captureSelected(_ sender: UIButton) {
    let capturedImage = arViewer.captureScene()
    UIImageWriteToSavedPhotosAlbum(capturedImage, self, #selector(image(_:didFinishSaving:contextInfo:)), nil)
}
```
<center>< 앱에서 캡쳐 기능을 구현한 예 ></center>


<br>

이렇게 역할 분담이 끝나면, 요구사항에 논의된 기능을 개발하고 약속된 개발 기간을 거쳐 SDK를 적용한 뒤 테스트를 진행합니다.




---



## 많고 많은 AR SDK 중, 왜 어반베이스 AR 이었을까?

이전 글에도 언급한 적이 있지만 AR을 개발한다는 것은 그리 단순한 과정이 아닙니다. 모바일 개발 환경에 익숙해야함은 물론이고 3D 환경에 대한 전반적인 지식이 필요하기 때문입니다. 뿐만 아니라 제품을 3D 파일로 제작해야 하는데 플랫폼(Android, iOS)에 따라 다른 포맷으로 관리가 되어야 하며 그에 따른 속성 관리에도 깊은 이해와 시행착오를 수반합니다.

많은 기업들이 AR을 처음부터 개발하기 보다는 기존에 나와 있는 SDK를 활용해 AR 서비스를 출시하는 이유일 겁니다. 사실 AR SDK는 유니티(AR Foundation), PTC(Vuforia) 등 내로라하는 기업들에서 이미 개발 및 운영을 하고 있습니다. 저희 어플리케이션팀에서는 이들과는 차별화된 기능을 중점적으로 개발하고 있고 롯데하이마트에서도 그 기능들을 높게 평가한 것으로 보입니다. 

### 간편한 적용
유니티의 AR Foundation은 모바일 개발환경이 아닌 유니티에서 AR 기능을 개발해야 한다는 단점이 있습니다. 그냥 간단하게 앱에 AR 기능을 넣고 싶었을 뿐인데 유니티 개발자가 필요한 상황이 생기는거죠.더군다나 유니티로 개발된 AR 기능은 `.xcodeproj` 같은 프로젝트 파일 형태로 생성되기 때문에 이미 개발하고 있는 앱이 있을 경우에 앱에 있는 코드를 유니티에서 생성된 `.xcodeproj` 에 옮겨야 하는 엄청난 작업을 진행해야 합니다.

그렇다면 PTC의 Vuforia는 어떨까요?
다행히 AR Foundation처럼 유니티에서 개발해야 할 필요는 없습니다만 샘플코드를 받아서 열어보니... 카메라 키고 제품 하나 배치하는 단순한 내용인데도 참조해야 할 파일들이 이렇게나 많습니다.

<div style="text-align : center;">
<img height="700" src="https://ub-mobile.s3.ap-northeast-2.amazonaws.com/github_blog/vuforia-project.jpg" alt="vuforia-project">
</div>

반면, AR Viewer는 정말 이게 끝입니다.
<div style="text-align : center;">
<img height="500" src="https://ub-mobile.s3.ap-northeast-2.amazonaws.com/github_blog/arviewer-project.jpg" alt="arviewer-project">
</div>




### 직관적이고 쉬운 기능

AR Foundation이나 Vuforia를 프로젝트에 적용하고 나면 제품을 어떻게 보여주고 동작할 것인지 구현을 해야합니다. 구현 과정에서 필연적으로 3D 관련 지식이 필요하고 적지 않은 러닝커브가 발생하게 됩니다. AR Viewer는 커머스(commerce)에 특화된 기능들이 이미 구현되어 있고, 구현된 기능을 직관적인 클래스 구조와 함수로 제공하고 있습니다.

아래는 AR Viewer iOS 버전의 기능을 간략하게 뽑은 내용입니다. 함수명과 파라미터가 간단하고 직관적이어서 AR에 대한 지식 없이도 원하는 기능을 쉽게 구현할 수 있습니다.

```swift
/// A Boolean value indicating whether the current device supports ARViewer.
public static func isSupported() -> Bool

/// Requests that the `Figure` to be loaded.
/// - Parameters:
///   - assetId: `asset_id` of `UBAsset`
public func requestFigure(assetId: String = ARViewer.kSampleAssetId)

/// Cancels the `Figure` being loaded.
public func requestCancel()

/// Sets that determines whether the light casts shadows.
/// - Parameter enabled: if the value of this property is `true`, set to enable shadows. The default value is `true`
public func setShadow(enabled: Bool)

/// Removes the figure from the scene.
/// - Parameter figure: The figure to remove.
public func deleteFigure(figure: ARViewer.Figure)

/// Sets the feature type
/// - Parameter feature: The type of feature which you want to use
public func setViewerMode(feature: ARViewer.FeatureType)

```



---



## 롯데하이마트에 적용된 AR 핵심 기능들

롯데하이마트 앱의 AR 서비스에는 AR Viewer의 어떤 기능이 적용되었을까요?
세세한 기능까지 다 말씀드리면 너무 TMI가 될 것 같아 굵직한 기능들만 소개해 드립니다.



### 360도 돌려보기

제품을 AR로 배치하기 전에 360도 돌려보기를 체험할 수 있습니다. 한 사이드에서만 볼 수 있었던 모양이나 색상을 다양한 각도로 돌려보며 디테일하게 확인해볼 수 있습니다.

<div style="text-align : center;">
<img width="200" src="https://ub-mobile.s3.ap-northeast-2.amazonaws.com/github_blog/himart_3dviewer.gif" alt="himart_3dviewer">
</div>


### 제품 사이즈 보여주기

제품의 실측 사이즈를 바로 확인할 수 있습니다. 텍스트로 제품의 가로-세로-너비를 수치로 확인하는 것보다 AR 환경에서 사이즈를 확인하는 것이 훨씬 더 직관적입니다. 왠만한 사람들은 17cm가 어느 정도로 짧은지 47cm가 어느 정도 길이인지 잘 가늠하지 못하니까요.

<div style="text-align : center;">
<img width="200" src="https://ub-mobile.s3.ap-northeast-2.amazonaws.com/github_blog/himart_size.jpg" alt="himart_size">
</div>


### AR로 배치하기

마음에 드는 제품을 우리집 공간에 놓아볼 수 있습니다. 지금 공간과 제품의 디자인이 어울리는지 공간에 비해 제품이 큰지 작은지 등을 가늠해볼 수 있어요. 제품을 배치한 후에도 자유자재로 움직이며 조작 가능합니다.

<div style="text-align : center;">
<img width="200" src="https://ub-mobile.s3.ap-northeast-2.amazonaws.com/github_blog/himart_translation.gif" alt="himart_translation">
</div>




### 옵션 변경하기

컬러 등 제품의 스타일을 바꿔보면서 확인할 수 있습니다. 벽지 디자인에 실버가 어울리는지 골드가 어울리는지 바로 바로 확인이 가능합니다.

<div style="text-align : center;">
<img width="200" src="https://ub-mobile.s3.ap-northeast-2.amazonaws.com/github_blog/himart_option.gif" alt="himart_option">
</div>





---



## 마치며

AR을 개발하는 과정이 아직까지 순조롭지만은 않습니다. SDK와는 별개로 AR 기능 구현 수준이 완벽하게 올라오지 않았고, <a href="https://developers.google.com/ar" target="_blank" style="color: #0366d6;"> ARCore</a>와 <a href="https://developer.apple.com/kr/augmented-reality/" target="_blank" style="color: #0366d6;"> ARKit</a>의 기능 지원에도 차이가 있다보니 분명 같은 제품인데도 안드로이드 앱과 아이폰 앱에서 똑같은 색상으로 보이지 않는 일도 있습니다. AR 기능을 사용하면 핸드폰이 뜨거워지기도 하고요.

하지만 AR은 가장 주목받고 있는 언택트 기술인만큼 구글, 애플 등 글로벌 기업들이 앞다투어 AR 기술과 하드웨어 디바이스를 업데이트 하고 있고, 저희 어플리케이션팀 또한 AR Viwer 고도화에 박차를 가하고 있습니다. 머지않아 AR이 인공지능과 더불어 4차 산업의 주요 트렌드로 자리잡을 것으로 보이니 아직 도입을 망설이고 있다면 빨리 로켓에 올라 타시기를 바랍니다.

마지막으로 AR Viewer 못지 않게 열심히 개발하고 있는 <a href="https://urbanar.page.link/wBJq" target="_blank" style="color: #0366d6;"> Urbanbase AR</a> 앱도 많은 관심 부탁드립니다.
감사합니다!





