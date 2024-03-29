---
layout: post
title: cs231n Summary - Image Classification with Linear Classifiers
date: 2023-05-28
description: recording_a Summary of lecture
tags: cs231 study AI
categories: study cs231 AI
related_posts: True
giscus_comments: true
toc:
  sidebar: left
---

## Image Classification
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic44.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic45.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>    
</div>

컴퓨터가 고양이 사진을 본다면 밝기 성분을 [0, 255] 범위의 정수의 집합으로 표현할 것입니다. 예시의 고양이 사진의 전체사이즈는 대략 가로 세로 1:2 비율이라고 본다면, 200px X 400px크기의 이미지로 둘 수 있습니다. 또한 color이미지이므로 RGB 3channels로 구성되어 있습니다. 네모난 창문(window)을 보면 왼쪽 상단에서 우측 상단으로 탐색하면서 해당 위치는 RGB가 각각 얼마만큼 (0~255 사이) 성분이 있는지를 나타내는 값으로 저장됩니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/rgb.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

하지만, 만약 고양이 사진을 다른 각도에서 찍거나 조명이 달라지거나 한다면 밝기 성분은 당연히 달라질 수 밖에 없겠죠. 사람은 같은 고양이로 인식할 것입니다. 누워있던 숨어있던 뒤돌아 있던, 하지만 컴퓨터는 어렵죠.<br>

Challenges of recognition : 
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic49.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

물론 고전적인 Computer vision에서는 edge detection 등 많은 방식으로 Classification task(분류 문제)를 풀려고 했습니다. 하지만 이렇다할 성능을 보이지는 못했고, 현대에 와서는 Data-driven 방식의 머신러닝 기법을 적용하게 되었습니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic53.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic54.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

### Nearest Neighbor Classifier

<p>KNN(K-Nearest Neighbor)분류기입니다. 우리가 타겟하는 포인트와 가까이에 있는 k개를 살펴보고 k개의 포인트가 가장 많이 속해있는 집단(class)을 정답으로 하는 분류기입니다. 
사진에서 고양이를 찾는다고 다시 생각해보겠습니다. 아래 그림처럼 어느 한 point가 우리가 찾고자하는 지점이라고 하겠습니다. k = 3으로 둘 경우, 타겟 포인트로 부터 근처에 3개의 샘플을 추출하여 어느 영역에 더 많이 속해있는지를 보고 타겟 포인트도 해당 영역의 값이라고 예측하는 것입니다.
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic58.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>


</p>
task 수행을 위해 모델이 필요하다고 했었죠. 하지만 KNN의 경우 특별한 모델이 필요한 것은 아니고 training data(학습 데이터)의 정답을 모두 저장해두었다가 나중에 test data(테스트 데이터)를 모델에 넣었을 때 정답과 가장 유사한 결과를 예측해내어 분류 task를 수행하는 것이 전부입니다. 그렇기 때문에 복잡한 인공지능 여타 모델과 달리 lazy model이라고 이야기 하기도 합니다.
<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic55.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

고양이라는 (Label)을 달고 있는 training data를 저장해두었다가, 나중에 query로 들어오는 Test data와의 거리 비교(Distance Metric)를 하여 그 차이가 가장 작은 데이터를 고양이라고 분류하는 거죠.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic56.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>


Distance Metric 즉, 거리 차이를 비교하는 방법은 대표적으로 두가지가 있습니다. L1 distance와 L2 distance 입니다.

L1 distance: 
\begin{equation} 
d_1(I_1, I_2) = \sum\limits_{p}|I_1^{p} - I_2^{p}| 
\end{equation}

두 이미지를 L1 거리로 비교하기 위해 픽셀별 차이를 사용하는 예입니다(예에서는 하나의 색상 채널에 대해).
두 개의 이미지를 요소별로 뺀 다음 모든 차이를 더하여 하나의 숫자로 만듭니다. 두 이미지가 동일하면 결과는 0이 됩니다. 그러나 이미지가 매우 다르면 결과가 커집니다.


<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic57.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

L2 distance: \begin{equation} d_1(I_1, I_2) = \sqrt{\sum\limits_{p}(I_1^{p} - I_2^{p})^2} \end{equation}

