---
layout: post
title: "AWS ECS Fargate 실습"
author: "xi-jjun"
tags: aws

---

# AWS ECS Fargate 실습

## 요구사항

```text
1. GET - /{name} 으로 요청하면 응답으로 'Hi! {name}!! Welcome to ECS Fargate!'
2. GET - /github/{github-id} 로 요청하면 응답으로 프로필 사진과 사용자 이름 반환하기.
```

```json
status : 200
data : {
	profileImage : GITHUB_PROFILE_IMAGE_URL,
	name : GITHUB_USER_NAME
}
```

2번의 요구사항은 위와 같은 `json` format의 데이터로 반환이 된다고 해보자. 

구현의 난이도는 어렵지 않고 간단하게 만들기 위해서 DB가 필요없는 실습을 준비했다.

<br>

## 진행

1. `Hello Fargate` API 코드 작성
2. Docker build
3. ECR에 업로드
4. ECS Fargate에 n분마다 실행되게 하여 결과 확인

<br>

## Hello Fargate API

```java
@RestController
public class HelloController {
	@GetMapping("/{name}")
	public String greeting(@PathVariable String name) {
		return "Hello " + name + "!! This is aws ECS fargate!";
	}
}
```

<br>

## Docker build

ECS는 이름 그대로 '컨테이너 관리 서비스'이다. 따라서 우리가 만든 어플리케이션을 container화하여 배포해야 하기에 `docker`를 사용할 것이다. `Docker`에 대해서는 이전에 포스팅을 다룬적이 있다. 그러니 오늘은 실습에 더 집중하여 진행하겠다.

```dockerfile
FROM openjdk:11-jdk
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app.jar"]
```

위와 같은 `Dockerfile`을 정의해줬다. 

### Docker build error

```shell
failed to solve with frontend dockerfile.v0: failed to read dockerfile: open /var/lib/docker/tmp/buildkit-mount208043088/Dockerfile: no such file or directory
```

너무 오랜만에 써서 `DockerFile`이라는 이름으로 파일을 생성했다가 다음과 같은 오류가 났으니 조심하도록 하자.

> `Dockerfile` 이 파일 이름이다....(f가 대문자로 되어 있어서 오류가 발생했던 것.)

<br>

![ecsFargate1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_1.png?raw=True){: height="70%"}

```shell
docker build -t MY_DOCKER_IMAGE_NAME -f PATH_OF_DOCKERFILE
```

<br>

![ecsFargate1_2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_2.png?raw=True){: height="90%"}

```shell
docker images
```

정상적으로 build가 돼서 `Docker image`가 만들어진 것을 볼 수 있다.

<br>

![ecsFargate1_3](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_3.png?raw=True){: height="90%"}

```shell
docker -d -p OUTER_PORT:DOCKER_EXPOSED_PORT CONTAINER_ID
```

`curl` command로 확인을 하기 위해 `demon`으로 실행시켰다. 결과적으로 우리가 만들었던 결과대로 잘 나온 것을 볼 수 있다.

<br>

## ECR upload

![ecsFargate1_4](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_4.png?raw=True){: height="100%"}

`AWS ECR` repository로 가서 새로운 repository를 만들어 주자.

![ecsFargate1_5](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_5.png?raw=True){: height="60%"}

적당히 이름 적어주고 `private` repository로 하면 설정을 더 해줘야했기에 그냥 `public`으로 했다. 이렇게 ECR repository를 생성만하면 그 다음은 쉽다. 그냥 하라는대로 하면 되기 때문이다.

<br>

![ecsFargate1_6](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_6.png?raw=True){: height="80%"}

여기에 나온 그대로 따라서 복사 붙여넣기만 한다면 쉽게 우리가 local에서 생성한 `image`를 업로드 할 수 있는 것이다.

![ecsFargate1_7](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_7.png?raw=True){: height="80%"}

잘 생성된 것을 볼 수 있다.

<br>

## ECS Fargate 생성

먼저 `cluster`를 생성해야 한다. `cluster`에 대해서 알고 싶다면 이전 포스팅을 참고하면 된다.

![ecsFargate1_8](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_8.png?raw=True){: height="70%"}

`create cluster`를 클릭하자.

![ecsFargate1_9](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_9.png?raw=True){: height="70%"}

우리는 `Fargate`실습이기에 `Networking Only`를 선택해주자.

<br>

## Task Definition

`cluster`를 생성하였다면, 이제 우리가 `cluster`위에서 '어떤 작업'을 할지 정의해줘야 한다.

![ecsFargate1_10](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_10.png?raw=True){: height="70%"}

![ecsFargate1_11](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_11.png?raw=True){: height="70%"}

여기서 외부 port와 docker exposed port를 명시적으로 8080:8080으로 매핑해줘야 한다.

![ecsFargate1_12](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_12.png?raw=True){: height="70%"}

`Task Size`는 그냥 최소 memory, cpu로 골랐다. 이번 포스팅에서는 `요구사항 1`만 하고 다음 포스팅에서 마저 다 하도록 하겠다.

<br>

## Run Task

바로 `Cluster` 관리 페이지로 들어가서 `Run Task`를 클릭하여 우리가 바로 위에서 정의한 `hello-fargate-task-def`를 실행시켜보자.

![ecsFargate1_13](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_13.png?raw=True){: height="100%"}

그러나 `STOPPED` 가 뜨는 애석한 상황이 펼쳐지고 말았다... 모든 것은 `log`로 알 수 있으니깐 `cloudwatch`에 기록된 `log event`를 보도록 하자.

<br>

## Cloud watch log event

```console
exec /usr/local/openjdk-11/bin/java: exec format error
```

위와 같은 에러가 발생했다. 뭐가 문제인지 `stackoverflow`에 [검색](https://mkyong.com/java/bash-usr-bin-java-cannot-execute-binary-file-exec-format-error/)을 해봤다. 

> The error `Exec format error` means we download the wrong JDK build for a specific platform.

그러니깐 나의 `local`환경은 `mac`이지만, `aws fargate`서버 컴퓨터 환경과 달랐기 때문에 생기는 오류라고 한다. 

<br>

## AWS Fargate platform

Mac m1에서 일부 이미지를 지원하지 않는다고 한다. 따라서 `--platform` 이라는 옵션을 추가하여 build 시에 명시해줘야 한다.

```shell
docker build --platform=linux/amd64 -t MY_DOCKER_IMAGE_NAME .
```

<br>

## Result

![ecsFargate1_14](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/aws/img/ecsFargate1_14.png?raw=True){: height="70%"}

정상적으로 잘 동작함을 알았다.

<br>

## Next...

이 다음으로는 요구사항을 마저 만들어보도록 하겠다.

<br>

## Reference

[Dockerfile build fail](https://stackoverflow.com/questions/64985913/failed-to-solve-with-frontend-dockerfile)

[fargate java invalid platform build](https://mkyong.com/java/bash-usr-bin-java-cannot-execute-binary-file-exec-format-error/)

[Mac m1 java build to aws fargate](https://dev.classmethod.jp/articles/how-to-resolve-errors-that-appear-when-creating-an-aws-ecs-task/)

