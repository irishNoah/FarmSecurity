- 이 README.md에서는 제 담당 파트였던 Spring Boot 및 연동 부분 위주로 설명드립니다.

# ⏰ Firebase(푸쉬 알림)
## Firebase를 선택한 이유
✔️ 서버에서 앱으로 푸쉬 알림을 전송하는 기능을 구현하기 위해서는 파이어베이스와 브로드캐스트리시버 2종류의 선택지가 존재했음 <br>
✔️ 해당 프로젝트는 앱이 실행 중이지 않을 때(즉, 백그라운드에서 실행되고 있을 경우)에도 알림이 필요할 경우, 클라이언트에게 알림을 전송해야 함 <br>
✔️ 이에 따라, 앱이 실행 중이거나 특수한 이벤트 발생 시에 작동되는 브로드캐스트리시버는 부적절하다고 판단 <br>
✔️ 따라서, 필요 조건을 충족할 수 있는 파이어베이스를 선택 <br>

## 📮 FCM(Firebase 클라우드 메시징)의 동작 원리 <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188641863-e223bc87-7b27-45ca-80fa-c1e56da04d02.JPG" alt="Firebase" width="550" height="400"/>
</div> <br>

1. 클라이언트 앱에서 Sender ID를 이용해 등록을 요청 <br>
2. FCM은 클라이언트 앱에서 전달받은 SenderId를 토대로 Registration Token을 클라이언트 앱에서 발급 <br>
3. 클라이언트 앱은 FCM에서 전달받은 Registration Token을 앱 서버에 전달하고, 이를 전달받은 앱 서버는 Registration Token을 저장 <br>
4. 앱 서버는 Registration Token, API Key, 전송할 메시지를 이용하여 GCM에 메시지를 전송 <br>
5. FCM은 앱 서버로부터 전달받은 메시지를 해당 클라이언트 앱에 메시지를 전송 <br>

## 코드
:one: 파이어베이스를 통해 데이터 페이로드 형식의 알람 메시지 수신
```Java
@Override
public void onMessageReceived(RemoteMessage remoteMessage) {
    Log.d("FCM Log : ", "MessageReceived");
    if(remoteMessage.getData().size()>0){
        sendNotification(remoteMessage.getData().get("body"),remoteMessage.getData().get("title"));
    }
}
```
- 탐지된 유해동물이 있다면, 이와 관련된 사진 및 정보가 DB에 저장된다.
- 이와 동시에 파이어베이스에 관련 정보가 전송되게 되고, 이 정보는 FCM을 거쳐 어플리케이션에 데이터 페이로드 형식으로 도착하게 된다.
- 이후, 아래 sendNotification() 함수에서 이 페이로드를 처리하도록 한다.

:two: 사용자에게 보여줄 알림 형식에 맞게 데이터 페이로드 형식 처리하기
```Java
private void sendNotification(String body, String title) {
    Intent intent = new Intent(this, AlarmActivity.class);
    intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, intent, PendingIntent.FLAG_IMMUTABLE);

    NotificationCompat.Builder builder = null;

    if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O) {
        NotificationChannel channel = new NotificationChannel("FarmSecurity", "FarmSecurity", NotificationManager.IMPORTANCE_DEFAULT);
        channel.setDescription("푸시 알림");
        channel.setLockscreenVisibility(Notification.VISIBILITY_PRIVATE);

          // 해당 정보를 화면단에 맞게 배치
          .
          .
          .
    }
}
```
- onMessageReceived()에서 받은 페이로드를 토대로 사용자에게 보여줄 형식에 맞게 데이터를 처리한다.
- 데이터를 처리한 이후, 이를 사용자가 어플리케이션에서 볼 푸쉬 알림 형식에 맞게 화면단에 배치한다.

# Spring Boot
## Spring Boot와 Maria DB 연동

## SQL 쿼리(Controller)

## Firebase에서 정보 받아와 Android Studio에 송신

## restapi/AlarmScheduler.java

# Rest API
- 사용자나 어플리케이션 서비스에서 어떤 정보를 (등록/조회/수정/삭제)할 때, Rest API 규칙에 맞게 처리하도록 한다.

## 회원 CRUD
``` Java
public interface InitMyAPI {
    //아이디로 검색
    @GET("select/{id}")
    Call<MainResponse> selectOne(@Path("id") String id);

    //이름과 전화번호로 검색
    @GET("select/{name}/{phone}")
    Call<MainResponse> selectId(@Path("name") String name, @Path("phone") String phone);

    //아이디와 전화번호로 검색
    @GET("select2/{id}/{phone}")
    Call<MainResponse> selectPw(@Path("id") String id, @Path("phone") String phone);

    //회원가입 정보 입력
    @POST("insert")
    Call<SignUpResponse> insertOne(@Body SignUpRequest signUpRequest);

    //입력한 아이디에 해당하는 계정 비밀번호 변경
    @POST("update/{id}")
    Call<MainResponse> updatePw(@Path("id") String id, @Body ChangePwRequest changePwRequest);

    //로그인되어있는 아이디에 해당하는 계정 주소 변경
    @POST("update2/{id}")
    Call<MainResponse> updateAddress(@Path("id") String id, @Body ChangeAdRequest changeAdRequest);

    //회원 테이블에서 해당 계정 정보 삭제
    @DELETE("delete/{id}")
    Call<Void> deleteOne(@Path("id") String id);
}
```

## 로그 기록 검색
```Java
public interface LogAPI {
    //로그인되어있는 계정에 해당하는 로그 검색
    @GET("select/{memberId}")
    Call<LogResponse> selectLog(@Path("memberId") String id);

    //로그인되어있는 계정에 해당하는 로그 리스트 검색
    @GET("select2/{memberId}")
    Call<List<LogResponse>> selectLogList(@Path("memberId") String id);
}
```

## 카메라 정보 (등록/조회/삭제)
```Java
public interface CameraAPI {
    //로그인되어있는 계정으로 카메라 정보 등록
    @POST("insert")
    Call<CameraResponse> insertCamera(@Body CameraRequest cameraRequest);

    @GET("select2/{memId}")
    Call<CameraLogResponse> selectCamera(@Path("memId") String id);

    @DELETE("delete/{memId}")
    Call<Void> deleteCamera(@Path("memId") String id);
}
```

## 참고
- [참고1](https://velog.io/@eeheaven/AndroidStudio-SpringBoot-KnockKnock-%EA%B0%9C%EB%B0%9C%EC%9D%BC%EC%A7%80-0118-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-%EC%8A%A4%ED%8A%9C%EB%94%94%EC%98%A4%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%EC%97%B0%EA%B2%B0)

- [참고2](https://velog.io/@plz_no_anr/Android-REST-API)

# 느낀점
