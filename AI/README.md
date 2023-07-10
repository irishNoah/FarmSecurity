- 해당 MD는 프로젝트에서 AI와 관련된 부분을 중점으로 다룹니다.
  - AI 파트에서, 저(박창영)는 데이터셋 수집 위주로 했으므로 AI와 관련된 부분은 잘 모릅니다.
  - 다만, 해당 MD는 프로젝트 설명 차원에서 작성했을 뿐이니 참고 바랍니다.

# :factory: YOLO를 활용한 객체 인식 과정(train&test 포함)
## :one: __학습 Dataset 개수 차이에 따른 비교__ <br>
:heavy_check_mark: 학습을 진행할 때마다 weights 파일 생성. Iteration을 중점으로 1000단위마다 파일 생성<br>
- 예) yolov4-1000.weights / yolov4-3000.weights 등<br>

:heavy_check_mark: 이 weights 파일을 통해 Avg Loss, mAP, IoU 등을 알 수 있음<br>
:heavy_check_mark: 보통 6000 Itertaion으로 많이 채택하여 사용<br>

__1) 이미지 수 기준(150장 vs 750장)__ <br>
:heavy_check_mark: 새 / 동물 / 사람 별 각 50장 씩 추출한 총 150장과, 각 150장 씩 추출한 총 750장 간 학습 및 실험에서 Iteration에 따른 Avg Loss 및 IoU 측정 <br><br>

__#1 Avg Loss, IoU 측면__ <br>

__1. 150장__
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/180736911-2fbc9124-587a-479b-b452-22a9e43c27c0.png" alt="50_dataset_6000_train_chart" width="500" height="500"/>
</div>

:stars: 위 표에서 파란색 선은 Avg Loss, 빨간색 선은 IoU를 지칭<br>
:stars: 150장에서 Avg Loss는 Iteration이 커질수록 감소, IoU는 Itration이 커질수록 증가 <br>

__2. 750장__
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/180737017-ea6835b5-eedc-4803-8f47-a45de30a0895.png" alt="250_dataset_6000_train_chart" width="500" height="500"/>
</div>

:stars: 각 선이 의미하는 바는 150장의 표와 동일<br>
:stars: 150장과 동일하게 750장에서도 Avg Loss는 Iteration이 커질수록 감소, IoU는 Itration이 커질수록 증가<br><br>

__3. 2250장__
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/192546039-d4f4b0e0-d09b-4910-8cb7-52236db6e0de.png" alt="750_dataset_6000_train_chart" width="500" height="500"/> </div> <br>
  
:stars: 각 선이 의미하는 바는 150장 및 750장 표와 동일<br>
:stars: 150장 및 750장과 동일하게 2250장에서도 Avg Loss는 Iteration이 커질수록 감소, IoU는 Itration이 커질수록 증가<br><br>

__4. 공통점__ <br>
:stars: 이미지 수와 상관없이 Iteration이 증가할수록, 대부분 Avg Loss는 감소 / IoU는 증가한다는 점을 알 수 있음<br><br>

__#2 mAP 측면__

__1. 이미지 수 기준(총 150장 vs 총 750장)__ <br>
:heavy_check_mark: 150장, 750장 훈련에서 발생한 각 6000.weights 파일 기준으로 test하여 mAP 측정 진행<br>

<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/180740427-84b54fec-7707-448b-a892-021542efb56e.JPG" alt="250vs750_mAP_chart" width="800" height="350"/>
</div>

:stars: 150장의 mAP는 58.87%인 반면, 750장은 mAP가 70.97%로 약 12% 정도 차이를 보임<br>
:stars: 750장이 확실히 mAP가 높은 것을 알 수 있음<br>
:stars: 이를 통해 사진 수가 많을수록 mAP가 높다는 것을 알 수 있음<br><br>

__2) Iteration 기준(6000번 vs 9000번)__ <br>
:heavy_check_mark: 동일한 이미지 수에서 Iteration이 차이날 경우 Avg Loss, IoU의 수치는 상호 간 약간의 차이가 있을 수 있으나 6000번과 9000번의 그래프 형상은 비슷한 모습이므로, Avg Loss와 IoU는 생략하였음<br>

