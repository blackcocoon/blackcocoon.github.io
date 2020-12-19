---
sort: 1
---

# 정리 - 1

스프링프레임웍의 JavaMailSenderImpl을 이용해서 메일을 발송하는 방법에 대해 알아 보겠습니다. 

메일을 발송하려면 메일을 발송해주는 메일 서버(SMTP Server)가 있어야 합니다. 
메일 서버를 통해 메일을 보낼때 서버에 접속하는 방법은 보통 두 가지가 있습니다.

`첫 번째`는 서버가 릴레이를 허용하는 경우 입니다. 메일 서버가 릴레이를 허용하는 경우 그 메일 서버에 계정이 없더라도 메일을 발송할 수 있습니다. 이렇게 릴레이를 허용하는 경우 타인에 의해 스팸 메일을 보내는데 악용이 될 수 있으므로 특정 IP에서만 릴레이가 되도록 하는게 일반적입니다.

`두 번째`는 메일 서버에 계정이 있어서 아이디와 비밀번호로 인증후 메일을 보내는 방법 입니다. 요즘은 두 번째가 가장 일반적인 방법일 것입니다.

```xml
<!-- 메일보내기 -->
<bean id="mailSender" class = "org.springframework.mail.javamail.JavaMailSenderImpl">
  <property name="host" value="111.111.111.111" />
  <property name="port" value="25" />
  <property name="username" value="your_smtp_id" />
  <property name="password" value="your_smtp_pw" />
  <property name="javaMailProperties">
	 <props>
		   <prop key="mail.smtp.starttls.enable">false</prop>
	 </props>
  </property> 
</bean>
```

```java
// 메일을 발송하는 곳에서 스프링프레임웍 설정에서 정의한 "mailSender" 빈을 인젝션 하여 사용합니다.
@Autowired 
private JavaMailSenderImpl mailSender;

/* 중략 */

// 첨부파일이 없는 간단하게
String setfrom = "아이디@gmail.com";
String tomail  = "받는 사람 이메일";
String title   = "제목";
String content = "내용";

/* 중략 */

MimeMessage message = mailSender.createMimeMessage();
MimeMessageHelper messageHelper = new MimeMessageHelper(message, true, "UTF-8");

messageHelper.setFrom(setfrom);  // 보내는사람 생략하거나 하면 정상작동을 안함
messageHelper.setTo(tomail);     // 받는사람 이메일
messageHelper.setSubject(title); // 메일제목은 생략이 가능하다
messageHelper.setText(content);  // 메일 내용

mailSender.send(message);

/* 중략 */

// 첨부파일이 있는경우
MimeMessage msg = this.webMailSender.createMimeMessage();
MimeMessageHelper message = new MimeMessageHelper(msg, true, "UTF-8");

message.setFrom(setfrom);
message.setTo(tomail);
message.setCc(cc);
message.setSubject(title);
message.setText(content);

// 첨부파일
String pathfile = "D:/aaa/20180101aaa.jpg"; // 파일명 중복방지때문에 파일명 바꿔서 서버에 저장된 파일명 (path와 file 이 있는 전체 경로)
FileDataSource fds = new FileDataSource(pathfile);
String originalFileNm = "니사진.jpg"; // DB에 저장된 원본 파일명

//B는 Base64, Q는 quoted-printable
message.addAttachment(MimeUtility.encodeText(originalFileNm, "UTF-8", "B"), fds);

mailSender.send(msg);
```

