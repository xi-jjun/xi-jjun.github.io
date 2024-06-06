---
layout: post
title: "ServletContainerInitializer파일은 어떻게 초기화 클래스를 등록할까?"
author: "xi-jjun"
tags: Spring

---

# ServletContainerInitializer파일은 어떻게 초기화 클래스를 등록할까?

## 개요

spring에 대해서 깊게 공부하기 위해서 [인프런 - 스프링부트 핵심원리 활용](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%ED%95%B5%EC%8B%AC%EC%9B%90%EB%A6%AC-%ED%99%9C%EC%9A%A9/dashboard)강의를 수강중이다.

이번에는 강의를 보다가 문득 궁금해진 부분에 대해서 알아보도록 할 것이다.

<br>

## 궁금했던 부분

`META-INF/services/jakarta.servlet.ServletContainerInitializer` 

```text
hello.container.MyContainerInitV1
hello.container.MyContainerInitV2
```

위와 같이 정의해놓으면, 어플리케이션이 실행되면서 `MyContainerInitV1`과 `MyContainerInitV2` 가 실행된다는 것이다!

처음에는 그냥 그러려니하고 넘어갔었는데, 과연 어디서 해당 파일을 읽어서 처리하는지 궁금해져서 찾기로 했다.

ROI는 상당히 낮을 것으로 보이지만, '눈으로 볼 때 까지는 못 믿는다'는 나의 성격(단점이라 생각..)이 여기서 갑자기... 그래서 호기심을 참지 못하여 확인해봤다.

<br>

## 0. 참고 사항

spring boot가 어떻게 발전해왔는지 알아보기 위해서 tomcat + springframework로 어플리케이션을 만들어보는 연습을 하고 있었다. 따라서 spring 프로젝트를 `WAR`로 빌드하여 tomcat에서 실행시키는 방식으로 어플리케이션을 실행 중이었다.

실행을 위해 IntelliJ에서 spring 프로젝트를 손쉽게 tomcat과 연동하여 실행시켜주는 설정을 사용하였다.

<br>

## 1. Tomcat Project setting

### 1.0) 들어가기 전, 착각했던 부분

코드를 deep dive하기 위한 사전준비 작업이다. 따라하실 분들은 아래 내용을 따라하시면 됩니다~!

먼저, 처음에 했던 실수는 spring 프로젝트에서 관련 의존성을 참조하고 있다고 착각했던 부분이다. (deep dive 하려고 했는데, spring 프로젝트에서는 해당 코드가 없었음. 알고보니 tomcat에 있던 것.)

처음에 `java.util.ServiceLoader` 클래스가 `META-INF` 하위의 경로를 참조하여 loading하는 코드를 확인했었고, 해당 코드에서 initializer 파일을 찾아서 초기화를 해주는 코드를 등록해주는 것이라 생각했다. 그러나 아무리 debug를 찍어도 `META-INF/services/jakarta.servlet.ServletContainerInitializer` 에서 참조하는 케이스는 보이지 않았다. (`fullName` 이 해당 케이스인 경우가 없었음)

아래는 decompile된 해당 코드이다.

```java
package java.util;

public final class ServiceLoader<S> implements Iterable<S> {
    ...
    /**
     * Implements lazy service provider lookup where the service providers are
     * configured via service configuration files. Service providers in named
     * modules are silently ignored by this lookup iterator.
     */
    private final class LazyClassPathLookupIterator<T>
        implements Iterator<Provider<T>>
    {
        static final String PREFIX = "META-INF/services/";
        ...
        private Class<?> nextProviderClass() {
            if (configs == null) {
                try {
                    String fullName = PREFIX + service.getName(); // => 여기에 디버그 찍었는데 확인 못함
                    ....
```

<br>

좀 더 확인해보니 spring 프로젝트를 `WAR` 로 build하여 tomcat에 배포 후 tomcat 서버를 실행하여 어플리케이션을 실행하는 방식이어서, spring 프로젝트 파일이 아닌, `tomcat` 에 있는 파일을 확인했어야 했다...

결국 tomcat에서 해당 작업을 해준다는 것을 깨닫게 되어 tomcat intellij 설정을 해줘야 했다. (Debug 찍기 위함)

<br>

### 1.1) 기존 프로젝트 코드 WAR build

```sh
./gradlew build
mv build/libs/app.war ..../tomcat/webapps/ROOT.war
```

spring 프로젝트를 war로 빌드하고, tomcat의 `webapp` 디렉토리 하위에 `ROOT.war` 라는 이름으로 위치시킨다. (인프런 강의 참고)

<br>

### 1.2) IntelliJ 설정

