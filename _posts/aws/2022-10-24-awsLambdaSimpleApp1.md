---
layout: post
title: "AWS Lambda 실습 : Simple Java application"
author: "xi-jjun"
tags: aws

---

# AWS Lambda 실습 : Simple Java application

나는 몇달 전에 만들었던 '[백발백준](https://github.com/xi-jjun/baekjoon-recommender)'프로젝트를 aws lambda를 사용하여 serverless 아키텍처로 배포해보려 한다. 그 전에 사용법에 대해 익히기 위해서 간단한 주문 어플리케이션을 만들어볼 것이다.

<br>

## 요구사항

```text
1. item을 추가할 수 있어야 한다.
2. 현재 DB에 저장된 item 목록을 조회할 수 있어야 한다.
3. item의 '이름(name)'으로 조회할 수 있어야 한다.
```

다음과 같은 3가지 요구사항을 만족하는 어플리케이션을 만들어보도록 하자. DB에 연결하기 전에 각각의 function이 잘 동작하는지 확인할 것이다.

<br>

## Environment

```text
== AWS ==
aws lambda java core
aws lambda java events
aws lambda java log4j2

== Spring ==
spring boot starter
spring boot starter test
spring cloud starter function web // for local testing
spring cloud function adapter aws 

== Java ==
lombok
Java 8
```

<br>

# 모르는 것 정리

일단 처음 접하는 것이기에 모르는 것 투성이다. 내가 하고 싶은 것은

1. Restful API로 개발하고 싶다
   - HTTP Method로 통신할 것이다. → Get, Post 등등의 요청에 대한 구별이 필요
2. DB와 연동하여 영구적인 데이터에 접근하고 싶다

그리고 내가 모르는 것은

1. python으로 lambda application을 만들었을 때에는 `def lambda_handler(event, context)`와 같이 비교적 직관적인 위치에 핸들러 함수가 존재했다. 하지만 Java는 package구조가 존재하기에 어디에 핸들러 함수를 만들어야 하는지 몰랐다.
   - 핸들러 함수의 parameter는 어떤 타입인지 궁금했다.
   - 각 parameter를 직접 parsing해서 사용해야하는 것인지 궁금했다.
2. Spring boot로 어플리케이션을 만들건데 외부 라이브러리를 어떻게 가져올지 고민이다.
   - python 으로 할 때에는 `lambda layer`로 해결. Java도 똑같이 하면 되는지 궁금했다.
3. packge 구성을 어떻게 해야할지 몰랐다.
   - 어떤 의존성이 필요한지

이제 모르는 것들을 하나씩 찾아보자.

<br>

## handler function

Handler function이란 무엇일까?

> Lambda 함수의 *핸들러*는 이벤트를 처리하는 함수 코드의 메서드입니다. 함수가 호출되면 Lambda는 핸들러 메서드를 실행합니다. 핸들러가 존재하거나 응답을 반환할 때, 또 다른 이벤트를 처리하기 위해 사용할 수 있게 됩니다.