**L1 VS L2**  특히, L2 거리는 L1 거리보다 두 벡터 사이의 차이에 대해 훨씬 더 까다롭습니다. 즉, L2 거리는 하나의 큰 불일치보다 많은 중간 불일치를 선호합니다.

#### Hyperparameter setting
<p>그렇다면, 얼마만큼의 k를 줘야 최적의 답을 끌어내는 모델일까요? 혹은 어떠한 distance metric을 사용해야 최고의 모델이 될까요?<br>
이러한 요소들이 우리들이 학습하면서 수정해 나아가야 할 KNN모델의 Hyperparameter라고 합니다.
매우 어려운 부분이고, 데이터셋에 의존적입니다. 모든 알고리즘을 실행해보고 어떤 것이 가장 잘 동작하는지 확인을 해야합니다.
</p>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic59.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

여러 가지 다른 값들을 시도해보고 가장 좋은 결과를 찾는 것은 당연히 좋은 아이디어이며, 실제로 수행해야 할 일입니다. 하지만 이 매우 신중하게 수행되어야 합니다. 특히 *하이퍼파라미터를 조정하기 위해 테스트 세트를 사용할 수 없습니다.* 기계 학습 알고리즘을 설계할 때 테스트 세트는 매우 귀중한 자원으로 간주해야 하며, 가능하면 맨 마지막에 한 번만 사용하는 것이 이상적입니다. 그렇지 않으면 실제로 모델을 배포할 때 성능이 크게 저하될 수 있는 실질적인 위험이 있습니다. 실전에서는 테스트 세트에 과적합(overfit)된다고 말합니다. 

다행히도 하이퍼파라미터를 조정하는 올바른 방법이 있으며, 이는 테스트 세트를 전혀 건드리지 않습니다. 아이디어는 훈련 세트를 두 개로 나누는 것입니다: 조금 더 작은 훈련 세트와 우리가 검증 세트라고 부르는 세트입니다. CIFAR-10을 예로 들면, 예를 들어 49,000개의 훈련 이미지를 훈련용으로 사용하고, 나머지 1,000개를 검증용으로 남겨둘 수 있습니다. 이 검증 세트는 사실상 하이퍼파라미터를 조정하기 위한 가짜 테스트 세트로 사용됩니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic60.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

훈련 데이터의 크기(따라서 검증 데이터의 크기도)가 작을 수 있는 경우, 하이퍼파라미터 튜닝을 위해 교차 검증(cross-validation)이라는 더 정교한 기법을 사용하는 경우도 있습니다. 이전의 예제를 가지고 작업할 때, 임의로 처음 1,000개의 데이터 포인트를 검증 세트로 선택하는 대신, 교차 검증을 사용하여 특정 k 값의 성능을 더 좋고 노이즈가 적게 평가할 수 있습니다. 예를 들어, 5-fold 교차 검증에서는 훈련 데이터를 5개의 동일한 폴드로 분할하여 4개를 훈련에 사용하고 1개를 검증에 사용합니다. 그런 다음 어떤 폴드가 검증 폴드인지 반복하면서 성능을 평가하고, 마지막으로 다른 폴드들 사이에서 성능을 평균합니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic61.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

#### k-Nearest Neighbor with pixel distance never used

k-Nearest Neighbor 알고리즘에서 픽셀 간의 거리를 사용하지 않는 이유는 다음과 같습니다:

1. 차원의 저주 (Curse of Dimensionality): 이미지 데이터의 경우 각 픽셀은 하나의 차원으로 간주됩니다. 이미지의 크기가 커질수록 차원의 수가 급격히 증가하게 되는데, 이는 차원의 저주로 알려진 문제를 야기할 수 있습니다. 차원의 저주는 고차원 공간에서 데이터 간의 거리 측정이 더 어렵고, 더 많은 데이터가 필요하게 만들어서 k-최근접 이웃 알고리즘의 성능을 저하시킵니다. $$ 2^{3000} $$ 보기만 해도 계산하기엔 참 어렵겠죠. 
2. 픽셀 간의 거리는 이미지의 의미를 적절하게 반영하지 않을 수 있습니다. 이미지는 일반적으로 시각적인 패턴, 구조, 텍스처 등을 포함하고 있으며, 이러한 특징들은 단순히 픽셀 값의 거리로만 표현하기에는 제한적일 수 있습니다. 따라서 픽셀 간의 거리를 사용하지 않는 대안적인 특징 추출 방법이나 거리 측정 방법을 사용하는 것이 더 효과적일 수 있습니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic62.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

