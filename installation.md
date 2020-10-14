---
description: Tips on how to install Jodd Mail library in your app
---

# Installation

**Jodd Mail** is released on Maven Central. You can use the following snippets to add it to your project:

{% tabs %}
{% tab title="Maven" %}
```markup
<dependency>
  <groupId>org.jodd</groupId>
  <artifactId>jodd-mail</artifactId>
  <version>x.x.x</version>
</dependency>
```
{% endtab %}

{% tab title="Gradle" %}
```text
implementation 'org.jodd:jodd-mail.x.x'
```
{% endtab %}

{% tab title="Gradl.kt" %}
```kotlin
implementation("org.jodd:jodd-mail.x.x")
```
{% endtab %}

{% tab title="Scala SBT" %}
```scala
libraryDependencies += "org.jodd" % "jodd-mail" % "x.x.x"
```
{% endtab %}

{% tab title="Ivy" %}
```markup
<dependency org="org.jodd" name="jodd-mail" rev="x.x.x" />
```
{% endtab %}

{% tab title="Leiningen" %}
```
[org.jodd/jodd-mail "x.x.x"]
```
{% endtab %}

{% tab title="Buildr" %}
```
'org.jodd:jodd-mail:jar:x.x.x'
```
{% endtab %}
{% endtabs %}

You will also need the mail implementation, for example:

```text
jakarta.mail:jakarta.mail-api:1.6.5
com.sun.mail:jakarta.mail:1.6.5
```

That is all!

### Snapshots

**Jodd JSON** snapshots are published on [Maven Central Snapshot repo](https://oss.sonatype.org/content/repositories/snapshots/org/jodd/jodd-lagarto/).

{% hint style="warning" %}
Snapshots are released manually. Feel free to contact me if you need a new SNAPSHOT release sooner.
{% endhint %}



