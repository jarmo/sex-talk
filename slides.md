# Security

### WTF is that?

---

![magic](/images/magic.gif)

---

![noob](/images/n00b.jpg)

---

## Achieving Security

---

## Trust no-one 
Always think from a perspective of an attacker

> I don't think that anyone is going to open up a Firebug just to hack our system.

---

## Ask questions

----

* Does framework X really escape HTML?

----

* Does CSRF protection really work?

----

* What if user changes id in some query?

----

* Is session cookie with 'httpOnly' and 'secure' flags?

----

* Is session cookie signed and encrypted?

----

* Is firewall enabled and application not directly accessible?

----

* Is user forced to https when coming from http?

----

* Is framework/library secure by default or do I have to configure and use
  "correct" API?

----

* Are we using a correct/slow password hashing algorithm?

----


![incorrect-password](/images/incorrect-password.jpg)

----

* The more questions, the better security.

---

## Learn

----

#### XSS - not to be confused with CSS

```html
<div>
  Hello, <%= name %>
</div>
```

----

#### HTTP methods - GET, POST, DELETE, PUT, etc.

```html
<a href="/delete-user?userId=33">Delete user</a>
```

----

#### CSRF

```html
<form action="/add-user" method="POST">
  <input name="new-user-email" type="email">
  <input type="submit" value="add">
</form>
```

---

## Think & Do More

----

#### Suspect 3rd party software

```
$ ls -l ~/.ssh/codeborne-active* | awk '{print $1, $9}'
-rw------- /Users/jarmo/.ssh/codeborne-active
-rw-r--r-- /Users/jarmo/.ssh/codeborne-active.pub

$ openssl pkcs8 -topk8 -inform PEM -outform PEM -nocrypt \
  -in ~/.ssh/codeborne-active \
  -out ~/.ssh/codeborne-active.pkcs8

$ ls -l ~/.ssh/codeborne-active* | awk '{print $1, $9}'
-rw------- /Users/jarmo/.ssh/codeborne-active
-rw-r--r-- /Users/jarmo/.ssh/codeborne-active.pkcs8
-rw-r--r-- /Users/jarmo/.ssh/codeborne-active.pub
```

----

#### Check edge cases

```java
public class AdminController {
  public Result index() {
    if (!isAdmin()) return Results.forbidden();

    return Results.html();
  }
}
```

----

#### White-list > Black-list

```java
public class ArticlesController {

  @CheckForCSRFToken
  public void destroy(int id) {
    Article.find(id).destroy();
  }

}
```

----

#### Insecure API-s

```java
public class OAuthController {

  public static Result checkToken(Long clientId,
                                  String clientSecret,
                                  String redirectUri) {
    OAuthClient client = OAuthClientRepository.get(clientId);

    if (client.secret != clientSecret)
      throw new RuntimeError("invalid secret");

    if (client.redirectUri != redirectUri)
      throw new RuntimeError("invalid redirect uri");
  }

}
```

----

#### Implicit Configuration

```
# catch-all routes

* /{controller}            {controller}.index
* /{controller}/{action}   {controller}.{action}
```

----

#### Insecure defaults

```java
configuration.getString("secret", "a267bce1872...")
```

----

#### Secrets in VCS

```
# production.conf

application.secret="f4f7edabc88b0de0b15bec67f2ed6b72ce4"
```

----

#### Secrets in logs

```
2015.10.01 18:01:54,990 [INFO] ApiController token=37e07e0...
```

---

# DEMO

---

<img src="/images/seen-cat.jpg", width="750" alt="seen cat">

----

## What happened?

----

* Humans (including developers) will be the weakest link - if not you then someone else

----

* E-mail is insecure and easy to spoof

----

* CSRF

----

* XSS

----

* Insecure session cookie

----

* Production data visible in source code

---

## Give up, you will be hacked!

----

![orly](/images/orly.gif)

----

## Just try to make it a little harder

----

![pretty-please](/images/pretty-please.jpg)

---

# kthxbye!