왼쪽에는 원본 이미지가 있고 그 옆에는 픽셀 기반 L2 거리에 따라 모두 동일한 거리에 있는 세 개의 다른 이미지가 있습니다. 분명히, 픽셀 단위의 거리는 인지적이거나 의미론적인 유사성과 전혀 일치하지 않습니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic63.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

따라서, 이미지 데이터의 경우에는 픽셀 간의 거리를 직접 사용하지 않고, 보다 효과적인 특징 추출 방법과 Distance Metric(L1, L2, etc) 방법을 활용하는 것이 일반적입니다.


## Linear Classifier
선형 분류기(Linear Classifier)는 입력 데이터를 선형 경계로 구분하는 분류 알고리즘입니다. 주어진 입력 데이터에 대해 각각의 특성을 가중치와 결합하여 선형 함수를 생성하고, 이 함수의 결과를 기반으로 데이터를 클래스로 분류합니다. 이 분류방식을 잘 학습해야 Neural Network와 Convolutional Nueral Network로 자연스럽게 개념을 확장해 갈 수 있습니다.

이 기법에는 원본 데이터를 클래스 점수에 맵핑하는 점수 함수(score function)와, 예측된 점수들과 참값 레이블이 얼마나 일치하는지를 정량화한 손실 함수(loss function), 이렇게 두 가지 중요한 요소가 있습니다. 이들을 이용하면 이미지 분류 문제를 점수 함수의 파라미터에 대해 손실 함수를 최소화하는 최적화 문제(optimization problem)로 바꿀 수 있습니다.

### 이미지에서 레이블의 점수로의 Parameterized mapping

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic64.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

이 접근 방식의 첫 번째 구성 요소는 이미지의 픽셀 값을 각 클래스의 신뢰도 점수에 매핑하는 점수 함수를 정의하는 것입니다. 구체적인 예를 들어서 접근 방식을 가시화 시켜보겠습니다. 그전에, 이미지의 training dataset인 $$ x_i \in \mathbf{R}^D $$ 의 성분을 정해 놓고 이야기 해보겠습니다. 각각의 이미지들은 $$ y_i (i = 1 \dots N) $$라는 레이블들과 엮여 있습니다. 즉 $$ x_i $$ 이미지는 $$ y_i $$라는 레이블을 갖게 되는 것입니다. 그리고 $$ y_i \in 1 \dots K $$입니다. 즉, N개의 example이 있으며(Example 각각 D 차원) K개의 별개의 카테고리가 있다는 의미입니다.

<p>예를 한 번 들어보죠
CIFAAR-10 데이터셋에서 training data는 50,000장이 있습니다. (N=50,000) 이미지는 각각 D차원 (D = 32 X 32 X 3 = 3072pixel) 로 되어 있고, K=10이기 때문에 10개의 카테고리(Classes dog, cat, car and so on and so forth)로 분류가 되는 것입니다. 이제 점수 함수(score function)는 $$ \mathbf{f: R^D \rightarrow R^K} $$ 로 정의할 수 있으며, raw 이미지 픽셀을 클래스 점수에 매핑할 수 있게 됩니다.
</p>

<p>


**Linear classifier.**  가장 간단한 함수인 선형 함수의 매핑 형태입니다.


\begin{equation} f(x_i, W, b) = Wx_i + b \end{equation}
</p>

위의 수식에서 이미지 $$ x_i $$는 모든 픽셀을 D X 1 형태의 하나의 열 벡터로 **Flatten**시킨다고 가정하겠습니다. 행렬 W(size : [K X D])와 벡터 b(size : [K X 1])는 함수의 매개변수(parameter)입니다. CIFAR-10에서 $$ x_i $$는 단일 [3072 x 1] 열 벡터로 병합된 i 번째 이미지의 모든 픽셀을 포함하고 있습니다. W는 [10 X 3072]이고 b는 [10 X 1] 크기 입니다. 따라서 3072개의 숫자가 input으로 들어오고(raw image pixels) 10개의 숫자가 output으로 나오게 됩니다.(클래스 점수)

