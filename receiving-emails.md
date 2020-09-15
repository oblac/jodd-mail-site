# Receiving Emails

Receiving emails is similar to sending: there are classes that encapsulate POP3 and IMAP connections, i.e. servers. Both create the same receiving session - `ReceiveMailSession` - that fetches emails and return them as an array of `ReceivedEmails`. This way you work with both POP3 and IMAP servers in the very same way.

Even the instances of the same class `ReceiveMailSession` are created by POP3 and IMAP server implementations, **not all** methods work in the same way! This difference depends on a server type. Commonly, POP3 has fewer features \(e.g. not being able to fetch all folder names for Gmail account\), while IMAP server is richer \(e.g. it supports server-side search\). {: .attn}

During receiving, all emails are fetched and returned as an array of `ReceivedEmail` objects. This is a POJO object, so its very easy to work with. It provides many helpful methods, too. Each `ReceivedEmail` also contains a list of all messages, attachments, and attached messages \(EMLs\).

### Receiving Builder

The proposed method to fetch emails is to use the builder. It is simple, powerful and you can fine-tune the receiving process. For example, you can mark or unmark some flags, select folder and so on. Here is how:

```java
session
    .receive()
    .markSeen()
    .fromFolder("work")
    .filter(...)
    .get();
```

#### Convenient methods

`ReceiveMailSession` contains some legacy but convenient methods for fetching emails:

* `receiveEmail()` - returns all emails, but don't change the 'seen' flag.
* `receiveEmailAndMarkSeen()` - returns all emails and marked all messages as 'seen'.
* `receiveEmailAndDelete()` - returns all emails and mark them as 'seen' and 'deleted'.

The first method does a little trick: since `javax.mail` always set a 'seen' flag when new message is downloaded, we do set it back on 'unseen' \(if it was like that before fetching\). This way `receiveEmail()` should not change the state of your inbox.

### POP3

For POP3 connection, use `Pop3Server`:

```java
Pop3Server popServer = MailServer.create().
        host("pop3.jodd.org",
        auth("username", "password")
        .buildPop3MailServer();

ReceiveMailSession session = popServer.createSession();
session.open();
System.out.println(session.getMessageCount());
ReceivedEmail[] emails = session.receiveEmailAndMarkSeen();
if (emails != null) {
    for (ReceivedEmail email : emails) {
        System.out.println("\n\n===[" + email.getMessageNumber() + "]===");

        // common info
        System.out.println("FROM:" + email.from());
        System.out.println("TO:" + email.to()[0]);
        System.out.println("SUBJECT:" + email.subject());
        System.out.println("PRIORITY:" + email.priority());
        System.out.println("SENT DATE:" + email.sentDate());
        System.out.println("RECEIVED DATE: " + email.receiveDate());

        // process messages
        List messages = email.getAllMessages();
        for (EmailMessage msg : messages) {
            System.out.println("------");
            System.out.println(msg.getEncoding());
            System.out.println(msg.getMimeType());
            System.out.println(msg.getContent());
        }

        // process attachments
        List<EmailAttachment> attachments = email.getAttachments();
        if (attachments != null) {
            System.out.println("+++++");
            for (EmailAttachment attachment : attachments) {
                System.out.println("name: " + attachment.getName());
                System.out.println("cid: " + attachment.getContentId());
                System.out.println("size: " + attachment.getSize());
                attachment.writeToFile(
                    new File("d:\\", attachment.getName()));
            }
        }
    }
}
session.close();
```

### Receiving emails using SSL

Again, very simply: use the very same `ssl()` flag. Here is how it can be used to fetch email from Google:

```java
Pop3Server popServer = MailServer.create()
    .host("pop.gmail.com")
    .ssl(true)
    .auth("username", "password")
    .buildPop3MailServer();

ReceiveMailSession session = popServer.createSession();
session.open();
...
session.close();
```

### IMAP

The above example can be converted to IMAP usage very easily:

```java
ImapServer imapServer = MailServer.create()
    .host("imap.gmail.com")
    .ssl(true)
    .auth("username", "password")
    .buildImapMailServer();

ReceiveMailSession session = imapServer.createSession();
session.open();
...
session.close();
```

Can't be easier:\)

As said above, when working with IMAP server, many methods of `ReceiveMailSession` works better or... simply, just works. For example, you should be able to use following methods:

* `getUnreadMessageCount()` - to get number of un-seen messages.
* `getAllFolders()` - to receive all folders names
* server-side filtering - read the next chapter

### Receiving Envelopes

There is an option to receive only email envelopes: the header information, like `from` and `subject` but not the content of the messages. Receiving only envelopes makes things faster and it make more sense in situations when not all messages have to be received.

Each email has it's own ID that is fetched as well. Later on, you can use this ID to filter out just the messages with specific ID.

### Filtering emails

IMAP server also knows about server-side filtering of emails. There are two ways how to construct email filter, and both can be used in the same time.

The first approach is by grouping terms:

```java
filter()
    .or(
        filter().and(
            filter().from("from"),
            filter().to("to")
        ),
        filter().not(filter().subject("subject")),
        filter().from("from2")
    );
```

Static method `filter()` is a factory of `EmailFilter` instances. Above filter defines the following query expressions:

```text
(from:"from" AND to:"to") OR not(subject:"subject") OR from:"from"
```

With this approach you can define boolean queries of any complexity. But there is a more fluent way to write the same query:

```java
filter()
    .from("from")
    .to("to")
    .or()
    .not()
    .subject("subject")
    .from("from2");
```

Here we use non-argument methods to define _the current_ boolean operator: `and()` and `or()`. All terms defined _after_ the boolean marker method us that boolean operator. Method `not()` works only for the very _next_ term definition. This way you probably can not defined some complex queries, but it should be just fine for the real-life usages.

Here is how we can use a simple filter when fetching emails:

```java
ReceivedEmail[] emails = session.receiveEmail(filter()
    .flag(Flags.Flag.SEEN, false)
    .subject("Hello")
);
```

This would return all unread messages with the subject equals to "Hello".

Note that for IMAP server, the search is executed by the IMAP server. This significantly speeds up the fetching process as not all messages are downloaded. Note that searching capabilities of IMAP servers may vary.

You can use the same filters on POP3 server, but keep in mind that the search is performed on the client-side, so still all messages have to be downloaded before the search is thrown.

