# Sending Emails

Emails are sent using `SendMailSession`. Mail session encapsulates the process of preparing emails, opening and closing transport connections, and sending emails.

Mail session is created by the `MailServer`.

```java
SmtpServer smtpServer = MailServer.create()
    .host("http://mail.com")
    .port(21)
    .buildSmtpMailServer();
...
SendMailSession session = smtpServer.createSession();
session.open();
session.sendMail(email1);
session.sendMail(email2);
session.close();
```

Since opening session and sending emails may produce `EmailException`, it is necessary to wrap methods in `try`-`catch` block and closing the session in the `finally` block.

### Sending using SSL

The preferred way of sending e-mails is by using SSL protocol. _Jodd_ supports secure e-mail sending. Just set the `ssl()` flag while creating the server.

Here is an example of sending e-mail via [Gmail](http://gmail.com) \(port `465` is set by default\):

```java
SmtpServer smtpServer = MailServer.create()
    .ssl(true)
    .host("smtp.gmail.com")
    .auth("user@gmail.com", "password")
    .buildSmtpMailServer();
...
SendMailSession session = smtpServer.createSession();
session.open();
session.sendMail(email);
session.close();
```

Everything is the same, just a different session provider is used.

