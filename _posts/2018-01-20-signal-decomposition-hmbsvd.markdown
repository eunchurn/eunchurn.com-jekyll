---
author: admin
comments: true
date: 2018-01-20 17:21:10+09:00
layout: post
title: 헹켈행렬(Hankel matrix) 기반 신호분리 이론 고찰
publish: true
description: MOT x EUNCHURN - 매일 그대와 작업일기에서 언급했던 비트 빌드업(그다지 좋은 결과는 아니었지만)에 대해 여기서 이론적으로 설명하고 다음 포스트에 프로그램에 대하여 설명하고 공유하려고 한다. 보통 이러한 신호처리기법은 기계 진동 가속도나 전기신호의 신호처리에 쓰이겠지만, 여기 블로그에서는 음원에서 신호를 분리해보고 스테레오라이징 그리고 그 의미를 고찰해보려고 한다.
featured: /public/images/2018/01/hmbsvd.png
category: engineering
tag:
- svd
- hankel matrix
- python
---
[MOT x EUNCHURN - 매일 그대와 작업일기]({{ site.baseurl}}/runway/2011/04/02/production-note-of-music-for-mvio-aw-collection-20112012/#beats)에서 언급했던 비트 빌드업(그다지 좋은 결과는 아니었지만)에 대해 여기서 이론적으로 설명하고 다음 포스트에 프로그램에 대하여 설명하고 공유하려고 한다. 보통 이러한 신호처리기법은 기계 진동 가속도나 전기신호의 신호처리에 쓰이겠지만, 여기 블로그에서는 음원에서 신호를 분리해보고 스테레오라이징 그리고 그 의미를 고찰해보려고 한다.

특이치분해(singular value decomposition, SVD)의 정의는 다음과 같은 $\mathrm{A} \in \mathrm{R}^{m\times x}$인 행렬을 2개의 직교행렬 $\mathrm{U} = \left[ \mathrm{u}_ {1}, \mathrm{u}_ {2}, ..., \mathrm{u}_ {m}\right] \in \mathrm{R}^{m \times m}$과 $\mathrm{V} = \left[ \mathrm{v}_ {1}, \mathrm{v}_ {2}, ..., \mathrm{v}_ {n}\right] \in \mathrm{R}^{n \times n}$로 아래의 조건식을 만족하면서 분리한다.[^golub2012matrix]

$$
\begin{equation}
\mathrm{A} = \mathrm{USV}^{\top}
\end{equation}
\label{eq:1}
\tag{1}
$$

여기서, $\mathrm{S}=\left[diag\left( \sigma_ {1}, \sigma_ {2}, ..., \sigma_ {q} \right), \mathrm{O}\right]$이거나, $m$과 $n$의 크기에 의해 전치된 행렬이 된다. 여기서 $\mathrm{S}\in\mathrm{R}^{m \times n}$이고, $\mathrm{O}$는 영행렬이다. $q=\min(m,n)$이고 $\sigma_ {1} \geq \sigma_ {2} \geq ... \sigma_ {q} > 0$이다. 여기서 $\sigma_ {i} (i=1,2,...,q)$를 행렬 $A$의 특이값이라고 한다.

SVD와 웨이브렛 변환은 같은 호흡에서 결코 언급 될 수 없는 두 가지 이론이며, 그들의 수학적 기초는 서로 완전히 다르다. 그러나 적절한 행렬 $A$가 적용되면 SVD는 신호 처리에서 웨이블릿 변환과 매우 놀랍도록 유사한 효과를 나타낼 것이라는 것을 발견했다. Toeplitz 행렬, 사이클 행렬 및 Hankel 행렬과 같은 1차원 신호로 많은 종류의 행렬을 만들 거나 신호를 연속적으로 잘라내고 채워서 만들 수 있다. 차이점은 매트릭스의 생성 방식이며, 차이점은 SVD의 신호 처리의 효과 일 것이다[^bangyan2007processing].

놀랍게도, SVD는 $A$ 행렬이 Hankel행렬 일 때 웨이블릿 변환에 대한 신호 처리와 매우 유사한 효과를 얻을 수 있다. 예를 들어 원본 신호는 두 가지 방법으로 일련의 세부 신호와 근사 신호의 조합으로 분해 될 수 있다. 두 가지 방법 모두 신호의 특이점을 검출 할 수 있다. 또한, 몇가지 면에서, SVD의 분해 결과들은 웨이브렛변환이 가진 특별한 이점을 갖는다. 이 두 가지 완전히 다른 이론의 신호 처리 효과가 서로 너무 비슷한 이유는 무엇일까? 이 질문은 연구할 가치가 있다.

이 현상을 합리적으로 설명하기 위해 이 두 가지 방법의 신호 분해의 본질을 확인해야한다. 잘 알려진 바와 같이 실제로 웨이블릿 변환의 본질은 신호를 여러 주파수 대역에있는 일련의 컴포넌트 신호로 분해하기 위해 2개의 HPF와 LPF를 사용하는 것이고 SVD의 경우는 신호처리에 있어 직접적인 설명이 없다. 문헌에서 SVD의 신호 처리에 대한 대부분의 언급은 항상 실용성에 중점을두고 있으며 SVD는 피쳐 추출[^cempel2003multidimensional] [^brenner2003non], 데이터 압축[^wei2001ecg] , 노이즈 감소[^shin1999iterative]와 같은 특정 신호 처리 문제를 해결하는 도구로만 사용된다. SVD의 신호 분해의 본질에 관한 연구는 거의 없지만, 이 논문[^zhao2009similarity]에서는이 본질이 확인되었고, 이를 바탕으로 SVD와 웨이블릿 변환사이의 신호 처리의 유사성 메커니즘이 밝혀졌다. 웨이블릿 변환은 벡터 공간의 기저(basis)의 각도로부터 설명된다. 또한, 이 논문[^zhao2009similarity]은 이 두 가지 방법으로 얻은 성분 신호의 직교성에 대해서도 연구하고 이론적으로 비교한다.

이론 분석에 기초하여, 이 두 방법의 신호 처리의 실제적으로 유사한 효과는 몇 가지 예에 의해 입증되며, Hankel 행렬 자체의 구조 특성에 대해 첫 번째 SVD 구성 요소 신호는 근사 신호와 일치 할 수 있다고 지적한다. 다른 SVD 성분 신호는 웨이브렛 변환에서 상세 신호에 대응할 수 있다.

한편, SVD의 분해 결과에는 위상 변화가 없고, 웨이브렛 변환에는 위상 지연이 존재한다는 점에서 두 방법의 차이점에 대해서도 연구되었다. 그리고 특이점 검출에 관해서는, 일단 웨이브렛이 선택되면 각 종류의 웨이브렛의 모멘트의 소멸 차수가 고정되어 있기 때문에 웨이브렛 변환에 의해 검출 될 수 있는 특이 인덱스도 고정되고, 또한, 임펄스의 폭 단일 지점의 위치를 ​​나타내는 것은 스케일의 증가와 함께 더욱 넓어지게 될 것이다. 이에 비해 SVD는 이런 현상에 비해 많이 다르다, 즉, SVD 성분의 모멘트 소멸 차수가 점진적으로 증가하고 $n$ 번째 SVD 성분 신호 중 하나가 '$$n-1$$'이므로 서로 다른 singular index를 갖는 singular point가 다른 SVD 구성 요소와 더불어 singular point의 위치를 ​​나타내는 임펄스의 폭은 모든 SVD 구성 요소에서 항상 동일하게 유지되며 이 폭은 Hankel 행렬의 열 수에 의해 결정되므로 이러한 측면에서 SVD는 웨이블릿 변환에 비해 장점을 가진다.

[^golub2012matrix]: {% reference golub2012matrix -f svd %}
[^bangyan2007processing]: {% reference bangyan2007processing -f svd %}
[^zhao2009similarity]: {% reference zhao2009similarity -f svd %}
[^cempel2003multidimensional]: {% reference cempel2003multidimensional -f svd %}
[^brenner2003non]: {% reference brenner2003non -f svd %}
[^wei2001ecg]: {% reference wei2001ecg -f svd %}
[^shin1999iterative]: {% reference shin1999iterative -f svd %}

## Hankel 행렬 SVD 기반 신호 분해 기법

이산 신호(discrete signal) $\mathrm{X} = \left[ x(1), x(2),\cdots,x(N)\right]$에 대해, Hankel 행렬은 다음과 같이 전개할 수 있다.

$$
\begin{equation}
\mathrm{A} = \begin{bmatrix} x(1) & x(2) & \cdots & x(n) \\ x(2) & x(3) & \cdots & x(n+1) \\ \vdots & \vdots & \vdots & \vdots \\ x(N-n+1) & x(N-n+2) & \cdots & x(N)\end{bmatrix}
\end{equation}
$$

여기서, $$1<n<N$$ 이고, $$m=N-n+1$$이라고 하자 단, $$ \mathrm{A} \in \mathrm{R}^{m \times n}$$.
SVD를 사용하여 신호를 격리시키는 과정을 이해하기 위하여 식\eqref{eq:1}을 열벡터 $$\mathrm{u}_{i}$$와 $$\mathrm{v}_{i}$$로 변환한다.

$$
\mathrm{A} = \sigma_{1}\mathrm{u}_{1}\mathrm{v}_{1}^{\top} + \sigma_{2}\mathrm{u}_{2}\mathrm{v}_{2}^{\top} + \cdots + \sigma_{q}\mathrm{u}_{q}\mathrm{v}_{q}^{\top}
\label{eq:2}
\tag{2}
$$

여기서, $$\mathrm{u}_{i}\in\mathrm{R}^{m\times 1}$$, $$\mathrm{v}_{i}\in\mathrm{R}^{n\times 1}$$, $$i=1,2,\cdots,q$$, $$q=\min(m,n)$$이다. 특이치분해의 이론에 따라서 벡터 $$\mathrm{u}_{i}$$는 다른 성분과 정확히 직교성을 띈다. 즉, $$m$$차원 공간에서의 직교기저(orthonormal basis)의 역할을 하게 된다. $$\mathrm{v}_{i}$$도 마찬가지로 다른 성분과 $$n$$차원의 공간에서 직교기저를 형성하게 된다.[^golub2012matrix]

$$\mathrm{A}_{i}=\sigma_{i}\mathrm{u}_{i}\mathrm{v}_{i}^{\top}$$, $$\mathrm{A}\in\mathrm{R}^{m\times n}$$라고 하면, $$\mathrm{P}_{i,1}$$를 $$\mathrm{A}_{i}$$의 첫번째 행벡터로, $$\mathrm{H}_{i,n}$$는 $$\mathrm{A}_{i}$$d의 마지막 열벡터로 가정하면, Hankel 행렬의 특징에 따라서 다음식으로 분리된 신호 $$\mathrm{P}$$를 생성할 수 있다.

$$
\begin{align}
\mathrm{P}_{i}=\left( \mathrm{P}_{i,1}, \mathrm{H}_{i,n}^{\top} \right). & ~~~~ \mathrm{P}_{i,1}\in\mathrm{R}^{1\times n}, \mathrm{H}_{i,n}\in\mathrm{R}^{(m-1)\times 1}
\label{eq:3}
\tag{3}
\end{align}
$$

$$\mathrm{A}_{i} (i, 1, 2, \cdots , q)$$에 의해 형성된 모든 성분 신호는 원 신호 $$\mathrm{X}$$에 대해 한 종류의 분해를 구성한다.
그러나 이 성분 신호는 근본적으로 무엇을 반영하는가? 이 질문을 확인하기 위해, 성분 신호 $$\mathrm{P}_{i}$$를 2개의 세그먼트로 분할 할 수 있는데, 전면 세그먼트는 $$\mathrm{A}_{i}$$의 제1 행벡터인 $$\mathrm{P}_{i,1}$$이고, 후면 세그먼트 $$\mathrm{L}_{i, n}$$의 전치이다. 여기서 $$\mathrm{L}_{i, n}$$은 $$\mathrm{A}_{i}$$의 마지막 열벡터이다.

원 신호에 의해 생성된 Hankel 행렬 $$\mathrm{A}$$가 행 벡터 $$\mathrm{X}_{1}, \mathrm{X}_{2}, \cdots, \mathrm{X}_{m}, \mathrm{X}_{m}\in\mathrm{R}^{1\times n}$$에 의해 표현된다고 가정하자. $$\mathrm{A}$$의 생성으로부터 알려진 바와 같이, 제1벡터 $$\mathrm{X}_{1}$$은 원신호의 프론트 세그먼트이고, $$n$$ 차원의 기저 벡터 $$\mathrm{v}_{i}$$에 대한 식\eqref{eq:1}의 투영 계수는 다음식과 계산할 수 있다:

$$
\mathrm{X}_{1}\mathrm{v}_{i}=\sigma_{i}u_{i1}
\label{eq:4}
\tag{4}
$$

여기서, $$u_{i1}$$은 기저 벡터 $$\mathrm{u}_{i}$$의 첫번째 좌표이다.

성분 신호 $$\mathrm{P}_{i}$$의 프론트 세그먼트인 $$\mathrm{P}_{i,1}$$이고 $$\mathrm{A}_{i}$$의 제1행 벡터이기 때문에, 이는 다음과 같이 $$\mathrm{A}_{i}$$의 정의에 따라 계산 될 수 있다:

$$
\mathrm{P}_{i,1}=\sigma_{i}u_{i1}\mathrm{v}_{i}^{\top}
\label{eq:5}
\tag{5}
$$

$$\mathrm{P}_{i,1}$$은 기본 벡터 $$\mathrm{v}_{i}$$와 이 기저 벡터에 있는 $$\mathrm{X}_{1}$$의 투영 계수의 곱으로, 투영 계수 $$\sigma_{i}u_{i1}$$이 $$\mathrm{P}_{i}$$의 크기를 결정하고 벡터 $$\mathrm{v}_{i}$$가 $$\mathrm{P}_{i,1}$$의 방향을 결정한다는 것을 쉽게 알 수 있다. $$\mathrm{X}_{1}$$은 원 신호의 앞부분 세그먼트이지만, 분명히 $$\mathrm{P}_{i,1}$$은 $$n$$차원 공간의 $$i$$번째 기저 벡터인 벡터 $$\mathrm{v}_{i}$$에 대한 원래 신호의 앞부분 부분의 투영이다.

원래 신호에 의해 생성된 Hankel 행렬 $$\mathrm{A}$$가 $$\mathrm{A}$$의 생성으로부터 알려진 바와 같이 열 벡터 $$\mathrm{Y}_{1}, \mathrm{Y}_{2}, \cdots, \mathrm{Y}_{n}, \mathrm{Y}_{n}\in\mathrm{R}^{m\times 1}$$에 의해 표현된다고 가정하면, 마지막 열 벡터 $$\mathrm{Y}_{n}$$은 원 신호의 뒤쪽 세그먼트이고 $$m$$차원의 기저 벡터 $$\mathrm{u}_{i}$$에 대한 투영계수 또한 식\eqref{eq:1}로부터 계산 될 수 있으며 다음과 같다:

$$
\mathrm{Y}_{n}^{\top}\mathrm{u}_{i} = \sigma_{1}\mathrm{v}_{in}
\label{eq:6}
\tag{6}
$$

여기서, $$v_{in}$$은 기저 벡터 $$\mathrm{v}_{i}$$의 $$n$$번째 좌표이다.

성분 신호 $$\mathrm{P}_{i}$$의 뒤쪽 세그먼트인 $$\mathrm{L}_{i, n}$$ 및 행렬 $$\mathrm{A}_{i}$$의 마지막 열벡터에 대해, $$\mathrm{A}_{i}$$의 정의에 따라 다음과 같이 계산 될 수 있다:

$$
\mathrm{L}_{i,n}^{\top}=\sigma_{i}v_{in}\mathrm{u}_{i}^{\top}
\label{eq:7}
\tag{7}
$$

$$L_{i, n}^{\top}$$는 기저 벡터 $$\mathrm{u}_{i}$$와 이 기저 벡터에 대한 $$\mathrm{Y}_{n}$$의 투영 계수의 곱이며, 투영 계수 $$\sigma_{i}v_{in}$$은 $$L_{i, n}^{\top}$$의 크기를 결정하고 벡터 $$\mathrm{u}_{i}$$는 $$L_{i, n}^{\top}$$의 방향을 결정한다는 것을 알 수 있다. $$\mathrm{Y}_{n}$$은 원 신호의 뒤쪽 세그먼트이기 때문에 $$L_{i, n}^{\top}$$는 실제로 원래 신호의 뒤쪽 세그먼트를 벡터 $$\mathrm{u}_{i}$$에 투영 한 것으로 $$m$$차원 공간의 $$i$$번째 기저 벡터이다.

따라서 위의 분석에서 Hankel 행렬 기반 SVD의 신호 분해의 본질은 신호를 두 공간 예를들면 $$m$$차원 및 $$n$$차원 공간으로 분해하는 것이고 실제로 성분 신호들이 반영하는 것은 이 두 가지의 직교하는 기저들에 원래 신호가 투영되었음을 알 수 있다, 즉 그 유사성은 원 신호와 이 두 직교 기저로 확장한다.

이러한 종류의 분해 특성을 더 자세히 분석해보자. $$\mathrm{A}$$는 행벡터 $$\mathrm{X}_{1}, \mathrm{X}_{2}, \cdots, \mathrm{X}_{m}, \mathrm{X}_{m}\in\mathrm{R}^{1\times n}$$에 의해 표현되는 반면, $$\mathrm{A}_{i}$$는 행벡터 $$\mathrm{P}_{i, 1}, \mathrm{P}_{i, 2}, \cdots, \mathrm{P}_{i, m}, \mathrm{P}_{i, m}\in\mathrm{R}^{1\times n}$$에 의해 표현된다고 가정한다. 식\eqref{eq:2}에서, $$\mathrm{A}$$의 각 행벡터는 모든 $$\mathrm{A}_{i} (i, 1, 2, \cdots, q)$$에서 대응하는 행벡터의 합과 동일하므로, 다음과 같은 결과를 얻는다.

$$
\mathrm{X}_{1}=\mathrm{P}_{1,1}+\mathrm{P}_{2,1}+\cdots+\mathrm{P}_{q,1}
\label{eq:8}
\tag{8}
$$

$$\mathrm{A}_{i}$$, $$\mathrm{H}_{i,n}\in\mathrm{R}^{(m-1)\times 1}$$에있는 열벡터 $$\mathrm{H}_{i, n}$$에 대해 $$\mathrm{A}$$의 해당 열벡터가 $$\mathrm{I}_{n}, \mathrm{I}_{n}\in\mathrm{R}^{(m-1)\times 1}$$이라고 가정하면 식\eqref{eq:2}에서, $$\mathrm{I}_{n}$$은 또한 모든 $$\mathrm{A}_{i} (i, 1, 2, \cdots, q)$$에서 대응하는 열벡터 $$\mathrm{H}_{i, n}$$의 합과 같고 그들의 전치 또한 이 관계를 만족한다. 즉,

$$
\mathrm{I}_{n}^{\top} = \mathrm{H}_{1,n}^{\top} + \mathrm{H}_{2,n}^{\top} + \cdots + \mathrm{H}_{q,n}^{\top}
\label{eq:9}
\tag{9}
$$

Hankel 행렬의 생성 원리에 따르면 원신호 $$\mathrm{X}$$는 벡터 형태 $$\mathrm{X}=\left(\mathrm{X}_{i}, \mathrm{I}_{n}^{\top}\right)$$로 표현 될 수 있고 성분 신호 $$\mathrm{P}_{i}$$는 벡터형태 $$\mathrm{P}_{i}=\left(\mathrm{P}_{i,1},\mathrm{H}_{i,n}^{\top}\right)$$이고, 이들 모든 성분 신호의 합은

$$
\mathrm{P}_{1} + \mathrm{P}_{2} + \cdots + \mathrm{P}_{q} = \left(\mathrm{P}_{1,1} + \mathrm{P}_{2,1} + \cdots + \mathrm{P}_{q,1},\mathrm{H}_{1,n}^{\top} + \mathrm{H}_{2,n}^{\top} + \cdots + \mathrm{H}_{q,n}^{\top}\right)
$$

식\eqref{eq:8}과 \eqref{eq:9}에서 위의 식의 오른쪽 부분은 $$\left(\mathrm{X}_{1}, \mathrm{I}_{n}^{\top}\right)$$이므로

$$
\mathrm{P}_{1} + \mathrm{P}_{2} + \cdots + \mathrm{P}_{q} = \mathrm{X}
\label{eq:10}
\tag{10}
$$

식\eqref{eq:10}에서 Hankel 행렬을 사용할 때, SVD에 의해 얻어진 성분 신호는 원신호에 대한 단순 선형 중첩을 형성 할 수 있음을 알 수 있다. 이 선형 중첩의 장점은 원신호에서 하나의 성분신호를 격리하면 원신호에서 이 성분신호를 단순히 빼게된다. 따라서 이 빼기 연산은 분리된 성분 신호가 원신호와 동일하게 유지되도록 한다, 즉 고립된 성분 신호에 위상 변화가 없다는 것을 의미한다. 식\eqref{eq:10}은 또한 Hankel 행렬 기반 SVD의 재구성 공식이며, 원래 신호의 특징 정보를 추출하기 위해 몇 가지 관심있는 성분 신호를 간단히 합할 수 있다는 점에서 그 특별함도 있다.

즉, Hankel 행렬을 구축하고 SVD를 수행하여 신호를 분리해낸 여러개의 신호의 경우 단순 선형중첩으로 원신호의 복원이 가능하다는 의미이며, 이러한 신호분리는 완변한 선형분리로 볼 수 있다. 따라서 다른 신호처리기법에서 발생하는 위상천이등의 현상이 발생하지 않는다. 그리고 Hankel 행렬의 행과 열의 크기를 정함으로 이론상으로 데이터개수(샘플개수)-1 만큼의 신호분리가 가능하다.

## Referencese
{% bibliography --file svd --cited %}
