---
layout: post
title: "모폴로지 변환 활용해 도면의 벽 검출하기"
date: 2019-06-03 00:00:00
categories: dev
tags: ComputerVision OpenCV 모폴로지변환 morphological_transformations 

---

안녕하세요. Computer Vision 개발자 윤대희입니다. 
<br>
어반베이스에서는 실시간으로 2D 도면을 3차원으로 자동 변환해주는 AutoSketch 개발을 담당하고 있습니다.




[![autosketch]({{ site.images | relative_url }}/assets/tech(demo)/1. auto sketch (thumbnail).png)](https://youtu.be/sHmokPYTp2Q){:target="_blank"}
**<center>이미지를 클릭하면 영상으로 이동합니다.</center>**

<br>
<br>

이번 글에서는 **모폴로지 변환(morphological transformations)을 활용해 도면의 벽을 검출하는 방식**을 설명해보고자 합니다. 이미지는 일반적으로 `래스터 그래픽스(Raster graphics)*` 이미지 인데요,  래스터 그래픽스 이미지에는 수많은 노이즈(noise)가 포함되어 있습니다. 노이즈는 객체의 검출을 방해하는 주된 요소이기 때문에 노이즈를 최소화하거나 제거하는 것이 검출의 핵심입니다. 



<h5> *래스터 그래픽스 : 비트맵 이미지로, 격자판의 형태로 각 격자마다 화소의 데이터가 담겨있는 이미지 파일 포맷</h5>

도면에는 수많은 정보가 포함되어 있어 검출하려는 벽 이외의 정보는 모두 노이즈가 됩니다. <그림 1.1>을 통해 벽 검출을 방해하는 다양한 요소들을 볼 수 있습니다.

<figure>
    <img data-action="zoom" src='{{ "/assets/5_morphology transformation/1.png" | relative_url }}' alt='<그림 1.1>'>
    <figcaption><center> <그림 1.1> 벽의 검출을 방해하는 요소들 </center>
    </figcaption>
</figure>


문, 창문, 세면대를 비롯해 워터마크나 방을 표시하는 그라데이션 등이 모두 벽의 검출을 방해하는 요소들입니다. 이 모든 요소를 제거하고 이상적인 벽의 데이터만 출력하기 위해 많은 방식들 중 모폴로지 변환을 사용합니다. 도면에서 벽이 검출되기까지 일련의 과정을 보시면 아래와 같습니다.

<br>
<br>

<figure>
    <img data-action="zoom" src='{{ "/assets/5_morphology transformation/2.png" | relative_url }}' alt='<그림 1.2>'>
    <figcaption><center> <그림 1.2> 도면 원본 이미지 </center>
    </figcaption>
</figure>

<br>
<br>

<figure>
    <img data-action="zoom" src='{{ "/assets/5_morphology transformation/3.png" | relative_url }}' alt='<그림 1.3>'>
    <figcaption><center> <그림 1.3> 기본적인 전처리 과정 진행 </center>
    </figcaption>
</figure>

<br>
<br>

<figure>
    <img data-action="zoom" src='{{ "/assets/5_morphology transformation/4.png" | relative_url }}' alt='<그림 1.4>'>
    <figcaption><center> <그림 1.4> 모폴로지 변환을 적용해 노이즈 제거 </center>
    </figcaption>
</figure>

<br>
<br>

<그림 1.2>는 원본 도면 이미지이며, <그림 1.3>은 기본적인 전처리가 적용된 이미지, 마지막으로 <그림 1.4>는 전처리가 진행된 이미지에서 문자, 도면 기호, 노이즈 등 불필요한 요소들을 제거한 결과 이미지입니다. 모폴로지 변환은 이렇게 노이즈를 제거하는 용도로 주로 활용됩니다. 이제부터 모폴로지 변환에 대해 알아보도록 하겠습니다.

<br>
<br>

## 모폴로지 변환

모폴로지 변환은 동영상이나 이미지를 형태학적 관점에서 접근하는 기법으로 영상 내의 픽셀값을 대체하기 위해 사용합니다. **전처리 또는 후처리 과정에서 가장 많이 사용되는 연산**이며, 집합의 포함 관계나 이동(translation), 대칭(reflection), 여집합(complement), 차집합(difference) 등을 적용해 **노이즈 제거, 그라디언트(gradient) 정의, 요소 결합 및 분리, 강도 피크(peaks) 검출 등에 활용**합니다.

<br>

기본적인 모폴로지 변환으로는 팽창 연산과 침식 연산이 있습니다. 팽창 연산과 침식 연산은 이미지와 `커널(kernel)*` 간 컨볼루션(convolution) 연산이며, 이 두 가지 기본 연산을 기반으로 복잡하고 다양한 모폴로지 연산을 구현할 수 있습니다.

<h5> *커널 (kernel) : 이미지에서 (x, y)의 픽셀과 해당 픽셀 주변을 포함한 작은 크기의 공간 </h5>

<br>
<br>

### 팽창(dilation)

팽창 연산은 커널 영역 안에 존재하는 모든 픽셀 값을 커널 내부의 **극댓값(local maximum)으로 대체**합니다. 즉, 구조 요소(element)를 활용해 현재 픽셀에서 이웃한 픽셀을 최대 픽셀값으로 대체하는 것인데요, 팽창 연산을 적용하면 어두운 영역은 줄어들고 밝은 영역은 늘어납니다. 

커널의 크기나 반복 횟수에 따라 밝은 영역이 늘어나 스펙클(speckle)이 커지며 객체 내부의 홀(holes)이 사라지는 특징이 있습니다. **노이즈 제거 후 줄어든 크기를 복구하고자 할 때 주로 사용하는 연산**입니다. 수식으로 표현한다면 다음과 같습니다.

![팽창 수식]({{ site.images | relative_url }}/assets/5_morphology transformation/math1.png)

<br>

### 침식(erosion)

반면, 침식 연산은 커널 영역 안에 존재하는 모든 픽셀의 값을 커널 내부의 **극솟값(local minimum)으로 대체**합니다. 즉, 구조 요소(element)를 활용해 현재 픽셀에서 이웃한 픽셀을 최소 픽셀값으로 대체하게 됩니다. 침식 연산을 적용하면 밝은 영역은 줄어들고 어두운 영역은 늘어납니다.

커널의 크기나 반복 횟수에 따라 어두운 영역이 늘어나 스펙클(speckle)이 사라지며 객체 내부의 홀(holes)이 커집니다. 이 연산은 **노이즈 제거에 주로 사용**되며, 아래와 같은 수식으로 표현합니다.

![침식 수식]({{ site.images | relative_url }}/assets/5_morphology transformation/math2.png)

<br>

### 구조 요소

모폴로지 연산은 커널의 영향을 크게 받으며 커널의 형태에 따라 결과가 달라지는데요, 커널보다 더 복잡한 개념인 **구조 요소(element)를 사용해 커널의 형태(shape)를 설정**할 수 있습니다. 커널은 n×n 크기의 직사각형 또는 정사각형 구조로만 활용하지만 **구조 요소는 직사각형을 비롯해 타원, 십자 모양의 형태로도 활용이 가능**합니다. 모폴로지 변환은 이 구조 요소를 사용해 수행합니다. Python OpenCV에서 활용되는 구조 요소 생성 함수는 다음과 같습니다. 

<br>

**Python OpenCV 구조 요소 생성 함수**

{% highlight python %}

kernel = cv2.getStructuringElement(
    shape,
    ksize,
    anchor = None
)

{% endhighlight %}

<br>

구조 요소 생성 함수는 커널의 형태를 설정할 수 있으며, 직사각형(Rect), 십자가(Cross), 타원(Ellipse)의 모양으로 구조화 요소를 생성합니다. 커널의 크기는 ksize 매개변수를 통해 설정하고, anchor 매개변수는 커널을 정렬하는 방식을 지정해 고정점(anchor)을 설정합니다. 커널의 크기가 작을 경우 구조 요소의 모양에 영향을 받지 않습니다. 커널의 크기가 작으면, 십자가 구조와 타원 구조를 비교했을 때 서로 형태가 동일해진다고 보면 됩니다. 

또한, 고정점은 필수 매개변수가 아닙니다. 미리 고정점의 위치를 할당하지 않고 모폴로지 함수에서 고정점의 위치를 할당할 수 있습니다. 고정점을 할당하지 않는다면 조금 더 유동적인 커널을 생성할 수 있습니다. <표 1.1>은 커널의 형태에 대한 플래그를 정리한 것입니다.


![표 1.1]({{ site.images | relative_url }}/assets/5_morphology transformation/table.png)

<center> 
<표 1.1> 구조 요소 생성 함수 중 커널의 형태에 따른 플래그
</center>

<br>
<br>

이제 팽창과 침식 변환 함수를 알아보도록 하겠습니다. 다음 함수는 Python OpenCV에서 활용되는 팽창 함수와 침식 함수입니다.

**Python OpenCV의 팽창 함수**

{% highlight python %}

dst = cv2.dilate(
    src,
    kernel,
    anchor=None,
    iterations=None,
    borderType=None,
    borderValue=None
)

{% endhighlight %}

<br>

**Python OpenCV의 침식 함수**

{% highlight python %}

dst = cv2.erode(
    src,
    kernel,
    anchor=None,
    iterations=None,
    borderType=None,
    borderValue=None
)

{% endhighlight %}

<br>

모폴로지 변환의 팽창과 침식 함수는 동일한 매개변수의 형태를 사용합니다. 입력 이미지(src)에 구조 요소를 사용해 팽창 또는 침식을 사용하는데, 고정점을 함수 내에서 할당할 수 있으며, 반복 횟수(iterations)를 설정해 해당 함수가 몇 회 연산할지 선택합니다. 모폴로지 변환 함수는 커널을 활용하므로 컨볼루션 연산처럼 테두리 외삽법(borderMode)과 테두리 색상(borderValue)을 설정할 수 있습니다.

<br>
<br>
<br>

## 모폴로지 연산

모폴로지 연산은 모폴로지 변환의 팽창과 침식을 기본 연산으로 사용해 고급 형태학을 적용하는 변환 연산입니다. **입력 이미지가 이진화된 이미지라면 팽창과 침식 연산으로도 우수한 결과를 얻을 수 있지만 그레이스케일이나 다중 채널 이미지를 사용하는 경우 더 복잡한 연산을 필요로 합니다.** 이때 모폴로지 연산을 활용한다면 좀 더 나은 결과를 얻을 수 있습니다. 먼저 모폴로지 연산 함수부터 알아보도록 하겠습니다.

<br>

**Python OpenCV의 모폴로지 연산 함수**

{% highlight python %}

dst = cv2.morphologyEx(
    src,
    op,
    kernel,
    anchor=None,
    iterations=None,
    borderType=None,
    borderValue=None
)

{% endhighlight %}

모폴로지 연산 함수는 모폴로지 변환의 팽창, 침식 함수와 형태가 흡사합니다. 모폴로지 변환에 기반을 두고 있기 때문에 커널, 반복횟수, 테두리 외삽법, 테두리 색상을 동일하게 사용합니다. 하지만 **모폴로지 연산 함수는 연산자(op)라는 매개변수를 추가로 사용하는데요. 연산자는 모폴로지 변환 함수를 조합해서 변환할 복합 연산 방식을 의미**합니다. 모폴로지 연산 함수는 다양한 방식으로 모폴로지 연산을 활용할 수 있습니다. 모폴로지 연산 함수는 총 여덟 가지의 모폴로지 변환을 지원하는데, 이번 글에서는 가장 많이 쓰이는 열림(open)과 닫힘(close) 연산에 대해서만 소개드리고자 합니다.

<br>
<br>

### 열림(Opening)

열림 연산은 팽창 연산자와 **침식 연산자의 조합으로 침식 연산을 적용한 후 팽창 연산을 적용**하는 방식입니다. 열림 연산을 적용하면 침식 연산으로 인해 밝은 영역이 줄어들고 어두운 영역이 늘어나는데, 줄어든 영역을 다시 복구하기 위해 팽창 연산을 적용하면 반대로 어두운 영역이 줄어들고 밝은 영역이 늘어납니다. 그 결과, **스펙클(speckle)이 사라지면서 줄어든 객체의 크기를 본래의 사이즈로 복구**할 수 있습니다. 다음 수식은 열림 연산을 나타냅니다.
<h3>
<center>
dst = dilate(erode(src))
</center>
</h3>

<그림 1.5>를 통해 전처리가 진행된 이미지(그림 1.3)에서 열림 연산을 적용한 결과를 볼 수 있습니다.

<figure>
    <img data-action="zoom" src='{{ "/assets/5_morphology transformation/5.png" | relative_url }}' alt='<그림 1.5>'>
    <figcaption><center> <그림 1.5> 3x3 직사각형 구조, 반복횟수 3회를 적용한 열림 연산 </center>
    </figcaption>
</figure>

<br>
<br>

### 닫힘(Closing)
닫힘 연산은 열림 연산과 동일하게 팽창 연산자와 침식 연산자의 조합입니다. 열림과 반대로 **팽창 연산을 적용한 다음, 침식 연산을 적용**한다는 점이 다릅니다. 닫힘 연산은 팽창 연산으로 인해 어두운 영역이 줄어들고 밝은 영역이 늘어나게 되는데, 늘어난 영역을 다시 복구하기 위해 침식 연산을 적용하면 밝은 영역이 줄어들고 어두운 영역이 늘어납니다. 그 결과로 **객체 내부의 홀(holes)이 사라지면서 증가한 크기를 본래의 사이즈로 복구**할 수 있습니다.  다음 수식은 닫힘 연산을 나타냅니다.

<h3>
<center>
dst = erode(dilate(src))
</center>
</h3>


<figure>
    <img data-action="zoom" src='{{ "/assets/5_morphology transformation/6.png" | relative_url }}' alt='<그림 1.6>'>
    <figcaption><center> <그림 1.6> 3x3 직사각형 구조, 반복횟수 3회를 적용한 닫힘 연산 </center>
    </figcaption>
</figure>

<br>
<br>


모폴로지 연산을 통해 노이즈를 제거하고 벽을 검출했습니다. 하지만 비교적 크기가 큰 노이즈는 남게 되는데요, 이런 노이즈도 간단한 후처리가 진행되면 <그림 1.4>와 같이 벽만 남길 수 있습니다. 다음 코드는 모폴로지 변환의 사용법을 보여줍니다.

**함수 사용 예시**

{% highlight python %}

src = cv2.imread("img.jpg", 0)


kernel = cv2.getStructuringElement(cv2.MORPH_RECT, (3, 3))
dst = cv2.morphologyEx(dst, cv2.MORPH_CLOSE, kernel, iterations=3)

{% endhighlight %}

<br>
<br>

마지막으로, 모폴로지 변환을 사용하는 경우 알아두면 좋은 점과 주의할 점 몇 가지를 알려드리면서 글을 마치려고 합니다. 

첫째, 모폴로지 변환은 커널의 크기나 구조 요소의 모양에 따라 결과값이 크게 달라집니다. 이때 **변수의 이름에 해당 구조요소가 가진 특성을 표시하거나 매개변수의 할당을 변수 값으로 대체**한다면 좀 더 유동적인 함수나 읽기 쉬운 코드로 구현할 수 있습니다.

둘째, 커널의 크기가 짝수일 경우, 원본 이미지와 약간 틀어진 이미지로 반환됩니다. 이는 고정점의 크기를 중심점에 할당할 수 없어서 발생하는 문제인데요, **특별한 경우 혹은 틀어진 이미지를 보정하는 후처리 과정이 없다면 가능한 홀수의 커널 크기를 사용**하시길 바랍니다.

또한, 모폴로지 열림 연산이나 닫힘 연산은 팽창과 침식 연산을 활용한 연산임을 기억해야 합니다. 즉, 열림 연산을 10회 반복하게 구성한 결과와 반복문을 통해 침식 연산과 팽창 연산을 10회 적용한 결과는 서로 다르다는 의미인데요. 열림 연산의 수식은 dilate(erode(src))입니다. 그러므로 열림 연산을 10회 적용한 의미는 침식, 침식, 침식, … 팽창, 팽창, 팽창입니다. (닫힘 연산도 동일) 단, 1회 연산의 경우 동일한 결과를 얻어냅니다. 쉬운 개념이지만 가끔 헷갈릴 수 있으니 주의해서 활용하신다면 좋을 것 같습니다. 
