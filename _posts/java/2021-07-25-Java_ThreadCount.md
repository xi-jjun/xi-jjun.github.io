---
layout: post
title: "JVM Thread vs Application Thread"
author: "xi-jjun"
tags: Java


---

# Java의 Thread 개수에 대하여

Java를 공부하던 중에 궁금한 것이 생겨서 찾아보게 됐다. 

> Java에서 코드가 실행될 때 thread는 `main thread` 1개인가?

결론은 **관점**에 따라 다르다는 것이다.

<br>

## Java System 관점 (JVM 관점)

JVM관점에서라고 생각해도 될 것 같다. 오라클에서 제공하는 [Java공식 문서](https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html) 를 참고하였다. 

내가 궁금증이 생겼던 이유이기도 하다. [해당 StackOverflow 글](https://stackoverflow.com/questions/25464748/what-is-the-default-number-of-threads-running-in-the-jvm) 의 질문을 보면,

>Recently I've been learning more about thread and I was wondering why the resource monitor shows always 19 threads running for the Java process.<br>
>
>Now my questions are:
>
>- Is this the VM using 19 threads?

여러개의 Thread가 JVM에서 동작하고 있음을 발견하고 이게 무엇인지 질문한 것이다. 이것에 대한 답변도 [해당 글](https://stackoverflow.com/questions/25464748/what-is-the-default-number-of-threads-running-in-the-jvm) 에 있지만 [공식 문서](https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html) 를 한번 더 참고하여 정확히 알아봤다.

<br>

> Every application has at least one thread — or several, **if you count "system" threads that do things like memory management and signal handling.**

[공식 문서](https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html) 의 마지막 부분을 보면 위와 같은 글이 적혀있다. 모든 Java application은 적어도 1개의 또는 여러개의 thread를 갖고 있다는 것이다. 근데 **"여러개의 thread를 가진다"** 라고 생각할 경우에는 `System Thread`까지 카운팅 한다면을 가정했을 때 라는 것이다. 과연 이 `System Thread` 라는 것은 뭘까?

<br>

### Java System Thread

Java에는 코드만 실행되는 것이 아니라 memory management나 signal handling하는 `System thread`도 있다고 위에서 언급됐다. JVM이 실행되고, 우리가 작성한 메인 코드가 실행되면서 GC, JIT Compiler 과 같은 Thread가 생성되는데 이를 `Java System Thread`라고 한다.

<br>

### Verification

`jstack`이라는 프로그램을 사용해서 Java code가 실행될 때 어떤 Thread가 생성되는지 알아볼 것이다. 3가지 단계로 검증을 진행할 것이다.

1. Java 코드 실행. `FactorialTest.class` 코드를 실행. (이미 컴파일된 상태의 binary code이다)

   ```java
   // FactorialTest.java source code
   import java.util.Scanner;
   
   public class FactorialTest {
       public static void main(String[] args) {
           Scanner scan = new Scanner(System.in);
           Factorial fact = new Factorial(); // no static declare
           int number = scan.nextInt();
           int num = fact.func(number);
           System.out.println(num);
       }
   }
   ```

   `int number = scan.nextInt()` 줄에서 코드는 `shell`에 사용자의 입력을 받기 전까지 기다려야 한다.

2. `shell`에 사용자의 입력을 넣지 않고 `Interrupt`가 걸리게끔 만들었으니, `ps`명령어를 사용하여 현재 동작하고 있는 process의 PID를 찾아보자.

   ```shell
   ➜  ~ ps
     PID TTY           TIME CMD
     992 ttys000    0:02.69 -zsh
   23768 ttys000    0:00.56 /usr/bin/java FactorialTest
   23739 ttys002    0:00.07 -zsh
   ```

   현재 동작하고 있는 Java binary code의 process ID를 알아냈다. -> 23768

3. `jstack -l [PID]`명령어를 사용하여 해당 PID에 대한 Thread를 볼 수 있다. ~~알지못할 것들이 엄청 나왔다....~~

   ```shell
   ➜  ~ jstack -l 23768
   2021-07-25 20:35:04
   Full thread dump OpenJDK 64-Bit Server VM (25.292-b10 mixed mode):
   
   "Attach Listener" #10 daemon prio=9 os_prio=31 tid=0x00000001400d3800 nid=0x3c0f waiting on condition [0x0000000000000000]
      java.lang.Thread.State: RUNNABLE
   
      Locked ownable synchronizers:
   	- None
   
   "Service Thread" #9 daemon prio=9 os_prio=31 tid=0x00000001400be800 nid=0x4603 runnable [0x0000000000000000]
      java.lang.Thread.State: RUNNABLE
   
      Locked ownable synchronizers:
   	- None
   
   "C1 CompilerThread3" #8 daemon prio=9 os_prio=31 tid=0x000000011f808800 nid=0x4703 waiting on condition [0x0000000000000000]
      java.lang.Thread.State: RUNNABLE
   
      Locked ownable synchronizers:
   	- None
   
   "C2 CompilerThread2" #7 daemon prio=9 os_prio=31 tid=0x000000013f851000 nid=0x4403 waiting on condition [0x0000000000000000]
      java.lang.Thread.State: RUNNABLE
   
      Locked ownable synchronizers:
   	- None
   
   "C2 CompilerThread1" #6 daemon prio=9 os_prio=31 tid=0x000000013f848800 nid=0x4903 waiting on condition [0x0000000000000000]
      java.lang.Thread.State: RUNNABLE
   
      Locked ownable synchronizers:
   	- None
   
   "C2 CompilerThread0" #5 daemon prio=9 os_prio=31 tid=0x000000013f846800 nid=0x4203 waiting on condition [0x0000000000000000]
      java.lang.Thread.State: RUNNABLE
   
      Locked ownable synchronizers:
   	- None
   
   "Signal Dispatcher" #4 daemon prio=9 os_prio=31 tid=0x0000000140070000 nid=0x4103 runnable [0x0000000000000000]
      java.lang.Thread.State: RUNNABLE
   
      Locked ownable synchronizers:
   	- None
   
   "Finalizer" #3 daemon prio=8 os_prio=31 tid=0x000000013f837800 nid=0x3503 in Object.wait() [0x00000001713e6000]
      java.lang.Thread.State: WAITING (on object monitor)
   	at java.lang.Object.wait(Native Method)
   	- waiting on <0x0000000795588ef0> (a java.lang.ref.ReferenceQueue$Lock)
   	at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:144)
   	- locked <0x0000000795588ef0> (a java.lang.ref.ReferenceQueue$Lock)
   	at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:165)
   	at java.lang.ref.Finalizer$FinalizerThread.run(Finalizer.java:216)
   
      Locked ownable synchronizers:
   	- None
   
   "Reference Handler" #2 daemon prio=10 os_prio=31 tid=0x000000014003d000 nid=0x3403 in Object.wait() [0x00000001711da000]
      java.lang.Thread.State: WAITING (on object monitor)
   	at java.lang.Object.wait(Native Method)
   	- waiting on <0x0000000795586c08> (a java.lang.ref.Reference$Lock)
   	at java.lang.Object.wait(Object.java:502)
   	at java.lang.ref.Reference.tryHandlePending(Reference.java:191)
   	- locked <0x0000000795586c08> (a java.lang.ref.Reference$Lock)
   	at java.lang.ref.Reference$ReferenceHandler.run(Reference.java:153)
   
      Locked ownable synchronizers:
   	- None
   
   "main" #1 prio=5 os_prio=31 tid=0x000000013f80c800 nid=0x1603 runnable [0x000000016fd62000]
      java.lang.Thread.State: RUNNABLE
   	at java.io.FileInputStream.readBytes(Native Method)
   	at java.io.FileInputStream.read(FileInputStream.java:255)
   	at java.io.BufferedInputStream.read1(BufferedInputStream.java:284)
   	at java.io.BufferedInputStream.read(BufferedInputStream.java:345)
   	- locked <0x000000079559c560> (a java.io.BufferedInputStream)
   	at sun.nio.cs.StreamDecoder.readBytes(StreamDecoder.java:284)
   	at sun.nio.cs.StreamDecoder.implRead(StreamDecoder.java:326)
   	at sun.nio.cs.StreamDecoder.read(StreamDecoder.java:178)
   	- locked <0x00000007955ed5d0> (a java.io.InputStreamReader)
   	at java.io.InputStreamReader.read(InputStreamReader.java:184)
   	at java.io.Reader.read(Reader.java:100)
   	at java.util.Scanner.readInput(Scanner.java:804)
   	at java.util.Scanner.next(Scanner.java:1483)
   	at java.util.Scanner.nextInt(Scanner.java:2117)
   	at java.util.Scanner.nextInt(Scanner.java:2076)
   	at FactorialTest.main(FactorialTest.java:7)
   
      Locked ownable synchronizers:
   	- None
   
   "VM Thread" os_prio=31 tid=0x000000013f835000 nid=0x5003 runnable 
   
   "ParGC Thread#0" os_prio=31 tid=0x000000014000e800 nid=0x1e07 runnable 
   
   "ParGC Thread#1" os_prio=31 tid=0x000000013f80e800 nid=0x1d03 runnable 
   
   "ParGC Thread#2" os_prio=31 tid=0x000000013f817000 nid=0x2b03 runnable 
   
   "ParGC Thread#3" os_prio=31 tid=0x0000000140015800 nid=0x5403 runnable 
   
   "ParGC Thread#4" os_prio=31 tid=0x000000013f818000 nid=0x2e03 runnable 
   
   "ParGC Thread#5" os_prio=31 tid=0x000000013f818800 nid=0x3003 runnable 
   
   "ParGC Thread#6" os_prio=31 tid=0x0000000140016000 nid=0x5203 runnable 
   
   "ParGC Thread#7" os_prio=31 tid=0x000000013f819800 nid=0x3203 runnable 
   
   "VM Periodic Task Thread" os_prio=31 tid=0x000000013f83c000 nid=0x5603 waiting on condition 
   
   JNI global references: 5
   ```

<br>

너무 길기도 하고 정확히 무엇이다! 라고 하기엔 나의 지식이 너무 짧은 것 같다. 최대한 해석해보도록 하겠다.

```shell
"main" #1 prio=5 os_prio=31 tid=0x000000013f80c800 nid=0x1603 runnable [0x000000016fd62000]
   java.lang.Thread.State: RUNNABLE
	at java.io.FileInputStream.readBytes(Native Method)
	at java.io.FileInputStream.read(FileInputStream.java:255)
	at java.io.BufferedInputStream.read1(BufferedInputStream.java:284)
	at java.io.BufferedInputStream.read(BufferedInputStream.java:345)
	- locked <0x000000079559c560> (a java.io.BufferedInputStream)
	at sun.nio.cs.StreamDecoder.readBytes(StreamDecoder.java:284)
	at sun.nio.cs.StreamDecoder.implRead(StreamDecoder.java:326)
	at sun.nio.cs.StreamDecoder.read(StreamDecoder.java:178)
	- locked <0x00000007955ed5d0> (a java.io.InputStreamReader)
	at java.io.InputStreamReader.read(InputStreamReader.java:184)
	at java.io.Reader.read(Reader.java:100)
	at java.util.Scanner.readInput(Scanner.java:804)
	at java.util.Scanner.next(Scanner.java:1483)
	at java.util.Scanner.nextInt(Scanner.java:2117)
	at java.util.Scanner.nextInt(Scanner.java:2076)
	at FactorialTest.main(FactorialTest.java:7)
```

`main thread`가 생성된 것을 알 수 있었다. 맨 마지막 줄을 보면 `FactorialTest.java:7`라고 돼있는데 내가 작성한 코드의 7번째 줄은 Scanner로 사용자의 입력을 기다리는 코드였다. 따라서 `main thread`는 현재 저 부분에서 멈춰있다는 것이다.

그 외로는 JVM의 여러 thread인 `Finalizer`나 `Reference Handler`같은 thread가 생성되어 동작하고 있는 것을 볼 수 있었다.

> 결론 : JVM관점에서는 JVM을 위해 딱 봐도 많은 thread가 생성된다.

<br>

<br>

## Application Programmer의 관점

[Java공식 문서](https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html)를 참고하여 작성했다.

>From the application programmer's point of view, you start with just one thread, called the *main thread*.

문서의 마지막 부분을 보면 위와 같은 말이 있다. 

>결론 : 결국 **programmer 관점에서 본다면**, thread는 main thread 1개가 동작하고 있음을 알 수 있다.

