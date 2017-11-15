# Security

### WTF is that?

Note:
Here's a short talk about everyday software security I gave at [@codeborne](https://github.com/codeborne).

Notes are here for people who didn't see that talk.

---

![magic](/images/magic.gif)

Note: Security is really often thought about being something magical, which is
enforced automatically in the background by the used framework/library. In
reality, this might not be the case and it should be each developer's
responsibility to think like an attacker to implement more secure systems.

---

![noob](/images/n00b.jpg)

Note: In the field of security, there's no such thing as ideal security and in
a way everyone is a n00b - there's always something to learn and improve.
I don't consider myself as an expert in this field, but I've accumulated some
knowledge throughout the years which I'd like to share.

---

## Achieving Security

---

## Trust no-one 

Always think from a perspective of an attacker

> I don't think that anyone is going to open up a Firebug just to hack our system.

Note: Best way to think about possible security issues within any software is
to think like an attacker. Instead of assuming that software is secure, think
about all the possible ways where it might have security issues. If you've never
done it, then it might be hard to start, but it will be easier after a while.

*Quote above is from a real customer who had an internal API publicly exposed
and didn't see it as a security threat.*

---

## Ask questions

Note: It is much easier for everyone to assume that responsibility of security is
not theirs and is handled by some other system/framework/library/developer. As
a developer, and a human being in general, never assume anything. Especially when
it is something security-related. Always question author of
a system/framework/library, fellow developers and yourself. Test your
assumptions even if someone says that X is 100% secure.

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

> Assumption is a mother of all... you know who?!

Note: This quote, said by one of my co-worker, is a perfect example of
assumption. It was assumed that this other
person knows this saying without validating it.

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

Note: By looking at the code above it is not clear if HTML is escaped or not. Test it out to be sure!
If it is not escaped by default, then configure your templating
engine/framework to do it so that there's no possibility of creating XSS issues.

----

#### HTTP methods - GET, POST, DELETE, PUT, etc.

```html
<a href="/delete-user?userId=33">Delete user</a>
```

Note: Actions having any side-effects (for example: create, delete, edit, etc.)
should not be done via *GET* http methods since it creates an opening for any
attacker to use so called cross-site request forgery attack.

----

#### CSRF

```html
<form action="/add-user" method="POST">
  <input name="new-user-email" type="email">
  <input type="submit" value="add">
</form>
```

Note: By looking at the form above it is quite probable, that this application
does not prevent CSRF attacks even though form is submitted via *POST*. It's possible that a CSRF token will be added
with JavaScript before submitting form, but it's not that likely. Test it out
to be sure!

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

Note: Here it is clearly visible that converting ssh private key (created by
ssh-keygen) with openssl to some other format creates private key with much more open file system permissions.

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

Note: Most of us would expect that the code above for non-administrators would
return HTTP response with a code of 403 with some default body for this
response. However, within one of the Java frameworks Admin/index.html would be
rendered with a response code of 403! Correct way would be to specify rendered
template explicitly, which is a smell of a bad API design.

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

Note: Instead of hoping that every developer annotates actions properly, it
should be a responsibility of some global filter to check CSRF token existence
and its equality so that it's impossible to forget to send that token with each
required request. Same approach should be used for permissions - every action
should be forbidden by default, unless specified otherwise. See
[authorize_action](https://github.com/jarmo/authorize_action) for an example.

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

Note: Here's an example of a dangerous API, which assumes that its caller
(developer) will check for secret and redirect URI. It should have an API,
which prevents to retrieve client accidentally without correct secret and/or
redirect URI.

----

#### Implicit Configuration

```
# catch-all routes

* /{controller}            {controller}.index
* /{controller}/{action}   {controller}.{action}
```

Note: Having defined so-called catch-all routes, might open CSRF attack vectors
or add a possibility to call any other public methods within all controllers.
Explicit route definitions should be used instead!

----

#### Insecure defaults

```java
configuration.getString("secret", "a267bce1872...")
```

Note: This pattern is seen quite often in the wild - a sensitive data is
retrieved from the configuration, but a default fallback is used in case
it is not defined in the configuration to make development easier.
It's probable that this same default will end up in production environment.
An exception should be thrown instead for all sensitive configuration
parameters when they are missing and different values should
be used between development and production!

----

#### Secrets in VCS

```
# production.conf

application.secret="f4f7edabc88b0de0b15bec67f2ed6b72ce4"
```

Note: When source code leaks (or some developer has any bad intentions) and sensitive data is part of it,
then it is possible to gain access to production environment. Sensitive data
should not be part of source code! Ever!

----

#### Secrets in logs

```
2015.10.01 18:01:54,990 [INFO] ApiController token=37e07e0...
```

Note: Sensitive data should be filtered automatically out of log files.

---

# DEMO

Note: I did show attack in live against one of the applications, which had
different small security problems. By using all of them together allowed an attacker to gain
administrative privileges.

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

Note: If your system is specifically targeted and attacker's goal is to gain
access to your system having enough monetary and time resources behing him then your
system will be probably compromised. Problem with security is that you can
invest endless amount of time and money to add yet another security
measurements. However, always thinking like an attacker, is not that expensive
way to add more security to your system, which in turn might drive your attackers to
target some weaker systems instead. For example, you have a lock installed
to your apartment door even though you're fully aware that if someone plans to
get into your apartment then that lock doesn't stop him. Right? Right?

----

![pretty-please](/images/pretty-please.jpg)

---

# kthxbye!
