## 퍼사드(Facade) 패턴

### 퍼사드 패턴이란?


![](https://images.velog.io/images/papakang22/post/d416ee85-d75b-4b14-a88a-964c6477c867/R1280x0.png)

퍼사드 패턴이란 복잡한 서브 시스템 의존성을 최소화하는 패턴입니다.
서브 시스템에 있는 인터페이스들에 대한 통합 인터페이스를 제공하여 서브 시스템을 더 쉽게 사용할 수 있도록 만드는 더 높은 수준의 인터페이스를 말합니다.

이메일 발송로직을 예로 들어봅시다.

### Before

```java

public class Client {

    public static void main(String[] args) {
        String to = "namookk@test.com";
        String from = "namookk2@test.com";
        String host = "127.0.0.1";

        Properties properties = System.getProperties();
        properties.setProperty("mail.smtp.host", host);

        Session session = Session.getDefaultInstance(properties);

        try {
            MimeMessage message = new MimeMessage(session);
            message.setFrom(new InternetAddress(from));
            message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));
            message.setSubject("Test Mail from Java Program");
            message.setText("message");

            Transport.send(message);
        } catch (MessagingException e) {
            e.printStackTrace();
        }
    }
}
```

위와 같이 이메일을 보내려면 이메일 서버셋팅, 이메일 내용 셋팅, 이메일 보내기 와같은 서브시스템들을 모두 호출해야합니다.
퍼사드 패턴을 적용해봅시다.

### After
```java
@Getter
@Setter
public class EmailMessage {

    private String from;

    private String to;
    private String cc;
    private String bcc;

    private String subject;

    private String text;
}

@Getter
@Setter
public class EmailSettings {
    private String host;
}
```
```java
public class EmailSender {

    private EmailSettings emailSettings;

    public EmailSender(EmailSettings emailSettings) {
        this.emailSettings = emailSettings;
    }

    /**
     * 이메일 보내는 메소드
     * @param emailMessage
     */
    public void sendEmail(EmailMessage emailMessage) {
        Properties properties = System.getProperties();
        properties.setProperty("mail.smtp.host", emailSettings.getHost());

        Session session = Session.getDefaultInstance(properties);

        try {
            MimeMessage message = new MimeMessage(session);
            message.setFrom(new InternetAddress(emailMessage.getFrom()));
            message.addRecipient(Message.RecipientType.TO, new InternetAddress(emailMessage.getTo()));
            message.addRecipient(Message.RecipientType.CC, new InternetAddress(emailMessage.getCc()));
            message.setSubject(emailMessage.getSubject());
            message.setText(emailMessage.getText());

            Transport.send(message);
        } catch (MessagingException e) {
            e.printStackTrace();
        }
    }
}
```
```java

public class Client {

    public static void main(String[] args) {
        EmailSettings emailSettings = new EmailSettings();
        emailSettings.setHost("127.0.0.1");

        EmailSender emailSender = new EmailSender(emailSettings);

        EmailMessage emailMessage = new EmailMessage();
        emailMessage.setFrom("keesun");
        emailMessage.setTo("whiteship");
        emailMessage.setCc("일남");
        emailMessage.setSubject("오징어게임");
        emailMessage.setText("밖은 더 지옥이더라고..");

        emailSender.sendEmail(emailMessage);
    }
}
```
위와 같이 EmailSettings, EmailMessage를 사용한다면
클라이언트는 EmailSender를 통해 sendEmail를 통해 이메일을 보낼 수 있습니다.
물론 예제에서는 셋팅과정도 포함되어있지만 실제 사용할 때는 셋팅은 한번만 셋팅하면 되므로 EmailMessage만 보내서 메일을 발송할 수 있습니다.

### 퍼사드 패턴의 장단점
#### 장점
+ 서브 시스템에 대한 의존성을 한곳으로 모을 수 있다.
#### 단점
+ 퍼사드 클래스가 서브 시스템에 대한 모든 의존성을 가지게 된다.


