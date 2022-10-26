---
layout: post
title: "AWS Lambda 실습 : Simple Order API"
author: "xi-jjun"
tags: aws

---

# AWS Lambda 실습 : Simple Order API

## 요구사항

```text
1. item을 추가할 수 있어야 한다.
2. 현재 DB에 저장된 item 목록을 조회할 수 있어야 한다.
3. 현재 DB에 저장된 Order 목록을 조회할 수 있어야 한다.
4. item의 '이름(name)'으로 조회할 수 있어야 한다.
5. Order(주문)을 할 수 있어야 한다.
```

다음과 같은 5가지 요구사항을 만족하는 어플리케이션을 만들어보도록 하자. 이전 포스팅에서는 3개였지만, 주문-상품 의 관계가 필요할 것 같아서 이왕이면 깔끔하게 만들기 위해 order api를 하나 만들기로 했다.

<br>

## Network Architecture : Restful API

- `POST - /items` : item을 추가하는 api
- `POST - /orders` : 주문하는 api
- `GET - /items` : 전체 item list 조회 api
- `GET - /orders` : 전체 order list 조회 api
- `GET - /items/{item_name}` : `item_name`에 해당하는 item목록 조회 api

<br>

## Entity

![awslambdasimpleapp2_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp2_1.png?raw=True){: height="70%"}

정말 간단한 DB table을 설계해봤다. 1개의 주문에 여러개의 상품을 가질 수 없냐고 말할 수도 있겠지만... 여기서는 간단하게 하기 위해서 1번의 주문에 대해서 상품의 종류는 1개로 제한했다.

<br>

## Order API Code

[github link](https://github.com/xi-jjun/AWSLambdaDemo)로 내가 이번에 사용한 코드를 볼 수 있다. 코드에 대한 설명보다는 aws lambda를 사용하는 것에 더 집중을 해보도록 하겠다.

<br>

## Architecture

![awslambdasimpleapp2_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp2_2.png?raw=True){: height="100%"}

각 lambda function에 정의된 method는 `spring-cloud`로 `@Bean`에 등록된 method들이다. 아래는 해당 코드이다. CI/CD는 프로젝트를 할 때에 할 예정이다.

<br>

## @Bean methods

```java
@RequiredArgsConstructor
@Component
public class APICollections {
	private final OrderService orderService;

	@Bean
	public Function<ItemRegisterDto, ResponseDto<?>> registerItem() {
		return orderService::registerItem;
	}

	@Bean
	public Supplier<ResponseDto<?>> getAllItemList() {
		return orderService::getTotalItemList;
	}

	@Bean
	public Function<OrderRequestDto, ResponseDto<?>> orderItem() {
		return orderService::orderItem;
	}

	@Bean
	public Function<String, ResponseDto<?>> getItem() {
		return orderService::getItem;
	}

	@Bean
	public Supplier<ResponseDto<?>> getAllOrderList() {
		return orderService::getTotalOrderList;
	}
}
```

<br>

## Test Lambda function : GetItemList

일단 테스트 데이터로 RDS에 `Item` 2개정보를 넣어놨다. `getAllItemList()` function부터 테스트를 해보겠다.

### Runtime Settings : `Handler`

```text
me.practice.awslambdademo.handlers.ItemHandler
```

<br>

### Environment variables

- `FUNCTION_NAME` : `getAllItemList`
- `MAIN_CLASS` : `me.practice.awslambdademo.AwsLambdaDemoApplication`

<br>

### Project package

![awslambdasimpleapp2_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp2_3.png?raw=True){: height="30%"}

<br>

### Handler class

```java
package me.practice.awslambdademo.handlers;

import me.practice.awslambdademo.domain.ItemRegisterDto;
import org.springframework.cloud.function.adapter.aws.SpringBootRequestHandler;

public class ItemHandler extends SpringBootRequestHandler<ItemRegisterDto, Object> {
}
```

<br>

이제 테스트만 남았다. 

<br>

## Error

```json
{
  "errorMessage": "2022-10-25T16:29:17.884Z f978e88d-39de-4b6f-86c7-61cf84ee036b Task timed out after 3.03 seconds"
}
```

와 같은 오류가 발생했다. `aws lambda`는 default timeout이 3초로 되어 있고, 최대 15분까지 가능하다. 아마 `Java spring project`를 실행하는데 spring이... 좀 많이 무거워서 3초안에 실행을 못한 것 같다. 근데 조금만 생각해봐도 당장 나의 노트북(mac m1)에서도

![awslambdasimpleapp2_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp2_4.png?raw=True){: height="90%"}

spring boot가 JVM에 load되는데 거의 3초...가 걸리는 것을 볼 수 있다. 따라서 시간을 넉넉하게 30초로 늘려주고 다시 테스트를 해봤다.

<br>

## RE: Error

```json
{
  "errorMessage": "2022-10-25T16:40:21.565Z 7c8e33a2-8290-4c2f-a38f-2c3aff30e662 Task timed out after 30.06 seconds"
}
```

???? 30초도 부족한가...? 싶어서 `cloudwatch` log를 뜯어봤다.

![awslambdasimpleapp2_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp2_5.png?raw=True){: height="100%"}

음... 딱히 이상한 점을 못찾겠다. 따라서 30초에서 60초로 시간을 늘려주고 다시 테스트를 진행했다.

<br>

## RE:RE: Error

```json
{
  "errorMessage": "2022-10-25T16:47:22.617Z 8bf047ff-04a7-493f-aefa-fe68804f9a61 Task timed out after 60.06 seconds"
}
```

...? 2분으로 늘려줬다.

<br>

## RE:RE:RE Error 

```console
...
2022-10-25 16:54:44.814  WARN 9 --- [           main] o.s.c.support.GenericApplicationContext  : Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'messageConverters' defined in class path resource [org/springframework/boot/autoconfigure/http/HttpMessageConvertersAutoConfiguration.class]: Bean instantiation via factory method failed; nested exception is java.lang.OutOfMemoryError: Metaspace
...
```

위와 같은 error가 `cloudwatch` log에서 발견이 됐다. 따라서 이번에는 default memory sizerk 512MB였기에 1024MB로 바꿔준 다음 테스트를 진행해봤다.

<br>

![awslambdasimpleapp2_6](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/awslambdasimpleapp2_6.png?raw=True){: height="100%"}

서버에 등록했던 `Item` 데이터 2개가 정상적으로 반환된 것을 볼 수 있다.

<br>

## Next..

aws lambda와 spring-cloud로 RDS에 연결하여 실행되는 것을 확인했다. 이제 다음 포스팅에서는 `API Gateway`를 연결하여 외부에서도 api를 사용할 수 있게 해볼 것이다.

<br>

## Reference

[aws lambda serverless application architecture](https://www.youtube.com/watch?v=UZBSIc5wRWw&list=PLfth0bK2MgIbpsMNmche-YXWwwEN4qK5k&index=10)

[aws lambda memory, timeout issue](https://stackoverflow.com/questions/65983490/deploying-spring-boot-with-spring-data-jpa-in-aws-lambda)