**w**는 Weight(가중치)라고 합니다. b는 bias vector(편향 벡터)라고 하고, 실제 데이터 $$ x_i $$와 상호 작용하지 않고 출력 점수 값에 영향을 주게 됩니다. 입력 특성들이 모두 0인 경우에도 분류기가 적절한 예측을 할 수 있도록 해줍니다. 즉, 바이어스는 결정 경계의 위치를 조정하여 데이터를 잘 분리할 수 있도록 도와주는 역할을 합니다. W와 b가 parameter에 속하지만 보통은 사람들은 가중치와 매개변수라는 용어를 같은 의미로 사용하기도 합니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic65.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

### Linear classifier의 해석 방법

선형 분류기는 3개의 색상 채널 모두에서 모든 픽셀 값의 가중 합계로 클래스의 점수를 계산합니다. 이러한 가중치에 대해 정확히 어떤 값을 설정했는지에 따라 함수는 이미지의 특정 위치에서 특정 색상을 좋아하거나 싫어할 수 있습니다(각 가중치의 부호에 따라). 예를 들어, 이미지 측면에 파란색이 많이 있는 경우(물에 해당할 수 있음) "선박" 클래스일 가능성이 더 높다고 상상할 수 있습니다. 그러면 "선박" 분류기가 파란색 채널 가중치에서 많은 양의 가중치를 가질 것이라고 예상할 수 있습니다. (파란색의 존재는 배의 점수를 높임), 빨강/녹색 채널의 음수 가중치(빨간색/녹색의 존재는 배의 점수를 감소시킵니다).

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic66.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

위 그림을 설명해보겠습니다. 시각화를 위해 이미지에 4개의 픽셀(단색 픽셀 4개, 간결함을 위해 이 예에서는 색상 채널을 고려하지 않음)만 있고 3개의 클래스(빨간색(고양이), 녹색(개), 파란색(배))가 있다고 가정합니다. (설명: 특히 여기서 색상은 단순히 3개의 클래스를 나타내며 RGB 채널과 관련이 없습니다.) 이미지 픽셀을 열로 쭉 펴고 행렬 곱셈을 수행하여 각 클래스의 점수를 얻습니다. 이 특정 가중치 세트 W는 전혀 좋지 않습니다. 가중치는 고양이 이미지에 매우 낮은 고양이 점수를 할당합니다. 특히, 이 가중치 세트는 개를 보고 있다고 확신하는 것 같습니다.

#### **고차원 점으로 이미지를 유추(Analogy of images as high-dimensional points.)**

이미지가 고차원 열 벡터로 확장되기 때문에 각 이미지를 이 공간의 단일 지점으로 해석할 수 있습니다(예: CIFAR-10의 각 이미지는 32x32x3 픽셀의 3072차원 공간의 지점입니다). 마찬가지로 전체 데이터 세트는 (레이블이 지정된) 포인트 세트입니다. 각 클래스의 점수를 모든 이미지 픽셀의 가중 합으로 정의했으므로 각 클래스 점수는 이 공간에 대한 선형 함수입니다. 3072차원 공간을 시각화할 수는 없지만 모든 차원을 2차원으로 압축한다고 상상하면 분류기가 수행할 수 있는 작업을 시각화할 수 있습니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic68.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

이미지 공간을 표현한 이미지. 데이터 셋의 각 이미지들은 하나의 점으로, 선형 분류기는 선으로 표현되어 있습니다. 자동차 분류기(빨간색)의 예를 사용하여 빨간색 선은 자동차 클래스에 대해 0점을 받은 공간의 모든 지점을 보여줍니다. 이때 빨간 화살표는 클래스 점수가 증가하는 방향을 보여줍니다. 즉, 빨간 선 오른편의 모든 점들은 (화살표 방향으로 선형적으로 증가하는) 양수 점수를 가지고, 빨간 선 왼편의 모든 점들은 (화살표 방향으로 선형적으로 감소하는) 음수 점수를 가집니다.<br>

