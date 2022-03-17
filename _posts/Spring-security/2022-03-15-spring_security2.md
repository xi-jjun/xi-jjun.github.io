---
layout: post
title: "Spring-Security 2 - Form Login"
author: "xi-jjun"
tags: Spring-security

---

# Spring-Security 공부 시작!

인프런 [정수원 강사님의 강의](https://www.inflearn.com/course/%EC%BD%94%EC%96%B4-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)를 보고 정리하는 글을 작성한 것이다.

<br>

# Form 인증

![security2_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_1.png?raw=True){: width="80%"}

<sub>[사진 출처 : 인프런 정수원 강사님 - 스프링 시큐리티](https://www.inflearn.com/course/%EC%BD%94%EC%96%B4-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)</sub>

가정 : `/home` url로의 접근은 인증된 사용자만이 가능하다고 정책이 설정되어 있다고 하자.

1. 사용자가 인증을 받지 않았기에 `/home`으로 접근하기 위해 GET 요청을 했지만 로그인 페이지로 redirect.
2. Login 화면 보여준다. (우리가 별도로 만들 수도 있다. 위 사진에서는 기본적으로 제공해주는 로그인 화면이다.)
3. username, password를 POST로 보낸다.
   1. 서버에서 `spring-security`가 세션을 생성한다.
   2. 세션에 인증결과에 대한 정보를 갖고있는 인증 객체(`Authentication type`)을 저장한다. 
   3. `SecurityContext` 객체를 생성한다.
   4. `SecurityContext` 를 세션에 저장한다.
4. 세션에 저장된 인증 토큰으로 `/home` url에 접근할 수 있게 된다.

<br>

## Form login 인증 API

```java
http.formLogin() : Form 로그인 인증 기능이 작동함
```

 <br>

```java
.loginPage("/loginPage")
```

인증이 안됐다면 사용자 정의 로그인 페이지 경로인 `/loginPage` 에 해당하는 URL로 이동하게 된다.

<br>

```java
.defaultSuccessUrl("/")
```

로그인에 성공한 후 이동하게 될 페이지 URL 경로를 정해줄 수 있다.

<br>

```java
.failureUrl("/login")
```

로그인에 실패하게 될 경우 이동하게 될 페이지 URL 경로를 정해줄 수 있다.

<br>

### UI 화면과 연관 있는 API

![security2_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_2.png?raw=True){: width="80%"}

```java
.usernameParameter("userId") // default : username
.passwordParameter("pw") // default : password
.loginProcessingUrl("/login_proc") // 로그인 form action URL. action="/login_proc"
```

id, password parameter명을 설정해줄 수 있다. 기본값은 username, password 이다. 로그인 form action url도 지정해줄 수 있다. 위 3가지는 UI화면에서 구성한 정보와 같아야 한다.

<br>

```java
.successHandler(loginSuccessHandler())
```

로그인 성공 후 핸들러

<br>

```java
.failureHandler(loginFailureHandler())
```

로그인 실패 후 핸들러

<br>

# UsernamePasswordAuthenticationFilter

이름이.. 엄청 길다..

![security2_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_3.png?raw=True){: width="90%"}

<sub>[사진 출처 : 인프런 정수원 강사님 - 스프링 시큐리티](https://www.inflearn.com/course/%EC%BD%94%EC%96%B4-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)</sub>

실제 로그인 처리를 하게 되면 인증 처리를 진행하게 되는데 그 인증처리를 해주는 filter가 `UsernamePasswordAuthenticationFilter`이다.

1. 사용자가 로그인 인증을 시도한다. 해당 요청을 Form인증이라면, Form인증을 처리해주는 `UsernamePasswordAuthenticationFilter`가 받게 된다.
2. `AntPathRequestMatcher(LOGIN_PATH)` 가 `LOGIN_PATH`로  해당하는 url이 시작하는지 검사한다.
   1. 만약에 `LOGIN_PATH`로 시작하지 않는다면 다른 filter에게 넘겨준다.
3. `Authentication` 객체를 만들어서 로그인 화면에서 사용자가 입력했던 정보를 해당 인증 객체에 저장한다.
4. `AuthenticationManager`에게 인증객체를 넘겨줘서 인증을 처리하도록 한다.
   1. `AuthenticationProvider`에게 인증처리를 위임한다. 따라서 실제로 인증처리를 담당하는 class이다.
   2. 인증에 실패하면, `AuthenticationException`발생하여 filter에게 처리를 맡긴다.
   3. 인증에 성공하게 되면,
      1. `Authentication` 인증 객체를 생성하게 된다.
      2. 우리가 받았던 인증을 했던 객체는 인증에 성공을 했으니, 해당 결과를 아까 만든 인증 객체에 저장한다.
      3. 만든 인증 객체를 `AuthenticationManager`에게 return.
5. Provider로 부터 받은 인증에 성공한 `Authentication` 객체를 Filter에게 return.
   - User : 최종적으로 인증에 성공한 사용자 객체.
   - Authorities : 해당 사용자에게 부여된 권한 정보.
6. Filter가 인증객체를 `SecurityContext`에 저장. 
   - `SecurityContext` : 인증 객체를 보관하는 저장소. session에도 저장된다. → 전역적으로 인증객체를 참조하기 위함.
7. `SuccessHandler`가 인증에 성공하면 그 다음 일을 처리해준다.

지금 과정을 말로 풀어서 설명했지만 전혀 와닿지 않을 수 있다. 따라서 break point를 잡고 코드를 직접 실행시키며 인증처리에 대한 흐름을 알아보도록 하자.

<br>

# Code 흐름

## 1. `/` 로 접속하려고 할 때

![security2_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_4.png?raw=True){: width="80%"}

`AbstractAuthenticationProcessingFilter` 가 해당 처리를 받는다. 

Q) ?? 아까 `UsernamePasswordAuthenticationFilter`가 처리한다면서요??

A) 그 클래스의 상위 클래스가 `AbstractAuthenticationProcessingFilter`이다...

<br>

![security2_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_5.png?raw=True){: width="80%"}

#1 에 해당하는 method를 보게 되면, 같은 클래스 내의 method임을 확인할 수 있다. 간단하게 말하자면 `AntPathRequestMatcher(/login)` 단계를 수행하고 있는 것이다. 그리고 Login_path는 바꿔줄 수 있었다!! 우리는 아까 아래와 같이 설정해줬었다.

```java
// SecurityConfig.java class

@Override
protected void configure(HttpSecurity http) throws Exception {
  http
      .formLogin() // 기본적으로 form-login 방식으로 인증을 할 수 있도록 api 를 설정.
      ...
      .loginProcessingUrl("/login_proc") // 로그인 form action URL. action="/login_proc"
      ...
}
```

우리는 위에서 Form Login API에 대해 공부하면서 form action URL을 `/login_proc`을 설정했었다. 따라서 위 사진에서 `request`의 url이 `/login_proc`으로 시작하지 않는다면, `requiresAuthentication()` method는 false를 return하게 된다.

자, 우리가 현재 무엇을 하였는가?

> `/`에 접근하려고 함.

따라서 `/login_proc`으로 시작하지 않는 URL이기 때문에 false를 return하게 된다.

<br>

#1 의 결과가 `false`이므로 if 조건을 만족하게 된다. 따라서 바로 밑의 `doFilter()` method가 실행되게 된다. 결과적으로 인증이 안된 사용자가 허가받지 않은 `/`에 접근하려고 했기에 login화면을 보여주게 된다.

<br>

### 정리1 !!

![security2_6](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_6.png?raw=True){: width="75%"}

현재 `AntPathRequestMatcher(/login_proc)` 에서 해당 URL과 일치하지 않았기에 `doFilter`로 가게 되는 모습을 볼 수 있었다. 

<br>

## 2. `/login` 에서 username, password 정보를 입력했을 때!

아까 1의 과정에서 로그인 화면을 받게 됐다. 그래서 로그인 화면에 우리가 설정했던 `user`, `123`이라는 아이디와 비밀번호를 입력했을 때!! 생기는 일에 대한 흐름을 보도록 하자.

![security2_7](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_7.png?raw=True){: width="85%"}

- #1 : 아까 위에서와 같은 break point이다. 하지만 이번에는 다른 결과가 나온다. 왜냐하면 로그인 화면에서 login form `action='/login_proc'`이기 때문에 `AntPathRequestMatcher(/login_proc)`에서 일치하기에 #1 method는 true를 return하기 때문이다.

  - 여기서 드는 의문. 아니 진짜 같은거 맞나...? 증거 가져와봐라!!

    - 우리가 설정한 로그인 인증을 처리하는 URL은 `/login_proc`이다.

      ![security2_10](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_10.png?raw=True){: width="85%"}

    - `/`로 접근할 때

      ![security2_8](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_8.png?raw=True){: width="85%"}

      오른쪽 밑에 흰색 박스를 보면, 경로가 `/`임을 알 수 있다. 로그인 인증을 처리하는 경로와 달랐기 때문에 해당 method는 `false`를 return한 것.

    - `/login_proc`으로 접근할 때

      ![security2_9](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_9.png?raw=True){: width="85%"}

      오른쪽 밑에 흰색 박스를 보면, 경로가 `/login_proc`임을 알 수 있다. 로그인 인증을 처리하는 경로와 같기에 해당 method는 `true`를 return한 것.

    parameter로 들어오는 `request` 객체 안을 뜯어서 해당 정보를 찾은 것이다.

- #2 : 아까 맨 처음에 흐름에 대해서 보고 시작했다. 알맞은 로그인 경로라면 인증객체를 만든다고 하였는데 지금 코드가 인증객체를 만드는 코드이다. 해당 `attemptAuthentication()` method를 보게 되면, 현재 `AbstractAuthenticationProcessingFilter` class의 abstract method이다. 따라서 이제 진짜 `UserPasswordAuthenticationFilter` 객체를 봐주는 것이다.

  ![security2_11](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_11.png?raw=True){: width="85%"}

  - #2-1 : 지금 보면, username, password를 request 객체로부터 받아와서 인증객체에 저장하는 모습을 볼 수 있다. 연두색 박스 끝을 보면, 우리가 설정했던 id, pw인 `user`, `123`을 볼 수 있다.
  - #2-2 : 인증객체를 만들었으면 filter는 `AuthenticationManager`에게 인증처리를 맡긴다고 했다. 따라서 생성한 인증객체를 반환하고 있는 것을 볼 수 있다.

<br>

## 3. 반환된 인증객체의 실질적인 처리는 AuthenticationProvider가 해준다!

위에서 전체 흐름에 대해 정리할 때, 인증객체에 대한 실질적인 인증 처리는 `AuthenticationManager`가 `AuthenticationProvider`에게 위임한다고 했다. 아까 2번의 `#2-2`과정을 자세히 보도록 하자.

![security2_12](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_12.png?raw=True){: width="80%"}

`ProviderManager`는 `AuthenticationProvider`interface의 구현체이고, 내부적으로 `Provider` 객체들을 보관하고 있다. 그래서 위 코드의 초록색 줄은 `Provider`들 중에서 현재 form 인증 방식을 처리할 provider를 찾는 것이다.

<br>

![security2_13](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_13.png?raw=True){: width="80%"}

알맞은 provider를 찾으면 위 사진에 표시된 초록색 줄로 가게 된다. 173번 line을 보면, `DaoAuthenticationProvider`인 것 같다. 이 provider가 parameter로 받은 인증 객체를 보고 성공 또는 실패를 판단하게 된다.

<br>

...? `DaoAuthenticationProvider`를 봤더니 authenticate method가 없었다. 그렇다는 뜻은 상속을 받았다는 뜻이기에 부모 클래스를 봤더니,

![security2_14](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_14.png?raw=True){: width="75%"}

`AbstractUserDetailsAuthenticationProvider` class임을 알게 됐다. 따라서 해당 클래스에서 authenticate method를 찾았다.

<br>

![security2_15](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_15.png?raw=True){: width="80%"}

authenticate method 안이다. 실질적인 인증처리를 하고 성공을 했다면 `createSuccessAuthentication()` method를 호출하게 되고,

![security2_16](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_16.png?raw=True){: width="80%"}

호출된 method는 *인증에 성공한 결과*를 return하게 됨을 볼 수 있다.

<br>

## 4. 인증에 성공한 객체는 SecurityContext에 저장!

![security2_17](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_17.png?raw=True){: width="85%"}

위 사진의 노란색, 빨간색 박스는 이해를 돕기 위해 1~3과정을 우리가 처음에 정리했던 흐름에 맞춰서 사진을 삽입해놓은 것이다. 

1~3까지의 과정동안 *'인증에 성공한 객체'* 로써 `authenticationResult` 인증 객체가 생성됐다. 이제 해당 객체를 인증 객체 저장소인 security context에 저장하는지 확인해볼 것이다. `#1`과정을 더 자세히 보도록 하자.

<br>

![security2_18](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_18.png?raw=True){: width="85%"}

같은 class `AbstractAuthenticationProcessingFilter`안의 method인 `successfulAuthentication()` method이다.

- #1-1 : `SecurityContext` 를 생성하여 인증에 성공한 인증 객체를 저장한다. 그리고 `SecurityHolder`에 해당 context를 저장하고 있다.

  - Security holder는 뭐지...?

    > This is where we store details of the present security context of the application, which includes details of the principal currently using the application. - [출처](https://docs.spring.io/spring-security/site/docs/5.1.5.RELEASE/reference/htmlsingle/#hello-web-security-java-configuration)

    application의 `security context`를 저장하고 있는 저장소라고 생각하면 될 것 같다.

- #1-2 : security context에 저장하고나서 `SuccessHandler`에게 인증 성공 후 처리를 맡긴다.

<br>

## 5. 아까 흐름과 같이 보면서 정리!!!

![security2_19](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_19.png?raw=True){: width="80%"}

<br>

![security2_20](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2_20.png?raw=True){: width="80%"}

<br>

이번에 흐름과 함께 코드를 보면서 정리하려고 하니 쉽지가 않음을 깨달았다... 



## Reference

[Inflearn Spring Security - 정수원 강사님](https://www.inflearn.com/course/%EC%BD%94%EC%96%B4-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)

[Spring Security docs](https://docs.spring.io/spring-security/site/docs/5.1.5.RELEASE/reference/htmlsingle/#hello-web-security-java-configuration)

