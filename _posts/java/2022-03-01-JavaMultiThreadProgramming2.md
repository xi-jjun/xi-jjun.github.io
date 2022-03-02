---
layout: post
title: "JAVA - Multi-Thread Programming 2"
author: "xi-jjun"
tags: Java


---

# Java

Java 공부를 정리 tag

<br>

## Intrinsic Locks and Synchronization

`synchronization`은 `Intrinsic lock` 또는 `moniter`로 알려진 entity로 구성되어 있다. `Intrinsic lock`의 역할로는

1. 객체에 대한 exclusive 접근 실행
2. happens-before 관계를 설정

모든 객체는 `Intrinsic lock`를 가진다. 기존에는 thread가 exclusive하고 consisitency를 위한 접근을 위해서 `critical-section`에 접근하기 전, `acquire()`를 하여 객체에 대한  `Intrinsic lock`을 얻어야 했다. 그리고 작업이 끝나면 `release()`로 `Intrinsic lock`을 반납해야 했다. 여기서 `acquire()` ↔ `release()` 사이에 해당 thread가  객체에 대한 `Intrinsic lock`을 소유하는 것.(다른 thread는 해당 객체에 접근 시 block된다.)

<br>

## Locks in Synchronized Methods

thread가 synchronized method 호출할 때 자동으로 method의 instance 객체에 대한 `Intrinsic lock`을 `acquire()`하고 method가 반환되면 `release()`를 한다. 그리고 예외상황에 의한 반환에도 `release()`를 한다.

- static synchronized method는...?

  Class의 객체에 대한 lock을 획득하고 끝나면 반환한다. Instance lock과 다른 별도의 lock에 의해 클래스의 static field가 제어된다.

  > Thus access to class's static fields is controlled by a lock that's distinct from the lock for any instance of the class.

<br>

## Synchronized Statements

동기화 코드를 만드는 2번째 방법이다. 이전에는 method 선언 때 `synchronized` keyword를 쓰는 방법이었다면, 이번엔 블록으로 지정하여 동기화를 해줄 수 있는 방법이다.

```java
public void addName(String name) {
  synchronized (this) {
    lastName = name;
    nameCount++;
  }
  nameList.add(name);
}
```

동기화 method와 차이점은 **'Intrinsic lock을 제공하는 객체를 명시'**해줘야 한다. 위 예제에서는 `this`로 해당 method를 가지는 객체 instance에 대한 intrinsic lock을 얻어야 저 블록 안의 코드를 실행시킬 수 있다는 뜻이다.

그렇다면 왜 2가지의 방법이 있는 것일까? 뭐가 다를 것일까

<br>

## Synchronized Method vs Synchronized Statement

다음과 같은 상황을 가정해보자.

> 하나의 클래스 instance에 2개의 변수 `c1`, `c2`가 있다고 하자.
>
> 1. 해당 instance는 1개만 생성한다고 가정.
> 2. 해당 instance에는 여러개의 thread가 접근할 것이라 가정.
> 3. `c1++`, `c2++` 라는 2가지 method만 제공한다 가정.
> 4. 원하는 동작 : `c1`, `c2`를 `TIMES`번만큼 각각 증가시키는 것

위 가정으로 코드를 만들어보도록 하자. ([원본코드](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html)) 

`synchronized method`로 구현해보자.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class SynchronizedStatement {
	static final int TIMES = 10000;

	public static void main(String[] args) throws InterruptedException {
		ExecutorService executorService = Executors.newFixedThreadPool(10);

		Counter counter = new Counter(0, 0);
		System.out.println("init c1, c2 = 0, 0");
		for (int i = 0; i < TIMES; i++) {
			executorService.submit(() -> counter.synMethodIncrementC1());
			executorService.submit(() -> counter.synMethodIncrementC2());
		}
		executorService.shutdown();
		executorService.awaitTermination(60, TimeUnit.SECONDS);

		// wait
		while (counter.c1 + counter.c2 != 2 * TIMES) {}
		System.out.println("after c1, c2 = " + counter.c1 + " " + counter.c2);
	}
}
```

```java
public class Counter {
	private int c1;
	private int c2;

	public Counter(int c1, int c2) {
		this.c1 = c1;
		this.c2 = c2;
	}

	public synchronized void synMethodIncrementC1() {
		c1++;
		System.out.println("c1 = " + c1 + ", 이 때 c2 = " + c2);
	}

