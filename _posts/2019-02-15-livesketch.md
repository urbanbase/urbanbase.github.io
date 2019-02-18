---
layout: post
title: "도면을 직접 그리고 바로 3D로 볼 수 있는 Live Sketch"
date: 2019-02-15 16:00:00
tags: livesketch urbanbase floorplan 3D_converting 3D_modeling urbanbasestudio 
---

# Live Sketch (API, Interface)
`Live Sketch`는 사용자가 웹상에서 직접 설계도면을 그리고 아이템들을 배치할 수 있는 Sketch 서비스입니다.

제작된 2D를 실시간으로 3D 변환해서 보여줌으로 사용자의 경험치를 극대화 시킵니다.
기존에는 AutoCAD, SketchUp 등은 일반인들이 다루기 어려워 전문인력 고용을 통해 설계 도면을 제작을 했기에 비용, 시간, 인력의 소비가 불가피 하였습니다.
하지만 `Live Sketch`를 통하여 일반사용자들이 웹상에서 쉽게 도면을 그리고 그대로 3D 모델링 된 건축물을 바로 볼 수 있으며 해당 도면상에 직접 아이템들을 배치하여 인테리어를 계획할 수 있게 되었습니다.

<br>

## Live Sketch Demo <br>

[![live sketch]({{ site.images \| relative_url }}/assets/tech(demo)/2. live sketch.gif)]({{ site.images \| relative_url }}/assets/tech(demo)/2. live sketch.gif)

<h4> Live Sketch를 직접 만나보세요. 
<br>
<a href="http://3d.floorplanner.urbanbase.com.s3-website.ap-northeast-2.amazonaws.com" target="_blank" style="color: #0366d6;"> >Live Sketch 경험 하기</a> </h4>

<br>

## 기술 설명

`Live Sketch`는 사용자가 깊은 전문적인 지식이 없이도 스스로 건축물의 평면도를 그리고 3D화 하여 확인해 볼 수 있는 서비스입니다.

고가의 3D 모델링 툴을 사용하거나 익힐 필요 없이 간단한 조작만으로 건물모델링을 제작, 편집이 가능하여, 집 구조의 프로토타입 제작 시 시간단축에 큰 이점이 있습니다.

3D화 한 오브젝트를 여러 각도로 둘러보고, 임의로 창과 문을 배치해 볼 수 있습니다.
<br>
<br>

## 라이브 스케치 모드

<table style="text-align: left;">
<tbody>
<tr style="width: 220px;">
    <td style="width: 220px;">Edit Mode</td>
    <td>건축물의 2D 평면도를 그리고 문과 창을 배치합니다.</td>
</tr>
<tr>
    <td>View Mode</td>
    <td>작성한 평면도를 바탕으로 3D화 하여 확인합니다.</td>
</tr>
</tbody>
</table>

<br>
### Edit Mode

- Wall Editing

  벽 구조물을 수정합니다.

    <table style="text-align: left;">
    <tbody>
        <tr style="width: 220px;">
        <td style="width: 220px;">벽 생성</td>
        <td>2D 평면도에서 Wall 편집 버튼이 활성화 되어있으면, 빈 공간을 클릭하여 벽생성을 시작하고, 다음 지점을 클릭하여 이어 나가는 방식으로 진행합니다. 종료지점에서 더블클릭을 하여 그리기를 중단합니다.</td>
        </tr>
        <tr>
        <td>벽 이동</td>
        <td>2D평면도 상에 그려진 선이나 점을 클릭하여 컨텍스트 메뉴를 띄운 후, 이동버튼을 누릅니다. 이 때 벽은 마우스에 스냅이 되고, 이동시킬 목표지점에서 클릭을 하면 벽이동이 완료됩니다.</td>
        </tr>
        <tr>
        <td>벽 삭제</td>
        <td>삭제하고자 하는 선이나 점을 클릭하여 컨텍스트 메뉴를 띄운 후, 삭제버튼을 누릅니다.</td>
        </tr>
    </tbody>
    </table>

<br>

- Door/Window Editing

  작성한 벽 위에 문/창문을 배치합니다. 


    <table style="text-align: left;">
    <tbody>
    <tr style="width: 220px;">
        <td style="width: 220px;">문/창문 생성</td>
        <td>2D 평면도에서 작성된 선의 원하는 위치를 더블 클릭하여 문/창문을 생성합니다.</td>
    </tr>
    <tr>
        <td>문/창문 삭제</td>
        <td>삭제하고자 하는 문/창문을 클릭하여 컨텍스트 메뉴의 삭제버튼을 누릅니다.</td>
    </tr>
    </tbody>
    </table>

<br>
<br>

### Camera Control

| 기능  |           입력키           |      보조키      |                 설명                |
|:----:|:-------------------------:|:----------------:|:-----------------------------------:|
| 확대 | 'Zoom in' button          | Mouse wheel Up   | 화면을 확대합니다.                  |
| 축소 | 'Zoom out'button          | Mouse wheel Down | 화면을 축소합니다.                  |
| 원점 | 'Origin'button            | -                | 기본 시작점과 스케일로 회귀 합니다. |
| 원점 | Mouse right button + drag | -                | 뷰 화면을 잡고 뷰를 이동시킵니다.   |


<br>
<br>

<hr>
<br>
<br>
<h3>
Auto Sketch 기술을 보유한 어반베이스는 3D 공간데이터 플랫폼입니다.
</h3>
<br>
<h4>
전 세계의 모든 공간정보를 하나의 플랫폼 안에서 자유롭게 활용할 수 있도록 도면 변환 기술 및 AR, VR 뷰어 기술이 담긴 API와 ADK를 제공하고 있습니다.<br>
<br>
<br>

<Br>


어반베이스 개발자 사이트 <a href="https://developer.urbanbase.com" target="_blank" style="color: #0366d6;"> 자세히 보기</a>
</h4>
<br><br><br>