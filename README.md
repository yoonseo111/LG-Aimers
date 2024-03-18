# LG_Aimers
MQL 데이터 기반 B2B 영업기회 창출 예측 모델 개발


## 📌목표

- 영업 과정에서 전환 가능성이 높은 고객에게 영업 자원을 집중하기 위해 고객의 전환 여부를 예측하기 위한 기계학습 도입
- 고객의 다양한 정보를 보고 해당 고객이 전환 고객인지 아닌지 판단하는 모델 구현 및 성능 비교

## 📊데이터

- train.csv : 학습 데이터
- submission.csv : 테스트 데이터 (제출 파일)

| Field | 설명 |
| --- | --- |
| bant_submit | MQL 구성 요소들 중 [1]Budget(예산), [2]Title(고객의 직책/직급), [3]Needs(요구사항), [4]Timeline(희망 납기일) 4가지 항목에 대해서 작성된 값의 비율 |
| customer_country | 고객의 국적 |
| business_unit | MQL 요청 상품에 대응되는 사업부 |
| com_reg_ver_win_rate | Vertical Level 1, business unit, region을 기준으로 oppty 비율을 계산 |
| customer_idx | 고객의 회사명 |
| customer_type | 고객 유형 |
| enterprise | Global 기업인지, Small/Medium 규모의 기업인지 |
| historical_existing_cnt | 이전에 Converted(영업 전환) 되었던 횟수 |
| id_strategic_ver | (도메인 지식) 특정 사업부(Business Unit), 특정 사업 영역(Vertical Level1)에 대해 가중치를 부여 |
| it_strategic_ver | (도메인 지식) 특정 사업부(Business Unit), 특정 사업 영역(Vertical Level1)에 대해 가중치를 부여 |
| idit_strategic_ver | Id_strategic_ver이나 it_strategic_ver 값 중 하나라도 1의 값을 가지면 1 값으로 표현 |
| customer_job | 고객의 직업군 |
| lead_desc_length | 고객이 작성한 Lead Descriptoin 텍스트 총 길이 |
| inquiry_type | 고객의 문의 유형 |
| product_category | 요청 제품 카테고리 |
| product_subcategory | 요청 제품 하위 카테고리 |
| product_modelname | 요청 제품 모델명 |
| customer_country.1 | 담당 자사 법인명 기반의 지역 정보(대륙) |
| customer_position | 고객의 회사 직책 |
| response_corporate | 담당 자사 법인명 |
| expected_timeline | 고객의 요청한 처리 일정 |
| ver_cus | 특정 Vertical Level 1(사업영역) 이면서 Customer_type(고객 유형)이 소비자(End-user)인 경우에 대한 가중치 |
| ver_pro | 특정 Vertical Level 1(사업영역) 이면서 특정 Product Category(제품 유형)인 경우에 대한 가중치 |
| ver_win_rate_x | 전체 Lead 중에서 Vertical을 기준으로 Vertical 수 비율과 Vertical 별 Lead 수 대비 영업 전환 성공 비율 값을 곱한 값 |
| ver_win_ratio_per_bu | 특정 Vertical Level1의 Business Unit 별 샘플 수 대비 영업 전환된 샘플 수의 비율을 계산 |
| business_area | 고객의 사업 영역 |
| business_subarea | 고객의 세부 사업 영역 |
| lead_owner | 영업 담당자 이름 |
| is_converted | 영업 성공 여부. True일 시 성공. |
- 데이터 변수 개수가 많음 → 적절한 변수 선택 중요
- 결측치 값이 많은 변수 존재 → 어떻게 처리할 것인지가 중요
- 상관관계가 있는 변수 존재 → 변수 선택이나 결측치 처리에 도움을 주지 않을까

## 🧐방법

### DecisionTree

1. 변수 선택 및 결측치 처리
    - 상관관계가 있는 변수 중 하나 삭제 - product_category, customer_country.1, product_modelname
    - 가중치를 나타내는 변수의 결측치 0으로 대체 - id_strategic_ver, it_strategic_ver, idit_strategic_ver
    - 결측치 최빈값으로 대체 - customer_type
2. 레이블 인코딩
    - 범주형 데이터를 시리즈 형태로 받아 숫자형 데이터로 변환
3. 모델 학습
    - 학습 데이터와 검증 데이터로 분리
    - 모델 정의 및 학습 (DecisionTreeClassifier)
4. 모델 평가
    - F1 score: **0.7589**
5. 테스트 데이터 예측
    - 예측에 필요한 데이터 분리
    - 테스트 데이터 예측
    - 제출 파일 작성 및 제출

### RandomForest

- 위 방법과 동일하지만, 결측치가 존재하는 변수 중 범주형 변수는 결측치를 최빈값으로 대체하고 수치형 변수는 결측치를 중앙값으로 대체하여 진행
- RandomForestClassifier로 모델 학습 및 평가 진행
- F1 score: **0.7976**

### LGBM

- 위 방법과 동일하지만, 레이블 인코딩을 제외한 별도의 데이터 전처리 진행하지 않음
- LightGBM으로 모델 학습 및 평가 진행
- F1 score: **0.8404**

✅ LGBM으로 모델 학습을 진행하였을 때 F1 score가 가장 높게 나오고 실제 테스트 예측을 진행하고 파일을 제출했을 때도 가장 높은 점수가 나오는 것을 확인함

## 💬평가

- 데이터 변수가 다양하고 많아 전처리 고민에 많은 시간이 걸렸다.
- 데이터 전처리도 중요하지만 적절한 모델을 선택하여 사용하는 것도 성능 향상에 큰 도움을 주는 것을 알게 되었다.
- 변수 선택에 있어 신중해야 하고 함부로 변수를 제거하지 않는 것이 중요하다는 것을 알게 되었다.

## 💡향후

- 변수의 형태에 따른 데이터 전처리 방법에 대해 더 공부해 볼 것이다.
- 다양한 모델 시도를 통해 성능을 향상시켜 볼 것이다.