<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/180742261-78b76660-9b3c-4112-9ddb-2e81632df34b.JPG" alt="6000Iterationvs6000Iteration_mAP_chart" width="800" height="350"/>
</div>

:stars: 이미지 수가 차이났을 때의 mAP는 확연한 차이가 있었으나, 이미지 수가 동일한 상황 속에서 Iteration이 3000번 정도나 차이가 남에도 불구하고 두 mAP의 차이는 1.2정도밖에 나지 않음<br>
:stars: 즉, Iteration의 차이는 mAP에 큰 영향을 미치지 않는다는 것을 알 수 있음<br>

__3) 2250장 mAP__ <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/192548486-5b21349c-d4c4-4816-9341-0fc7e922ccc8.png" alt="2250_mAP" width="300" height="300"/>
</div>

:stars: 위 사실을 토대로 2250장을 학습했을 경우 위 이미지와 같이 mAP가 88.12%를 기록<br>
:stars: 즉, Dataset이 많은 상태로 학습 할 경우 학습 효과가 높아진다는 것을 알 수 있음<br>

## :two: 기법을 적용한 정확도 개선 확인 <br>
✔️ 1️⃣에서 확인할 수 있듯이 이미지 수를 늘릴수록, mAP 등 정확도가 높아진다는 것을 알 수 있음 <br>
✔️ 이미지 수만 늘려서 정확도를 높이기보다, 특정 기법을 적용해서 정확도를 높이기 위한 방식을 찾아보았음 <br>
✔️ sharpning 기법과 sobel 기법을 적용하여 test를 진행하였음 <br>

