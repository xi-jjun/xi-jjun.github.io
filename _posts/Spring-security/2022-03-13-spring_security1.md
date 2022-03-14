---
layout: post
title: "Spring-Security 1"
author: "xi-jjun"
tags: Spring-security

---

# Spring-Security 공부 시작!

인프런 [정수원 강사님의 강의](https://www.inflearn.com/course/%EC%BD%94%EC%96%B4-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)를 보고 정리하는 글을 작성한 것이다.

<br>

# 왜? 굳이? 그냥 if else 로 하면 안될려나?

라는 생각이 가장 먼저 들었다. 사실 `spring-security` 에 대해 아는 것이 하나도 없을 때 SSAFY 7기 광주2반을 위한 *[질문 게시판](https://github.com/xi-jjun/question-board)* 을 만든 적이 있었다. 그 때는 session 이란 것도 처음 알았을 때라... 그저 아이디, 비밀번호를 DB에 넣고 일치하면 로그인, 불일치하면 로그인이 안되게 하는게 끝이었다. 그리고 권한 없는 URL에 대한 접근은 `controller` 의 `GetMapping` method에서 session이 유지되고 있는지 여부에 따라 허용해줬다.

> 이렇게 하는게 맞는건가...?

라는 생각을 머리 속에서 지울 수가 없었다... 그러던 와중에 spring-security에 대해 알게 됐고, 공부를 시작하게 됐다.

그렇다면 왜 쓸까? 생각보다 많은 블로그 글을 봤지만, 글의 서두를 'spring-security란...' 으로 시작했다. "왜" 쓰는지 알고 싶어서 좀 더 찾아보니 다음과 같은 [블로그](https://sieunlim.tistory.com/19) 를 발견했다. 해당 블로그에서 걸어놓은 [공식 문서 링크](https://docs.spring.io/spring-security/site/docs/5.1.5.RELEASE/reference/htmlsingle/#hello-web-security-java-configuration) 를 보도록 하자.

<br>

## Spring-Security 를 쓰는 이유

1. application의 모든 URL에 대해 인증을 하게 하도록 한다.
2. Login form을 제공해준다.
3. Username, password를 가진 사용자가 form 기반 인증을 하도록 해준다.
4. Logout 을 하게 해준다.
5. CSRF 공격을 예방해준다.
6. Session Fixation 보호.
7. Security Header integration
   - [HTTP Strict Transport Security](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security) for secure requests
   - [X-Content-Type-Options](https://msdn.microsoft.com/en-us/library/ie/gg622941(v=vs.85).aspx) integration
   - Cache Control (can be overridden later by your application to allow caching of your static resources)
   - [X-XSS-Protection](https://msdn.microsoft.com/en-us/library/dd565647(v=vs.85).aspx) integration
   - X-Frame-Options integration to help prevent [Clickjacking](https://en.wikipedia.org/wiki/Clickjacking)
8. Integrate with the following Servlet API methods
   - [HttpServletRequest#getRemoteUser()](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#getRemoteUser())
   - [HttpServletRequest.html#getUserPrincipal()](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#getUserPrincipal())
   - [HttpServletRequest.html#isUserInRole(java.lang.String)](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#isUserInRole(java.lang.String))
   - [HttpServletRequest.html#login(java.lang.String, java.lang.String)](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#login(java.lang.String, java.lang.String))
   - [HttpServletRequest.html#logout()](https://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpServletRequest.html#logout())

공식 문서의 

> There really isn’t much to this configuration, but it does a lot. 

라는 말에 동의를 하게 됐다.. 해주는게 정말 많아 보인다. `Spring-Security` 없이 웹 개발을 하게 된다면 위에 해당하는 것들을 하나하나 구현해야 한다는 것이고, 이는 반복적인 노동일 뿐이다. 따라서 보안에 관련된 이러한 부분들을 모아서 framework로 만들어서 쓰는 것이다. 우리는 그저 만들어진 framework를 쓰기만 함으로써 비지니스 로직에 더 집중을 할 수 있다.

<br>

# Spring-Security 동작 원리

## 용어 정리

먼저 용어에 대한 정리가 먼저일 것 같다. 

> Spring Security is a framework that provides [authentication](https://docs.spring.io/spring-security/reference/features/authentication/index.html), [authorization](https://docs.spring.io/spring-security/reference/features/authorization/index.html), and [protection against common attacks](https://docs.spring.io/spring-security/reference/features/exploits/index.html). - [출처](https://docs.spring.io/spring-security/reference/)

위 문장은 spring-security에 대해 한 문장으로 설명해 놓은 글이다. 저기서 가장 중요한 2가지 단어에 대해 살펴보자.

- Authentication : 인증. 해당 사용자가 맞는지 확인하는 절차.
- Authorization : 인가. 해당 사용자가 접근할 수 있는 권한이 맞는지 확인하는 절차.

따라서 *인증*이 진행된 후, *인가*가 진행된다. 나머지 동작의 흐름에 대해서는 뒤에가서 자세히 다루도록 하겠다.

<br>

# Authentication API

Spring boot 는 Main class를 실행하게 되면, Tomcat WAS가 실행된다. 여기서 `spring-security`에 대한 의존성을 추가해주면, 바로 보안이 적용된다.

## Example #1 - Spring security : X

1. `"/"` root path로 GET request
2. path에 해당하는 html 파일을 랜더링 해주게 된다.

<br>

## Example #2 - Spring security : O





1. `"/"` root path로 GET request
2. spring security관련 설정을 해주지 않은 상태이므로 `"/login"` path로 가게된다.
3. spring-security에서 기본으로 제공하는 로그인 화면이 뜬다.
4. ID : `user`, PW :  `랜덤한 문자열` 로 기본 계정을 제공해준다. 랜덤한 문자열은 spring이 실행되면서 자동으로 설정해준다.

<br>

## 의존성을 추가했을 뿐인데... 어떤 일이 발생한 것일까?

`spring-security`의존성을 추가하게 되면 어떤 일이 일어나는지 대략적으로 살펴보자

1. WAS 서버가 기동되면 spring-security의 초기화 작업 및 보안 설정이 자동으로 이뤄진다.
2. 따라서 별도의 설정이나 구현을 안해도 기본적인 웹 보안 기능이 현재 시스템에 연동되어 작동한다.
   1. 모든 요청은 인증되어야 접근 가능. → 위 `example #2`에서 로그인 페이지로 이동한 이유
   2. 인증 방식은 `form-login`, `http basic-login` 2가지 방식을 제공한다.
   3. 기본 로그인 화면과 계정을 제공해준다.

너무 대략적..일 수 있지만 일단 여기까지만 아는 것으로 충분하다. 뒤로 갈수록 더 깊게 살펴볼 것이기 때문이다.

근데 이렇게만 되면 된게 아닌가...? 라는 생각이 들 수 있다... 아니 사실 딱 봐도 저 위의 기능만으로 부족함이 커보인다. 뭐가 문제냐면,

- 계정이 하나밖에 없다. 추가를 해줘야 한다.
- 계정에 권한을 추가할 수 있어야 한다. 그리고 DB연동도 돼야 한다.
- 권한에 따른 접근과 같이 시스템에서 필요로 하는 세부적이고 추가적인 보안 기능 필요.
  - 로그인을 안하더라도 접근할 수 있게 하고 싶을 수도 있다.

→ 문제점을 몇개 보면, 현재 단순하고 최소한의 보안 시스템만 존재하고 있다는 사실을 느낄 수 있다.

<br>

## User-defined 보안 기능 구현

![security1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security1.png?raw=True){: width="65%"}

<sub>[사진 출처 : 인프런 정수원 강사님 - 스프링 시큐리티](https://www.inflearn.com/course/%EC%BD%94%EC%96%B4-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)</sub>

아까 우리가 spring-security가 대략적으로 어떻게 동작하는지 알아봤을 때 *'초기화 작업 및 보안 설정이 자동으로 이뤄진다.'*라는 말을 했었다. 그 역할을 해주는 class가 `WebSecurityConfigurerAdapter`이다. 

Spring이 실행될 때 강의에서 break point를 잡은 것처럼 똑같이 포인트를 잡아서 디버깅으로 돌려서 확인했다. 3군데를 잡았는데 시간 순서대로 나열해보겠다. (spring만 실행시키고 아무 것도 안함. 현재 목적은 초기화를 하는지 확인하는 것, 그리고 사용자 정의 설정을 어떻게 하는지 파악하기 위함)

```java
// WebSecurityConfigurerAdapter.java class 
...
protected final HttpSecurity getHttp() throws Exception {
  ...
  this.http = new HttpSecurity(this.objectPostProcessor, this.authenticationBuilder, sharedObjects);
  if (!this.disableDefaults) {
	  applyDefaultConfiguration(this.http); // 초기화 설정. 강의에서 중요하게 본 포인트1
	  ClassLoader classLoader = this.context.getClassLoader();
	  List<AbstractHttpConfigurer> defaultHttpConfigurers = SpringFactoriesLoader
		    .loadFactories(AbstractHttpConfigurer.class, classLoader);
	  for (AbstractHttpConfigurer configurer : defaultHttpConfigurers) {
	    this.http.apply(configurer);
	  }
  }
  configure(this.http); // 강의에서 중요하게 본 포인트2
  return this.http;
}
...
```

<br>

위 코드에서 초기화를 해주는 method를 찾으면 아래와 같다. `http`객체는 위에서 만들었으니, 해당 객체에 대한 초기화 설정을 각각의 api를 호출하여 해주는 것.

```java
// WebSecurityConfigurerAdapter.java class
// 초기화 설정 해주는 method
...
private void applyDefaultConfiguration(HttpSecurity http) throws Exception {
  http.csrf();
  http.addFilter(new WebAsyncManagerIntegrationFilter());
  http.exceptionHandling(); // 예외를 처리해주는 클래스를 불러주는 api
  http.headers();
  http.sessionManagement();
  http.securityContext();
  http.requestCache();
  http.anonymous();
  http.servletApi();
  http.apply(new DefaultLoginPageConfigurer<>());
  http.logout();
}
...
```

<br>

추가적인 설정을 해주는 코드를 아래에서 볼 수 있다. 아래 method는 `getHttp()` method에서 호출된 것이다. 아래 코드에서 해준 설정 때문에 우리가 `"/"` root 경로로 접속을 했을 때 로그인 화면이 뜬 것이다.

> *어떠한 요청*에도 *보안검사*를 하겠다고 설정되어 있는 코드이기 때문이다.

```java
// WebSecurityConfigurerAdapter.java class
// configure(http) method
...
protected void configure(HttpSecurity http) throws Exception {
  ...
  http.authorizeRequests((requests) -> requests.anyRequest().authenticated()); // 현재 사용자가 http 로 요청할 때 보안 검사를 하겠다는 것. → requests.anyRequest()[어떠한 요청] 에 대해 authenticated()[보안 검사]를 하겠다는 뜻.
  http.formLogin(); // 인증을 안받았다면 form-login 과,
  http.httpBasic(); // http basic-login 2가지 방식을 제공하겠다라는 뜻
}
...
```

이 method는 보안에 관한 *추가적인 설정*을 해주고 있다. 따라서 우리는 이 method를 overriding해서 `user-defined 보안 설정`을 할 수 있다.

<br>

자 우리의 목적이었던

> 초기화를 어떻게, 진짜 하는가? - O
>
> 사용자 정의 함수를 어떻게 구현하는가? - `configure()` method를 `override`해서 구현

2가지 물음에 대한 답을 얻었다.

<br>

## 이제 진짜 사용자 정의 보안 설정 class를 만들어 보자.

먼저 해야할 것은 `"/"` path로의 경로설정이다. 접근했을 때 뭐라도 있어야 하지 않겠는가.

```java
package io.security.basicsecurity;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class SecurityController {
	/**
	 * 정상적으로 서버에 접근할 수 있지만 '누구라도' 접근이 가능하다.
	 * => 보완에 취약한 구조.
	 * 현재 시스템을 보완이 적용된 시스템으로 작업을 할 것이다. => SecurityConfig 로 사용자 정의 보안 class 생성
	 */
	@GetMapping("/")
	public String home() {
		return "home";
	}
}
```

위와 같이 구현했다.

이제 진짜진짜진짜 사용자 정의 보안 설정 class를 만들어보자.

```java
package io.security.basicsecurity;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

// 이 annotation들을 명시해줘야 웹 보안이 활성화된다.
@Configuration
@EnableWebSecurity 
public class SecurityConfig extends WebSecurityConfigurerAdapter {
	@Override
	protected void configure(HttpSecurity http) throws Exception {
		/**
		 * 인가 정책. 인가 API
		 */
		http
				.authorizeRequests()
				.anyRequest().authenticated(); // 어떠한 요청에도.인증을 받아라  라는 뜻의 코드

		/**
		 * 인증 정책. 인증 API
		 */
		http
				.formLogin();
	}
}
```

![security2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/Spring-security/img/security2.png?raw=True){: width="65%"}

위 사진에서 초록색 줄이 내가 걸었던 break-point이다. 디버깅 툴로 실행한 결과, 아까는 기본적으로 제공되는 configure() method가 실행됐지만, 이제 우리가 정의한 클래스의 configure() method가 선언된 것을 확인할 수 있다.

<br>

### 해놓으면 편한 설정

그리고 현재 랜덤한 문자열의 비밀번호를 계속 복사 붙여넣기 해줘야 하는 번거로움이 있기에 `application.properties` file에서 기본 계정에 대한 정보를 fix해보자.

```properties
spring.security.user.name=user
spring.security.user.password=123
```

난 간편한게 가장 좋기에 ID : `user`, PW : `123`으로 설정했다. 이렇게 설정하면 더 이상 랜덤한 문자열의 비밀번호가 생성되지 않는다.

 <br>

이제 `SecurityConfig` class에 우리가 원하는 보안 설정을 추가하면서 api에 대해 공부해보도록 하자.

<br>



## Reference

[Inflearn Spring Security - 정수원 강사님](https://www.inflearn.com/course/%EC%BD%94%EC%96%B4-%EC%8A%A4%ED%94%84%EB%A7%81-%EC%8B%9C%ED%81%90%EB%A6%AC%ED%8B%B0/dashboard)

[Spring Security reference](https://docs.spring.io/spring-security/reference/)

[Spring Security 써야 하는 이유](https://sieunlim.tistory.com/19)

[Spring Security docs](https://docs.spring.io/spring-security/site/docs/5.1.5.RELEASE/reference/htmlsingle/#hello-web-security-java-configuration)

[Authentication, Authorization](https://mangkyu.tistory.com/76)