라고 aws docs에서 말하고 있다. Java로 lambda에서 발생한 요청을 처리하기 위해서 handler function을 정의해야 하는데 다행히도 [예제 코드](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/java-handler.html#java-handler-samples)를 제공했기에 수월했다.

```java
// Handler.java
package example;

import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.RequestHandler;
import com.amazonaws.services.lambda.runtime.LambdaLogger;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

import java.util.Map;

// Handler value: example.Handler
public class Handler implements RequestHandler<Map<String,String>, String> {
  Gson gson = new GsonBuilder().setPrettyPrinting().create();
  
  @Override
  public String handleRequest(Map<String,String> event, Context context) {
    LambdaLogger logger = context.getLogger();
    String response = "200 OK";
    // log execution details
    logger.log("ENVIRONMENT VARIABLES: " + gson.toJson(System.getenv()));
    logger.log("CONTEXT: " + gson.toJson(context));
    // process event
    logger.log("EVENT: " + gson.toJson(event));
    logger.log("EVENT TYPE: " + event.getClass());
    return response;
  }
}
```

aws에서 제공하는 코드들 중 하나를 가져왔다([Code](https://github.com/awsdocs/aws-lambda-developer-guide/blob/main/sample-apps/java-basic/src/main/java/example/Handler.java)).

<br>

위와 같이 예제가 있었지만 좀 더 쉬운 이해를 위해서 다음과 같은 [유투브 영상](https://www.youtube.com/watch?v=6A9pqr4qQMo)을 참고하였다. 이제 aws lambda로 간단한 문자열 뒤집기 함수를 만들어 보도록 하겠다.

<br>

# Java : 문자열 뒤집기

쉽게 만들기 위해서 `spring cloud`를 사용하여 구현했다.

```groovy
dependencies {
	// Handler interface 제공
	implementation group: 'com.amazonaws', name: 'aws-lambda-java-core', version: '1.2.1'
	// AWS Lambda 이벤트를 다루기 위한 의존성
	implementation group: 'com.amazonaws', name: 'aws-lambda-java-events', version: '3.9.0'
	// AWS Lambda Logging
	runtimeOnly 'com.amazonaws:aws-lambda-java-log4j2:1.5.0'
	...
	// spring cloud
	implementation 'org.springframework.cloud:spring-cloud-starter-function-web'
	implementation group: 'org.springframework.cloud', name: 'spring-cloud-function-adapter-aws', version: '3.2.7'

	...
}
```

<br>

![awslambdasimpleapp1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_1.png?raw=True){: height="60%"}

package구성은 위와 같이 했다. Main class가 있는 위치에 아래와 같은 `Handler` class를 만들어준다.

```java
@Component
public class Handler {
	@Bean
	public Function<String, String> reverse() {
		return (inputString) -> new StringBuilder(inputString).reverse().toString();
	}
}
```

와 같이 Handler class 안에 우리가 동작시킬 기능을 `@Bean`으로 등록해준다.

<br>

## Spring Cloud Function이란(간단하게)

- `Function`을 통한 비지니스 로직 구현
- 동일한 코드가 웹 엔드포인트, 스트림 프로세서 또는 작업으로 실행될 수 있도록 비즈니스 로직의 개발 라이프사이클을 특정 런타임 대상에서 분리합니다.
- 서버리스 공급자 간에 동일한 프로그래밍 모델을 지원하고 독립 실행형(local 또는 PaaS) 실행 기능을 지원합니다.
- 서버리스 공급자에서 spring boot 기능(auto-configuration, DI, metric)을 사용하도록 설정합니다.

그리고 다음과 같은 특징을 가졌다.

> *Adapters for [AWS Lambda](https://github.com/spring-cloud/spring-cloud-function/tree/master/spring-cloud-function-adapters/spring-cloud-function-adapter-aws), [Microsoft Azure](https://github.com/spring-cloud/spring-cloud-function/tree/master/spring-cloud-function-adapters/spring-cloud-function-adapter-azure), [Apache OpenWhisk](https://github.com/spring-cloud/spring-cloud-function/tree/master/spring-cloud-function-adapters/spring-cloud-function-adapter-openwhisk) and possibly other "serverless" service providers.*

aws lambda와 그 외 serverless provider adapter이기에 [영상](https://www.youtube.com/watch?v=6A9pqr4qQMo)에서도 사용한 것 같다.

<br>

## Local test

spring cloud를 local환경에서 테스트하기 위해서 

```groovy
implementation 'org.springframework.cloud:spring-cloud-starter-function-web'
```

의존성을 추가해줬다. 

이제 다음과 같은 명령어를 terminal에 입력하여 문자열이 뒤집어서 나오는지 확인해보자.

```shell
curl -H "Content-Type: text/plain" localhost:8080/reverse -d Hello
```

## Error 발생

```console
Execution failed for task ':compileJava'.
> Could not resolve all files for configuration ':compileClasspath'.
   > Could not find org.springframework.cloud:spring-cloud-starter-function-web:.
     Required by:
         project :

Possible solution:
 - Declare repository providing the artifact, see the documentation at https://docs.gradle.org/current/userguide/declaring_repositories.html
```

위와 같은 에러가 발생했다. 의존성은 알맞게 넣어준 것 같은데 [stackoverflow](https://stackoverflow.com/questions/55077143/failure-to-find-org-springframework-cloudspring-cloud-dependenciespom)에서는 `spring cloud version`을 명시하라고 했다. 정확하게 짚고 넘어가기 위해서 [공식문서](https://spring.io/projects/spring-cloud/)에 갔다...

![awslambdasimpleapp1_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_2.png?raw=True){: height="60%"}

> If you an existing Spring Boot app you want to add Spring Cloud to that  app, the first step is to determine the version of Spring Cloud you  should use. The version you use in your app will depend on the version of Spring  Boot you are using.

`Spring cloud`의존성을 추가하기 위해서 현재 나의 `Spring Boot` version에 따라 다르게 추가해줬어야 했다.

### 현재 나의 spring boot version 

```groovy
plugins {
	id 'org.springframework.boot' version '2.7.5'
	id 'io.spring.dependency-management' version '1.0.15.RELEASE'
	id 'java'
}
...
```

따라서 `Jubilee`를 받아야 함을 알게 됐다. 그리고 아래에 어떤 것을 추가해야하는지 다 나와있었기에 복붙으로 해결했다.

```groovy
// example build.gradle
buildscript {
  dependencies {
    classpath "io.spring.gradle:dependency-management-plugin:1.0.10.RELEASE"
  }
}

ext {
  set('springCloudVersion', "Hoxton.SR8")
}


apply plugin: "io.spring.dependency-management"

dependencyManagement {
  imports {
    mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
  }
}
```

나는 현재 spring cloud 'Jubilee'버전을 받아와야 하기에

```groovy
ext {
	set('springCloudVersion', "2021.0.4")
}
```

이렇게 바꿔줬다.

<br>

## RE: Local Test

```shell
curl -H "Content-Type: text/plain" localhost:8080/reverse -d Hello
```

![awslambdasimpleapp1_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_3.png?raw=True){: height="60%"}

문자열이 `olleH`로 잘 뒤집어진 것을 확인할 수 있었다.

<br>

# AWS Lambda에 배포하기

## Create AWS Lambda Function

![awslambdasimpleapp1_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_4.png?raw=True){: height="60%"}

aws 에 로그인을 한 뒤에 위 사진에서 오른쪽 위 `create function` 버튼을 클릭한다.

<br>

![awslambdasimpleapp1_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_5.png?raw=True){: height="60%"}

function name은 기능에 알맞게 지어줬다. 현재 `StringReverse-Function`프로젝트는 `Java 8`이기에 runtime환경을 java8 on aws linux2 로 해줬다.

`IAM` role같은 경우는 실제 백발백준 프로젝트라면 직접 `cloudwatch`와 같은 서비스에도 접근 권한을 줬을 것이다. 그러나 현재는 연습이기 때문에 lambda생성시 자동으로 생성되는

![awslambdasimpleapp1_6](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_6.png?raw=True){: height="70%"} 

`AWSLambdaBasicExecutionRole`권한을 줬음을 볼 수 있다.

<br>

![awslambdasimpleapp1_7](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_7.png?raw=True){: height="70%"}

오른쪽에 `Upload from` 을 누르면 jar 또는 zip file로 lambda에 배포할 수 있다. 

<br>

## Test Lambda function

![awslambdasimpleapp1_8](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_8.png?raw=True){: height="70%"}

Aws lambda에서는 테스트를 진행할 수 있는데 보다시피 에러가 발생했다. 자세히 보도록 하자.

<br>

## Error : class not found

```console
{
  "errorMessage": "Class not found: example.Hello",
  "errorType": "java.lang.ClassNotFoundException"
}
```

다음과 같은 오류가 나왔는데 이것은 아까 jar file을 업로드할 때 Handler class의 위치를 내가 만든 package위치로 안했기 때문에 발생한 오류이다. 

내가 작성한 `Handler.java`는 `me.practice.stringreverseapi`에 있음을 알 수 있다.

```java
package me.practice.stringreverseapi;

import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

import java.util.function.Function;

@Component
public class Handler {
	@Bean
	public Function<String, String> reverse() {
		return (inputString) -> new StringBuilder(inputString).reverse().toString();
	}
}
```

![awslambdasimpleapp1_9](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_9.png?raw=True){: height="100%"}

`me.practice.stringreverseapi.Handler::reverse`로 바꿔줬다.

<br>

## RE: Test Lambda function

## RE: Error : class not found

```console
{
  "errorMessage": "Class not found: me.practice.stringreverseapi.Handler",
  "errorType": "java.lang.ClassNotFoundException"
}
```

이게 어떻게된 것일까...? 이 부분에 대해서 몇시간의 삽질 끝에.... 결국엔 또 [공식문서](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/java-package.html)로 회귀했다. 

![awslambdasimpleapp1_10](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_10.png?raw=True){: height="70%"}

음... 한글이라 약간 헷갈려서 잠시 영어로 바꾼 뒤 해석해보자.

> To create a deployment package with your function's code and dependencies, use the `Zip` build      type.

그러니깐 내 lambda function code(`Handler.java`)와 dependencies를 담음 package를 배포하고 싶다면 `zip` build type을 사용하라고 했다. 이거를...못봐서 2시간을 삽질 했다는게 믿기지가 않는다.

```groovy
task buildZip(type: Zip) {
    from compileJava
    from processResources
    into('lib') {
        from configurations.runtimeClasspath
    }
}
```

문서에서 제공해준 스크립트를 `build.gradle`에 추가하였다. 그리고 다음과 같은 명령어로 zip file build를 해줬다.

```shell
./gradlew buildZip
```

![awslambdasimpleapp1_11](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_11.png?raw=True){: height="50%"}

> This build configuration produces a deployment package in the `build/distributions` directory.

`build/distributions` directory에 build결과물이 생성된다고 한다. 

![awslambdasimpleapp1_12](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_12.png?raw=True){: height="50%"}

제발 잘 되길 바라면서...

<br>

## Uploading a deployment package with the Lambda API

`update-function-code` command를 사용해서 aws cli로 zip file을 lambda function에 배포할 수 있는 코드가 있길래 사용해봤다.

```shell
aws lambda update-function-code --function-name MY_FUNCTION_NAME --zip-file fileb://MY_BUILD_ZIP_FILE.zip
```

형식으로 쓰면 된다. 클릭하기 귀찮았는데 편하게 배포할 수 있을 것 같다.

<br>

## RE:RE: Test Lambda function

![awslambdasimpleapp1_13](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_13.png?raw=True){: height="70%"}

됐...?

![awslambdasimpleapp1_14](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_14.png?raw=True){: height="70%"}

?? `olleH` 가 나와야 하는데...?

![awslambdasimpleapp1_15](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_15.png?raw=True){: height="70%"}

제 output어디간지 아시는 분~?

문제의 원인은 `deserialization`이라고 생각했다. 왜냐하면 포스팅의 첫 부분에 `handler function example code`를 보면 알 수 있다. 현재 내가 spring cloud로 작성한 코드는 아래와 같다.

```java
@Component
public class Handler {
	@Bean
	public Function<String, String> reverse() {
		return (inputString) -> new StringBuilder(inputString).reverse().toString();
	}
}
```

그러나 예제의 코드는 interface를 구현했기에 똑같이 바꾸고 해봤다.

```java
public class Handler implements RequestHandler<String, String> {
	@Override
	public String handleRequest(String input, Context context) {
		return new StringBuilder(input).reverse().toString();
	}

//	@Bean
//	public Function<String, String> reverse() {
//		return (inputString) -> new StringBuilder(inputString).reverse().toString();
//	}
}
```

`method-name`이 바뀌었기에 당연히 `Handler` 위치로 다음과 같이 수정해줬다.

```text
me.practice.stringreverseapi.Handler::handleRequest
```

 <br>

## RE:RE:RE: Test Lambda function

![awslambdasimpleapp1_16](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_16.png?raw=True){: height="70%"}

두근거린다... 제발... 착하게 살테니 한번만...

![awslambdasimpleapp1_17](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp1_17.png?raw=True){: height="60%"}

정상적으로 동작한다!!

<br>

~~`spring-cloud`는 결국...여기서는 필요없게 됐다는 사실~~

<br>

## Spring-cloud 뭐냐? ㅋㅋㅋ

다음과 같이 쓰일 수 있다.

```java
// LambdaHandler.java
...
import org.springframework.cloud.function.adapter.aws.SpringBootRequestHandler;
...

// 여기 클래스의 Generic 이 바로 Deserialize 해주는 역할
public class LambdaHandler extends SpringBootRequestHandler<OrderRequestDto, Object> {

}
```

```java
@Slf4j
@RequiredArgsConstructor
@Component
public class APICollections {
	private final OrderService orderService;

	@Bean
	public Function<OrderRequestDto, OrderResponseDto> registerOrderItem() {
		orderService.getOrders()
				.forEach(order -> log.info(order.toString()));
		return orderService::orderItem;
	}

	@Bean
	public Supplier<List<OrderResponseDto>> getOrderList() {
		return orderService::getOrders;
	}
}
```

바로 위와 같이 `@Bean`으로 등록한 메서드를 spring cloud의 `SpringBootRequestHandler<E, O>` 를 사용하면 lambda에서는 그저 Handler method이름만 바꿔줌으로써 lambda function을 정의할 수 있다.

<br>

## 이렇게 쓰면 좋을 것 같은데...?

백발백준 프로젝트에서 API별로 `@Bean`을 등록한 다음에 같은 zip file, 다른 handler method이름으로 lambda function에 배포한다면 프로젝트 관리가 쉬워질 것 같다는 생각을 해봤다. 내가 생각한 것은 

```console
com.example.lambdaprac
ㄴSpringBootApplication.java
ㄴhandlers
	ㄴrecommendationHandler.java
	ㄴproblemMigrationHandler.java
	ㄴuserApiHandler.java
	...
ㄴservice
ㄴrepository
ㄴdomain
ㄴcontroller
...
```

와 같이 `handlers` package를 만들어서 각각의 lambda function이 될 `handler method`를 만들고, handler method는 기존에 존재하는 프로젝트의 메서드를 쓰면 되는 것이다!

물론 아직 해보지 않았기에 직접 해보는게 빠를 것 같다.

<br>

# Register Order item API

## 몰랐던 것들

1. python으로 lambda application을 만들었을 때에는 `def lambda_handler(event, context)`와 같이 비교적 직관적인 위치에 핸들러 함수가 존재했다. 하지만 Java는 package구조가 존재하기에 어디에 핸들러 함수를 만들어야 하는지 몰랐다.
   - 핸들러 함수의 parameter는 어떤 타입인지 궁금했다.
   - 각 parameter를 직접 parsing해서 사용해야하는 것인지 궁금했다.
2. Spring boot로 어플리케이션을 만들건데 외부 라이브러리를 어떻게 가져올지 고민이다.
   - python 으로 할 때에는 `lambda layer`로 해결. Java도 똑같이 하면 되는지 궁금했다.
3. packge 구성을 어떻게 해야할지 몰랐다.
   - 어떤 의존성이 필요한지

<br>

## 알게된 것들

1. [공식문서](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/java-handler.html#java-handler-interfaces)를 보고 하면 된다. 
2. 의존성과 함께 배포 package를 만들기 위해서는 zip 으로 build를 해야한다. [공식문서](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/java-package.html)
3. 평소에 Spring project만들던대로 하면 된다.

<br>

알게된 것들을 바탕으로 다음 포스팅에서는 간단한 주문 어플리케이션을 만들어 볼 것이다. 

<br>

## Reference

[aws lambda 사용법](https://www.youtube.com/watch?v=6A9pqr4qQMo)

[aws lambda handler:java - aws docs](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/java-handler.html#java-handler-samples)

[spring cloud Function](https://spring.io/projects/spring-cloud-function)

[spring cloud dependency build error](https://stackoverflow.com/questions/55077143/failure-to-find-org-springframework-cloudspring-cloud-dependenciespom)

[spring cloud docs](https://spring.io/projects/spring-cloud/)

[spring boot aws lambda로 배포하기](https://velog.io/@ayoung0073/SpringBoot-AWS-Lambda%EC%97%90-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0)

[aws lambda zip file deploy - aws docs](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/java-package.html)