	public synchronized void synMethodIncrementC2() {
		c2++;
		System.out.println("c2 = " + c2 + ", 이 때 c1 = " + c1);
	}
}
```

코드를 실행해보면 깔끔하게 `c1`, `c2`는 `TIMES`의 개수만큼 결과가 나온다. 근데 이 작업을 보면, 한 thread가 `synMethodIncrementC1()`method에 접근하게 될 때, 다른 thread가 `synMethodIncrementC2()`method에 접근할 수 없다. 현재 우리가 의도하는 상황에서는 '동시에 2개의 method를 실행시키면 안되는건가...?'라는 생각이 들 수 있다.

<br>

그래서 `synchronized statement`에서는 위와 같은 코드를 아래와 같이 바꿀 수 있다고 한다.

```java
static class Counter {
  private final Object lockC1 = new Object();
  private final Object lockC2 = new Object();
  private int c1;
  private int c2;

  public Counter(int c1, int c2) {
    this.c1 = c1;
    this.c2 = c2;
  }

  public void synStatementIncrementC1() {
    synchronized (lockC1) {
      c1++;
      System.out.println("c1 = " + c1 + ", 이 때 c2 = " + c2);
    }
  }

  public void synStatementIncrementC2() {
    synchronized (lockC2) {
      c2++;
      System.out.println("c2 = " + c2 + ", 이 때 c1 = " + c1);
    }
  }
}
```

`lock1`, `lock2`를 사용하여 각각의 객체 lock을 얻는다면, 해당 method들에 접근을 가능하게 바꾼 것이다. 따라서 `synchronized statement`는 세분화된 동기화로 concurrency 향상에 유용하다고 한다.

> Synchronized statements are also useful for improving concurrency with fine-grained synchronization.

하지만 2개의 영역이 서로에게 영향을 주는지 잘 확인한 후 사용해야 한다.

[여기서](https://github.com/xi-jjun/data-structure-and-algorithm/tree/master/src/javastudy/concurrency)전체 코드를 확인해볼 수 있다.

<br>

### 여기서 의문

1. `synchronized method`로 선언된 2개의 method들에 각기 다른 thread가 1개씩 접근을 진짜 못하는가??

   그렇다.

   > First, it is not possible for two invocations of synchronized methods on the same object to interleave. When one thread is executing a synchronized method for an object, all other threads that invoke synchronized methods for the same object block (suspend execution) until the first thread is done with the object.

2. `synchronized statament`로는 가능한게 진짜 맞는가?

   그렇다.

   > Instead of using synchronized methods or otherwise using the lock associated with `this`, we create two objects solely to provide locks.

   이거의 결과로 `lock1`, `lock2`라는 lock을 위한 객체를 생성했던 것이다. 그리고 위 코드를 실제로 실행시켜보면 statement로 선언된 코드에서는 `, 이 때 cX = ${c!}` 출력에서 이전에 나왔던 `c!`와는 다른 값을 보여준다.

<br>

## Reentrant Synchronization

thread는 다른 thread가 소유하고 있는 객체의 intrinsic lock을 `acquire()`할 수 없다. **그러나!!** **'자신이 이미 소유하고 있는 lock에 대해서는'** `acquire()`가 가능하다. `reentrant synchronization`을 가능하게 해준다는 것이다.

동기화된 코드가 직/간접적으로 동기화된 코드를 포함하는 method를 호출하면서 이 2개의 코드가 모두 같은 lock을 사용하게 되는 것이다. 

<br>

## Atomic Access

> atomic operation : interleaving 될 수 없는 연산. 기능적으로 분할 할 수 없거나 분할되지 않게 보장된 연산. - [출처](https://vaert.tistory.com/39)

따라서 연산 사이에 다른 연산들이 끼어들 수 없기 때문에 `thread interference`가 없다. 이전에 thread 간섭에 대해 말했을 때 `c++`에 대한 코드를 자세히 보도록 하자.

```java
public class ByteCodeTest {
	int c1 = 0;

	public static void main(String[] args) {
		ByteCodeTest test = new ByteCodeTest();
		test.incrementMethod();
		System.out.println("c1 = " + test.c1);
	}

	public void incrementMethod() {
		c1++;
	}
}
```

```assembly
public void incrementMethod();
  Code:
     0: aload_0
     1: dup
     2: getfield      #2                  // Field c1:I
     5: iconst_1
     6: iadd
     7: putfield      #2                  // Field c1:I
    10: return
