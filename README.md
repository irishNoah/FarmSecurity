# :blue_book: 프로젝트 소개

## :bell: 프로젝트 주제
:heavy_check_mark: AI를 이용한 농작물 피해 완화 시스템

## :beginner: 개발 배경
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/179216377-5c0d525e-64dd-4a1e-8bd0-22841a7d0f2d.JPG" alt="img1"/>
  
  :file_folder: [표 1] : 동물별 농작물 피해실태(환경부 생물다양성과/연도별 유해생물에 의한 피해현황(2014~2018)/2019)
</div>

:heavy_check_mark: [표 1]에서 확인할 수 있듯이 '18년 기준 피해 규모는 '14년 대비 8.1% 증가 <br>
:heavy_check_mark: 즉, 해마다 피해 규모가 전반적 증가 추세임을 알 수 있음 <br>
:heavy_check_mark: 동물 부분에서는 멧돼지, 고라니, 청설모 순으로 피해 규모가 큼 <br>
:heavy_check_mark: 새 부분에서는 꿩, 까치, 오리류 순으로 피해 규모가 큼 <br>
> :bangbang: 새도 과학적으로 동물에 포함되나, 해당 프로젝트에서는 동물은 새가 아닌 동물로 지정하여, 동물과 새로 구분하였음

## :fire: 개발 목적
:heavy_check_mark: 개발 배경을 통해 확인할 수 있는 피해 규모 완화 위해 사람이 직접 유해야생동물 포획 시 인건비, 안전 등 고려요소가 많음<br>
:heavy_check_mark: 이에 따라, 사람 이외의 피해 규모 완화 위한 무인 시스템 필요성을 느껴 해당 프로젝트 개발<br>

## :sweat_drops: 개발 설계
:one: __객체 탐지를 위해 AI 활용__<br>
:heavy_check_mark: 객체는 [사람/동물/새/사물]로 구분<br>
:heavy_check_mark: 객체 중 새 및 동물은 퇴치 대상에 해당 <br>

:two: __차영상 및 YOLO를 활용한 객체 판별 & 객체 탐지__<br>
:heavy_check_mark: 탐지 측면에서 효율성을 높이기 위해 차영상을 활용<br>
:heavy_check_mark: 차영상을 통해 기존 배경과 차이 발생 시 YOLO를 실행하여 객체 판별 및 탐지 과정 진행<br>

:three: __퇴치 동작 다양화__<br>
:heavy_check_mark: 퇴치 객체 탐지 시 : 1-4단계 순서대로 퇴치 진행<br>
> :rotating_light: __1단계 : 고강도 조명 출력<br> :sound: 2단계 : 랜덤 퇴치 신호 출력<br> :zap: 3단계 : 고주파수 출력<br> :smiling_imp: 4단계 : 1~3단계 종합 출력__<br>

:four: __애플리케이션을 통한 퇴치 알림 서비스__<br>
:heavy_check_mark: 퇴치 대상 식별 및 퇴치 단계, 퇴치 여부를 농장 주인에게 알림<br> 

## 🐟 H/W & S/W 구성도<br>
__#1 단순 구성도__
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/186291096-df9253c7-dadf-42e8-898d-3786f0734994.JPG" width="800" height="500"/>
</div> <br><br>

__#2 상세 구성도__ <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188639417-deef579e-22c8-481c-afca-3c71f208b1e8.JPG" width="700" height="350"/> 
</div><br><br>

__#3 구성도 설명__ <br>
✔️ 라즈베리(카메라)는 실시간으로 영상 촬영 및 AI 모듈에 영상 제공 <br>
✔️ AI 모듈은 전달받은 영상에서 먼저 차영상을 구함. 탐지된 차영상 있을 시 YOLO로 객체 판별 진행 <br>
✔️ 판별 객체가 퇴치 객체(동물 또는 새)일 경우, AI 모듈은 퇴치 단계에 따라 라즈베리(빛) 또는 라즈베리(스피커) 제어 <br>
✔️ 라즈베리(빛) 또는 라즈베리(스피커)는 AI 모듈의 제어 신호에 따라 작동됨 <br>
✔️ AI 모듈은 동물 또는 새 탐지 시 [카메라 일련번호 / 탐지 객체 캡처 링크 / 퇴치 단계 / 탐지 시간] 정보를 서버(=Spring Boot)에 송신 <br>
✔️ 서버는 실시간으로 AI 모듈에서 보내는 정보를 감지. 감지된 정보가 있을 경우 해당 정보를 DB에 삽입. 또한 이 정보를 사용자에게 파이어베이스 알림을 통해 전송 {-> 탐지 객체 있을 경우 사용자가 알아야 하므로} <br>
✔️ 사용자가 과거 기록 확인 요청할 경우 해당 요청 정보 확인 가능 <br>

## 👬 ERD <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188640237-c1407dee-c22d-4075-9293-b1dc26ec1a10.JPG" alt="ERD" width="600" height="450"/>
</div> <br>

## :chart_with_upwards_trend: 순서도
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/179218577-c5aa5d2d-a47b-4818-831c-6e1da456e6f2.JPG" alt="img2"/>
</div>

## :dart: 사용 Tool / 사이트 / 프레임워크

| 구분 | 사용 Tool / 사이트 / 프레임워크 |     
| :------: | :-----------------------------------------------:|
| Data collection | AI Hub / Kaggle |
| Date train & test | Goolge Colab Pro & Yolov4 |
| Front End | Android Studio |
| Back End | Spring Boot |
| DB | Maria DB |
| Notification | Firebase |
| Hardware | Raspberry Pi |
| Devops | Github |
| Etc | GCP, GCM, FCM |

## :fire: 사용자 페이지
### 주요 기능

<div align="center">
  <img width="80%" src="https://github.com/irishNoah/FarmSecurity/assets/80700537/65303927-c8d8-4c18-a61e-2f4675dbd8dd"/> <br/>
  <p>로그 기록 / 카메라 관리 / 사용자페이지</p>  
</div>

- "로그 기록"에서는 동물들이 과거부터 현재까지 유해동물이 농경지에 침입시 탐지한 시간 및 대응 단계를 확인할 수 있다.
- "카메라 관리"는 사용자의 농경지에 있는 카메라를 등록/삭제할 수 있다.
- "회원정보"에서는 정보변경/로그아웃/회원탈퇴를 할 수 있다.

<div align="center">
  <img width="80%" src="https://github.com/irishNoah/FarmSecurity/assets/80700537/0f3b99da-52fa-48c1-a3d2-83e95a543895"/> <br/>
  <p>푸쉬 알림 누르기 전 / 푸쉬 알림 누른 후</p>  
</div>

- 푸쉬 알림 누르기 전 > 좌측 이미지와 같이, 동물이 인식될 경우 왼쪽과 같이 푸쉬 알림이 온다.
- 푸시 알림 누를 경우 > 우측 이미지와 같이, 동물이 인식된 사진 및 데이터를 사용자에게 보여준다.

### 기타 기능
<div align="center">
  <img width="80%" src="https://github.com/irishNoah/FarmSecurity/assets/80700537/e58a4c70-bd4b-4a28-8c0e-cba2e9e64a32"/> <br/>
  <p>앱 로딩 / 회원가입 / 로그인</p>  
</div>

- 차례대로 앱 로딩 / 회원가입 / 로그인에 관련한 화면이다.
