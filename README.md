# [ DL_Project1 ] 생육 기간 예측 프로젝트
* 목적 및 진행 방향<br/>
\- Part 1. 한 쌍의 작물 이미지를 입력받아 해당 작물의 생육 기간을 예측하는 모델 개발 및 성능 테스트<br/>
\- Part 2. Feature Map 시각화를 통해 Convolutional Layer가 이미지를 이해하는 방식에 대해 살펴보기

## 데이터 정보 및 학습 진행 방식
* DACON의 "생육 기간 예측 경진대회"에서 제공된 데이터 활용
* 2개 작물(청경채, 적상추)에 대한 생육 기간 경과일자별 이미지 데이터 저장<br/>
\- 총 753개(청경채 353개, 적상추 400개)
* 작물별 이미지 2장씩을 다양하게 조합하여 2장의 이미지간 경과일 기준 학습 및 모델 성능 테스트 진행
* 모델 평가 기준 : RMSE(Root Mean Squared Error)

## Part 1. MobileNet 기반 성능 향상 테스트

### 1) Baseline구성 및 데이터셋 생성 --> "DL_project1_v0.3c_유현준.ipynb"

* baseline 구성 및 기본 셋팅<br/>
\- 모델 : mobilenet_v2 활용<br/>
\- learning rate : 0.00005
* 데이터 전처리<br/>
\- 이미지 사이즈가 크므로 효과적인 학습 진행을 위해 이미지 Resize
* 데이터셋 생성<br/>
\- 작물별 2개 이미지 랜덤 추출 및 각각의 time_delta 계산

-----
### 2) 기본 셋팅 및 테스트(1차) --> "DL_project1_v0.5c_유현준.ipynb"

* 주요 셋팅 현황<br/>
\- 모델 : mobilenet_v2 활용<br/>
\- learning rate : 0.0005<br/>
\- epochs : 10<br/>
\- batch_size : 64

* 데이터셋 생성 현황<br/>
\- 작물별 2개 이미지 랜덤 추출한 5000개 데이터 기준 학습 진행<br/>
\- train, validation 비율 => 9 : 1

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 8.109<br/>
\- private : 8.243

-----
### 3) 추가 셋팅 및 테스트(2차) --> "DL_project1_v0.7c_유현준.ipynb"

#### 추가 셋팅 1. Normalize transformation(현 데이터 기준 산출한 평균, 표준편차 사용)

* 내용<br/>
\- 현재 이미지 데이터 기준, RGB 평균 및 표준편차 산출 후 적용

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 8.053<br/>
\- private : 8.278

#### 추가 셋팅 2. 상하반전, 좌우반전 transformation

* 내용<br/>
\- RandomHorizontalFlip(p=0.5) 적용,<br/>
\- RandomVerticalFlip(p=0.5) 적용

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 6.875<br/>
\- private : 7.010

-----
### 4) 추가 셋팅 및 테스트(3차) --> "DL_project1_v0.92c_유현준.ipynb"

#### 추가 셋팅 1. 이상치 검토 및 훈련 데이터 제외

* 내용<br/>
\- 훈련 데이터 중 "BC_03" 폴더 이미지의 경우 작물의 생육 상태가 이상치로 판단되어 제외 처리 후 테스트<br/>
\- 테스트 결과 오히려 제외 전보다 모델의 성능이 떨어진 것으로 확인됨 => 제외 전 데이터 기준으로 이후 테스트 진행

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 7.078<br/>
\- private : 7.317

#### 추가 셋팅 2. 90도 회전 transformation

* 내용<br/>
\- RandomRotation(90) 적용<br/>
\- 로테이션만 추가 반영한 것임에도 앞선 테스트(2차) 대비 모델 성능이 향상된 것으로 확인됨

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 6.326<br/>
\- private : 6.419

#### 추가 셋팅 3. Normalize transformation(Imagenet 데이터셋 기준 산출된 평균, 표준편차 사용)

* 내용<br/>
\- Imagenet 데이터셋 기준으로 산출된 평균 및 표준편차를 사용하여 테스트 진행<br/>
\- 테스트 결과 public 기준으로는 소폭 성능이 향상되었으나, private 기준으로는 약간 성능이 저하된 것으로 확인됨<br/>
\- 종합적으로 봤을 때, 기존 대비 유의미한 성능 향상은 없는 것으로 판단됨 => 현재 데이터 기준으로 산출한 평균, 표준편차를 사용하여 이후 테스트 진행

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 6.278<br/>
\- private : 6.502

-----
### 5) 현 모델 기준 epochs 셋팅 및 테스트(4차) --> "DL_project1_v1.1c_유현준.ipynb"

#### 1. epochs 15

* 내용<br/>
\- 오히려 epochs 10일 때 보다 모델의 성능이 떨어진 것으로 확인됨

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 6.498<br/>
\- private : 6.566

#### 2. epochs 20

* 내용<br/>
\- epochs 15는 물론이고, epochs 10일 때 보다 모델의 성능이 향상된 것으로 확인됨

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 6.067<br/>
\- private : 6.347

#### 3. epochs 19

* 내용<br/>
\- 20번의 epoch 중, validation 데이터 기준 성능이 가장 좋게 나타났던 model state를 불러와 테스트 진행<br/>
\- 지금까지의 테스트 중, 가장 좋은 성능을 나타냄 => public 기준 RMSE 5점대 진입

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 5.820<br/>
\- private : 6.040

-----
### 6) learning rate 조정, epochs 셋팅 및 테스트(5차) --> "DL_project1_v1.3c_유현준.ipynb"

#### 1. lr 0.00003, epochs 30

* 내용<br/>
\- 기대와 달리 오히려 기존 대비 성능이 저하된 것으로 확인됨

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 6.323<br/>
\- private : 6.510

#### 2. lr 0.00003, epochs 28

* 내용<br/>
\- 30번의 epoch 중, validation 데이터 기준 성능이 가장 좋게 나타났던 model state를 불러와 테스트 진행<br/>
\- public 기준 다시 5점대에 진입하긴 했으나, 기존 모델의 성능 대비 향상되지는 않은 것으로 확인됨

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : 5.873<br/>
\- private : 6.080

#### 3. lr 0.00001, epochs 40

* 내용<br/>
\- 

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : <br/>
\- private : 

#### 4. lr 0.00001, epochs 32

* 내용<br/>
\- 40번의 epoch 중, validation 데이터 기준 성능이 가장 좋게 나타났던 model state를 불러와 테스트 진행<br/>
\- 

* 테스트 결과 SCORE (DACON 제출 후 산출된 점수 기준)<br/>
\- public : <br/>
\- private : 

-----

## Part 2. Feature Map 시각화