위에서 보았듯이 **W** 의 모든 행 벡터는 클래스 중 하나에 대한 분류기가 됩니다. 이 숫자의 기하학적 해석은 W의 행 중 하나를 변경할 때, 픽셀 공간의 해당 라인은 다른 방향으로 회전합니다. 반면에 Biase인 b는 분류기가 수평이동 할 수 있도록 합니다. 특히 bias term이 없다면, $$ x_i=0 $$ 일 경우, 가중치에 관계없이 항상 0점을 주므로 모든 선이 원점을 지나도록 강제됩니다.


#### **선형 분류기를 템플릿 매칭으로 해석(Interpretation of linear classifiers as template matching)**

가중치 **W** 에 대한 또 다른 해석은 W의 각 행은 클래스 중 하나에 대한 템플릿(또는 때로는 프로토타입이라고도 함)에 해당한다고 보는 것입니다. 그런 다음 이미지에 대한 각 클래스의 점수는 이미지와 각 템플릿을 하나씩 내적하여 가장 "적합한 템플릿"을 찾는 방식으로 얻습니다. 다르게 생각해보면 즉, 선형 분류기는 여전히 Nearest Neighbor를 찾는 방식으로 효과적으로 수행하고 있지만 수천 개의 훈련 이미지를 다 가지고 있는 것이 아니라 클래스당 하나의 템플릿만 사용하여, L1 또는 L2 거리 대신 (음의) 내적을 거리로 사용합니다. 선형 분류기가 가지고 있는 템플릿 이미지는 학습을 통해 만들어지는데, 템플릿 이미지가 반드시 학습 데이터의 사진 중 한 장일 필요는 없습니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic67.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

예를 들어 ship 템플릿에는 예상대로 파란색 픽셀이 많이 포함되어 있습니다. 따라서 이 템플릿은 해상의 배 이미지와 내적을 통해 일치되었을 때 높은 점수를 줄 것입니다.

## 좋은 가중치(Weight) 선택하기 - Loss function

앞선 고양이 이미지 분류 문제에서 -96.8이라는 저조한 성적을 거둔 것을 알 수 있었습니다. 이제는 손실함수(때때로 cost function or objective라고 함)를 통해 훈련 데이터의 점수에 대한 불만을 측정하는 손실 함수를 정의하겠습니다. 직관적으로 보면, 훈련 데이터 분류를 잘 수행하면 손실이 낮을 것이고 반대면 높겠죠.<br>

### Multiclass Support Vector Machine loss
SVM(Multiclass Support Vector Machine) 손실 함수를 살펴보겠습니다. SVM 손실은 SVM이 각 이미지에 대한 올바른 클래스가 고정된 margin $$ \Delta $$만큼 잘못된 클래스보다 높은 점수를 갖기를 "원"하도록 설정됩니다. 손실 함수를 의인화하여 능동적으로 "원한다"고 표현하는 것은 종종 이해에 도움이 됩니다. SVM은 결과가 더 낮은 손실(좋음)을 산출한다는 의미에서 특정 결과를 "원합니다".

수식으로 표현해보겠습니다. $$ i $$ 번째 이미지의 전체 픽셀을 하나의 열 벡터로 만든 $$ x_i $$ 와 해당 미지의 label $$ y_i $$ 가 있을 경우, score function(f)는 $$ x_i $$를 입력받아 클래스 점수를 나타내는 벡터 $$ s=f(x_i, W) $$를 출력합니다.(s는 점수 score를 의미) $$ x_i $$ 의 $$ j $$ 번째 클래스의 점수는 $$ s_j $$의 $$ j $$번째 요소인 $$ s_j = f(x_i, W)_{j} $$라 표현할 수 있습니다.

$$ i $$번째 이미지에 대한 SVM 손실 함수는 다음과 같이 일반화할 수 있습니다. $$ max(0, ~) $$ 형태는 힌지 손실 함수(hinge loss)라 불리는, 0에 대한 문턱값(threshold)을 나타내는 함수입니다. 

\begin{equation} L_i = \sum\limits_{j\neq y_i}max(0, s_j - s_{y_i} + \mathit{\Delta}) \end{equation}

#### **예제**
예시를 들어 자세히 설명해 보겠습니다. 세개의 클래스가 각각 $$ s = [13, -7, 11] $$ 만큼의 점수를 가지고 있고 첫 번째 클래스가 정답(true class, $$ i.e. y_i = 0 $$)이라고 가정해보겠습니다. 그리고 $$ \Delta = 10 $$ 으로 두겠습니다. 위 식은 잘못 매칭된 클래스 들의 점수의 합계라고 볼 수 있죠. ($$ j \neq y_i $$) 이제 식에 대입해보겠습니다.

