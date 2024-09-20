# :blue_book: 프로젝트 소개

## :fire: 프로젝트 주제
:heavy_check_mark: AI를 활용한 농작물 피해 완화 시스템

## :fire: 역할 분담
:heavy_check_mark: 박창영
- 팀장 / 논문 작성
- ERD 및 Workflow 설계
- SpringBoot
  - JPA 활용한 Member / Camera / Log 테이블 생성
  - 단계별 퇴치 서비스 개발
- AI에 학습할 데이터 수집

:heavy_check_mark: 임병찬
- 부팀장
- SpringBoot & Firebase
  - 알림 서비스 개발

:heavy_check_mark: 권동원
- Android Studio 활용 Mobile UI 개발

:heavy_check_mark: 황종환
- Android Studio 활용 Mobile UI 개발
- Firebase & GCM
  - 알림 서비스 개발

:heavy_check_mark: 조은
- AI 모델 학습 담당
- Rasberry Pi
  - 카메라로 인식된 물체를 Back-End에 송신
  - Back-End에서 수신된 결과를 소리 / 빛 등의 센서로 출력

:heavy_check_mark: 진민주
- AI 모델 학습 담당

## :fire: 개발 배경
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/179216377-5c0d525e-64dd-4a1e-8bd0-22841a7d0f2d.JPG" alt="img1"/>
  
  :file_folder: [표 1] : 동물별 농작물 피해실태(환경부 생물다양성과/연도별 유해생물에 의한 피해현황(2014~2018)/2019)
</div>

:heavy_check_mark: [표 1]에서 확인할 수 있듯이 '18년 기준 피해 규모는 '14년 대비 8.1% 증가했다. <br>
:heavy_check_mark: 즉, 해마다 피해 규모가 전반적 증가 추세임을 알 수 있다. <br>
:heavy_check_mark: 동물 부분에서는 멧돼지, 고라니, 청설모 순으로 피해 규모가 크다. <br>
:heavy_check_mark: 새 부분에서는 꿩, 까치, 오리류 순으로 피해 규모가 크다. <br>
> :bangbang: 새도 과학적으로 동물에 포함되나, 해당 프로젝트에서는 동물은 새가 아닌 동물로 지정하여, 동물과 새로 구분하였다.

## :fire: 개발 목적
:heavy_check_mark: 개발 배경을 통해 확인할 수 있는 피해 규모 완화 위해 사람이 직접 유해야생동물 포획 시 인건비, 안전 등 고려요소가 많다.<br>
:heavy_check_mark: 이에 따라, 사람 이외의 피해 규모 완화 위한 무인 시스템 필요성을 느껴 해당 프로젝트 개발한다.<br>

## :fire:: 개발 설계
:one: __객체 탐지를 위해 AI 활용__<br>
:heavy_check_mark: 객체는 [사람/동물/새/사물]로 구분한다.<br>
:heavy_check_mark: 객체 중 새 및 동물은 퇴치 대상에 해당한다.<br>

:two: __차영상 및 YOLO를 활용한 객체 판별 & 객체 탐지__<br>
:heavy_check_mark: 탐지 측면에서 효율성을 높이기 위해 차영상을 활용한다.<br>
:heavy_check_mark: 차영상을 통해 기존 배경과 차이 발생 시 YOLO를 실행하여 객체 판별 및 탐지 과정 진행한다.<br>

:three: __퇴치 동작 다양화__<br>
:heavy_check_mark: 퇴치 객체 탐지 시 : 1-4단계 순서대로 퇴치 진행한다.<br>
> :rotating_light: __1단계 : 고강도 조명 출력<br> :sound: 2단계 : 랜덤 퇴치 신호 출력<br> :zap: 3단계 : 고주파수 출력<br> :smiling_imp: 4단계 : 1~3단계 종합 출력__<br>

:four: __애플리케이션을 통한 퇴치 알림 서비스__<br>
:heavy_check_mark: 퇴치 대상 식별 및 퇴치 단계, 퇴치 여부를 농장 주인에게 푸쉬 알림을 통해 알린다.<br> 

## :fire: H/W & S/W 구성도<br>
__#1 단순 구성도__
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/186291096-df9253c7-dadf-42e8-898d-3786f0734994.JPG" width="800" height="500"/>
</div> <br><br>

__#2 상세 구성도__ <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188639417-deef579e-22c8-481c-afca-3c71f208b1e8.JPG" width="700" height="350"/> 
</div><br><br>

__#3 구성도 설명__ <br>
✔️ 라즈베리(카메라)는 실시간으로 영상 촬영 및 AI 모듈에 영상 제공한다. <br>
✔️ AI 모듈은 전달받은 영상에서 먼저 차영상을 구함. 탐지된 차영상 있을 시 YOLO로 객체 판별 진행한다. <br>
✔️ 판별 객체가 퇴치 객체(동물 또는 새)일 경우, AI 모듈은 퇴치 단계에 따라 라즈베리(빛) 또는 라즈베리(스피커) 제어한다. <br>
✔️ 라즈베리(빛) 또는 라즈베리(스피커)는 AI 모듈의 제어 신호에 따라 작동된다. <br>
✔️ AI 모듈은 동물 또는 새 탐지 시 [카메라 일련번호 / 탐지 객체 캡처 링크 / 퇴치 단계 / 탐지 시간] 정보를 서버(=Spring Boot)에 송신한다. <br>
✔️ 서버는 실시간으로 AI 모듈에서 보내는 정보를 감지. 감지된 정보가 있을 경우 해당 정보를 DB에 삽입. 또한 이 정보를 사용자에게 파이어베이스 알림을 통해 전송한다. {-> 탐지 객체 있을 경우 사용자가 알아야 하므로} <br>
✔️ 사용자가 과거 기록 확인 요청할 경우 해당 요청 정보 확인 가능하다. <br>

