---
description: Create Emails
---

# Email Definition

An email is defined as a simple POJO bean of type `Email`. Each part of the email message can be set individually. Moreover, `Email` supports a fluent interface, so even the definition of an e-mail message would look natural.

`Email` supports plain text, HTML messages, and any combination of both. When the only text or HTML message is set, a simple email will be sent. When both text and HTML message is set, or when attachments are added, a multi-part e-mail will be sent. Actually, `Email` supports any number of separate messages to be sent as an email. Here are some examples using fluent interface.

Plain-text email:

```java
Email email = Email.create()
    .from("john@jodd.org")
    .to("anna@jodd.org")
    .subject("Hello!")
    .textMessage("A plain text message...");
```

HTML email:

```java
Email email = Email.create()
    .from("john@jodd.org")
    .to("anna@jodd.org")
    .subject("Hello HTML!")
    .htmlMessage("<b>HTML</b> message...");
```

Text and HTML email, high priority:

```java
Email email = Email.create()
    .from("john@jodd.org")
    .to("anna@jodd.org")
    .subject("Hello!")
    .textMessage("text message...")
    .htmlMessage("<b>HTML</b> message...")
    .priority(PRIORITY_HIGHEST);
```

### Email Addresses

All email addresses \(from, to, cc...\) may be specified in the following ways:

* only by email address, e.g.: `some.one@jodd.com`
* by personal \(display\) name and email address in one string, e.g.: `John <some.one@jodd.com>`
* by separate personal \(display\) name and email address
* by providing `EmailAddress`, a class that parses and validates emails per specification
* by providing `InternetAddress` or just `Address` instance.

Consider using personal names as it is less likely your message is going to be marked as spam ;\)

Multiple email addresses are specified using arrays or by calling methods `to()` or `cc()` multiple times:

```java
Email email = Email.create()
    .from("john@jodd.org")
    .to("adr1@jodd.org", "adr2@jodd.org")
    .cc("xxx@bar.com")
    .cc("zzz@bar.com")
    .subject("Hello HTML!")
    .htmlMessage("<b>HTML</b> message");
```

### Attachments

There are several attachment types that can be added:

* from a memory byte array,
* from an input stream,
* from a file,
* from generic `DataSource`.

{% hint style="warning" %}
File attachments depend on `javax.mail` content type resolution \(that might not work for you\). You can always attach files as byte or input stream attachment.
{% endhint %}

Attachments are created using the `EmailAttachment` class:

```java
.attachment(EmailAttachment.with()
    .name("some name")
    .content(bytesOfImage)
```

The `content()` method accepts different attachment types.

### Embedded \(inline\) attachments

A special case of attachments is _inline_ attachments. These are usually related content for HTML message, like images, that should appear inside the message, and not separate as a real attachment.

Embedding is also supported. All attachments created with the `ContentID` set will be considered as inline attachments. However, they also need to be embedded to a certain message, to form a so-called _related_ part of an email. Email clients usually require to have all inline attachments related to some message.

### Example

```java
Email email = Email.create()
    .from("inf0@jodd.org")
    .to("ig0r@gmail.com")
    .subject("test6")
    .textMessage("Hello!")
    .htmlMessage(
        "<html><META http-equiv=Content-Type content=\"text/html; " +
        "charset=utf-8\"><body><h1>Hey!</h1><img src='cid:c.png'>" +
        "<h2>Hay!</h2></body></html>")
    .embeddedAttachment(EmailAttachment.with()
        .content(new File("/c.png")))
    .attachment(EmailAttachment.with()
        .content("/b.jpg"));
```

