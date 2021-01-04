---
layout: post
title: "[AR개발 Tip] Sceneform에서 바닥인식 벗어난 곳으로 Node 이동 시키기"
date: 2020-03-06 10:00:00
categories: dev
tags: AR Application
cover: "/assets/20_02_Sceneform/20_02_thumbnail.png"

---

<img src="/assets/20_02_Sceneform/20_02_thumbnail.png" width="" alt="ARSceneform">
<br>

안녕하세요. 어반베이스 안드로이드 개발자 심재원입니다. 어반베이스에서는 ARViewer와 SpaceViewer의 개발을 담당하고 있습니다.

Android AR 개발을 하시는 많은 개발자 분들이 Google의 `ARCore`를 사용해서 개발을 하실거라 생각합니다. 하지만 ARCore를 사용해 완성도 있는 결과를 얻기 위해서는 3D 및 OpenGL, 렌더링 등의 높은 기술력이 뒷받침 되어야 합니다.
<br>
이 때문에 Google에서는 AR 개발에 대한 진입 장벽을 낮추기 위해 `Sceneform` 이라는 멋진 녀석을 제공하고 있습니다. Sceneform을 사용하면 ARCore를 사용할 때보다 좀 더 쉽게 AR 개발을 할 수 있습니다.

이번 포스트에서는 Sceneform을 사용하여 개발하던 중 고객사의 어려운 요구사항이 들어왔고, 그것을 해결했던 노하우를 공유하려 합니다.

<br>
<br>

## Why Use Sceneform?

일단 이 멋진 녀석 Sceneform이 어떤 것인지, 왜 사용하고 있는지 잠시 소개하겠습니다.

- <a href="https://developers.google.com/ar/develop/java/sceneform" target="_blank" style="color: #0366d6;"> Sceneform이란?</a>
- ARCore에서 기술 장벽이 높은 OpenGL을 통해 직접 구현해야하는 렌더링, AR Session 관리 등을 쉽게 처리 할 수 있어서 개발자는 AR 앱 서비스 자체에 집중 할 수 있다.
- 렌더링 엔진인 filament가 포함되어 있어 렌더링 퀄리티가 높다. (Sceneform 1.10.0 이상의 버전에서 적용 됨)

<br>

## 우리가 해결해야 할 문제는 무엇일까요?

고객사가 요구했던 것은 다음과 같았습니다.

<br>

> <h4>"카메라 상에서 멀리 있는 곳까지는 Node가 이동이 되지 않아요. 자연스럽게 멀리까지 이동되게 해주세요."</h4>

<br>

멀리 있는 곳까지 제품을 이동 시켜 달라는 요구사항.. 

AR 기능의 가장 기본은 바닥을 찾는 것입니다. 바닥을 찾아야 그 곳을 기준으로 Node를 배치 할수 있는 것이죠. 

그런데 내가 있는 위치에서 멀리 떨어진 곳은 바닥으로 인식이 안 될 것이고, 따라서 현재 버전에서는 멀리 있는 곳까지 제품이 이동이 되지 않는 것이 일반적입니다. Sceneform의 전제 조건은 바닥으로 인식 된 곳에 Node를 배치 할 수 있는 것인데, 그 전제 조건을 부정해야 하는 경우가 생겨 버린 것입니다.
<br>

하지만 일단 요구사항이 왔으니 검토는 해야만 하는것이 인지상정! 

<br>

### TransformableNode를 살펴보자

현재 우리가 사용중이며 Sceneform에서 가장 많이 사용하는 Node인 `TransformableNode` 를 살펴봅시다.

TransformableNode란? (참조 : <a href="https://developers.google.com/sceneform/reference/com/google/ar/sceneform/ux/TransformableNode" target="_blank" style="color: #0366d6;">구글 개발자 사이트</a> & <a href="https://github.com/google-ar/sceneform-android-sdk/blob/master/sceneformux/ux/src/main/java/com/google/ar/sceneform/ux/BaseTransformableNode.java" target="_blank" style="color: #0366d6;">Sceneform Github</a>)


```java
public TransformableNode(TransformationSystem transformationSystem) {
    super(transformationSystem);

    translationController =
        new TranslationController(this, transformationSystem.getDragRecognizer());
    addTransformationController(translationController);

    scaleController = new ScaleController(this, transformationSystem.getPinchRecognizer());
    addTransformationController(scaleController);

    rotationController = new RotationController(this, transformationSystem.getTwistRecognizer());
    addTransformationController(rotationController);
  }
```

