## Pre-trained Model Test
  
### UniAD 프레임워크는 두 단계로 학습된다.

Stage 1 : Perception training으로 안정적인 가중치를 얻기 위해 인지 과정(Track, Map)을
     먼저 훈련한다.
 
Stage 2 : End to End training으로 인지 과정을 포함한 예측(Motion) 계획(Planning)까지 모든 작업 모듈을 함께 최적화한다. 

---

### 실험 환경

Pre-trained model 의 pkl은 다운과 설정이 되지만 이 때 pkl은 full(v1.0 – trainval)를 기준으로 하고 다운로드와 환경설정이 잘 되지 않았다.

> 데이터셋은 nuScenes v1.0-mini로 하고 여기에 맞는 pkl을 새로 생성
tools/uniad_create_data.sh 준비 스크립트 활용 (데이터셋 준비 옵션2: nuScenes 데이터셋을 모델이 사용할 수 있는 .pkl로 포맷으로 변환하기 위한 사용을 안내하고 있다.)

미리 만들어진 공식 Stage1 모델을 mini-dataset으로 만든 pkl을 사용해 평가 + Stage2 학습 및 평가

---
### 실험 순서

1. 베이스라인으로 삼을 사전학습 모델을 평가

2. Stage2 모델 전체 추가 학습 (2Epochs)

3. Stage 2까지의 사전학습 모델을 Minidataset으로 추가학습

---
### 1. 사전학습 모델 평가

<img width="776" height="76" alt="image" src="https://github.com/user-attachments/assets/f14a819e-06ba-4950-879c-90fa57339ec0" /> </br>

주요 클래스별 성능</br>
Car : 0.654</br>
Bus / Truck : 0.711 / 0.750</br>
Pedestrian : 0.477
</br>

### 2. stage2 모델 추가학습

목표 : Stage-2 모델 전체를 Mini-dataset으로 짧게 추가 학습시켰을 때의 성능 변화 관찰, 추가 학습이니 성능이 개선될 거라고 예상

<img width="608" height="276" alt="image" src="https://github.com/user-attachments/assets/fce06a82-87be-4bd0-b1da-ef0e67a72721" /></br>

결론 : 소규모 데이터로 전체 모델을 얕게 파인튜닝하는 방식은 오히려 성능 저하를 유발할 수 있다.</br>
따라서 더 정교한 파인튜닝 전략이 필요성을 확인했다.
</br>

### 3. stage2까지의 사전학습 모델을 추가학습

학습 방식</br>
동결 : MotionHead 를 제외한 인식 모듈의 가중치를 고정</br>
학습 : 오직 MotionHead만 minidataset으로 8epoch 집중 학습 
목표 : 강력한 인식 성능(Stage1)은 보존하면서 목표로 하는 예측성능만 개선하기 위함

성능

<img width="847" height="70" alt="image" src="https://github.com/user-attachments/assets/2fee9144-ad51-4b74-bc9a-92da89099e60" /> </br>

결론 : MotionHead만 선택적으로 학습하는 전략이 효과가 있었음을 확인했다. </br>
대규모로 얕은 추가 파인튜닝보다 목표 모듈을 선택적으로 학습하는 것이 더 안정적으로 성능이 향상됨을 알 수 있었다.

---




