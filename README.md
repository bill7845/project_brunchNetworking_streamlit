# < Brunch Networking >

<br>
<br>

<b> - 브런치 작가를 위한 Text 분류모델과 추천시스템 - </b>

글쓰기 플랫폼 "Brunch"를 아시나요?
브런치는 "누구나 작가가 될 수 있다"를 모토로 하는 콘텐츠 퍼블리싱 플랫폼입니다.브런치가 주목받는 이유 중 하나는 전문 작가가 아닌 일반인들도 자신들의 글을 연재할 수 있는 서비스를 제공해준다는것인데요, 이러한 서비스를 바탕으로 현재 브런치에는 수 많은 일반인 작가들이 다양한 글을 게시하고 독자들을 끌어들이고 있습니다. <br><br>
"Brunch Networking" 프로젝트의 목적은 "머신러닝"을 활용하여 작가들에게 편리함을 제공하는것 입니다. "브런치 네트워킹"은 데이터 수집부터 전처리, 모델링, API를 활용한 실제 서비스까지 머신러닝 프로젝트의 전 과정을 다루고 있습니다.

<br>

## 최종 결과물 실행 영상(이미지 클릭)

먼저, 최종 결과물을 실행 영상으로 확인해보겠습니다.

[![Video Label](https://img.youtube.com/vi/RpEBgY3_stA/0.jpg)](https://youtu.be/RpEBgY3_stA)

<br>

## < 전체 흐름도 >

<br>

프로젝트의 전체적인 흐름입니다. BeautifulSoup과 Selenium을 사용하여 브런치의 게시글 20만개를 수집하였고, 한글 형태소 분석기인 Konlpy를 활용하여 수집된 텍스트 데이터를 전처리하였습니다. 이후에 여러가지 분류모델을 실험하고 가장 적합한 모델을 최종 선택하여 Streamlit을 활용해 API형태로 구현하였습니다.

![메인흐름](https://user-images.githubusercontent.com/35517797/81902112-8f7e4080-95fa-11ea-8954-1ab9952ec4e6.PNG)

<br>

## < 모델 학습 및 선택 >

<br>

모델 학습 과정과 최종 모델 선택에 관한 부분입니다. "브런치 네트워킹"의 분류모델이 풀어야할 과제는 18개의 클래스를 가지는 텍스트 다중분류 문제입니다. 가장 중요하고 우선시되어야 하는것은 역시 수집된 텍스트 데이터에 대한 전처리입니다. 다음의 과정으로 진행하였습니다.

  1. <b> url, 특수 문자, 마침표를 제거한다. </b>
  2. <b> 영어,한글을 제외한 언어를 제거한다. </b>
  3. <b> 한글 불용어를 제거한다. </b>
  4. <b> 형태소 분석 </b>

형태소 분석의 경우에 Konlpy의 mecab,Okt를 사용하여 각각 명사추출,품사추출 등 여러가지 경우를 실험하였고 최종적으로 길이가 2이상인 명사추출법을 적용하였습니다. 다음으로는 데이터를 머신러닝 모델이 이해할 수 있도록 벡터화 과정을 진행하였습니다.

  1. <b> Bag-of-words </b>
  2. <b> Word2Vec </b>

BOW와 Word2Vec 2가지의 방법을 실험하였습니다. 먼저 BOW 방식은 문서가 가지는 모든 단어(Words)를 문맥이나 순서를 무시하고 단어에 빈도 값을 부여하는 방식입니다. 즉, 전체 문서에서 각 단어들이 등장하는 빈도수를 기반으로 피처 벡터화를 수행하는 것입니다. BOW 방식은 "문맥의미 반영부족"과 "희소 행렬"이라는 문제점을 발생시킵니다. 따라서 이러한 문제를 보완한 TF-IDF 방식을 적용하였습니다. TF-IDF 방식도 BOW에 속하지만 개별 문서에서 자주 나타나는 단어에 높은 "가중치"를 주되, 모든 문서에서 전반적으로 나타나는 단어에 대해서는 페널티를 주는 방식을 통해 BOW가 가진 문제점을 보완한 방식입니다.

<br>

Word2Vec의 경우 BOW처럼 n차원의 희소 벡터를 만드는것이 아닌 밀집된 벡터를 통해 좀 더 압축된(Dense) 데이터로 변환을 가능케 합니다. 따라서 Word2Vec을 사용할 경우에는 "차원의 저주"와 같이 희소행렬 방식에서 자주 발생되는 문제를 피할수 있고 단어간의 관계(ex. 엄마+아빠=부모님)와 단어간 유사도를 구할수 있는 장점이 있습니다.

<br>

~~ 모델 설명 부분 추가 ~~

* 현재 브런치의 카테고리는 총 24개입니다. 이 중에서 디자인, 그림웹툰 등 텍스트 보다는 이미지 위주의 글이 대다수인 6개의 카테고리를 제외한 18개의 카테고리만을 사용하였습니다.

![모델학습 및 선택](https://user-images.githubusercontent.com/35517797/81902305-e08e3480-95fa-11ea-88bb-b151e2a45848.PNG)

## < 분류모델 API >

![분류모델](https://user-images.githubusercontent.com/35517797/81902312-e4ba5200-95fa-11ea-82ea-8109261abbfa.PNG)

## < 키워드 기반 text 추천 >

<br> 

"브런치 네트워킹"에 적용한 추천시스템에 관련된 부분입니다. 추천 시스템은 크게 2가지로 나누어 집니다.

  1. 콘텐츠 기반 필터링(Content based filtering)
  2. 협업 필터링(Collaborative Filtering)
  
협업 필터링 방식의 경우 사용자가 글에 매긴 평점 정보나 구독 이력과 같은 사용자 행동 양식(User Behavior)을 기반으로 합니다. 이런 사용자 행동 양식 데이터는 따로 수집이 어렵기 때문에 "브런치 네트워킹"의 추천 시스템은 콘텐츠 기반 필터링 방식을 사용하였습니다. <br>

콘텐츠 기반 필터링 방식은 사용자가 특정한 아이템을 매우 선호하는 경우, 그 아이템과 비슷한 콘텐츠를 가진 다른 아이템을 추천하는 방식입니다. 
본 프로젝트는 브런치의 "작가"를 위한 서비스이므로 사용자를 작가로 한정하고, 작가가 게시하고자 하는 글을 기반으로 콘텐츠 기반 필터링 추천을 진행합니다. 

  1. <b> 키워드 기반 추천 </b> 
  2. <b> 문서 유사도 기반 추천 </b>
  
 키워드 기반 추천은 우선, 사용자가 자신의 게시글에 대한 키워드를 선택하고 입력받은 키워드 리스트와 기존 브런치 게시글(입력 게시글과 같은 카테고리)의 문서별 키워드 리스트간의 코사인 유사도를 통해 비교하는 방식으로 진행합니다. 만약 


![추천1](https://user-images.githubusercontent.com/35517797/81902318-eab03300-95fa-11ea-9b23-8061e83324c7.PNG)

## < 문서 유사도 기반 text 추천 >
![추천2](https://user-images.githubusercontent.com/35517797/81902325-ec79f680-95fa-11ea-9f97-5c5c35322ab4.PNG)