코드를 살펴보면 3개의 컨트롤러를 이용해서 터치 제스처를 등록하고, 해당 컨트롤러에서 Node를 관리하고 있습니다.

<br>

### TranslationController를 살펴보자

Node를 이동시키는 것은 TranslationController이므로 해당 클래스를 상속받아 커스터마이징을 하도록 합시다.

```java
@Override
  public boolean canStartTransformation(DragGesture gesture) {
    Node targetNode = gesture.getTargetNode();
    if (targetNode == null) {
      return false;
    }

    BaseTransformableNode transformableNode = getTransformableNode();
    if (targetNode != transformableNode && !targetNode.isDescendantOf(transformableNode)) {
      return false;
    }

    if (!transformableNode.isSelected() && !transformableNode.select()) {
      return false;
    }

    Vector3 initialForwardInWorld = transformableNode.getForward();
    Node parent = transformableNode.getParent();
    if (parent != null) {
      initialForwardInLocal.set(parent.worldToLocalDirection(initialForwardInWorld));
    } else {
      initialForwardInLocal.set(initialForwardInWorld);
    }

    return true;
  }

  @Override
  public void onContinueTransformation(DragGesture gesture) {
    Scene scene = getTransformableNode().getScene();
    if (scene == null) {
      return;
    }

    Frame frame = ((ArSceneView) scene.getView()).getArFrame();
    if (frame == null) {
      return;
    }

    Camera arCamera = frame.getCamera();
    if (arCamera.getTrackingState() != TrackingState.TRACKING) {
      return;
    }

    Vector3 position = gesture.getPosition();
    List<HitResult> hitResultList = frame.hitTest(position.x, position.y);
    for (int i = 0; i < hitResultList.size(); i++) {
      HitResult hit = hitResultList.get(i);
      Trackable trackable = hit.getTrackable();
      Pose pose = hit.getHitPose();
      if (trackable instanceof Plane) {
        Plane plane = (Plane) trackable;
        if (plane.isPoseInPolygon(pose) && allowedPlaneTypes.contains(plane.getType())) {
          desiredLocalPosition = new Vector3(pose.tx(), pose.ty(), pose.tz());
          desiredLocalRotation = new Quaternion(pose.qx(), pose.qy(), pose.qz(), pose.qw());
          Node parent = getTransformableNode().getParent();
          if (parent != null && desiredLocalPosition != null && desiredLocalRotation != null) {
            desiredLocalPosition = parent.worldToLocalPoint(desiredLocalPosition);
            desiredLocalRotation =
                Quaternion.multiply(
                    parent.getWorldRotation().inverted(),
                    Preconditions.checkNotNull(desiredLocalRotation));
          }

          desiredLocalRotation =
              calculateFinalDesiredLocalRotation(Preconditions.checkNotNull(desiredLocalRotation));
          lastArHitResult = hit;
          break;
        }
      }
    }
  }

  @Override
  public void onEndTransformation(DragGesture gesture) {
    HitResult hitResult = lastArHitResult;
    if (hitResult == null) {
      return;
    }

    if (hitResult.getTrackable().getTrackingState() == TrackingState.TRACKING) {
      AnchorNode anchorNode = getAnchorNodeOrDie();

      Anchor oldAnchor = anchorNode.getAnchor();
      if (oldAnchor != null) {
        oldAnchor.detach();
      }

      Anchor newAnchor = hitResult.createAnchor();

      Vector3 worldPosition = getTransformableNode().getWorldPosition();
      Quaternion worldRotation = getTransformableNode().getWorldRotation();
      Quaternion finalDesiredWorldRotation = worldRotation;

      // Since we change the anchor, we need to update the initialForwardInLocal into the new
      // coordinate space. Local variable for nullness analysis.
      Quaternion desiredLocalRotation = this.desiredLocalRotation;
      if (desiredLocalRotation != null) {
        getTransformableNode().setLocalRotation(desiredLocalRotation);
        finalDesiredWorldRotation = getTransformableNode().getWorldRotation();
      }

      anchorNode.setAnchor(newAnchor);

      // Temporarily set the node to the final world rotation so that we can accurately
      // determine the initialForwardInLocal in the new coordinate space.
      getTransformableNode().setWorldRotation(finalDesiredWorldRotation);
      Vector3 initialForwardInWorld = getTransformableNode().getForward();
      initialForwardInLocal.set(anchorNode.worldToLocalDirection(initialForwardInWorld));

      getTransformableNode().setWorldRotation(worldRotation);
      getTransformableNode().setWorldPosition(worldPosition);
    }

    desiredLocalPosition = Vector3.zero();
    desiredLocalRotation = calculateFinalDesiredLocalRotation(Quaternion.identity());
  }
```