\begin{equation} L_i = max(0, -7-13+10) + max(0, 11-13+10) \end{equation}

첫 번째 항은 손실이 0이 됩니다. 올바른 클래스 점수(13)가 잘못된 클래스 점수(-7)보다 최소 마진 10만큼 크기 때문에 이 쌍에 대해 손실이 0이 됩니다. 두 번째 항은 손실이 8이 됩니다. 요약하면 SVM 손실 함수는 올바른 클래스 yi의 점수를 원합니다. 잘못된 클래스 점수보다 적어도 $$ \Delta $$ 만큼 더 커야 합니다. 점수 함수를 선형 함수$$ (f(x_i;W)=Wx_i) $$로 작업했었죠. 그렇다면 (4)번 식은 이렇게 바꿔 쓸 수도 있습니다. 
\begin{equation} L_i = \sum_{j \neq y_i}max(0, \omega_{j}^{T}x_i - \omega_{y_i}^{T}x_i + \Delta)\end{equation}

$$ \omega_{j} $$는 $$ W $$의 j번째 행이 열로 재구성된 것입니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic73.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
$$ \Delta $$ 보다 작은 항을 더 강하게 불리하게 만들기 위해 선형적인 식이 아닌 2차식 형태의 제곱 힌지 손실 SVM 함수(squared hinge loss SVM, 혹은 $$ L_2-SVM, max(0,−)^2 $$)을 대신 사용할 때도 있습니다. 제곱하지 않은 단순한 힌지 손실 함수를 사용하는 것이 더 일반적인 형태이나, 몇몇 데이터 셋에서는 제곱 힌지 손실 함수가 더 잘 작동할 수 있습니다. 두 손실값 중 어느쪽이 더 나은지는 교차 검증법(cross validation)을 적용해 결정합니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic74.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

아래 예시를 보면 Multiclass SVM 손실 함수를 어떻게 구해야 할지 알 수 있습니다. 

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic69.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic70.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic71.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic72.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

## Softmax Clssifier

소프트맥스 분류기(Softmax Classifier)는 다중 클래스 분류 문제를 해결하기 위한
알고리즘입니다.SVM이 출력으로 $$ f(x_i, W) $$ 를 각 클래스가 나올 점수(score)로 할당하는 
것과는 달리 Softmax classifier는 normalize된 클래스 확률로 더욱 직관적인 출력을 보여줍니다. 
함수 자체가 맵핑되는 것은 $$ f(x_i, W) $$로 SVM과 같습니다. 하지만, 점수를 unnormalized 
log probabilties(비정규화 로그 확률)로 해석하고 힌지 손실을 교차 엔트로피 손실로 대체해서 
해석합니다.

\begin{equation} L_i = -log(\frac{e^{f_{y_i}}}{\sum_{j}e^{f_j}}) 
\end{equation}

또는

\begin{equation}
L_i = -f_{y_i}+ log\sum_{j}e^{f_j}
\end{equation}

$$ f_j $$는 클래스 점수 f의 벡터의 j번째 요소를 의미합니다. $$ f_j(Z) = \frac{e^{f_{y_i}}}{\sum_{j}e^{f_j}} $$ 은 **softmax function** 입니다. 임의의 실수값으로 이루어진 벡터를 받아서(in Z), 해당 벡터를 0과 1 사이의 값으로 압축하고, 그 값들의 합이 1이 되도록 만듭니다. softmax 기능과 관련된 전체 교차 엔트로피 손실은 어려워 보일 수 있지만, 자주 쓰일 개념이므로 잘 이해해야 합니다. 해당 글은 <a href="https://chojinie.github.io/blog/2023/cs231-cross-entropy/">cross entropy</a>편에 정리해 놨습니다.

### 정보이론 관점에서 해석
참 값의 분포인 p와 예측 분포인 q간의 관계는 아래와 같이 정의 할 수 있습니다.
\begin{equation} h(p,q) = -\sum_{i=1}^n (q_i lg_2(p_i)) \end{equation}