## :fire: ERD <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188640237-c1407dee-c22d-4075-9293-b1dc26ec1a10.JPG" alt="ERD" width="600" height="450"/>
</div> <br>

## :fire: 순서도
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/179218577-c5aa5d2d-a47b-4818-831c-6e1da456e6f2.JPG" alt="img2"/>
</div>

## :fire: 사용 Tool / 사이트 / 프레임워크

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

✔️ "로그 기록"에서는 동물들이 과거부터 현재까지 유해동물이 농경지에 침입시 탐지한 시간 및 대응 단계를 확인할 수 있다. <br>
✔️ "카메라 관리"는 사용자의 농경지에 있는 카메라를 등록/삭제할 수 있다. <br>
✔️ "회원정보"에서는 정보변경/로그아웃/회원탈퇴를 할 수 있다. <br>

<div align="center">
  <img width="80%" src="https://github.com/irishNoah/FarmSecurity/assets/80700537/0f3b99da-52fa-48c1-a3d2-83e95a543895"/> <br/>
  <p>푸쉬 알림 누르기 전 / 푸쉬 알림 누른 후</p>  
</div>

✔️ 푸쉬 알림 누르기 전 > 좌측 이미지와 같이, 동물이 인식될 경우 왼쪽과 같이 푸쉬 알림이 온다. <br>
✔️ 푸시 알림 누를 경우 > 우측 이미지와 같이, 동물이 인식된 사진 및 데이터를 사용자에게 보여준다. <br>

### 기타 기능
<div align="center">
  <img width="80%" src="https://github.com/irishNoah/FarmSecurity/assets/80700537/e58a4c70-bd4b-4a28-8c0e-cba2e9e64a32"/> <br/>
  <p>앱 로딩 / 회원가입 / 로그인</p>  
</div>

✔️ 차례대로 앱 로딩 / 회원가입 / 로그인에 관련한 화면이다. <br>

## :fire: 시연 영상
✔️ [모바일 어플리케이션 시연 영상(With AI 탐지)](https://youtu.be/LWhlDqM6Kf8) <br>

## :fire: 참여 대회 및 한국정보기술학회 투고 논문(2022년)
:one: __AI를 이용한 농작물 피해 완화 시스템__ <br>
✔️ 우수논문상 동상 수상 <br>
> :running: [제출 파일 다운로드하러 가기](https://github.com/irishNoah/FarmSecurity/blob/main/KIIT/%ED%95%9C%EA%B5%AD%EC%A0%95%EB%B3%B4%EA%B8%B0%EC%88%A0%ED%95%99%ED%9A%8C_AI%EB%A5%BC%EC%9D%B4%EC%9A%A9%ED%95%9C%EB%86%8D%EC%9E%91%EB%AC%BC%ED%94%BC%ED%95%B4%EC%99%84%ED%99%94%EC%8B%9C%EC%8A%A4%ED%85%9C.hwp.pdf)
<br>

:two: __영상인식에서 전처리 유무 및 환경변화에 따른 객체 인식률 비교__ <br>
✔️ 우수논문상 은상 수상 <br>
> :running: [제출 파일 다운로드하러 가기](https://github.com/irishNoah/FarmSecurity/blob/main/KIIT/%ED%95%9C%EA%B5%AD%EC%A0%95%EB%B3%B4%EA%B8%B0%EC%88%A0%ED%95%99%ED%9A%8C_%EC%98%81%EC%83%81%EC%9D%B8%EC%8B%9D%EC%97%90%EC%84%9C%EC%A0%84%EC%B2%98%EB%A6%AC%EC%9C%A0%EB%AC%B4%EB%B0%8F%ED%99%98%EA%B2%BD%EB%B3%80%ED%99%94%EC%97%90%EB%94%B0%EB%A5%B8%EA%B0%9D%EC%B2%B4%EC%9D%B8%EC%8B%9D%EB%A5%A0%EB%B9%84%EA%B5%90.hwp.pdf)
<br>

## :fire: 백엔드 및 AI 소개글
- [프로젝트 백엔드 부분 소개글로 이동하기](https://github.com/irishNoah/FarmSecurity/tree/main/FE%26BE)
- [프로젝트 AI 부분 소개글로 이동하기](https://github.com/irishNoah/FarmSecurity/blob/main/AI/README.md)

## :fire: 소감
### 좋았던 점
✔️ 처음으로 한 Java 기반 프로젝트에서 Spring Boot와 Rest API를 활용하여 좀 더 개발자로써 성장할 수 있어서 좋았다. <br>
✔️ AWS EC2를 활용한 배포를 통해 로컬 환경에서 끝난 것이 아,닌 사용자에게도 서비스를 제공할 수 있는 환경까지 구축한 점이 좋았다.  <br>
✔️ 첫 모바일 기반 프로젝트에서 푸쉬 알림을 포함하여 각 프로그램과 연계할 수 있는 과정을 겪을 수 있어서 좋았다. <br>
✔️ 약 8개월 정도의 프로젝트를 통해 2번의 우수논문상이라는 좋은 결과를 얻을 수 있어서 좋았다. <br>

### 아쉬웠던 점
✔️ 카메라를 한 대로 프로젝트를 진행했기에 여러 사용자가 접속을 못하는 점을 개선하면 좋을 것 같다. <br>
✔️ 실제로 playstore에 등록을 해서 농업인들이 사용하면 좋을 것 같다. <br>