```

`javap -c`로 disassembler로 `incrementMethod()`를 확인했을 때 결과이다. `c1++`이라는 한 줄의 코드이어도 여러개의 bytecode로 이뤄짐을 알 수 있다. 따라서 해당 코드는 atomic한 연산이 아니다.

atomic에 대해서는 아래와 같이 설명되어 있다.

- `long`, `double`를 제외한 모든 primitive type과 reference variable의 read/write는 atomic하다.
- `volatile`로 선언된 모든 변수들의 read/write는 atomic 하다.

atomic 연산은 thread 간섭이 없지만 여전히 memory consistency error는 발생할 수 있다. `volatile`변수에 대한 write는 뒤따르는 해당 변수에 대한 모든 read와 `happens-before`관계를 형성해준다. 따라서 `volatile`변수에 대한 모든 변경사항은 다른 thread에서 항상 인지 가능해진다. 그러나 2개의 thread가 공유자원에 접근하는 때에는 연산에 대한 lock을 제공하지 않기 때문에 `synchronized` 키워드와 같이 쓰여야 하거나 `java.util.concurrent` package에 있는 AtomicInteger class같은걸 쓰면 된다.

동기화된 코드보다 atomic한 접근이 더 간단하고 효율적이지만 memory consistency error를 피하기 위해 좀 더 많은 주의가 필요하다.

<br>

## Liveness

> A concurrent application's ability to execute in a timely manner is known as its *liveness*.

적절한 시기에 concurrent application을 실행할 수 있는 기능을 뜻한다. 자주 발생하는 liveness problem인 `deadlock`과 함께 `starvation`, `livelock`에 대해 알아볼 것이다.

<br>

## Deadlock

> 교착상태. 2개 이상의 thread가 영원히 서로를 기다리는 상황. (서로 필요한 자원이 상대방에게 있기 때문에 하염없이 기다리기만 해야 한다.)

```java
public class Deadlock {
	public static void main(String[] args) {
		final Friend alphonse =
				new Friend("Alphonse");
		final Friend gaston =
				new Friend("Gaston");
		new Thread(() -> alphonse.bow(gaston)).start();
		new Thread(() -> gaston.bow(alphonse)).start();
	}

	static class Friend {
		private final String name;

		public Friend(String name) {
			this.name = name;
		}

		public String getName() {
			return this.name;
		}

		public synchronized void bow(Friend bower) {
			System.out.format("%s: %s"
							+ "  has bowed to me!%n",
					this.name, bower.getName());
			bower.bowBack(this);
		}

		public synchronized void bowBack(Friend bower) {
			System.out.format("%s: %s"
							+ " has bowed back to me!%n",
					this.name, bower.getName());
		}
	}
}
```

[여기서](https://docs.oracle.com/javase/tutorial/essential/concurrency/deadlock.html) 제공해주는 코드이다. `Alphonse`가 `Gaston`에게 bow를 하면, 마찬가지로 `Gaston`도 bow를 한다. 그리고 누구 한명이 다시 원래대로 돌아올 때 나머지 한명도 돌아오기로 하는 코드다. 하지만 문제점이 생긴다.

> 서로 인사를 하고, 누가 먼저 원래대로 돌아와야 하는가...?

위 코드는 보다시피 동기화된 코드이다. 따라서 아무런 문제가 없을 것 같지만 실행시켜 보면 끝까지 실행이 안된다.

```shell
Alphonse: Gaston  has bowed to me!
Gaston: Alphonse  has bowed to me!
(멈춤)
```

서로 `bower.bowBack()`method에 접근하려고 해서 그런 것인데, 상대방 객체의 intrinsic lock을 얻으려고 보니 이미 누가 쓰고 있었기에 기다리게 되는 것이었다. [예전에 포스팅](https://xi-jjun.github.io/2021-05-10/kocwOS_14)한 deadlock에 관한 글도 참고바란다. 해결 방법은 [여기](https://xi-jjun.github.io/2021-05-12/kocwOS_15)를 참고 바란다. 마찬가지로 예전 포스팅 내용이다.

<br>

## Starvation

> thread가 공유 자원에 대한 접근을 못하게 되는 것.

접근을 못하게 되는것인데 deadlock과는 이유가 다르다. thread에 우선순위에 의해 할당되는 시간과 순서가 다 다를 수 있다. 그런 상황에서 가장 우선순위가 낮은 thread가 있다고 해보자. 해당 thread가 실행되려고 할 때 다른 더 높은 우선순위의 thread가 실행된다면, 그리고 그 과정이 반복된다면 우선순위가 낮은 thread는 CPU Time을 할당 못받아서 굶을 것이다. 이를 `starvation`이라고 한다. 

<br>

## Livelock

> A, B 2명의 사람이 골목길에서 만났다고 가정해보자. 골목길은 사람 2명이 아슬아슬하게 동시에 지나갈 수 있는 폭을 가졌다.
>
> A가 B를 피해 지나가려고 피하려는 순간, 
>
> B도 A를 피하게 되고,
>
> 그에 따라 A가 다시 B를 피하려는 순간,
>
> B도 다시 A를 피하게 되고....(반복)

이러한 상황을 `livelock`이라고 한다. `deadlock`은 thread가 blocked되어 더 이상의 진행이 불가능하다는 것이었는데, `livelock`에서는 blocked된게 아니라 서로 busy wait를 하고 있다는 차이점이 있다.

위 `deadlock`에서 [예전 포스팅](https://xi-jjun.github.io/2021-05-12/kocwOS_15)에서 prevention 부분 '식사하는 철학자'문제를 해결하는 방안으로 **"젓가락을 하나 못집었다면 내려놓기"**라는 전략을 제시했는데 이는 `livelock`을 발생시킬 수 있다.

> 한번에 모든 철학자가 젓가락을 들고, 내려놓고를 반복하게 될 수 있기 때문이다.

<br>

# High Level Concurrency Object

## Lock Object

```java
public class Counter{
  private int count = 0;

