---
author: admin
comments: true
date: 2017-12-30 17:21:10+09:00
layout: post
title: 콘크리트 균열 분석 B-COSFIRE(MATLAB)
publish: true
description: 콘크리트 균열을 탐지 하는 방법론은 여러가지가 있다. 특히 직접 이미지프로세싱을 통한 직접 추출하는 방식이 일반적이다. 그러나 알고리즘의 수행 능력은 둘째 치더라도 수행 성능은 담보하기 어렵다. B-COSFIRE 필터는 의료 영상에서 혈관을 탐색하는 알고리즘에서 착안한다. 특히 선구조를 식별하는 알고리즘으로써 균열에도 적용이 충분히 가능할꺼라는 시도로 몇몇 엔지니어가 최근 제시하였다.
featured: /public/images/2017/12/readme/screenshot.png
category: engineering
tag:
- crack
- concrete
- matlab
---

콘크리트 균열을 탐지 하는 방법론은 여러가지가 있다. 특히 직접 이미지프로세싱을 통한 직접 추출하는 방식이 일반적이다. 그러나 알고리즘의 수행 능력은 둘째 치더라도 수행 성능은 담보하기 어렵다. B-COSFIRE 필터는 의료 영상에서 혈관을 탐색하는 알고리즘에서 착안한다. 특히 선구조를 식별하는 알고리즘으로써 균열에도 적용이 충분히 가능할꺼라는 시도로 몇몇 엔지니어가 최근 제시하였다. 이 연구자는 MATLAB 소스파일을 공개하였다. [Trainable COSFIRE filters for curvilinear structure delineation in images](https://kr.mathworks.com/matlabcentral/fileexchange/49172-trainable-cosfire-filters-for-curvilinear-structure-delineation-in-images?requestedDomain=true) 또한 [Gitlab Repository](https://gitlab.com/nicstrisc/B-COSFIRE-MATLAB) 에도 계속 업데이트 하고 있다. 자세하게 서술된 학술지 저널까지 공개되어 있다. [Azzopardi, G., Strisciuglio, N., Vento, M., Petkov, N.: Trainable COSFIRE  filters for vessel delineation with application to retinal images. Medical Image Analysis 19(1), 46 - 57, 2015](http://www.cs.rug.nl/~george/articles/MEDIA2015.pdf) 예제는 3가지로 소개되어 있다.

- `ExampleBloodVesselSegmentation.m` : 라인 검출기와 라인-엣지 검출기의 구성 및 망막 이미지에서의 혈관 분할에 대한 예제 코드.
최종 응답은 두 필터의 응답 합계로 응용한다.
- `PavementCrackDelineation.m` (CAIP 2017) : CAIP17에서 제시된 도로 포장 이미지의 균열 감지를 위한 이미지 프로세싱 파이프 라인에 B-COSFIRE 필터 적용. 이 코드는 논문에서 보고 된 벤치 마크 결과를 제공한다.
_Azzopardi, G. Petkov, N. "Detection of Curved Lines with B-COSFIRE Filters: A Case Study on Crack Delineation", IWOBI 2017_
- `INRIAImages.m` (IWOBI 2017) : B-COSFIRE 필터를 사용하여 이미지의 가늘고 긴 선형구조를 감지한다.
이 코드는 종이에 사용 된 INRIA 데이터의 이미지에 대한 벤치 마크 결과를 제공한다

저자는 소스를 공개하며 다음과 같이 소개하였다.
<br><br>

> B-COSFIRE filters are non-linear trainable filters for detection of elongated patterns in images.
> This is the code of the trainable non-linear B-COSFIRE filters for delineation of elongated patterns in images.

<br><br>
하지만 학습이라기 보다는 파라미터 최적화를 통해 특정 도로에 적합하도록 개선하는 정도이다. 또한 미세균열을 찾기 위한 작업에서 고해상도 이미지를 한번에 처리하기엔 성능면에서 큰 효과를 거두지 못할 것 같다.
<br><br>
그러나 추후 딥러닝을 위한 ground truth 자료를 충분히 확보하기 위해 본 알고리즘을 써보는 것은 나쁘지 않을 것 같다. 딥러닝에서는 실측자료를 얼마나 많이 얻었냐에 따라 그 성능이 많이 달라지기 때문에 GT작업이 매우 중요하다. 모두 사람의 노동력을 빌릴 수 없기 때문에 최초로 걸러내는 작업에서 B-COSFIRE 알고리즘을 사용하는 방법은 나쁘지 않을 것 같다. 따라서 GT작업을 위한 프로그램을 개발해보았다. MATLAB이지만 MATLAB이 없어도 GUI로 빌드하여 MCR(MATLAB Runtime Compiler)를 통해 배포가 가능하다.
<br><br>
개발자의 논문에 서술된 B-COSFIRE 방법론에 대하여 간략히 소개하고 프로그램 사용법을 기술하였다. 근무하던 회사와 연관되어 있어 소스는 공개가 불가능하다. 하지만 위의 [Gitlab Repository](https://gitlab.com/nicstrisc/B-COSFIRE-MATLAB)에서 응용하여 관심있는 사람이라면 쉽게 개발이 가능할 것이다.
<br><br>
## B-COSFIRE 방법론
논문에서는 다른 균열 분석 알고리즘과 벤치마크를 수행하였다. 그러나 앞서 이야기한 것처럼 딥러닝을 위한 중간과정으로 채택하였기 때문에 알고리즘의 워크플로는 다음과 같다.

 1. B-COSFIRE filtering
 2. binarization with non-maximum suppression
 3. ~~hysteresis thresholding~~ (최적화 및 벤치마크를 위함)
 4. minimum enclosing rectangle
 5. making ground truth

### Configure of a B-COSFIRE filter
B-COSFIRE 필터는 Gaussian 함수의 그룹 $DoG_{\sigma}$로부터 입력을 받으며 외부 Gaussian 함수는 표준 편차 $\sigma $를 가진다.

\begin{equation}
DoG_{\sigma}(x,y)= \frac{\textrm{exp}\left( -\frac{x^2 + y^2}{2(0.5\sigma)^2}\right)}{2\pi\left(0.5\sigma\right)^2} - \frac{\textrm{exp}\left( -\frac{x^2 + y^2}{2\sigma^2}\right)}{2\pi\sigma^2}
\end{equation}

Petkov와 Vosser[^petkov2005modifications]에서, 위의 함수에 대해 0.96 $\sigma$의 반경 또는 1.92 $\sigma$의 너비를 가진 라인에 대해 최대 응답이 추출 되었음이 나타났다. 이 결과를 바탕으로 외부 표준 편차 $\sigma = w/1.92$를 설정한다. 여기서 $w$는 관심있는 선의 너비이다.
<br>
B-COSFIRE 필터의 구조, 즉 우리가 DoG 응답을 고려하는 위치는 주어진 프로토타입 패턴에 대한 자동 구성 프로세스에서 결정된다. 구성에 대한 자세한 내용은 Azzopardi et al.의 논문[^azzopardi2015trainable]을 참조바람. 우리는 너비 $w$, 길이 $l$ 및 방향 $\phi$의 프로토타입 선 구조에 B-COSFIRE 필터를 구성한다. 구성 결과는 다음과 같이 설정된다. $B_{w, l, \phi}$ :

[^petkov2005modifications]: Petkov, N.; Visser, W.T. Modifications of center-surround, spot detection and dot-pattern selective operators. _Institute of Mathematics and Computing Science, University of Groningen, The Netherlands (CS 2005-9-01) 2005_, pp. 1–4..
[^azzopardi2015trainable]: Azzopardi, G.; Strisciuglio, N.; Vento, M.; Petkov, N. Trainable COSFIRE filters for vessel delineation with application to retinal images. Medical image analysis 2015, 19, 46–57.

\begin{equation}
B_{w,l,\phi} = \left[ (0,0),(\lambda,\phi),(\lambda, 2\pi-\phi)\right] \cup \left[ (\rho_{i},\phi) \right] \cup \left[ (\rho_{i},2\pi-\phi) \right]
\end{equation}

여기서 $\lambda = \left[ (l-1)/2 \right]$ 와 $\rho_{i} = \eta i$ with $i=1,...,\left[(\lambda-1)/\eta \right] -1$. $\phi$ 은 선의 기본 방향이다. The two-tuples in the set $B_{w,l,\phi}$ 집합에서 두개의 튜플은 B-COSFIRE 필터 지원 센터에 대한 위치 (거리 및 극각)를 나타내며 center-on Gaussians (DoG) 필터의 응답을 나타낸다. 파라미터 $\eta \left(\textrm{with } 1\leq \eta \leq \lambda \right)$는 고려 된 DoG 응답 사이의 픽셀 간격을 나타낸다. $\eta=1$일때 우리는 원하는 너비 $w$, 길이 $l$, 그리고 방향 $\phi$, 그리고 $\eta=\left[(l-1)/2\right]$일때 (가능한 최대 값) 결과 필터는 세 개의 튜플로만 구성된다: 중앙에서 DoG 응답을 참조하는 튜플 $ (0,0 $ 및 두 개의 튜플 $\left(\left[(l-1)/2\right],\phi \right)$ 및 $\left(\left[(l-1)/2\right],2\pi-\phi\right)$는 B-COSFIRE 필터의 선택도는 지지체의 양쪽면에서 가장 먼 거리를 가리키는 값이다.

### Response of a B-COSFIRE filter

B-COSFIRE 필터 $B_{w,l,\phi}(x,y)$의 응답은 *filter-blur-shift-combine*의 네 단계로 계산된다.
첫 번째 단계에서는 입력 이미지 $I$를 DoG 커널 $DoG_{\sigma=w/1.92}$로 *filter*하고 $C$로 결과 이미지를 나타낸다:

\begin{equation}
C(x,y)=\left| \sum_{x'=-3\sigma}^{3\sigma} \sum_{y'=-3\sigma}^{3\sigma} I(x,y)DoG_{\sigma}(x-x',y-y')\right|^{+}
\end{equation}

여기서 연산자 $\left\|.\right\|^{+}$는 반파 정류를 나타내며 최근 정류 선형 유닛 (ReLU)으로 알려져 있다. 

인공 신경망의 맥락에서, 정류기는 인수의 양의 부분으로 정의된 활성화 함수이다. $\left\|x\right\|^{+} = \max(0,x)$ 여기서 $x$는 뉴런에 대한 입력이다. 이 함수는 램프(ramp) 기능으로도 알려져 있으며 전기 공학의 반 파장 정류와 유사하다. 이 활성화 함수는 처음 Hahnloser et al.[^hahnlose2000]에 의해 강한 생물학적 동기와 수학적 정당성을 지닌 저널 Nature지의 2000년 논문[^hahnlose2000]에서 소개되었다.  이 함수는 널리 사용되는 로지스틱 시그모이드(확률론에 의해 영감을 얻은 것; 로지스틱 회귀분석 참조)보다 효과적으로 Convolution 네트워크[^azzopardi2013trainable]에 사용되게 되었다. 또한 [^azzopardi2015trainable] 대응안인 쌍곡선 탄젠트(hyperbolic tangent) 보다 실용적이다. 2017년에 들어서 현재 이 정류기는 딥 뉴럴 네트워크에서 가장 널리 알려진 활성화 함수이다. [^bibiloni2016survey] [^chai2013recovering]

[^hahnlose2000]: Hahnloser R. H., Sarpeshkar R., Mahowald M. A., Douglas R. J., Seung H. S., Digital selection and analogue amplification coexist in a cortex-inspired silicon circuit, *Nature* **2000** Jun 22;405(6789):947-51.
[^azzopardi2013trainable]: Azzopardi, G.; Petkov, N. A CORF computational model of a simple cell that relies on LGN input outperforms the Gabor function model. *Biological cybernetics* **2012**, pp. 1–13.
[^azzopardi2015trainable]: Azzopardi, G.; Strisciuglio, N.; Vento, M.; Petkov, N. Trainable COSFIRE filters for vessel delineation with application to retinal images. *Medical image analysis* **2015**, 19, 46–57.
[^bibiloni2016survey]: Bibiloni, P.; González-Hidalgo, M.; Massanet, S. A survey on curvilinear object segmentation in multiple applications. *Pattern Recognition* **2016**, 60, 949–970
[^chai2013recovering]: Chai, D.; Forstner, W.; Lafarge, F. Recovering line-networks in images by junction-point processes. *Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition*, **2013**, pp. 1894–1901.

그후 선호하는 위치와 관련하여 약간의 tolerance를 허용하기 위해 가중치가 최대인 비선형 blurring 연산으로 DoG 응답을 *blur* 처리한다. 가중치는 표준 편차 $\sigma_{i}$' 가 B-COSFIRE 필터의 지원 센터로부터의 거리가 증가함에 따라 선형 적으로 증가하는 가우시안 함수에 의해 주어진다 : $\sigma_{i}$'$=\sigma_{0}$'$+\alpha\rho_{i}$. $\sigma_{0}$' 와 $\alpha$ 값은 파라미터이며, 프로토타입 형태에서 변형에 대한 tolerance를 정규화한다.


세 번째 단계에서는 벡터 $(\rho_{i}, 2\pi-\phi_{i})$에 의해 $i$번째 *blur* 처리한 DoG 응답을 *shift* 변환한다. 이러한 방식으로 모든 관련 DoG 응답은 관련 위치, 즉 관련 B-COSFIRE 필터의 지지점에서 연결된다. $i$ 번째 튜플의 $(x, y)$ 위치에서 *blur*처리되고 *shift* 된 DoG 응답 $s_{\rho_{i},\phi_{i}}(x,y)$ 은:

\begin{equation}
s_{\rho_{i},\phi_{i}}(x,y) = \max_{x',y'} \left[ C(x-x'-\Delta x,y-y'-\Delta y) G_{\sigma'_{i}}(x',y')\right]
\end{equation}

여기서 $\Delta x = -\rho_{i}\cos\phi_{i}$ 이고, $\Delta y = -\rho_{i}\sin\phi_{i}$ 이다.
마지막 단계에서 B-COSFIRE필터의 응답 $r_{B_{w,l,\phi}}(x,y)$,의 기하평균은 다음과 같다.

\begin{equation}
r_{B_{w,l,\phi}}(x,y) = \left(\prod_{i=1}^{\left| B_{w,l}\right|} s_{\rho_{i},\phi_{i}}(x,y)\right)^{1/\left|B_{w,l}\right|}
\end{equation}

논문에서 방법론 제시 이후의 작업은 타 알고리즘과 비교하기 위한 벤치마크를 수행하는 결과를 나타낸다. 나는 조금 다른 방식으로 이진화(binarization)을 수행했으며, 균열을 분류하기 위한 MER(Minimum enclosing rectangle)작업을 수행하였다. 이 방법은 직관적인 방법론이라서 이미지 프로세싱에 관심이 있다면, DoG 응답을 가지고 충분히 추출해낼 수 있다.

### Binarization
이진화 방법은 1차적으로 `binarize()`함수로 수행되며 2차적으로 pixel 연결성을 찾아 쓰레숄딩 한다. 이것은 MATLAB *Image Processing Toolbox*에 있는 `bwconncomp()` 함수를 사용한다. `binarize()`함수는 아래와 같다.
```
function [binarymap] = binarize(rotoutput1, highthresh)
    orienslist = 0:pi/12:pi-pi/12;
    [viewResult, oriensMatrix] = calc_viewimage(rotoutput1,1:numel(orienslist), orienslist);
    thinning = calc_thinning(viewResult, oriensMatrix, 1);
    binarymap = calc_hysteresis(thinning, 1, 0.5*highthresh*max(thinning(:)), highthresh*max(thinning(:)));
end
```
2차 연결픽셀의 쓰레숄딩하는 방법은 아래와 같다. 연결 픽셀 수 `ccthreshold`값을 지정해 주어야 한다.

```
    cc = bwconncomp(binImg, 4);
    nograin=cellfun('length',cc.PixelIdxList);
    [val,id]=sort(nograin,'descend');
    
    ite = find(val>ccthreshold,1,'last'); % thresholding cc
```

### Minimum Enclosing Rectangle
MER 알고리즘은 [`minBoundingBox()`](https://www.mathworks.com/matlabcentral/mlc-downloads/downloads/submissions/31126/versions/2/previews/minBoundingBox.m/index.html?access_key=)을 응용하였다.
```
function bb = minEnclosingRectangle(X)

k = convhull(X(1,:),X(2,:));
CH = X(:,k);

E = diff(CH,1,2);
T = atan2(E(2,:),E(1,:));
T = unique(mod(T,pi/2));

R = cos( reshape(repmat(T,2,2),2*length(T),2) ...
    + repmat([0 -pi ; pi 0]/2,length(T),1));

RCH = R*CH;

bsize = max(RCH,[],2) - min(RCH,[],2);
area  = prod(reshape(bsize,2,length(bsize)/2));

[a,i] = min(area);

Rf    = R(2*i+[-1 0],:);
bound = Rf * CH;
bmin  = min(bound,[],2);
bmax  = max(bound,[],2);

Rf = Rf';
bb(:,4) = bmax(1)*Rf(:,1) + bmin(2)*Rf(:,2);
bb(:,1) = bmin(1)*Rf(:,1) + bmin(2)*Rf(:,2);
bb(:,2) = bmin(1)*Rf(:,1) + bmax(2)*Rf(:,2);
bb(:,3) = bmax(1)*Rf(:,1) + bmax(2)*Rf(:,2);
```

## 프로그램 UI
 ![screnshot]({{ site.baseurl }}/public/images/2017/12/readme/screenshot.png)

### Usages
 - 실행화면
![intro]({{ site.baseurl }}/public/images/2017/12/readme/intro.png)
 - Image File Open : 이미지 열기 Open 버튼을 클릭하면 다음과 같은 파일탐색창이 나온다. 균열 이미지 파일을 선택한 후 열기를 선택한다.
 - 이미지 로딩 : 아래 그림과 같이 이미지 파일을 불러온다.
![load image]({{ site.baseurl }}/public/images/2017/12/readme/loadimage.png)
 - 파라미터 설정 : 논문을 참고하여 DoG (Difference of Gaussian)의 파라미터와, 이진화 임계값(threshold)과 픽셀 연결 임계값을 설정한다. 기본설정은 아래표와 같다.

|  sigma  |  length  |  sigma_0  |  alpha  |  no. of orientation  |  padding size  |  DoG threshold  |  Connected pixels threshold  |
| :-----: | :------: | :-------: | :-----: | :------------------: | :------------: | :-------------: | :--------------------------: |
|  3.3    |  14      |  2        |  1      |  12                  |  50            |  0.5            |  200                         |

 - 분석 시작 : Start Analysis 를 클릭하여 분석을 수행한다. (화면 하단에서 분석 진행상황이 표시 된다.)
 - 분석 완료 : 분석이 완료되면 이미지의 왼쪽은 DoG 응답 결과와 오른쪽 균열 분석결과가 표시 된다. 그리고 이미지의 아래쪽엔 균열 분석한 결과가 스프에드시트 형태로 표시된다.
 - zoom tool ![]({{ site.baseurl }}/public/images/2017/12/readme/zoomtool.png) 을 이용하여 분석결과를 zoom-in zoom-out하여 결과를 확인할 수 있다. (확대툴을 사용하여 마우스를 이미지위에서 드래그하면, 관심 영역이 확대 됨, 마우스를 더블클릭하면 zoom out 됨)
 ![zoomed crack]({{ site.baseurl }}/public/images/2017/12/readme/zoomed.png)
 - 좌측이미지 상단의 Radio버튼에서 original 과 DoG response를 선택하여 원본이미지와 DoG 응답결과를 함께 확인 할 수 있다. (단, 이미지 변경 후 마우스 더블클릭으로 zoom out이 되지 않으니 zoom reset 버튼을 사용)

|  original image                |  DoG response image                    |
| :----------------------------: | :------------------------------------: |
| ![original]({{ site.baseurl }}/public/images/2017/12/readme/zoomed.png) | ![DoG response]({{ site.baseurl }}/public/images/2017/12/readme/zoomed_dog.png) |

 - 균열 Result 테이블은 행방향으로 순서대로 검출된 균열의 크기가 큰 균열부터 정렬이 되어있다. 첫번째 열부터
![cracks result]({{ site.baseurl }}/public/images/2017/12/readme/result.png)
  1. 균열의 orientation : 진행방향을 기준으로 가로방향에서 균열의 방향을 각도(degree)로 표현한다.
  2. 균열 위치 X : MER(Minimum Enclosing Rectangle)결과에서 균열의 중심좌표의 X축 좌표를 표시한다. (현재는 pixel 단위)
  3. 균열 위치 Y : MER(Minimum Enclosing Rectangle)결과에서 균열의 중심좌표의 Y축 좌표를 표시한다. (현재는 pixel 단위)
  4. 균열 길이 : MER(Minimum Enclosing Rectangle)결과에서 균열의 가장 긴변 축의 길이를 표시한다. (현재는 pixel 단위)
  5. 균열 폭 : Thinning 결과와 이진화 결과를 비교하여 평균 균열 폭을 표시한다. (현재는 pixel 단위)
  6. 실측 균열 : 이 균열이 육안으로 확인했을 때 실제로 균열으로 판단될 때 체크 (ground truth)
  7. 실측 구조물 : 이 균열이 육안으로 확인했을 때 도상의 구조물을 판단될 때 체크 (ground truth)
  8. 실측 노이즈 : 이 균열이 육안으로 확인했을 때 노이즈로 판단될 때 체크 (ground truth)

 - 균열 검측 결과는 마우스로 셀을 선택할 때 마다 균열만 zoom in 해서 보여준다. 이를 해제 하기 위한 방법은 zoom reset 버튼을 클릭하면 전체 이미지 화면을 볼 수 있다. 차례대로 균열을 ground truth로 선택하며 실측과정을 수행한다.

|  ![crack 1]({{ site.baseurl }}/public/images/2017/12/readme/crack01.png)  |  ![crack 2]({{ site.baseurl }}/public/images/2017/12/readme/crack02.png)  |
| :------------------------------: | :------------------------------: |
|  ![crack 3]({{ site.baseurl }}/public/images/2017/12/readme/crack03.png)  |  ![crack 4]({{ site.baseurl }}/public/images/2017/12/readme/crack04.png)  |
|  ![crack 5]({{ site.baseurl }}/public/images/2017/12/readme/crack05.png)  |  ![crack 6]({{ site.baseurl }}/public/images/2017/12/readme/crack06.png)  |
|  ![crack 7]({{ site.baseurl }}/public/images/2017/12/readme/crack07.png)  |  ![crack 8]({{ site.baseurl }}/public/images/2017/12/readme/crack08.png)  |

 - 결과 저장 : 균열의 ground truth 결과는 csv(comma separated value)파일로 저장할 수 있다. 또한 DoG 응답 결과와 ground truth에서 균열만 탐색한 결과를 이미지 파일로 저장할 수 있다.

### Ground Truth Result (example)
 - 테스트 이미지로 분석한 콘크리트 균열의 실측자료
 - Ground truth (binary image 결과) : 추후 CNN을 위한 실측자료로 활용

|  Original Image                    |  Ground Truth (Cracks)                           |
| :--------------------------------: | :----------------------------------------------: | 
|  ![original]({{ site.baseurl }}/public/images/2017/12/readme/original.png)  |  ![ground truth]({{ site.baseurl }}/public/images/2017/12/readme/ground_truth_cracks.png) |

### Version History
- __v1.0__ 한 개의 이미지를 분석함
- __v1.0.1__ Parameter 오류 수정
- __v1.0.1__ 균열 결과 default로 노이즈 선택 (효율적인 ground truth를 위함)

### 추후 개발 사항
- [ ] 배치 프로세스 : ground truth 하기전에 분석시간이 조금 걸리기 때문에 배치 분석을 하고 결과 파일을 로딩하여 GT를 수행할 수 있도록 개선 필요
- [ ] GT결과를 바탕으로 crack, structure, noise를 blob을 다른 색상으로 표시할 수 있도록 개선
- [ ] 분석결과 서버 DB에 결과를 저장할 수 있도록 개선
- [ ] 다양한 UI 개선 등