소프트맥스 분류기는 예측 클래스 확률 (q = \frac{e^{f_{y_i}}}{\sum_{j}e^{f_j}})와 참 값의 분포 사이의 크로스 엔트로피를 최소화 합니다. 이 해석에서 "참 값"의 분포는 모든 확률 질량이 정답 클래스에 위치한 분포입니다.(즉, p = [0, ..., 1, ...,0]은 $$ y_i $$ 번째 위치에 단일한 1을 갖습니다.) 또한 교차 엔트로피는 엔트로피와 KL Divergence의 합으로 나타낼 수 있습니다. 델타 함수 p의 엔트로피는 0이므로, 이는 두 분포 간의 KL Divergence를 최소화하는 것과 동등합니다. 즉, 교차 엔트로피 목적은 예측된 분포가 올바른 답에 대한 확률을 모두 가지도록 하는 것입니다.

### 확률론 관점에서 해석

이를 이해하기 위해, 소프트맥스 분류기는 출력 벡터 "f" 내의 점수를 비정규화된 로그 확률로 해석합니다. 
이러한 값을 지수화함으로써 (비정규화된) 확률을 얻을 수 있으며, 나눗셈은 확률을 정규화하여 확률이 1이 
되도록 합니다. 확률적 해석에서는 따라서 올바른 클래스의 음의 로그 우도를 최소화하는 것으로, 최대 우도 
추정(MLE)을 수행한다고 볼 수 있습니다.


<!--예를 들어, 3개의 수 $$ x_0=2, x_1=1, x_2=-1 $$이 있고 이 수의 대소 관계를 유지하면서 각각의 -->
<!--확률을 나타내는 $$ y_0, y_1, y_2 $$로 변환하려고 할 경우, 확률이므로 0에서 1 사이의 숫자가 -->
<!--나와야 하며, 모두 더하면 1이어야만 합니다. 이 떄 사용되는 것이 소프트맥스 (Softmax) 함수입니다. -->
<!--먼저 $$ x_i $$의 지수함수(exp) 꼴들의 합계를 구해서 u라고 두겠습니다. 지수함수가 사용되는 이유는 -->
<!--미분이 가능하도록 하게 함이며, 입력값 중 큰 값은 더 크게 작은 값은 더 작게 만들어 입력벡터가 더 잘 -->
<!--구분되게 하기 위함입니다.-->
<!---->
<!--\begin{equation} u = exp(x_0) + exp(x_1) + exp(x_2)-->
<!--\end{equation}-->
<!--위 식을 사용하여 y꼴로 변환해준다면, $$ y_0 = \frac{exp(x_0)}{u} , y_1 = \frac{exp(x_1)}{u}, y_2 = \frac{exp(x_2)}{u} $$ 로 나타낼 수 있습니다. <br>-->


소프트맥스 분류기는 손실 함수로 크로스 엔트로피 손실(cross-entropy loss)을 사용하여 모델을 
훈련합니다. 크로스 엔트로피 손실은 실제 클래스와 예측된 클래스의 확률 분포 사이의 차이를 측정합니다. 
모델의 매개변수를 업데이트하면서 손실을 최소화하는 방향으로 학습이 진행됩니다.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="/assets/img/cs231n/assignment1/pic75.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
가장 왼쪽의 파란 박스는 logits이라고 합니다. 이는 확률화되지 않은 날 것의 예측 결과를 지칭합니다. 멀티 클래스 분류문제에서 보통 softmax 함수의 입력으로 사용됩니다. (4) 식의 소프트맥스 함수에 적용하여 exponential을 취해줍니다.
그 결과가 빨간색 박스 형태로 나오게 됩니다. 무조건 0 이상의 수가 나오기 때문에 확률로 변경할 수 있게 됩니다. 이후에  $$ \sum\limits_{j}e^{s_j} $$ 로 나눠서 normalize 시켜, 우리가 아는 분수 형태의 확률 값 형태로 변형시킵니다.
확률이므로 각 클래스 별로 나오게 될 숫자의 합은 무조건 "1"이 나와야만 합니다. 


### Softmax 분류기는 각 클래스에 대한 "확률"을 제공합니다.

