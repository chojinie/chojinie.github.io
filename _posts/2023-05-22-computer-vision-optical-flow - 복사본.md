---
layout: post
title: Computer Vision_Optical Flow
date: 2023-05-22
description: recording_a Summary of a optical flow
tags: optical_flow computer_vision Lucas_Kanade Horn_schunck
categories: study
related_posts: false
toc:
  sidebar: left
---

석사 과정 재학 중, 공부한 내용을 기록하기 위해 작성하는 글입니다.


## Optical Flow의 정의


옵티컬 플로우는 관찰자와 장면 간의 상대적인 움직임으로 인해 발생하는 물체, 표면 및 가장자리의 두드러지는 움직임 패턴을 의미합니다. 제가 이해한대로 표현하자면 영상(Image)이나 동영상에서 사물이나 배경의 움직임을 표현하는 기법인데, 사물이나 광원이 움직일 경우 인간의 눈에는 어떠한 흐름의 크기와 방향성이 느껴집니다. 이를 수식적으로 표현하는 것을 뜻합니다.


## Motion Field와 Optical Flow


### Motion Field
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of1.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of2.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

$$ \mathrm{v}_i $$(Image velocity) 와 $$ \mathrm{v}_o $$(Seen velocity) 간의 관계를 나타내는 것이 초기 목적입니다. 
$$ \Rightarrow $$ Perspective projection을 이용하게 됩니다.

$$ \mathrm{v}_i $$ 나 Motion Field를 측정할 수 없으므로 Brightness Pattern을 측정할 수 밖에 없습니다.
연속되는 두 이미지 간에 포인트의 모션은 두 포인트의 Depth와 관련이 있습니다.
오른쪽 그림에서 Pinhole은 Z 평면을 가지고 있습니다. 삼각형 모양 간의 비례식으로 표현하여 식을 구할 수 있게 됩니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of3.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

위에서 Motion Field는 측정이 어려우며, Brightness Pattern을 측정할 수 밖에 없다고 했습니다. 즉, 계산 과정에서 물체의 움직임을 명시적으로 반영하지는 않지만
물체가 움직이면 그에 따른 명암 변화가 발생하므로 물체의 움직임인 Motion Field를 반영한다고도 할 수 있습니다.
오른쪽 그림의 벡터의 길이(크기)는 시간 내에 얼마나 빨리 움직였는지를 나타내며, 화살표의 방향은 어느 방향으로 이동하는지를 나타내줍니다. 
이상적으로는 optical flow는 motion field와 같게 표시 될 수 있습니다. 하지만 현실 세계에서 많은 경우 그 둘은 같지 않게 됩니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of4.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

위 두 그림의 공통점은 광원(Source)과 구체(Sphere)가 있다는 것입니다. 구체는 Lambertian BRDF라고 생각하면 좋을 것 같습니다. 이에 대해서는 추후 보완 설명하겠습니다.
왼쪽의 그림은 광원의 위치는 그대로 두고, 구체가 회전하는 상태입니다. 즉, Motion Field는 존재하지만 Optical Flow는 발생하지 않게 됩니다.
오른쪽 그림은 이와 반대입니다. 구체는 가만히 있지만 광원이 움직여서 밝아 보이는 위치에 변화가 발생합니다. 

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of5.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>


### Optical Flow


실생활의 예시를 들어보겠습니다. 이발소 영업 상태를 나타내는 돌돌이가 있죠. 실제로 그 안의 패턴은 오른쪽으로 움직여서 Motion Field는 왼쪽에서 오른쪽을 향하게 됩니다.
하지만, 우리가 눈으로 볼 때는 위에서 아래로 움직이는 것 같은 효과가 발생하죠. 즉 Motion Field와 Optical Field가 해당 경우에는 orthogonal한 관계임을 보여줍니다.
아래 두 그림 역시 움직임은 없으나 시각적으로 흐르고 있는 것을 느낄 수 있습니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of6.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

그렇다면 이제는 Optical Flow에 대해 얘기해보겠습니다. 사진 상의 새가 $$ \delta t $$ 만큼의 시간동안 이동하였습니다. 즉 변위(Displacement)가 발생한 것입니다.
그 찰나동안 x, y 방면으로 이동하는 동안의 속도(u, v)를 point와 연관이 있는 Optical Flow라고 표현합니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of7.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