TranslationController에서 이동을 시키는 코드를 확인해 보면

- canStartTransformation : 터치 시점을 기준으로 이동이 시작되는 단계에서 호출
- onContinueTransformation : 드래그 제스처 등을 통해 이동이 진행중인 단계에서 호출
- onEndTransformation : 터치가 끝나는 시점을 기준으로 이동이 마무리되는 단계에서 호출

이렇게 3가지 단계로 Node에 대한 이동 처리를 하는 것으로 보입니다. 

우리의 목적인 **멀리까지 이동하기 위해서는** onContinueTransformation 소스를 분석해야 할 것 같습니다.

<br>

### onContinueTransformation 소스를 분석해보자

코드의 마지막 부분에서 힌트를 발견했는데요.

```java
public void onContinueTransformation(DragGesture gesture) {
  ...
  중략
  ...
  List<HitResult> hitResultList = frame.hitTest(position.x, position.y);
  for (int i = 0; i < hitResultList.size(); i++) {
    HitResult hit = hitResultList.get(i);
    Trackable trackable = hit.getTrackable();
    Pose pose = hit.getHitPose();
    if (trackable instanceof Plane) {
      Plane plane = (Plane) trackable;
      if (plane.isPoseInPolygon(pose) && allowedPlaneTypes.contains(plane.getType())) {
  ...
  중략
}
```

위 부분을 보면 조건문 중에 **"if (plane.isPoseInPolygon(pose) && allowedPlaneTypes.contains(plane.getType()))"** 조건이 보입니다. 아마도 저 "plane.isPoseInPolygon(pose)" 에서 바닥으로 인지한 영역에서만 위치를 할수 있게 제한을 하는 것으로 생각됩니다.

<br>

> *그럼 저 조건만 해제하면 쉽게 처리 되겠네~ 유후~~*

<br>

**조건을 해제하고 확인 해보니!!**

...
<br>
...
<br>
...
<br>

해당 조건을 해제해도 Node가 멀리까지 이동되지는 않았습니다. orz..
<br>
어째서... 도대체 왜...ㅠㅠ 

<img src="/assets/20_02_Sceneform/TT.jpg" width="300px" alt="TT">


<br>

**좀 더 분석해 본 결과!!**

단순히 isPoseInPolygon 조건을 제거 한다고 해도, HitResult의 getHitPose()에서 얻어올수 있는 Pose 값이 제한적이어서 Node의 이동이 되지 않는 것이었습니다.

Frame에서 hitTest(x, y)를 통해 가져오는 HitResult의 경우 바닥 인식을 통해 최소 Trackable(바닥 인식 취소 단위)로 인지가 된 영역에 대해서만 HitResult를 반환 해준다는 것을 확인!!

바닥으로 인식 되지 않은 곳을 터치한 x,y좌표에 대해서 HitResult 값을 가져올 수는 없는지..

<br>

**추가 분석한 결과!**


Sceneform에서 제공해주는 클래스로는 바닥으로 인식한 영역 외에 터치한 x,y 좌표에 대한 HitResult를 가져올 방법이 없다는 사실에 또 한번 좌절... HitResult를 바탕으로 이동을 시켜야 하는데 HitResult를 가져올 수 없다니...ㅠㅠ

<img src="/assets/20_02_Sceneform/interTT.jpeg" width="300px" alt="TT">


>*아... 이렇게 이번 요구사항은 적용할 수 없다고 회신을 보내야 하나.....*

<br>

**아니다.. 여기서 포기 할 순 없어..!!**


<img src="/assets/20_02_Sceneform/inter1.png" width="300px" alt="TT">

<br>

### HitTestResult의 발견

포기하지 않고 HitResult를 임의로 생성할 수 있는 방법이 없는지 찾아보던 중..

**유레카!**

HitTestResult라는 클래스를 발견했습니다.

HitTestResult 데이터는 Node에 한해서는 내가 어디를 선택했는지에 대한 AR상 위치를 가져올 수 있다는 것을 알아냈습니다. (노드의 특정 부분를 터치 했을 때 AR상 그 위치를 가져올 수 있음)
Scene에서 터치한 곳에 해당되는 노드를 가져올 수 있는 기능을 활용해서 노드를 찾고 HitTestResult 에서 RayHit을 통해 Point 와 Distance 등을 가져올 수 있습니다.

