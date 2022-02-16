---
layout: post
title: "JAVA - JVM Class Loader"
author: "xi-jjun"
tags: Java


---

# Java

Java 공부를 정리 tag

<br>

# JVM 내부구조

지난 포스팅에서 JVM에 대해 알아봤다. 이번 포스팅에서는 JVM의 내부구조에 대해 자세히 살펴볼 생각이다.

![javaJVM_internal1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/javaJVM_internal1.png?raw=True){: width="65%"}

<sub>[사진 출처 : Naver D2](https://d2.naver.com/helloworld/1230) </sub>

Java로 작성된 코드는 위와 같은 과정을 통해서 수행된다. bytecode로 변환시키는 것 까지는 저번 포스팅에서 했었기에 이번엔 `Class Loader`부터 설명하도록 하겠다.

<br>

# JVM : Class Loader

JDK에서 개발한 다음, JRE로 실행시킨다는 것을 알게 됐다. 더 자세히는 JRE로 환경을 제공받은 JVM이 compile된 bytecode를 loading시켜서 실행하는 것이다. 

> " JVM은 어떻게 class file을 load할까...? "

*Java Compiler* 를 통해서 *.class* 파일은 각 directory에 흩어져 있다. 또한, 기본적인 라이브러리의 클래스 파일들은 `$JAVA_HOME` 내부 경로에 존재한다. 따라서 **흩어져있는 *.class* 파일을 모두 찾아서 JVM에 loading**해줘야 하는데 그 역할을 하는게 `Class Loader`인 것이다.

- Class Loader의 역할
  1. Loading : `.class` 파일을 loading하는 과정
  2. Linking : loading된 `.class` 파일을 사용하기 위해 유효한지 검증하고 기본값으로 초기화하는 과정
  3. Initialization : *static field*의 값을 정의한 값으로 초기화하는 과정

<br>

<br>

## Loading

> Class Loader가 필요한 *.class* 파일을 찾아서 JVM에 loading하게 해주는 과정.

기본적으로 Java에서 클래스가 선언되면, 컴파일 시점에서 해당 클래스가 loading되는 것이 아닌, **Run time** 때 loading되는 것이다. 따라서 JVM이 bytecode를 실행시키는 중에  `Class Loader`가 필요한 Class 들을 찾아오는 것이다.

- 왜 loading을 run time에 하는 것인가? 컴파일 때 하면 한번에 되는 것 아닌가?

  `static linking` 을 한다고 가정해보자. bytecode에 현재 쓰고 있는 모든 라이브러리가 존재하게 되고, 만약 이러한 bytecode가 다른 bytecode에 있는 동일한 라이브러리를 쓰게 될 경우 중복이 발생한다. 따라서 **run time 때에 지금 필요한 라이브러리를 가져오게 되면** 불필요한 메모리의 낭비를 줄일 수 있고 프로그램의 크기도 작게할 수 있는 장점이 있다.

<br>

```java
public class Main {
  public static void main(String[] args) {
    Foo foo = new Foo(); // 이 때 Foo Class를 Class Loader를 통해 Foo.class file을 메모리에 loading하는 것!
  }
}
```

<br>

Loading은 각각의 *.class* file이 기본으로 제공받는 파일인지, 개발자가 정의한 파일인지에 따라 `Class Loader` 가 3가지로 나뉘게 된다.

1. `Bootstrap Class Loader` 

   - 다른 모든 class loader들의 부모. 
   - *rt.jar* 를 포함하여, *JVM* 을 구동시키기 위한 가장 필수적인 라이브러리의 클래스들을 *JVM* 에 탑재.
   - 가장 상위의 *ClassLoader* 이므로 다른 *ClassLoader* 와는 다르게 탑재되는 OS에 맞게 네이티브 코드로 쓰여있다.

   ```shell
   [Opened /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/jre/lib/rt.jar]
   [Loaded java.lang.Object from /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/jre/lib/rt.jar]
   [Loaded java.io.Serializable from /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/jre/lib/rt.jar]
   [Loaded java.lang.Comparable from /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/jre/lib/rt.jar]
   [Loaded java.lang.CharSequence from /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/jre/lib/rt.jar]
   [Loaded java.lang.String from /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/jre/lib/rt.jar]
   ...
   ```

   위는 `java -verbose:class ClassLoaderTest` 명령어를 친 결과이다. 보다시피 *rt.jar* 에서 구동을 위한 필수적인 라이브러리 클래스를 JVM에 loading하고 있음을 알 수 있다. (전체적으로 아래에서 보면 된다.)

2. `Extension Class Loader` 

   - `Bootstrap Class Loader`다음으로 우선순위를 갖는 *Class Loader*이다. 
   - 다른 표준 핵심 라이브러리 클래스를 JVM에 loading한다.

3. `Application Class Loader` 

   - `System Class Loader`라고도 한다.
   - 개발자들이 Java code로 작성한 *.class* 파일들을 찾아서 JVM에 loading한다.
   - 개발자가 *Class Loader*를 구현하여 사용한다면, *Application Class Loader* 의 자식형태로 사용한다.

   ```shell
   ...
   [Loaded ClassLoaderTest from file:/Users/xi-jjun/workspace/data-structure-and-algorithm/src/javastudy/jvm/classloader/]
   [Loaded sun.launcher.LauncherHelper$FXHelper from /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/jre/lib/rt.jar]
   [Loaded java.lang.Class$MethodArray from /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/jre/lib/rt.jar]
   [Loaded java.lang.Void from /Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/jre/lib/rt.jar]
   [Loaded UserClass from file:/Users/xi-jjun/workspace/data-structure-and-algorithm/src/javastudy/jvm/classloader/]
   ...
   ```

   File 경로에서 우리가 실행시키려는 ClassLoaderTest.class 라는 파일을 찾아서 JVM에 loading하고 있음을 확인했다.

   ![javaJVM_internal2](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/javaJVM_internal2.png?raw=True){: width="65%"}

   위 그림을 보면 실제로 저장된 곳을 확인할 수 있다. 비교해보면 당연하게도 내가 작성한 *.class* 파일을 잘 찾아서 Load했다.

4. `User-Defined Class Loader`

   애플리케이션 사용자가 직접 코드 상에서 생성해서 사용하는 클래스 로더이다.

<br>

- 특징

  - Hierarchical

    부모 클래스 로더에서 자식 클래스 로더를 갖는 것과 같이 계층형 구조를 가진다. 최상위에는 *Bootstrap Class Loader* 가 있다

    - 왜 계층형 구조를 가질까? 어떤 장점이 있을까?

      웹 애플리케이션 서버(WAS)와 같은 프레임워크는 웹 애플리케이션들, 엔터프라이즈 애플리케이션들이 서로 독립적으로 동작하게 하기 위해 사용자 정의 클래스 로더를 사용한다. 즉, 클래스 로더의 위임 모델을 통해 애플리케이션의 독립성을 보장하는 것 - [Naver D2](https://d2.naver.com/helloworld/1230)

    

  - Delegate load request

    계층 구조를 바탕으로 클래스 로더끼리 로드를 위임하는 구조로 동작한다. 클래스를 로드할 때 먼저 상위 클래스 로더를 확인하여 상위 클래스 로더에 있다면 해당 클래스를 사용하고, 없다면 로드를 요청받은 클래스 로더가 클래스를 로드한다.

  - Have visibility request

    하위 클래스 로더는 상위 클래스 로더의 클래스를 찾을 수 있지만, 상위 클래스 로더는 하위 클래스 로더의 클래스를 찾을 수 없다.

  - Cannot unload classes

    클래스 로더는 클래스를 로드할 수는 있지만 언로드할 수는 없다. 언로드 대신, 현재 클래스 로더를 삭제하고 아예 새로운 클래스 로더를 생성하는 방법을 사용할 수 있다.

위 각각의 *Class Loader* 를 다 거쳤는데도 불구하고 못찾았다면 `ClassNotFoundException` 을 던지게 된다.

아래는 `ClassLoaderTest.java` 라는 파일을 [여기를](https://tecoble.techcourse.co.kr/post/2021-07-15-jvm-classloader/) 참고하여 작성한 후 테스트 한 결과이다.

```shell
$ java -version               
openjdk version "1.8.0_292"
OpenJDK Runtime Environment (Zulu 8.54.0.21-CA-macos-aarch64) (build 1.8.0_292-b10)
OpenJDK 64-Bit Server VM (Zulu 8.54.0.21-CA-macos-aarch64) (build 25.292-b10, mixed mode)
```

<br>

```java
/**
 * https://tecoble.techcourse.co.kr/post/2021-07-15-jvm-classloader/
 * java -verbose:class Main 명령어 테스트를 위한 코드
 * 현재 파일 이름 : ClassLoaderTest.java
 * java -verbose:class ClassLoaderTest
 */
import java.util.LinkedList;
import java.util.List;

public class ClassLoaderTest {
	public static void main(String[] args) {
		UserClass userClass = new UserClass(100);
		int number = 1;
		List<Integer> numbers = new LinkedList<>();

		System.out.println("Application ClassLoader: " + UserClass.class.getClassLoader());
		System.out.println("Extension ClassLoader: " + UserClass.class.getClassLoader().getParent());
		System.out.println("Bootstrap ClassLoader: " + UserClass.class.getClassLoader().getParent().getParent());
		/**
		 * java zulu-8.jdk 에서 실행시킨 결과
		 *
		 * Application ClassLoader: sun.misc.Launcher$AppClassLoader@4e0e2f2a
		 * Extension ClassLoader: sun.misc.Launcher$ExtClassLoader@2a139a55
		 * Bootstrap ClassLoader: null → native하게 작성됐기 때문에 null을 반환한다고 한다... 다음에 자세히 알아봐야겠다
		 */
	}
}

/**
 * https://tecoble.techcourse.co.kr/post/2021-07-15-jvm-classloader/
 * 를 참고하여 Class Loader 테스트를 위한 코드입니다.
 */
class UserClass {
	private int num;

	public UserClass(int num) {
		this.num = num;
	}

	public int getNum() {
		return num;
	}
}
```

<br>

```shell
$ java -verbose:class ClassLoaderTest
```

<br>

```shell
Application ClassLoader: sun.misc.Launcher$AppClassLoader@4e0e2f2a
Extension ClassLoader: sun.misc.Launcher$ExtClassLoader@2a139a55
Bootstrap ClassLoader: null
```

<br>

<br>

## Linking

> load된 클래스 파일들을 검증하고 사용할 수 있게 준비하는 과정.

좀 더 자세히 말해보자면, Class Loader가 아직 load안된 class 찾으면 verification, preparation, resolution 을 거쳐 class를 JVM에 load하고 link하고 초기화 한다. 그 때 `verification, preparation, resolution` 이 3가지 단계가 Linking에 속하는 과정인 것이다.

- Verification

  *.class* 파일이 유효한지 확인. JVM의 구동조건대로 구현되지 않았을 경우 `VerifyError`. 전 과정 중 가장 까다로운 검사이기 때문에 가장 복잡하고 오래걸린다고 한다. JVM 테스트를 거칠 때 가장 주요하게 테스트를 보는 부분이라고도 한다.

  - 유효하다?

    읽어드린 클래스가 JLS(Java Language Specification) 및 JVM Spec에 명시된 대로 잘 구성이 되어있는지 검사하는 것.

- Preparation

  Class 및 Interface에 필요한 static field 메모리를 할당하고 이를 기본값으로 초기화. 뒤에 `Initialization` 과정에서 코드에 작성한 초기값으로 변경이 된다고 한다.

- Resolution

  Symbolic Reference 값을 JVM의 메모리 구성 요소인 `Method Area` 의 run time pool을 통하여 Direct Reference라는 메모리 주소 값으로 바꾸어 준다.

  → 클래스에 다양한 방식으로 참조된 모든 클래스들을 loading한다. Ex) super class, interface...

<br>

<br>

## Initialization

*.class* 파일의 코드를 읽어서 코드에서 지정한 값으로 초기화 및 초기화 메서드를 실행시켜 준다. 이 때 JVM은 mulit-threading으로 동작하기 때문에 초기화 할 때 동시성을 고려해줘야 한다.

<br>

위 단계들이 모두 끝나면 JVM에서 클래스 파일을 구동시킬 준비가 끝나게 된다!

<br>

<br>

<br>

## Rerference

[우아한Tech Youtube](https://www.youtube.com/watch?v=UzaGOXKVhwU)

[Tecoble - JVM](https://tecoble.techcourse.co.kr/post/2021-08-09-jvm-memory/)

[Naver D2 - JVM](https://d2.naver.com/helloworld/1230)

[Oracle Java SE8 JVM Spec](https://docs.oracle.com/javase/specs/jvms/se8/html/index.html)

[Class Loader](https://keichee.tistory.com/105)

[static linking vs dynamic linking](https://live-everyday.tistory.com/69)

Wikipedia 참고