Optical Flow가 성립되기 위해서는 중요한 가정들이 몇가지 있습니다. 이를 Optical Flow Constraint Equation이라고 표현하며, 비단 Optical Flow 뿐 아니라 
Computer Vision분야에서는 이러한 '제약'들을 걸어서 많은 문제를 푸는 것을 알 수 있습니다.

우선 모든 시간에 걸쳐서 이미지의 Point들의 brightness는 불변하다고 전제합니다. 매우 짧은 순간인 $$ \delta t $$ 만큼의 시간이기에 가능한 가정인 것 같습니다.
위와 같이 수식으로도 표현할 수 있죠.(I = Intensity = Brightness)

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of8.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

두번째로는 $$ (\delta x, \delta y, \delta t) $$는 매우매우 작아야 한다는 것입니다. 이를 통해 Taylor Expansion을 적용하여 수식을 간소화 할 수 있습니다.
아래 Taylor Series Expansion의 설명을 참고하시면 됩니다.


#### Taylor Series


<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of9.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of10.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of11.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

가정들을 종합하면 위와 같이 식을 나타낼 수 있게 됩니다. Constraint Equation으로 $$ \mathbf{I}_x{u} + \mathbf{I}_x{v} + \mathbf{I}_t = 0 $$ 가 도출됩니다.
또한 $$ \mathbf{I}_x, \mathbf{I}_y, \mathbf{I}_t $$는 이미지의 두 프레임만으로 쉽게 구할 수 있습니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of12.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

Constraint를 optical flow 좌표상에 표현해보면 아래와 같이 나타낼 수 있습니다. 좌표로 나타낸다면, 성분을 분리하여 나타낼 수도 있죠.
Normal Flow의 방향, 크기를 각각 식으로 구분할 수 있습니다. 하지만 constraint line과 평행한 $$ u_p $$ 는 무한하므로 특정지을 수가 없습니다. 

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of13.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

이러한 ambiguity는 Aperture Problem을 야기하게 됩니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of14.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

line의 실제 움직임은 오른쪽 아래 방향으로 이루어집니다. 하지만, local(일부분)만 볼 경우 오른쪽 위 방향으로 움직이는 것처럼 보이게 됩니다.
이를 Aperture Problem이라고 하며 이를 해결하기 위한 해결책은 후술하겠습니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of15.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

결과적으로 Under constraint한 환경에서 optical flow를 구하게 되는 것이고, 이를 찾기 위한 몇가지 알고리즘을 이제 소개하도록 하겠습니다.


## Lucas-Kanade Optical Flow


<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of16.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

Lucas-Kanade 방식은 local 방식입니다. 픽셀 $$(x, y)$$ 를 중심으로 하는 윈도우 영역 $$\mathbf{W} $$의 Optical Flow는 같다고 가정합니다.
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of17.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

아래와 같이 모든 point를 쌓아서 matrix form으로 나타낼 수 있게 되고 계산이 편리해 집니다. 
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of18.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of19.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

그렇다면, 이러한 조건을 갖기 위해서는 어떻게 해야할까요?<br>
$$ \bullet \mathbf{A}^T\mathbf{A}$$ 는 반드시 invertible해야 합니다. determinant $$ \neq 0$$ 이어야만 계산할 수 있기 때문이죠.<br>
$$ \bullet \mathbf{A}^T\mathbf{A}$$ 는 Well-conditioned 상태여야 합니다. 풀어쓰자면,<br>
$$\mathbf{A}^T\mathbf{A}$$ 의 eigen value인 $$ \lambda_1 , \lambda_2$$ 는 positive definite해야하며,<br> 
eigen value를 나열하는 순서대로 $$ \lambda_1 \geq \lambda_2 $$이지만, 그 크기의 차이가 너무 커서도 안됩니다.<br>
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of20.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

해당 조건에 대해 자세히 살펴보겠습니다.
만약에 eigen value가 너무 작고 크기에 차이가 거의 없는 경우 $$\mathbf{A}^T\mathbf{A}$$는 well condition이 아니게 됩니다.
반면 오른쪽 그림처럼 Edge라서 eigen value값의 차이가 너무 큰 경우, Aperture problem과 같은 side effect가 발생합니다.
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of21.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of22.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

