# Spring Boot <br>
- hi

# Spring Boot <br>
- hi


# ⏰ 알림을 위한 Firebase 채택 이유 <br>
✔️ 서버에서 앱으로 푸쉬 알림을 전송하는 기능을 구현하기 위해서는 파이어베이스와 브로드캐스트리시버 2종류의 선택지가 존재했음 <br>
✔️ 해당 프로젝트는 앱이 실행 중이지 않을 때(즉, 백그라운드에서 실행되고 있을 경우)에도 알림이 필요할 경우, 클라이언트에게 알림을 전송해야 함 <br>
✔️ 이에 따라, 앱이 실행 중이거나 특수한 이벤트 발생 시에 작동되는 브로드캐스트리시버는 부적절하다고 판단 <br>
✔️ 따라서, 필요 조건을 충족할 수 있는 파이어베이스를 선택 <br>

# ✉️ GCM(Google Cloud Messaging) 구성 <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188641475-ea68109a-baf2-4b81-b0eb-464eb2aed978.JPG" alt="GCM" width="550" height="400"/>
</div> <br>

# 📮 Firebase 클라우드 메시징(이하, FCM)의 동작 원리 <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188641863-e223bc87-7b27-45ca-80fa-c1e56da04d02.JPG" alt="Firebase" width="550" height="400"/>
</div> <br>

1. 클라이언트 앱에서 Sender ID를 이용해 등록을 요청 <br>
2. FCM은 클라이언트 앱에서 전달받은 SenderId를 토대로 Registration Token을 클라이언트 앱에서 발급 <br>
3. 클라이언트 앱은 FCM에서 전달받은 Registration Token을 앱 서버에 전달하고, 이를 전달받은 앱 서버는 Registration Token을 저장 <br>
4. 앱 서버는 Registration Token, API Key, 전송할 메시지를 이용하여 GCM에 메시지를 전송 <br>
5. FCM은 앱 서버로부터 전달받은 메시지를 해당 클라이언트 앱에 메시지를 전송 <br>