Scene.hitTestAll (참조 : <a href="https://developers.google.com/sceneform/reference/com/google/ar/sceneform/Scene#hitTestAll(android.view.MotionEvent)" target="_blank" style="color: #0366d6;">구글 개발자 사이트</a> & <a href="https://github.com/google-ar/sceneform-android-sdk/blob/master/sceneformsrc/sceneform/src/main/java/com/google/ar/sceneform/Scene.java" target="_blank" style="color: #0366d6;">Sceneform Github</a>)
<br>

이 데이터를 활용하는 것으로 방식을 변경하기로 합니다.

임의의 Node를 바닥과 가깝게 깔아놓고 그 Node상에서 내가 터치한 좌표에 모션이벤트를 발생시켜서 해당 좌표를 가져올 수 있습니다.
<br>
쉽게 말하면 Sceneform에서 제공해주는 Plane을 이용한 바닥 정보를 사용하지 않고 바닥에 넓은 Node를 하나 놓아 두고 그 바닥용 Node 위에서만 이동할 수 있도록 처리하는 것입니다.

이렇게 하면 내가 바닥에 둔 바닥용 Node 안에서는 멀리까지도 각 제품 별 Node를 자연스럽게 이동할 수 있습니다.

<br>

### 바닥 노드 이용한 거리 좌표 계산 소스

```java
public void onContinueTransformation(DragGesture gesture) {
  ...
  중략
  ...
// Scene 에서 강제로 터치 좌표에 모션 이벤트를 발생 시킨다.
  List<HitTestResult> testResultList = scene.hitTestAll(MotionEvent.obtain(SystemClock.uptimeMillis(), SystemClock.uptimeMillis(), MotionEvent.ACTION_DOWN, position.x, position.y, 0));
  for (HitTestResult testResult : testResultList) {
      Log.e("TAG", "HitTestResult [" + testResult.getNode().getName() + "]. point : " + testResult.getPoint().toString() + ", distance : " + testResult.getDistance());
      if (testResult.getNode() != null && testResult.getNode().getName().equals("AnchorNode")) {  // 터치한 좌표에 해당되는 바닥노드를 찾는다
          Log.e("TAG", "HitTestResult [" + testResult.getNode().getName() + "]. node size : " + ((Box) testResult.getNode().getCollisionShape()).getSize().toString());
          // 바닥노드에 터치한 좌표를 바탕으로 이동할 Pose를 생성
          mInfinityMovePose = Pose.makeTranslation(testResult.getPoint().x, testResult.getPoint().y, testResult.getPoint().z);
          setPoseAndRotation(mInfinityMovePose);
      }
  }
  ...
  중략
}
```

<br>

이번 요구사항은 위와 같은 방식을 이용해서 직경 50미터짜리 바닥 노드를 생성해 바닥인식의 한계를 조금 우회해서 요구사항을 맞출 수 있었습니다.

<br>

단 위와 같은 방식에도 단점은 존재합니다.

- 바닥에 놓아두는 Node(이하 바닥Node)를 구분 할수 있는 유니크한 이름으로 줄 것
- 바닥Node를 AR 상에 위치시키기 위해 최초 1회 이상은 정확한 바닥인식을 통해 바닥 Node를 배치 해야 한다는 점 (정확하지 않은 바닥Node가 배치되면 제품 Node 또한 정확한 위치를 할 수 없습니다.)
- 바닥Node와 실제 바닥의 Anchor가 다를 수 있다는 점 (실제 바닥은 지속적으로 트래킹을 통해 업데이트를 하고 있으나 바닥에 위치한 Node는 업데이트 하지 못합니다.)
- 바닥Node의 크기를 임의로 정해야 한다는 점에서 실내의 경우 정확하지 않을 수 있는 점

<br>
<br>

## 끝내며

아직 AR 기능은 바닥 인식이나 기타 여러가지 면에서 조금 부정확한 건 사실입니다.
저와 같이 Sceneform을 사용하는 개발자들 모두 Sceneform 자체의 커스터마이징의 한계 때문에 여러 좌절을 겪어 보았을 것입니다. 시간이 지나면 Sceneform도 많은 기능을 커스터마이징 할 수 있게 풀어 주지 않을까... 하는 작은 소망이 있습니다.

기술이 발전해서 카메라로 완벽한 바닥인식 및 거리인식을 하게 되는 날이 오면 AR 기능의 활용도는 정말 무궁무진 해 질겁니다. 그런 날이 오겠죠... AR 개발이 지금보다 무척 재미 있어 질 그런 날이...
