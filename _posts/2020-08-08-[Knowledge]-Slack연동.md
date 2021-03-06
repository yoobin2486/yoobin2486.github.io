---
layout: post
title: "[Knowledge] Slack 연동"
date: 2020-08-08 22:00:00
categories: Knowledge
permalink: /Knowledge/slack
---

# Slack WebHooks 사용해서 메세지 보내기

1. Slack 가입 후 Workspace 생성

   - 구글 이메일 혹은 본인 소속의 이메일을 이용해 슬랙에 가입한다.
   - 가입한 이메일로 로그인 한 후 workspace를 생성한다.
   - <https://slack.com/get-started#/create>

![slack_01](/img/slack_01.JPG)

![slack_02](/img/slack_02.JPG)

​									(본인의 메일로 온 code를 적어준다.)

2. 채널 생성
   - 아래와같이 팀/회사 이름을 적는다. 테스트 해볼거면 무심한듯 시크하게 지어보자.
   - 프로젝트(Channel) 이름을 적어보자.
   - 초대할 팀원이 있다면 email을 적고 아니라면 skip하자.
   - 채널 생성이 생성되었다.

![slack_03](/img/slack_03.JPG)

![slack_04](/img/slack_04.JPG)

![slack_05](/img/slack_05.JPG)

![slack_06](/img/slack_06.JPG)

3. WebHooks 추가
   - More을 누르면 App이 보일거다. App에서 incoming WebHooks를 검색한다.
   - Add버튼을 누른다.
   - Add to slack을 누른 후 본인이 메세지할 채널을 연결한다.
   - Add Incoming WebHooks Integration버튼을 누르면 WebHooks URL이 나올것이다.
   - 이것을 복사하자.

![slack_07](/img/slack_07.JPG)



![slack_08](/img/slack_08.JPG)

![slack_09](/img/slack_09.JPG)

![slack_09_02](/img/slack_09_02.JPG)

​		(이 아래 테스트 하는 방법이 나오는데 그것을 참고해서 한다면 여기서 그만 봐도 좋다.)

4. 메세지 보내기
   - 이제 해당 채널에 메세지를 보낼 수 있도록 해보자

```java
public class SlackUtils {
    private static String slackWebhookUrl = "복사한 WebHooks URL";

    public static void sendMessage(SlackMessage message) {
        CloseableHttpClient client = HttpClients.createDefault();
        HttpPost httpPost = new HttpPost(slackWebhookUrl);

        try {
            ObjectMapper objectMapper = new ObjectMapper();
            String json = objectMapper.writeValueAsString(message);

            StringEntity entity = new StringEntity(json);
            httpPost.setEntity(entity);
            httpPost.setHeader("Accept", "application/json");
            httpPost.setHeader("Content-type", "application/json");

            client.execute(httpPost);
            client.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

```java
@AllArgsConstructor
@Builder(builderClassName = "Builder")
@Getter
@Setter
public class SlackMessage implements Serializable {
    private String channel;
    private String username;
    private String text;
    private String icon_emoji;
}
```

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SlackMessage slackMessage = SlackMessage.builder()
                .channel("study") // 보낼 채널 이름
                .username("yb") // username
                .text("yeah..") // 보낼 내용
                .icon_emoji(":ghost:") // 이모티콘 이름
                .build();

        SlackUtils.sendMessage(slackMessage);
    }
}
```

![slack_10](/img/slack_10.JPG)

테스트를 해보았다면 각자의 프로젝트에서 필요한 부분에 logger 레벨(info, warn, error)에 따라 각 채널을 만들어서 필요한 곳에 에러메세지를 쏴주도록 해보자.