보정되지 않고 모든 클래스에 대한 점수를 해석하기 어려운 SVM과는 달리 소프트맥스 분류기는 모든 레이블에 
대한 확률을 계산할 수 있습니다. 예를 들어 이미지가 주어지면 SVM 분류기는 "cat", "dog" 및 
"ship" 클래스에 대해 [12.5, 0.6, -23.0] 점수를 줄 수 있습니다. 대신 softmax 분류기는 세 
레이블의 "확률"을 [0.9, 0.09, 0.01]로 계산할 수 있습니다. 이렇게 각 클래스에 대한 신뢰도를 해석 
할 수 있게 됩니다. 그러나 "확률"이라는 단어에 강조를 하는 이유는 이러한 확률의 뾰족함 또는 흩어짐이 
정규화 강도 \lamda 에 직접적으로 의존하기 때문입니다. 이 정규화 강도는 시스템에 입력으로 주어지는 
값입니다. 예를 들어, 어떤 세 개의 클래스에 대한 비정규화된 로그 확률이 [1, -2, 0]으로 나온다고 
가정해보겠습니다. softmax 함수는 다음을 계산합니다. 
\begin{equation} [1,-2,0] \rightarrow [e^1,e^{-2},e^0]=[2.71,0.14,1] 
\rightarrow [0.7,0.04,0.26] \end{equation}
스코어를 exp로 지수화하고, 그 값을 정규화하여 확률의 합계를 1로 만드는 것입니다. (0.55 + 0.12 + 
0.33 = 1) 이제 확률은 더 흩어져 있습니다. 또한, 매우 강한 정규화 강도 λ로 인해 가중치가 매우 작은 
수로 수렴하는 한계에서는 출력 확률이 균일에 가까워질 것입니다. 따라서 소프트맥스 분류기에 의해 계산된 
확률은 SVM과 유사하게 순위 지정 가능하지만, 절대적인 숫자(또는 그 차이)는 기술적으로 해석할 수 없는 
대신, 신뢰도로 생각하는 것이 더 나은 접근입니다.

### 실제로 SVM과 Softmax는 일반적으로 비슷합니다.

SVM과 Softmax의 성능 차이는 일반적으로 매우 작으며, 어떤 분류기가 더 잘 작동하는지에 대한 의견은 
다양할 수 있습니다. Softmax 분류기와 비교하여 SVM은 더 지역적인 목적 함수로, 버그로 생각될 수도 
있고 특징으로 생각될 수도 있습니다. [10, -2, 3]의 점수를 달성하고 첫 번째 클래스가 올바른 예를 
고려해보겠습니다. SVM (예: 원하는 여유 간격 \Delta =1을 가진 경우)은 올바른 클래스가 다른 
클래스들과 비교하여 여유 간격보다 높은 점수를 이미 가지고 있으므로 손실을 0으로 계산합니다. SVM은 
개별 점수의 세부 사항에는 관심이 없습니다. 예를 들어, 점수가 [10, -100, -100]이거나 [10, 9, 
9]라면, SVM은 여유 간격이 1로 만족되므로 손실이 0이기 때문에 차이를 느끼지 않습니다. 그러나 이러한 
시나리오는 Softmax 분류기와 동일하지 않습니다. Softmax 분류기는 점수 [10, 9, 9]에 대해 [10, 
-100, -100]보다 훨씬 더 높은 손실을 누적합니다. 다시 말해, Softmax 분류기는 생성된 점수에 대해 
완전히 만족하지 않습니다. 올바른 클래스는 항상 더 높은 확률을 가지고 있어야 하고, 잘못된 클래스는 항상 
더 낮은 확률을 가져야 하며, 손실은 항상 좋아집니다. 그러나 SVM은 여유 간격이 충족되면 만족하며, 
정확한 점수를 이 제약 조건 이상으로 관리하지 않습니다. 이는 직관적으로 특징으로 생각할 수 있습니다. 
예를 들어, 자동차 분류기는 자동차와 트럭을 분리하는 어려운 문제에 대부분의 "노력"을 기울이고 있을 
것이며, 이미 매우 낮은 점수를 할당하는 개구리 예제에 영향을 받지 않아야 하며, 아마도 데이터 클라우드의 
완전히 다른 쪽에 군집화될 가능성이 높습니다.


## 참고
https://heekangpark.github.io/Stanford_cs231n/03-linear-classification<br>
http://cs231n.stanford.edu/schedule.html