아래와 같이 texture가 있는 영역은 optical flow 계산에 좋은 영역입니다.
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of23.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

그렇다면, 만약 사물의 위치 변화가 크면 어떻게 될까요? 이 경우에는 $$ \delta t $$ $$\delta x $$ $$\delta y$$를 가정하기 어렵습니다.
그래서 constraint를 가하기도 어렵구요. 결국 Aliasing이 일어나게 됩니다.
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of24.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

이를 해결하기 위한 방법으로 Coarse-to-Fine Estimation방법이 있습니다. 직역하자면 거친 이미지에서 좋은 이미지로 옮겨가며 추정한다는 것입니다.
거친 이미지는 저해상도를 좋은 이미지는 고해상도의 이미지를 의미합니다. 이러한 워딩이 왜 나왔는지는 아래 그림으로 설명이 가능합니다.
왼쪽의 원본 이미지 상에서는 $$ \delta t$$의 시간 동안 사물이 꽤 많은 위치를 이동한 것으로 보입니다. 이러한 Gap을 줄이려면 downsampling을 하면
가능합니다. 맨 우측의 N/8 사이즈의 저해상도 이미지의 경우 1pixel안에서 모션의 gap 차이를 담을 수 있게 됩니다. 이 상황에서는 optical flow의
contraint equation을 다시 적용할 수 있게 되는 것입니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of25.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of26.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

아래와 같이 optical flow가 잘 나타나는 것을 볼 수 있습니다.
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of27.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>


## Horn-Schunck Optical Flow

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of28.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

LK 방식은 지역적 방법이므로 굉장히 Sparse한 결과를 낸다는 단점이 있었습니다. LK의 윈도우 내부만을 보는 방식이 아니라 이미지 전역(GLOBAL)에서 
이를 극복하기 위해서는 Smoothness를 더하는 방안이 있겠는데요, 보통 사물은 단단하거나 탄력적으로 변형되며, 포인트가 개별이 아닌 사물 전체가 함께 움직인다는 점에서 착안된 방식입니다.
Horn-Schunck 방식의 key idea는 Frame간의 움직임이 작아서, brightness의 일관성과 Optical Flow의 균일성을 강제로 둔다는 것입니다.


### Enforce Brightness constancy


Brightness를 일관되게 유지하기 위해서는 $$ \mathbf{I}_x{u} + \mathbf{I}_y{v} + \mathbf{I}_t = 0$$를 다시 불러옵니다.
모든 픽셀에 대해 적용해야 하기 때문에 아래 식을 만족하면 되겠네요.
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of29.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>


### Enforce Smooth flow field


smoothness는 아래처럼 표현하면 간단할 것 같습니다. 
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of30.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

좀 더 쉽게 설명해보겠습니다. smooth하다면 optical flow 맵은 균일할 것입니다. 균일한 정도는 아래 식을 사용하여 추정합니다.
gradient가 작다면 이웃한 픽셀간의 u, v가 비슷하다는 의미이므로 optical flow가 균일하다고도 볼 수 있습니다. 아래 이미지처럼 오른쪽은 균일하지 못하죠.
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of32.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of33.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

위 두 key idea를 합치면 optical flow를 계산할 수 있습니다. 결국, smoothness와 brightness constancy를 최소화 시키는 방향의 해를 구해야 합니다.
여기서 $$ \lambda $$는 어느 것에 더 비중을 둬야하는지를 나타내는 가중치를 의미합니다. 이와 같이 가중치가 붙어 있는 항을 regularization term이라고 하며,
균일한 optical flow 해를 구하는 방법을 정규화 기법이라고 합니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/optical_flow_study/of31.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>


## Reference

Optical flow. (2023, April 29). In Wikipedia. https://en.wikipedia.org/wiki/Optical_flow<br>

https://youtube.com/@firstprinciplesofcomputerv3258<br>

https://gaussian37.github.io/vision-concept-optical_flow/<br>

http://www.cs.cmu.edu/~16385/<br>
