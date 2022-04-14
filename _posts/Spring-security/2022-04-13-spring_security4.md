---
layout: post
title: "Spring-Security 4 - Remember me 인증"
author: "xi-jjun"
tags: Spring-security

---

# Spring-Security 공부 시작!

인프런 [정수원 강사님의 강의](https://www.inflearn.com/course/%EC%BD%94%EC%96%B4-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)를 보고 정리하는 글을 작성한 것이다.

<br>

# Remember Me 인증

> remember me : 세션이 만료되고 웹 브라우저가 종료된 후에도 application이 사용자를 기억하는 기능

`remember me` cookie에 대한 요청을 확인한 후 token기반 인증을 사용해 유효성을 검사하고 token이 검증되면 사용자는 로그인이 되는 방식이다.

<br>

## Remember Me 인증 API

```java
http.rememberMe()
```

`rememberMe` 기능이 동작함.

<br>

```java
.rememberMeParameter("remember") // default : remember-me
```

![security4_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_1.png?raw=True){: width="85%"}

login.html의 parameter name 과 동일해야 한다.

<br>

```java
.tokenValiditySeconds(3600) // default : 14 days
```

`remember-me` cookie의 만료시간 설정 가능하다.

<br>

```java
.alwaysRemember(true)
```

`remember-me` 기능이 활성화 되어있지 않아도 항상 실행되게 하는 API.

<br>

```java
.userDetailsService(userDetailsService)
```

`remember-me` 기능을 수행할 때, 시스템에 있는 사용자 계정을 조회하는 과정에 필요한 class.

<br>

## 시나리오 #1 : 그냥 로그인

1. 로그인 화면에서 `remember-me` 를 비활성화 시킨 다음에 로그인.

2. home화면에 접속 완료! ← 인증되었기 때문에 접속이 가능했다.

   - 인증이 되었다 == 그 사용자의 session이 생성됐다는 뜻. 해당 session에 *인증에 성공한 '인증 객체'*가 있다.

   - 서버는 인증에 성공한 client에게 사용자의 session id를 response header에 담아서 보낸다.

     → client가 다시 서버에 접속했을 때 인증을 안받아도 되는 이유이다.

     - client의 session id를 서버가 보고 매칭되는 session을 꺼낸다. 그 session에 security context가 있고, 그 security context안에 인증객체가 있다. 이 정보를 갖고 사용자가 인증된 사용인지 판단하는 것이다.

   ![security4_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_2.png?raw=True){: width="75%"}

   우리가 접근하려던 `/`경로에 접근하여 `home`이라는 문자가 뜬 것을 볼 수 있다. 그 때 생성된 session id도 확인할 수 있다.

3. 여기서 session id를 제거한다면? (제거한 뒤 새로고침 진행한 결과)

   ![security4_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_3.png?raw=True){: width="75%"}

   다시 로그인화면으로 이동되는 것을 볼 수 있다.

<br>

## 시나리오 #2 : remember-me 활성화 후 로그인

1. `remember-me` checkbox를 체크한 뒤 로그인을 진행한다.

2. home화면에 접속 완료! 

   ![security4_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_4.png?raw=True){: width="75%"}

   `remember-me` cookie가 생성된 것을 볼 수 있다.

3. 여기서 session id를 제거한다면? (제거한 뒤 새로고침 진행한 결과)

   ![security4_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_5.png?raw=True){: width="75%"}

   새로고침을 했지만, 이전처럼 로그인화면으로 이동하게 되는 것이 아니라 계속 로그인이 된 것 처럼 `/`에 접근할 수 있었다. 그리고 새로운 session id도 만들어졌음을 확인할 수 있었다.

session id가 없다고 해도, `spring-security`에서는 `remember-me`라는 이름의 cookie명을 가진 값이 있다면, 추출해서 사용자 계정을 얻는다. 그리고 얻은 계정으로 다시 인증을 시도하는 과정이 `remember-me` 기능들을 통해 이뤄진다.

<br>

# Remember Me 인증 Filter : RememberMeAuthenticationFilter class

`RememberMeAuthenticationFilter`는 filter가 사용자 요청을 받아서 해당 요청을 처리할 때 어떤 조건이 만족되어야 한다.

1. `Authentication`(인증객체)가 null일 경우. → session이 만료됐거나 없다는 뜻.
2. session은 이미 만료된 상태고, Form인증을 받을 그 때 request Header에 `remember-me` cookie가 있는 경우.

아까 시나리오#2에서는 말이랑 사진으로만 간단하게 설명을 했다. 일단 저런 흐름임을 아는 것도 중요하기 때문이다. 한번 전체적으로 본 뒤에 자세히 보도록 하자.

<br>

## RememberMeAuthenticationFilter Flow

![security4_6](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_6.png?raw=True){: width="80%"}

<sub>[사진 출처 : 인프런 정수원 강사님 - 스프링 시큐리티](https://www.inflearn.com/course/%EC%BD%94%EC%96%B4-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)</sub>

- 상황 

  1. session이 만료돼서 없는 상태

  2. 이미 Form 인증 당시 `remember-me` check box에 체크를 하여 `remember-me`기능을 활성화 시켰었음.

     → `remember-me` cookie를 이미 갖고 있다는 뜻.

1. 먼저 위에서 설명했던대로 인증객체가 null이거나 `remember-me`쿠키가 있을 때 `RememberMeAuthenticationFilter`가 실행되게 된다.
2. `RememberMeServices` : interface이다. `TokenBasedRememberMeServices`, `PersistentTokenBasedRememberMeServices`와 같은 구현체가 실제로 `remember-me`인증처리를 진행한다.
   1. `TokenBasedRememberMeServices` : 메모리에 저장한 token과 사용자 요청으로 온 token(cookie)를 비교하여 인증을 진행한다.
   2. `PersistentTokenBasedRememberMeServices` : DB에 저장된 token과 사용자 요청 token을 비교하여 인증을 진행한다.
3. Token cookie를 추출. 왜? 써야하니깐;;
4. Token이 존재하는가? : 사용자가 가진 cookie의 이름이 `remember-me`와 같은지 확인. 없다면 다음 filter 실행.
5. 있다면, decode token. 아까 추출한 정상유무 판독을 위해서 token을 해독한다.
6. token이 서로 일치하는지, User 계정이 존재하는지 확인 후 모두 정상이라면 새로운 인증객체 생성.
7. 생성된 인증객체를 `AuthenticationManager`가 실질적인 인증을 처리.

결국 우리가 이러한 기능을 넣은 이유는

> session이 만료되어도 인증처리를 자동으로 하기 위해서 쓰는 것.

생각해보면 `remember-me`라는 기능은 간편하지 않은가? 만료가 되면 일일이 다시 로그인하는 것이 아닌, 자동으로 인증을 진행해주는게 더 편하다. 실제로 [SWEA](https://swexpertacademy.com/main/main.do) 사이트에서 문제를 풀다가 오래걸리면, 다시 로그인을 해줘야 했었다ㅜㅜ 여기에 `remember-me`기능을 넣는다면 확실히 더 편해질 것 같다.

자 이제 코드레벨에서 더 자세히 보도록 하자. flow의 어느 부분이 코드상에서는 어떻게 구현됐는지 보도록 할 것이다.

<br>

# Code Flow : remember-me 등록

먼저 'remember-me'기능을 활성화 하는 최초의 로그인 때 코드 흐름을 보도록 할 것이다. 그 다음으로 실제 `remember-me`기능이 어떤 식으로 동작하는지 살표보도록 하자.

- 상황 : `remember-me` checkbox를 활성화 한 뒤에 User계정으로 로그인을 하려는 상황. User계정의 Id, pw는 정확히 입력됐다고 하자!

<br>

## AbstractAuthenticationProcessingFilter

![security4_7](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_7.png?raw=True){: width="100%"}

[이전에 했던 포스팅](https://xi-jjun.github.io/2022-03-15/spring_security2)에서 form 인증을 하려고 할 때 동작하는 filter로 배웠던게 다시 나왔다!! 당연하기도 한게, 지금 우리는 User계정으로 로그인을 하려는 상황이기 때문이다. 아이디와 비밀번호를 정확히 입력했기에 `successfulAuthentication()` method가 호출되는 것을 볼 수 있다.

위 사진에서 `rememberMeService`의 `loginSuccess()`method가 호출되는 것을 볼 수 있다. 따라서 해당 method 안에 가서 분석을 하려고 했으나... `RememberMeServices` interface만 나올 뿐이었다... 아까 `Flow`에서 말한대로 interface임을 확인했으니 구현체를 보도록 하자.

<br>

## AbstractRememberMeServices

```java
public abstract class AbstractRememberMeServices implements RememberMeServices, InitializingBean, LogoutHandler, MessageSourceAware {
  ...
}
```

`RememberMeServices` interface를 구현하고 있음을 볼 수 있다. 이 class에서 `loginSuccess()` method를 보면,

![security4_8](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_8.png?raw=True){: width="100%"}

먼저 `remember-me`기능을 요청했는지 여부를 확인한다. 우리는 현재 기능을 활성화 했기에 바로 `onLoginSuccess()`method를 실행하게 된다. 근데 해당 method는 현재 `AbstractRememberMeServices` class에서 `abstract` method로 선언이 되었다... 

다시 `flow`그림을 떠올려보자. 생각해보니 `RememberServices` interface의 2가지 구현체가 실제 `remember-me`인증처리를 한다고 했었다. 그 중 `TokenBasedRememberMeServices` 구현체 class를 보도록 하자.

```java
public class TokenBasedRememberMeServices extends AbstractRememberMeServices 
```

`AbstractRememberMeServices` class를 상속받고 있는게 보인다. 잘 찾은 것 같다.

```java
@Override
public void onLoginSuccess(HttpServletRequest request, HttpServletResponse response,
    Authentication successfulAuthentication) {
  String username = retrieveUserName(successfulAuthentication);
  String password = retrievePassword(successfulAuthentication);

  ...
  int tokenLifetime = calculateLoginLifetime(request, successfulAuthentication);
  long expiryTime = System.currentTimeMillis();
  // SEC-949
  expiryTime += 1000L * ((tokenLifetime < 0) ? TWO_WEEKS_S : tokenLifetime);
  String signatureValue = makeTokenSignature(expiryTime, username, password);
  setCookie(new String[] { username, Long.toString(expiryTime), signatureValue }, tokenLifetime, request,
      response);
  ...
}
```

`onLoginSuccess()`method 내부이다. 중요한 코드들만 보도록 하자.

```java
String username = retrieveUserName(successfulAuthentication);
String password = retrievePassword(successfulAuthentication);
```

사용자 정보 얻고,

```java
setCookie(new String[] { username, Long.toString(expiryTime), signatureValue }, tokenLifetime, request, response);
```

cookie를 설정해주는 것을 볼 수 있다. 이 `setCookie()`method는 `AbstractRememberMeServices` class의 method이다.

```java
// AbstractRememberMeServices class
protected void setCookie(String[] tokens, int maxAge, HttpServletRequest request, HttpServletResponse response) {
  String cookieValue = encodeCookie(tokens);
  Cookie cookie = new Cookie(this.cookieName, cookieValue);
  ...
  response.addCookie(cookie);
}
```

마지막 `response.addCookie(cookie)`에 주목하자. 우리는 결국엔 만든 `remember-me` cookie를 사용자의 request에 대하여 response해줘야 한다. 따라서 응답에 `remember-me` cookie를 추가한 것이다.

이렇게 로그인은 성공하게 되고, 우리는 원하던 `remember-me` 기능을 활성화 시켰다! 

![security4_9](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_9.png?raw=True){: width="65%"}

Session과 `remember-me` 2개의 쿠키가 있는 것을 볼 수 있다.

<br>

# Code Flow : remember-me 기능 활성화 후 자동 로그인

이번에 할 것은 예고한대로 실제로 `remember-me`가 어떻게 동작하는지 자세히 볼 것이다.

- 상황 : 위 과정을 거친 후, session을 제거. 그 후 새로고침을 진행하여 `remember-me`기능이 어떻게 동작하는지 확인할 것

  ![security4_14](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_14.png?raw=True){: width="75%"}

  session을 지운 후 갖고 있는 cookie들이다!

<br>

## RememberMeAuthenticationFilter

자 `Flow`에서 설명한대로 `RememberMeAuthenticationFilter`에서 처리를 실행하게 된다. 해당 class의 `doFilter()`method를 보도록 하자. 이해를 돕기 위해서 `Flow`그림과 함께 볼 것이다.

![security4_10](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_10.png?raw=True){: width="100%"}

- #1 : `SecurityContext`에서 session에 해당하는 인증객체가 없기에 `null`값을 가지게 된다. 따라서 바로 #2로 이동
- #2 : `RememberMeServices`의 구현체인 `AbstractRememberMeServices`의 `autoLogin()`method를 호출. 사용자의 request에서 `remember-me`cookie가 있는지 확인하고 추출한 다음에 몇가지 검증을 거친 뒤에 인증객체를 생성하여 `rememberMeAuth`변수에 저장.
- #3 : `AuthenticationManager`에게 `rememberMeAuth`의 인증처리를 위임. 성공하게 되면 그 이후에 `SecurityContext`에 저장된다.

자 대략적으로 위와 같이 진행될 것임을 미리 알아봤다. 이제 각각을 더 자세히 보도록 하자.

<br>

## #2 autoLogin(request, response)

![security4_11](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_11.png?raw=True){: width="100%"}

아까 위에서 봤던 `#2`과정에 대한 method이다. 

1. `extractRememberMeCookie(request)` : request에서 `remember-me`cookie를 찾는다. 아까 쿠키에 `remember-me`가 있었기에 쿠키가 존재한다.

   ![security4_12](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_12.png?raw=True){: width="65%"}

   아까 위에서 있었던 `remember-me`cookie와 당연하게도 같은 값을 갖고 있는 것을 볼 수 있다.

   <br>

   ![security4_13](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security4_13.png?raw=True){: width="100%"}

   `remember-me`라는 이름을 가진 cookie가 있다면 해당 value를 return받는 것을 알 수 있다.

2. 검증을 진행한다. 

3. `Authentication`객체를 생성해준다.

<br>

이정도만 보면 대략적인 흐름이 어떤지 파악할 수 있다고 생각한다.

<br>

## Reference

[Inflearn Spring Security - 정수원 강사님](https://www.inflearn.com/course/%EC%BD%94%EC%96%B4-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)