tomcat local로 run config 설정하고, `Run configuration > deployment > Deploy at the server startup` 에서 ROOT.war를 추가한다. 

이후 디버그 모드로 실행시키면 tomcat관련 의존성에 break point를 찍을 수 있게 된다.

<br>

## 2. 코드 분석

`jakarta.servlet.ServletContainerInitializer` 를 읽어서 초기화를 해주는 방식에 대해서... 완전 이해는 못하더라도 그 흐름만큼은 익혀보도록 하자.

framework deep dive는 처음인데, 일단 할 수 있는 만큼까지만 진행해볼 예정이다. (목표 : 시작부터 초기화하는 그 흐름까지 추적해보기)

<br>

### 2.1) Main 코드

tomcat이 시작될 때 실행되는 main 메서드 코드를 찾았다.

```java
package org.apache.catalina.startup;

public final class Bootstrap {
    private static final Log log = LogFactory.getLog(Bootstrap.class);
    private static final Object daemonLock = new Object();
    private static volatile Bootstrap daemon = null;
    ...
    
    public static void main(String[] args) {
        // daemonLock 은 class variable이다.
        // 해당 변수로 tomcat을 실행할 때 동시성 문제가 발생하지 않도록 방지하기 위해서 Lock을 걸은 모습을 확인할 수 있다.
        synchronized(daemonLock) {
            if (daemon == null) {
                // bootstrap 현재 클래스를 인스턴스화. 서버 시작 당 하나의 bootstrap만이 존재하는게 보장된다.
                Bootstrap bootstrap = new Bootstrap(); // 내부에는 아무런 코드가 없다. 단순 생성자이다.

                try {
                    /* 인스턴스 초기화 코드
                     *
                     * 1. 필요한 class를 loading하기 위해서 class loader 생성
                     * 2. 현재 Thread가 알맞은 클래스를 로딩하도록 class loader 설정
                     * 3. org.apache.catalina.startup.Catalina 클래스로의 setParentClassLoader 메서드 실행 (reflaction 사용)
                     *
                     * 나중에 시간될 때 init에 대해서 깊게 알아보도록 하자!
                     */
                    bootstrap.init();
                } catch (Throwable var5) {
                    ...
                    return;
                }

                daemon = bootstrap;
            } else {
                Thread.currentThread().setContextClassLoader(daemon.catalinaLoader);
            }
        }

        try {
            String command = "start";
                ...
            } else if (command.equals("start")) { // 최초 main 실행될 때에는 start 로 실행된다.
                daemon.setAwait(true);
                daemon.load(args);
                daemon.start();
                if (null == daemon.getServer()) {
                    System.exit(1);
                }
            } ...
    }
    ...
```

tomcat 코드를 하나씩 설명하다가는 끝이 없을 듯 하여 나중에 시간 남을 때 하기로 하고 (안하게될 듯) 우리가 원하는 `jakarta.servlet.ServletContainerInitializer` 를 읽어서 초기화하는 코드는 아래를 참고하면 된다. (디버그 찍어서 찾으면 쉬움)

<br>

### 2.2) WebappServiceLoader 분석

 tomcat이 초기셋팅을 할 때에 `ContextConfig` 클래스가 `processServletContainerInitializers` 메서드를 호출하는데, 이 때 `WebappServiceLoader`에서 내가 궁금했던 처리를 해주는 것이었다.

