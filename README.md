# 신용카드 사기 거래 탐지 AI 경진대회

[신용카드 사기 거래 탐지 AI 경진대회](https://dacon.io/competitions/official/235930/overview/description)

기간: 22년 7월 1일 ~ 22년 8월 5일

## 아이디어

크게 3단계로 나눌수 있습니다.

1. feature engineering
    - feature correlation 분석 및 feature 선택
2. train dataset class mapping
    - validation data를 바탕으로 train dataset class 매핑
3. classifier problem
    - xgboost classifier 활용

### feature engineering

신용카드 거래 데이터는 이미 익명화 처리되었습니다.

도메인 지식으로는 feature와 fraud 간 관계를 찾는 방법은 시도할 수 없습니다.

그리하여, 통계적방법으로 feature-fraud 관계를 분석하여 feature를 선택했습니다.

새로운 feature를 생성하지 않았습니다. feature scaling을 제외한 feature 값 변형은 없습니다.

### train dataset class mapping

validation data의 fraud 정보를 train에 반영시키기위해 KNN(neighbor=1) classifier 이용했습니다.

feature engineering으로 정한 15개의 feature를 15차원의 벡터공간으로 생각하시면됩니다.

- KNN은 10차원 이상되면 Curse of Dimensionality(고차원의 저주)가 있다고합니다. 그래서 T-SNE, umap으로 차원을 축소하여 진행하였지만 성능이 떨어져서 15차원을 그대로 이용하였습니다. 15차원 이지만 가장 가까운 대상 하나의 정보만을 반영하기때문에 영향이 크지 않다고 생각됩니다.

### classifier problem

xgboost 이진 분류 모델을 이용했습니다.

- 분류 클래스 세분화
    - 사기 거래에도 종류가 있다는 가정에 class를 3개로 늘렸습니다. class를 나누는 기준은 t-sne, pca, svc 그래프를 통해 fraud/non-fraud 경계선 근처에 있는 fraud를 다른 클래스로 지정했습니다.
    - 즉, 3개의 클래스 : non-fraud, fraud-1(애매), fraud-2(확실)
    - 하지만, 클래스를 2개로 진행하는것이 더 나은 성능을 보여줬습니다. 이는 안그래도 부족한 fraud 데이터에서 소수의 애매한 fraud를 분리해서 클래스로 지정한것이 유의미한 분류모델을 만들기에 도움이 되지 않은것 같습니다.

- 클래스 불균형
    - SMOTE 패키지를 이용한 오버샘플링 방법과 xgboost의 파라미터로 scale_pos_weight를 주는 방법 중 scale_pos_weight를 주는 방법이 더 좋은 성능을 보여줬습니다.

## 결과
macro-f1
- public: 0.93419 (3rd)
- private: 0.91221 (6nd)