### 🔪 sharpning 기법 <br>
✔️ ["sharpning"이란](https://marisara.tistory.com/entry/%ED%8C%8C%EC%9D%B4%EC%8D%AC-openCV-13-%EB%B8%94%EB%9F%AC%EB%A7%81blurring%EC%8A%A4%EB%AC%B4%EB%94%A9smoothing%EC%83%A4%ED%94%84%EB%8B%9Dsharpening)  <br>
✔️ sharpning 기법을 적용한 결과는 아래와 같음 <br>

<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/185849277-7f990217-69c1-4b6e-a371-376ca7889cd7.JPG" width="300" height="300"/>
</div> <br>

🌠 750장 test시 사용했던 동일한 Dataset에 sharpning 기법을 적용 후 train하였고, 이 중 6000.weights 파일 기준으로 test하여 mAP 측정 진행한 것임<br>
🌠 아무 기법 적용하지 않은 750장 mAP 결과보다, sharpning 기법을 적용한 mAP가 약 10% 정도 낮은 것을 확인할 수 있음<br>

### 🔮 sobel 기법 <br>
✔️ ["sobel"이란](https://deep-learning-study.tistory.com/205)  <br>
✔️ sobel 기법을 적용한 결과는 아래와 같음 <br>

<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/185861525-a0d738c5-ba70-4a03-83f5-ec7c2712da0d.png" width="300" height="300"/>
</div> <br>

🌠 750장 test시 사용했던 동일한 Dataset에 sobel 기법을 적용 후 train하였고, 이 중 6000.weights 파일 기준으로 test하여 mAP 측정 진행한 것임<br>
🌠 아무 기법 적용하지 않은 750장 mAP 결과보다, sobel 기법을 적용한 mAP가 약 13~14% 정도 낮은 것을 확인할 수 있음<br>

## 3️⃣ 명확한 전처리를 통한 정확도 개선  <br>
✔️ 2️⃣ 에서 확인할 수 있듯이 sharpning, 차영상, sobel 기법을 통해 정확도가 개선되지 않음을 알 수 있음<br>
✔️ 이에 특정 기법을 적용해서 정확도 개선을 기대하기 보다는, 더 명확한 전처리를 통해 개선을 기대하기로 함<br>
✔️ 맨 처음 150장, 750장을 학습시키기 위한 전처리는 labeling과 각 이미지 속에 있는 객체 좌표값만 있었음<br>
✔️ 이에 따라, 아래와 같은 방식으로 전처리를 진행하였음<br>

### 🍑 이전 전처리 방식과 달라진 점<br>
🌠 __416*416으로 모든 데이터 크기 맞추기 및 좌표 재설정__ <br>
- 이전에는 각 이미지의 크기가 달랐고, cfg 파일에서 416*416 사이즈로 입력받도록 하였음<br>
- 하지만, 객체 좌표값은 기존 이미지 크기에 입각한 좌표값인데, 416*416 사이즈로 변경될 경우 이 좌표값과 matching이 안되는 경우가 발생<br>
- 이에 따라 cfg에서 resize를 진행하기 보다, 애초에 모든 이미지 size를 416*416으로 조정<br>
- 이후 이 사이즈 규격을 기반으로 객체 좌표값 산출<br>

🌠 __해상도 높이기(bilateralFilter)__ <br>
- [이미지 속 객체 해상도 높이기 참고 링크](https://deep-learning-study.tistory.com/164) <br>
- 해상도를 높이면 학습 효과가 더 올라가는 경우가 존재함에 따라 해상도를 높임<br>

🌠 __cfg 파일도 재설정__ <br>
- cfg 파일 점검 재진행<br>

🌠 __animal, bird, human 클래스 데이터별로 train, test, valid 크기 맞추기__ <br>
- 예전 학습 때에는 한 Dataset 파일에 animal, bird, human 클래스 데이터를 넣고 train, test, valid를 임의로 6:2:2 비율로 나누었음<br>
- 이럴 경우, 각 클래스 데이터별로 6:2:2로 나누어지는 것이 아니기 때문에, 특정 클래스 데이터가 더 많은 학습 데이터에 사용될 수도 안될수도 있음<br>
- 이에 따라, 클래스 데이터별로 폴더로 나누어 train, test, valid를 6:2:2 비율로 정확히 나누었음<br>
- 이로 인해 모든 클래스 데이터가 공평한 이미지 수로 학습에 참여할 수 있게 되었음<br>

🌠 __yolov4 정확도 높이기(cfg 관련) 적용__ <br>

### 🎸 달라진 결과 확인(mAP)<br>

<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/186552231-3e3637c5-888a-4ba1-9bf3-f59944a1a5ba.png" width="350" height="350"/>
</div> <br>

- 학습 때 사용한 Dataset은 이전 전처리 방식의 750장 Dataset과 동일함<br>
- 이전 전처리 방식에서의 mAP는 약 71%였던 것에 비해, 이번 전처리 방식에서의 mAP는 약 77%로 6% 가량 오른 것을 확인할 수 있음<br>
- 이를 통해, 명확한 전처리 방식이 학습 효과 향상에 기여한다는 것을 확인할 수 있음<br>

### 🌸 달라진 결과 확인(동영상)<br>
✔️ README.md에 동영상(GIF)를 올리려 했으나, 동영상 용량 문제로 인해, 각 영상 별 대표 이미지와 각 영상 설명란에 유튜브 링크를 첨부하였습니다.<br>
✔️ 확실한 차이는 각 영상 별 이미지보다 유튜브에 더 잘 보이기 때문에, 유튜브 링크를 통해 차이를 꼭 비교하시길 바랍니다.<br>
✔️ 각 유튜브 영상에서 가운데를 기준으로 __왼쪽이 old version, 오른쪽이 new version에 해당합니다.__ <br>
✔️ __old version은 명확한 전처리 이전의 학습 결과물로 동영상을 test한 것입니다.__ <br>
✔️ __new version은 명확한 전처리 이후의 학습 결과물로 동영상을 test한 것입니다.__ <br>
✔️ 탐지 개체마다 Bounding Box가 존재하는데, __Red Box는 animal, Blue Box는 bird, Green Box는 human으로 Yolo가 탐지했을 때 생깁니다.__ <br><br>

__#1 영상 1__<br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188833756-ed099fb8-1c5b-4fd6-9f69-a389487a64b7.JPG" width="350" height="250"/>
</div> <br>

🌠 [영상 1 시청하러 가기](https://www.youtube.com/watch?v=m7LAfAoSNYU&list=PLXqZ70DL_8OpOzJjbJVKdIw7nivN18d2O&index=1) <br>
🌠 old version에서는 __한 개체에 여러개의 Bounding Box가 생성__ <br>
🌠 new version에서는 __한 개체에 하나의 Bounding Box가 생성__ <br><br>

__#2 영상 2__<br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188833763-6b2e7a76-7532-4752-9ecc-1e8953b234cd.JPG" width="350" height="250"/>
</div> <br>

🌠 [영상 2 시청하러 가기](https://www.youtube.com/watch?v=VWNyDYlmNVE&list=PLXqZ70DL_8OpOzJjbJVKdIw7nivN18d2O&index=2) <br>
🌠 old version에서는 다람쥐가 나무 위에 있어서 __다람쥐를 animal이 아닌 bird로 인식__ <br>
🌠 new version에서는 __다람쥐를 animal로도 인식함. 다만, 다람쥐를 animal, bird로 동시에 인식.__ 해당 문제는 지속적인 학습을 통해 해결해야 할 필요성이 있음 <br><br>

__#3 영상 3__<br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188833772-9bbde1e3-dabd-4e0a-ae66-aee12a7916a0.JPG" width="350" height="250"/>
</div> <br>

🌠 [영상 3 시청하러 가기](https://www.youtube.com/watch?v=1QCsn8tHLl4&list=PLXqZ70DL_8OpOzJjbJVKdIw7nivN18d2O&index=3) <br>
🌠 old version에서는 __새때 중 대부분 또는 일부 새를 인식하지 못하였음__ <br>
🌠 new version에서는 __새때 중 대부분의 새를 인식__ <br><br>

__#4 영상 4__<br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188833789-06ec5ee2-5e6f-414e-9131-5af6666e238c.JPG" width="350" height="250"/>
</div> <br>

🌠 [영상 4 시청하러 가기](https://www.youtube.com/watch?v=EjBetpMZBPo&list=PLXqZ70DL_8OpOzJjbJVKdIw7nivN18d2O&index=4) <br>
🌠 old version에서는 __사람을 사람이 아닌 새로 인__식 <br>
🌠 new version에서는 __사람을 대부분 사람으로 인식.__ 일부는 아직 새로 인식하는데, 해당 문제는 지속적인 학습을 통해 해결해야 할 필요성이 있음 <br><br>


### 🎰 Number of objects per class (클래스별 탐지 객체 수)

<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/186587636-5cbdfe8e-6471-45ae-b3c5-a16e4e423438.png" width="450" height="350"/>
</div> <br>

- True Positive(이하 TP) : 예측을 잘함<br>
- False Positive(이하 FP) : false 인데 positive로 판단<br>
- bird의 FP/TP+FP는 약 0.18을 기록<br>
- animal의 FP/TP+FP는 약 0.11을 기록<br>
- human의 FP/TP+FP는 약 0.19를 기록<br>
- 즉, bird와 human의 오탐율은 비교적 높고, animal의 오탐율은 비교적 낮음을 확인할 수 있음<br>

## 4️⃣ 날씨 및 환경에 따른 학습 및 실험 영향 확인 <br>
✔️ 이전까지는 날씨 및 환경이 좋은 사진들로만 학습 및 실험을 진행하였음 <br>
✔️ 하지만 __날씨 및 환경이 상대적으로 좋지 않을 경우(예 : 눈/비/일출/일몰), 이러한 것이 객체 인식에 영향을 줄 수 있음__ <br>
✔️ 이에 따라, 본 연구팀은 __날씨 및 환경이 좋았던 상황에서 77% mAP를 기록하기 위해 750장의 dataset이 필요한 것처럼, 날씨 및 환경이 좋지 않을 경우 약 77%를 기록하기 위해, 얼마나 많은 dataset이 필요한지 확인해보기로 했음__ <br>
✔️ 이에 따라, 각 종류별 사진들을 눈/비/일출&일몰/밤 필터 총 4개와 합성하여 총 750장의 이미지를 생성 <br>
✔️ 각 필터 적용된 종류별 구성은 다음과 같음 > 일출&일몰(60장)+눈(60장)+비(65장)+밤(65장) > 한 종류당 250장 <br>

__1. 이미지 생성__  <br>
🌠 각 필터와, 동일한 동물 이미지에 대한 결과의 예시는 아래와 같음  <br>
🌠 결과 이미지는 흑백 처리까지 한 결과임  <br>

__#1 이미지 필터__  <br>

<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/180904419-008b90d7-005c-4c4c-b597-04960853f0f6.JPG" alt="img-filters" width="500" height="300"/></div>

__#2 필터 적용 이미지__  <br>

<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/180904530-f19a422f-12e5-4581-a4ab-17bd652ea076.JPG" alt="img-filters-results" width="500" height="300"/></div> <br>
  
__#3 학습 이후 test한 이미지__ <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188819244-b93ff5a4-b2a0-42bd-8b2d-29df0471270b.JPG" alt="img-filters-results" width="500" height="300"/></div> <br>
✔️ 해당 이미지는 비 필터가 적용된 동물(animal) 사진임 <br>
✔️ 필터가 적용돼도 잘 탐지하는 것을 확인할 수 있음 <br>
    
<br>

__#4 필터 적용 mAP__ <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188819737-7d46334a-f391-4063-9b63-b299daa88df0.JPG" alt="" width="500" height="300"/></div> <br>
<br>

__#5 결과__ <br>
✔️ 필터가 적용되지 않은 mAP와 적용된 mAP는 각각 __약 77%와 74%로로, 3% 정도의 차이를 기록__ <br>
✔️ 해당 과정을 실험하기 전에는 필터가 적용된 것이 안된 것보다 __mAP 측면에서 10~15% 정도 낮을 것으로 예상__ <br>
✔️ 그러나, 필터가 적용된 것의 mAP가 안된 것보다 약 3% 낮기는 하나, __필터가 적용된 것을 감안하였을 때 예상보다는 많이 좋은 mAP를 산출__ <br>
✔️ 실제 상황에서는 적용된 필터보다 더 다양한 날씨 및 환경이 존재하겠으나, __특별한 경우가 아닐 경우 대부분 상황에서도 객체 인식을 정상적으로 수행할 것으로 기대 가능__ <br>

__#6 참고 자료__ <br>
1. [전·후처리를 이용한 딥러닝 기반의 주차여부인식](https://www.kci.go.kr/kciportal/ci/sereArticleSearch/ciSereArtiView.kci?sereArticleSearchBean.artiId=ART002519323) <br>
2. [파이썬 코드를 이용한 파이카메라 제어](https://neosarchizo.gitbooks.io/raspberrypiforsejonguniv/content/chapter4.html) <br>

<br><br> 

## 5️⃣ 차영상 이후 YOLO 실행 <br>
✔️ 실제 촬영되는 영상을 입력받을 경우, 계속 YOLO로 탐지를 진행하고 있으면 프로그램에 과부하가 높을 것으로 예상 <br> 
✔️ 이에 따라, 먼저 입력된 영상 속에서 차영상이 나타날 경우에 YOLO를 가동시켜 해당 객체가 동물/새/사람인지 구분 <br>
✔️ 이를 통해 프로그램 상 주어질 수 있는 과부화 중 일부를 완화할 수 있을 것으로 기대 <br>
✔️ 더 나아가, 여러 프로젝트에서 대부분 객체 인식을 실시간 YOLO로만 구동시키는데, 본 연구팀은 차영상 이후 YOLO를 실행함으로써 실행 방식에서의 차이를 기대 <br><br>

### 차영상 적용 전후 비교 <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/190208416-f6b16c64-db28-4cfe-b1fd-d8a7a1e19dc7.JPG" alt="차영상-적용-전후-비교" width="500" height="300"/></div> <br><br>
🌠 차영상 적용 전이나 후나 먼저 영상에서 객체 탐지 진행 후 판별을 진행 <br>
🌠 판별 부분에서는 동일한 성능을 보여줌 <br>
🌠 탐지 부분에서는 차영상 적용 전과 후가 약 45초 정도 차이나는 것을 확인할 수 있음 <br>
🌠 차영상 이후 YOLO를 가용하는 것이 시스템 측면에서 유리한 것을 알 수 있음 <br>
🌠 본 시스템처럼, 특정 객체를 퇴치해야 하는 경우 더 효율적인 방안임을 알 수 있음 <br><br>