  public int inc(){
    synchronized(this){
      return ++count;
    }
  }
}
```

우리는 이전까지 `synchronized statement`를 만들 때 위와 같이 한다고 알고 있었다. 더 정교한 제어를 위해서는 `this`말고 다른 `object`를 넣어줬었다. 하지만 이제 그럴 필요 없이

```java
public class Counter{
  private Lock lock = new ReentrantLock();
  private int count = 0;

  public int inc(){
    lock.lock();
    int newCount = ++count;
    lock.unlock();
    return newCount;
  }
}
```

이렇게 `lock`을 쓰면 된다.

<br>

### 간단한 Lock 구현

```java
public class MyLock{
  private boolean isLocked = false;

  public synchronized void lock()
  throws InterruptedException{
    while(isLocked){
      wait();
    }
    isLocked = true;
  }

  public synchronized void unlock(){
    isLocked = false;
    notify();
  }
}
```

위와 같이 while loop로 기다리게 하는 것을 `spin-lock`이라고 한다.

<br>

## Executors

이전까지의 간단한 프로그램들 정도야 `Thread`나 `Runnable` interface를 구현해서 thread를 실행시켰다. 그러나 large-scale application에서는 thread관리와 생성의 책임을 application과 분리시키는게 좋다. 이러한 객체를 encapsulate한게 `Executors`이다.

<br>

## Executor Interfaces

`java.util.concurrent` package에서 제공되는 interface들이다.

1. `Executor` interface : 새로운 작업 시작을 지원하는 간단한 interface. 

   ```java
   package java.util.concurrent;
   
   public interface Executor {
       void execute(Runnable command);
   }
   ```

   `execute()` method를 구현하여 `Runnable` 객체를 받아서 사용한다.

2. `ExecutorService` interface : `Executor`의 subinterface. 개별 작업과 `Executor`자체의 life cycle을 관리하는데 도움되는 기능이 추가됐다.

   ```java
   import java.util.Collection;
   import java.util.List;
   
   public interface ExecutorService extends Executor {
       void shutdown();
   
       List<Runnable> shutdownNow();
   
       boolean isShutdown();
   
       boolean isTerminated();
   
       boolean awaitTermination(long timeout, TimeUnit unit)
           throws InterruptedException;
   
       <T> Future<T> submit(Callable<T> task);
   
       <T> Future<T> submit(Runnable task, T result);
   
       Future<?> submit(Runnable task);
   
       <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
           throws InterruptedException;
   
       <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks,
                                     long timeout, TimeUnit unit)
           throws InterruptedException;
   
       <T> T invokeAny(Collection<? extends Callable<T>> tasks)
           throws InterruptedException, ExecutionException;
   
       <T> T invokeAny(Collection<? extends Callable<T>> tasks,
                       long timeout, TimeUnit unit)
           throws InterruptedException, ExecutionException, TimeoutException;
   }
   ```

   

3. `ScheduledExecutorService` interface : `ExecutorService`의 sub interface. 

   ```java
   public interface ScheduledExecutorService extends ExecutorService {
       public ScheduledFuture<?> schedule(Runnable command,
                                          long delay, TimeUnit unit);
   
       public <V> ScheduledFuture<V> schedule(Callable<V> callable,
                                              long delay, TimeUnit unit);
   
       public ScheduledFuture<?> scheduleAtFixedRate(Runnable command,
                                                     long initialDelay,
                                                     long period,
                                                     TimeUnit unit);
   
       public ScheduledFuture<?> scheduleWithFixedDelay(Runnable command,
                                                        long initialDelay,
                                                        long delay,
                                                        TimeUnit unit);
   
   }
   ```

   실행하는 시기를 정해줄 수 있다.

<br>

## Thread Pools

Process 내 thread의 생성 및 수거는 kernel object를 동반하는 리소스이기 때문에 많은 비용이 든다. 그래서 `Thread Pool`이 등장했다.

> `Thread Pool` : 미리 thread의 사용자가 설정해둔 개수만큼 생성해두고 thread를 사용하기 위함.

- 장점 
  1. Thread 생성 비용 줄어듦.
  2. Context-switching 때 delay 줄어든다.
- 단점 
  1. 너무 많이 만들어 놓으면 메모리가 낭비된다.

<br>

- `corePoolSize` : 생성할 thread 개수
- `maximumPoolSize` : 생성할 thread의 최대 개수
- `keepAliveTime` : 유지할 시간
- `execute()` method : 작업 요청 method. 예외 발생시 해당 thread종료 및 thread pool에서 제거. 새로운 thread 다시 생성하여 다른 작업 처리. 처리결과는 반환 안한다.
- `submit()` method : 작업 요청 method. 예외 발생시 해당 thread종료 안하고 다음 작업에 사용. 처리결과는 `Future<?>`로 반환. 따라서 `execute()`method보단 `submit()`쓰는게 더 좋다고 한다.
- `Executors.java`
  - `newFixedThreadPool(int nThread)` : nThread만큼의 고정된 크기의 thread pool생성.
  - `newCachedThreadPool()` : 초기 thread 개수 0개. 적업 요청이 들어오고, 그 때 thread개수보다 요청이 많으면 thread 생성. 작업 끝난 thread에 대해서 60초 동안 아무것도 안하면 해당 thread 제거.
  - `newScheduledThreadPool(int corePoolSize)` : thread를 일정 시간이 지나고 실행되게 하는 기능들을 제공.

<br>

## Fork/Join Framework

효율적인 병렬처리를 위해서 사용한다.

> fork : 어떤 작업을 여러 thread가 나눠서 진행하고, 
>
> join : 끝나면 다시 합치는 것.

`RecursiveTask<T>` or `RecursiveAction`을 상속받아 구현한다. 반환값이 존재하냐의 차이이다.

<br>

## Concurrent Collections

`Collections` framework는 대부분 single thread상황만 고려되었다. `Vector`, `Hashtable`등만 동기화로 구현되어 thread-safe하다. 그러나 `thread`들이 작업을 할 때 lock이 걸리기 때문에 **병렬적 처리**가 안된다. 따라서 `concurrent Collections`를 사용한다.

- 동기화 기능 : 하나의 thread가 접근시 다른 thread에서 접근을 못하게 block한다.
- `Concurrent Collections` : 보관하는 data를 여러부분들로 lock을 걸어서 다른 부분들에 대해서는 접근이 가능하게 만듦.

<br>

## Reference

[Java JDK 8 docs](https://docs.oracle.com/javase/tutorial/essential/concurrency/locksync.html)

[Java Intrinsic Lock](http://happinessoncode.com/2017/10/04/java-intrinsic-lock/)

[Atomic Operation](https://vaert.tistory.com/39)

[Java Bytecode Inspection](https://starblood.tistory.com/entry/Java-byte-code-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0)

[volatile variable, visiability](https://parkcheolu.tistory.com/16)

[Lock Object](https://parkcheolu.tistory.com/24?category=654619)

[Thread Pool - Tecoble](https://tecoble.techcourse.co.kr/post/2021-09-18-java-thread-pool/)