```java
public class WebappServiceLoader<T> {
    private static final String CLASSES = "/WEB-INF/classes/";
    private static final String LIB = "/WEB-INF/lib/";
    private static final String SERVICES = "META-INF/services/";
    ...

    public WebappServiceLoader(Context context) { ... }

    public List<T> load(Class<T> serviceType) throws IOException {
        // 디버그를 찍고 보면, 서비스 타입 이름으로 인해 `META-INF/services/jakarta.servlet.ServletContainerInitializer` 로 되어 있는 것을 확인할 수 있다.
        // 위 이름으로의 모든 리소스 파일을 찾아서 로딩해주는 역할을 해당 메서드가 해주는 것을 보인다.
        String configFile = "META-INF/services/" + serviceType.getName();
        ClassLoader loader = this.context.getParentClassLoader();
        Enumeration containerResources;
        if (loader == null) {
            containerResources = ClassLoader.getSystemResources(configFile);
        } else {
            // 1. URLClassLoader로 tomcat 내부적으로 참조하는 파일 url을 탐색하여 Enumeration<URL>로 가져온다.
            // - 디버그 찍고 봤을 때에는 2개가 존재하는 것으로 보임
            //   - org.apache.tomcat.websocket.server.WsSci
            //   - org.apache.jasper.servlet.JasperInitializer
            containerResources = loader.getResources(configFile);
        }

        LinkedHashSet<String> containerServiceClassNames = new LinkedHashSet();
        Set<URL> containerServiceConfigFiles = new HashSet();

        while(containerResources.hasMoreElements()) {
            URL containerServiceConfigFile = (URL)containerResources.nextElement();
            // 2. url을 configFiles set에 추가
            containerServiceConfigFiles.add(containerServiceConfigFile);
            // 3. url로 파일을 찾아 내용을 확인하여 패키지+클래스명만 containerServiceClassNames에 추가하는 작업
            // - 1번단계에서 확인했던 클래스들이 containerServiceClassNames에 추가되는 것.
            this.parseConfigFile(containerServiceClassNames, containerServiceConfigFile);
        }

        if (this.containerSciFilterPattern != null) {
            ...
        }

        LinkedHashSet<String> applicationServiceClassNames = new LinkedHashSet();
        List<String> orderedLibs = (List)this.servletContext.getAttribute("jakarta.servlet.context.orderedLibs");
        if (orderedLibs == null) {
            // tomcat 내부적으로가 아닌, 우리가 추가해준 설정파일이 있는지도 확인하기 위해서 모든 리소스를 조회
            // 이 때 우리가 등록해준 `META-INF/services/jakarta.servlet.ServletContainerInitializer` 파일 URL가 존재.
            Enumeration<URL> allResources = this.servletContext.getClassLoader().getResources(configFile);

            while(allResources.hasMoreElements()) {
                URL serviceConfigFile = (URL)allResources.nextElement();
                if (!containerServiceConfigFiles.contains(serviceConfigFile)) {
                    // applicationServiceClassNames에 우리가 등록해줬던 2개의 servlet container init 클래스가 2개 존재하는 것을 확인
                    // - hello.container.MyContainerInitV1
                    // - hello.container.MyContainerInitV2
                    // - 그 외 spring container가 tomcat servlet container에 등록되기 위한 init 클래스들이 등록되는 것을 확인
                    //   - org.springframework.web.SpringServletContainerInitializer
                    this.parseConfigFile(applicationServiceClassNames, serviceConfigFile);
                }
            }
        } else {
            ...
        }

        containerServiceClassNames.addAll(applicationServiceClassNames);
      
        // 결론 : 총 5개의 클래스들이 loadServices 메서드에 의해 클래스가 초기화 된다.
        //   - ==> JVM에 의해 관리가 된다.
        //   - 참고) 클래스의 초기화는 JVM의 method area에 올라가게 되는 것. (클래스 메타데이터와 byte code가 등록됨)
        return containerServiceClassNames.isEmpty() ? Collections.emptyList() : this.loadServices(serviceType, containerServiceClassNames);
    }
```

이후에는 `ContextConfig` 클래스의  `initializerClassMap` 변수에 초기화된 클래스들이 추가되어 초기화 설정을 이어가는 듯 하다.

<br>

## 3. 결론(느낀점)

위에 주석으로 결론을 써놨지만 이번에 얻게된 것을 2가지 정도로 추릴 수 있을 듯 하다.

1. tomcat에서 초기화 해주는 코드를 찾았고, 직접 코드를 분석하여 확인했다. 
   => 라이브러리 소스코드 분석은 이번이 거의 처음인 것 같은데, 정말로 흥미로운 시간이었다. 다만... 시간이 생각보다 많이 들었고, 든 것에 비해 알아낸게 적다는 것이 아쉽다.
2. 이번에 `SpringServletContainerInitializer` 가 실제로 tomcat servlet container에 의해 초기화되는 것을 직접 확인함으로써 "servlet container에 spring container를 등록한다"라는 말이 뭔지 코드로 확인했다! 
   => 강의에서 '다음 시간에는 servlet container만 사용했는데, 여기에 spring container도 등록해보도록 할게요~'라고 했어서... 말로만 이해하는게 아닌 체득했다는게 얻어간 부분인 것 같았다.

그리고 이번에 소스코드 보면서 나도 리팩터링 가능할 수준의 코드를 찾았는데, tomcat 11버전에서는 수정되었던 것을 확인한... 언젠가는 contributor가 될 수 있도록 열심히 공부해야겠다는 생각이 들었다.

<br>

## References

- [스프링부트 핵심원리 활용(김영한 강사님) - 인프런](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-%ED%95%B5%EC%8B%AC%EC%9B%90%EB%A6%AC-%ED%99%9C%EC%9A%A9/dashboard)
- [JVM 메모리 구조 - 느리더라도 꾸준하게](https://steady-coding.tistory.com/305)
- [tomcat source code - Github](https://github.com/apache/tomcat/blob/main/java/org/apache/catalina/startup/WebappServiceLoader.java)
