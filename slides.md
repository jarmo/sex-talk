# Security

### WTF is that?

---

![magic](https://i.imgur.com/iZcUNxH.gif)

---

![noob](http://i0.kym-cdn.com/photos/images/facebook/000/149/678/131091292545.jpg)

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

<img src="https://blog.codinghorror.com/content/images/2017/03/incorrect-password.jpg">

----

* The more questions, the better security.

---

## Learn

----

#### XSS - not to be confused with CSS

```
<div>
  Hello, <%= name %>
</div>
```

----

#### HTTP methods - GET, POST, DELETE, PUT, etc.

```
<a href="/delete-user?userId=33">Delete user</a>
```

----

#### CSRF

```
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

#### Verify edge cases

```
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

#### Explicit Configuration

```
# catch-all routes

* /{controller}            {controller}.index
* /{controller}/{action}   {controller}.{action}
```

----

#### Unsecure defaults

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

<img src="http://i.imgur.com/I5oai.jpg" width="750">

----

## What happened?

----

* Humans will be the weakest link - if not you then someone else

----

* E-mail is unsecure and easy to fake

----

* CSRF

----

* XSS

----

* Unsecure session cookie

----

* Production data visible in source code

---

## Give up, you will be hacked!

----

<img src="https://stash.jarmopertman.com/orly.gif" height="550">

----

## Just try to make it a little harder

----

<img src="http://www.quickmeme.com/img/c5/c5d5b517c9a526eb953dd4fd121667040b3512959c8de616ec8f2f01190d3540.jpg">

---

# kthxbye!
