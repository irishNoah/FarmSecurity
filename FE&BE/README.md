✔️ 이 README.md에서는 제 담당 파트였던 Spring Boot와 Firebase 위주로 설명드립니다. <br>
✔️ 설명 순서 : Spring Boot > Firebase(푸쉬 알림) > 느낀 점 <br>
✔️ 다른 설명 글이 읽고 싶다면? <br>
- [프로젝트 소개&설계&모바일 화면 & 시연 영상 관련 글로 이동하기](https://github.com/irishNoah/FarmSecurity) 
- [프로젝트 AI 관련 글로 이동하기](https://github.com/irishNoah/FarmSecurity/tree/main/AI) <br>

# :fire: Spring Boot
## 🙍‍♂ Spring Boot와 Maria DB 연동
```Java
public class DBConfig { // Sprinboot & maria DB 연동

    @Primary
    @Bean(name = "maria_dataSource")
    @ConfigurationProperties("spring.data.maria")
    public DataSource mariaDataSource() {
        return DataSourceBuilder.create().type(HikariDataSource.class).build();
    }

    @Primary
    @Bean(name = "mariaDB_entityManagerFactory")
    public LocalContainerEntityManagerFactoryBean entityManagerFactory(
            EntityManagerFactoryBuilder builder,
            @Qualifier("maria_dataSource") DataSource dataSource) {
        Map<String, String> map = new HashMap<>();
        map.put("hibernate.ejb.naming_strategy", "org.hibernate.cfg.ImprovedNamingStrategy");
        map.put("hibernate.dialect", "org.hibernate.dialect.MySQL5InnoDBDialect");
        return builder.dataSource(dataSource)
                .packages("com.farmsecurity.restapi.model") // TODO Model 패키지 지정
                .properties(map)
                .build();
    }

    @Primary
    @Bean(name = "mariaDB_transactionManager")
    public PlatformTransactionManager transactionManager(
            @Qualifier("mariaDB_entityManagerFactory") EntityManagerFactory entityManagerFactory) {
        JpaTransactionManager transactionManager = new JpaTransactionManager();
        transactionManager.setEntityManagerFactory(entityManagerFactory);
        return transactionManager;
    }
}
```
✔️ mariaDataSource()를 통해 빌드를 진행한다. <br>
✔️ entityManagerFactory()를 통해 DB와 실제 연동을 진행한다. <br>
✔️ transactionManager()를 통해 어플리케이션이나 파이어베이스를 통해 수행되는 쿼리에 맞게, 영속/비영속 등을 진행하는 엔티티 매니저를 생성한다. <br>

## 🙍‍♂ 푸쉬 알림 송신
```Java
// 파이어베이스 메시지 보내기
public void sendMessageTo(String targetToken, String title, String body) throws IOException {
    String message = makeMessage(targetToken, title, body);
    OkHttpClient client = new OkHttpClient();
    RequestBody requestBody = RequestBody.create(message, MediaType.get("application/json; charset=utf-8"));
    Request request = new Request.Builder()
            .url(API_URL)
            .post(requestBody)
            .addHeader(HttpHeaders.AUTHORIZATION, "Bearer " + getAccessToken())
            .addHeader(HttpHeaders.CONTENT_TYPE, "application/json; UTF-8")
            .build();

    Response response = client.newCall(request).execute();

    System.out.println(response.body().string());
}

// 파이어베이스 메시지 만들기
private String makeMessage(String targetToken, String title, String body) throws JsonParseException, JsonProcessingException {
    FcmMessage fcmMessage = FcmMessage.builder()
            .message(FcmMessage.Message.builder()
                    .token(targetToken)
                    /*
                    .notification(FcmMessage.Notification.builder()
                            .title(title)
                            .body(body)
                            .image(null)
                            .build()
                    )
                    */
                    .data(FcmMessage.FcmData.builder()
                            .title(title)
                            .body(body)
                            .image(null)
                            .build()
                    ).build()).validateOnly(false).build();

    return objectMapper.writeValueAsString(fcmMessage);
}

// 안드로이드 토큰 얻어오기
private String getAccessToken() throws IOException {

    // 안드로이드 스튜디오 json 파일 얻어오기
    String firebaseConfigPath = "firebase/firebase_service_key.json";

    GoogleCredentials googleCredentials = GoogleCredentials
            .fromStream(new ClassPathResource(firebaseConfigPath).getInputStream())
            .createScoped(List.of("https://www.googleapis.com/auth/cloud-platform"));

    googleCredentials.refreshIfExpired();
    return googleCredentials.getAccessToken().getTokenValue();
}
```
✔️ getAccessToken()을 통해서 사용자의 안드로이드에서 토큰을 얻어온다. <br>
✔️ 해당 토큰을 기반으로 하여 makeMessage()에서 푸쉬 알림 메시지를 만든다. <br>
✔️ makeMessage()에서 만들어진 메시지를 sendMessageTo()를 통해 안드로이드에 전송한다. <br>

## 🙍‍♂ 보유하고 있던 로그 기록 삭제
```Java
//매일 0시 1분에 날짜 체크
@Scheduled(cron = "0 1 0 * * * ")
public void DailyCheck(){
    int idx = 0;
    // 로그 테이블 로그 전체 검색
    List<Log> logs = logRepository.findAll();
    LocalDateTime today = LocalDateTime.now();
    for (Log log : logs) {
        // 로그 테이블의 시간 데이터와 오늘의 시간 비교
        String time = logs.get(idx).getTime();
        CompareDate(time, today);
        idx ++;
    }
}

// 날짜 비교
public void CompareDate (String t, LocalDateTime today){
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd/HH:mm:ss");
    LocalDateTime time = LocalDateTime.parse(t,formatter);
    // 로그 테이블에 있는 시간 데이터를 정해진 타입에 맞게 변경
    LocalDate timeDate = LocalDate.from(time);

    // 기존 시간 데이터에 한 달 더해주기
    LocalDate timeDate2 = timeDate.plusMonths(1);
    LocalDate todayDate = LocalDate.from(today);

    if(timeDate2.isEqual(todayDate)){
        // 삽입된 데이터는 한 달이 지나면 삭제
        logRepository.deleteByTime(t);
        System.out.println("삭제완료");
    }
}
```
✔️ 각 사용자에게 있는 로그(유해 동물 탐지 시간 및 대응 단계)를 무한히 가질 경우 비효율적이라고 생각했다. <br>
✔️ 따라서, 로그 생성 시점을 기준으로 한 달이 지난 경우 DB에서 삭제하도록 하였다. <br>

# :fire: Firebase(푸쉬 알림)
## 🙍‍♂ Firebase를 선택한 이유
✔️ 서버에서 앱으로 푸쉬 알림을 전송하는 기능을 구현하기 위해서는 파이어베이스와 브로드캐스트 리시버 2종류의 선택지가 존재했음 <br>
✔️ 해당 프로젝트는 앱이 실행 중이지 않을 때(즉, 백그라운드에서 실행되고 있을 경우)에도 알림이 필요할 경우, 클라이언트에게 알림을 전송해야 함 <br>
✔️ 이에 따라, 앱이 실행 중이거나 특수한 이벤트 발생 시에 작동되는 브로드캐스트 리시버는 부적절하다고 판단 <br>
✔️ 따라서, 필요조건을 충족할 수 있는 파이어베이스를 선택 <br>

## 🙍‍♂ FCM(Firebase 클라우드 메시징)의 동작 원리 <br>
<div align="center">
  <img src="https://user-images.githubusercontent.com/80700537/188641863-e223bc87-7b27-45ca-80fa-c1e56da04d02.JPG" alt="Firebase" width="550" height="400"/>
</div> <br>

1. 클라이언트 앱에서 Sender ID를 이용해 등록을 요청 <br>
2. FCM은 클라이언트 앱에서 전달받은 SenderId를 토대로 Registration Token을 클라이언트 앱에서 발급 <br>
3. 클라이언트 앱은 FCM에서 전달받은 Registration Token을 앱 서버에 전달하고, 이를 전달받은 앱 서버는 Registration Token을 저장 <br>
4. 앱 서버는 Registration Token, API Key, 전송할 메시지를 이용하여 GCM에 메시지를 전송 <br>
5. FCM은 앱 서버로부터 전달받은 메시지를 해당 클라이언트 앱에 메시지를 전송 <br>

## 🙍‍♂ FCM 핵심 코드
### :one: 파이어베이스를 통해 데이터 페이로드 형식의 알람 메시지 수신
```Java
@Override
public void onMessageReceived(RemoteMessage remoteMessage) {
    Log.d("FCM Log : ", "MessageReceived");
    if(remoteMessage.getData().size()>0){
        sendNotification(remoteMessage.getData().get("body"),remoteMessage.getData().get("title"));
    }
}
```
✔️ 탐지된 유해 동물이 있다면, 이와 관련된 사진 및 정보가 DB에 저장된다. <br>
✔️ 이와 동시에 파이어베이스에 관련 정보가 전송되게 되고, 이 정보는 FCM을 거쳐 어플리케이션에 데이터 페이로드 형식으로 도착하게 된다. <br>
✔️ 이후, 아래 sendNotification() 함수에서 이 페이로드를 처리하도록 한다. <br>

### :two: 사용자에게 보여줄 알림 형식에 맞게 데이터 페이로드 형식 처리하기
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
✔️ onMessageReceived()에서 받은 페이로드를 토대로 사용자에게 보여줄 형식에 맞게 데이터를 처리한다. <br>
✔️ 데이터를 처리한 이후, 이를 사용자가 어플리케이션에서 볼 푸쉬 알림 형식에 맞게 화면단에 배치한다. <br>

## 🙍‍♂ 회원 CRUD
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

## 🙍‍♂ 로그 기록 검색
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

## 🙍‍♂ 카메라 정보 (등록/조회/삭제)
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

# :fire: 배포
✔️ 아래 블로그를 참고하여 AWS EC2에서 배포 실행
✔️ [참고 링크](https://velog.io/@swchoi0329/EC2-%EC%84%9C%EB%B2%84%EC%97%90-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%EB%B0%B0%ED%8F%AC#ec2-%EC%84%A4%EC%A0%95)

# :fire: 느낀점
## 🙍‍♂ 좋았던 점
✔️ 작년까지의 팀 프로젝트에서는 PHP로 진행했었다. 이 당시에는 최대한 테이블 설계를 좋게 구성해도 테이블에 수정 사항이 발생했을 경우, DB뿐만 아니라 PHP에서 SQL 쿼리를 변경해 주어야 했다. 하지만, 이번 프로젝트에서는 Java와 Spring Boot를 통해서 개발을 진행했고, 테이블을 수정해야 하는 상황이 발생해도 유연하게 변경할 수 있었던 점이 좋았다. <br>
✔️ FCM을 토대로 사용자에게 어떠한 방식으로 알림을 생성하고 또 전송하는지의 과정을 배울 수 있어서 좋았다. <br>
✔️ [Spring Boot/FCM/Android Studio/Maria DB]과 같이 다양한 tool을 활용하여 상호 간 연계하는 과정을 배울 수 있어 좋았다. <br>

## 🙍‍♂ 아쉬웠던 점
✔️ Restful하지 못하게 개발했던 점이 아쉬웠다. <br>
✔️ Test를 많이 못 해본 점이 아쉬웠다. <br>

## 🙍‍♂ 개인적인 생각
✔️ 비록, 아쉬웠던 점이 있었지만 이전 프로젝트에 비해 훨씬 큰 규모로 진행할 수 있었던 점이 좋았던 것 같다. 개인적으로 Java와 Spring을 활용해서 백엔드를 구현해 보고 싶었는데, 해당 프로젝트를 통해서 실현할 수 있었던 것이 좋았다. <br>
✔️ 이번에 Spring 같은 경우 구글링이나 책을 통해서 공부를 했었다. 물론, 성공리에 프로젝트를 마무리했으나, 무엇인가 부족하다는 느낌이 들었다. 인프런에서 스프링으로 유명한 "김영한" 개발자 님의 강의를 추후에 들어봐서 스프링 실력을 키워봐야겠다. <br>
✔️ 다음 프로젝트에서는 Resftful한 개발, Test 위주의 개발, 다양한 API(지도, 날씨 등)을 적용한 개발, 대량 트랜잭션에 대비한 개발을 해보고 싶다. <br>
