---
layout: post
title: "JAVA - Collection Frameworks1"
author: "xi-jjun"
tags: Java


---

# Java

Java 공부를 정리 tag

<br>

[JDK 8 Collections: Java docs](https://docs.oracle.com/javase/tutorial/collections/index.html)를 공부하며 정리하는 글입니다.

<br>

# Collection Frameworks

> A *collection* — sometimes called a container — is simply an object that groups multiple elements into a single unit. Collections are used to store, retrieve, manipulate, and communicate aggregate data.

해석해보자면 `Collection`이란,  '여러 element를 하나의 단위로 그룹화하는 객체'라는 것이다. 말로만 들으면 와닿지 않을 수 있다. 하지만 우리는 Java로 코딩하면서 이미 Collections 를 많이 사용해봤다.

```java
List<Integer> list = new ArrayList<>();
```

`List`객체는 정렬할 때, 그리고 크기가 정해져있지 않은 데이터의 배열을 다룰 때 정말 자주 쓰인다. 이처럼 우리는 이미 Collections에 친숙하다.

<br>

> Collection Framework : `Collections`를 나타내고 조작하기 위한 통합 architecture 이다.

모든 Collections Frameworks는 아래 3가지를 포함한다.

1. Interfaces : `collections`를 표현하기 위한 abstract data type 이다. Interfaces를 통해 `collections`가 그들의 세부 표현을 독립적으로 조작할 수 있게 해준다. OOP에서 interface는 대게 계층형이다.
2. Implementations : Collection interface들의 구현체. 본질적으로 재사용 가능한 data structure.
3. Algorithms : Collection interface를 구현하는 객체에 대해 검색과 정렬과 같은 유용한 연산을 하는 method가 있다. algorithm는 *polymorphic*하다고 말할 수 있다. 왜냐면 같은 method가 적절한 Collection interface의 구현체에 사용될 수 있기 때문이다. 본질적으로 algorithm은 재사용 가능한 기능이다.

<br>

## 왜 Java Collection Framework 를  써야 하는가?

> 그냥 `int[][] arr = new int[2][3];`과 같이 기존에 있던 것을 계속 쓰면 안되는가? 

라고 생각했다. Java docs에는 `Collection Frameworks`를 쓰면 다음과 같은 장점이 있다고 말한다.

- **Reduces programming effort:** 프로그래밍에 드는 수고를 줄여준다고 한다. Collections Framework가 유용한 자료구조와 알고리즘을 제공해서 `sort`와 같은 low level 프로그래밍 말고, 더 중요한 프로그램의 business logic에 집중할 수 있도록 해준다고 한다. 또한, 관련없는 API들과 상호운용을 가능하게 해서 adapter API같은걸 쓸 필요가 없다.

- **Increases program speed and quality:** Collections Framework은 높은 퀄리티의 유용한 자료구조와 알고리즘의 구현체를 제공하여 높은 성능을 제공한다. 각 interface의 다양한 구현체들은 상호 호환이 가능하기 때문에 프로그램은 collection 구현체만 바꿔서 쉽게 조정될 수 있다. 자료구조를 귀찮게 일일이 만들지 않아도 되기 때문에 프로그램의 질과 성능을 높이는데 집중할 수 있게 해준다.

  ```java
  // List interface, impl : ArrayList, LinkedList, Vector
  List<Integer> arrayList = new ArrayList<>();
  List<Integer> linkedList = new LinkedList<>();
  // Set interface, impl : HashSet, TreeSet, LinkedHashSet
  Set<Integer> hashSet = new HashSet<>();
  Set<Integer> treeSet = new TreeSet<>();
  Set<Integer> linkedHashSet = new LinkedHashSet<>();
  ```

  하나의 interface에 여러 구현체들을 만들어서 상호 호환이 가능하게 만들었다.

- **Allows interoperability among unrelated APIs:** collection interfaces는 collections를 앞 뒤로 전달하는 API 표준이다. 각각 독립적으로 작성됐을지라도, API는 원활하게 동작할 것이다.

- **Reduces effort to learn and to use new APIs:** 예전에는 Collections같은걸 조작하는 작은 sub-API가 있었는데 일관성이 거의 없어서 처음부터 하나하나 배웠어야 했다. 하지만 Java Collection framework의 등장으로 각각의 interface별로 사용법을 공부하면 되기에 배우기가 쉬워졌다고 한다.

- **Reduces effort to design new APIs:** 개발자들은 collections에 의존하는 API를 만들 때 뼈대를 다시 만들 필요가 없다. 그냥 collection interface사용해서 구현체를 만들면 되기 때문이다. 따라서 새로운 API를 디자인하는데 수고를 줄여준다.

- **Fosters software reuse:** collection interface 표준을 준수하는 자료구조는 기본적으로 재사용이 가능하다. 이러한 interfaces를 구현하는 객체에서 동작하는 새로운 알고리즘도 재사용이 가능하다.

만약 Collection framework가 없다고 해보자. 우리는 int array의 중간에 삽입 연산을 하고 싶다면, 해당 index~끝까지 element를 새로운 배열에 옮기고, 새로운 배열의 해당 index에 삽입하고... 등등 매번 이런 귀찮은 작업을 해야할 것이다. 개발자는 중요한 프로그램의 logic에 집중하고, 이와 같은 나머지 low-level plumbing은 Java Collection Framework가 해준다는 것이다.

<br>

## Lesson: Interface

![collection1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/java/img/collection1_1.png?raw=True){: width="65%"}

<sub>[사진출처](http://dawoonjeong.com/java-jcf/)</sub>

`core collection interfaces`는 Java Collections Frameworks의 기반이며 위 그림에서와 같이 다른 type의 collections를 캡슐화 한다.

- 그렇다면 왜? 굳이? 저렇게 자잘자잘 나눠놓은 것인가?

  위에서도 언급했었지만, 하나의 interface에 여러 구현체를 만들 수 있기 때문에 구현 세부사항을 독립적으로 조작할 수 있다. 만약 interface에 구현체를 만들길 원한다면 우리가 구현체를 만들어도 되는 것이다. 

- 아니 도대체 왜 구현체로 안만들고 interface로 만든건가?

  이거는 말로 설명하는 것 보단 눈으로 보는게 더 빠르다.

  ```java
  // Class Node
  public class Node {
    public int value;
    public Node parent;
    public ArrayList<Node> childNodes = new ArrayList<>();
  }
  ```

  Node라는 클래스가 있다고 가정해보자. 해당 Node 클래스는 `ArrayList` 라는 Collections를 사용하고 있다. 근데 어떤 클래스에서 해당 Node 클래스의 `childNodes`정보를 parameter로 받는 method가 있다고 가정해보자.

  ```java
  // 어떤 Class...
  ...(생략)
  public Node someProcess(ArrayList<Node> nodes) {
    ...
  }
  ...(생략)
  ```

  위와 같은 method의 parameter는 당연하게도 `childNodes`의 type인 `ArrayList`이다. 하지만 여기서 하나의 문제가 생겼다고 가정해보자. 

  > `childNodes`에 대한 데이터 추가/삭제 작업이 너무 많아서 `LinkedList`구현체로 바꿔야 하는 상황.

  그렇다면 우리는 코드를 수정해야 한다...

  ```java
  public LinkedList<Node> childNodes = new LinkedList<>();
  
  public Node someProcess(LinkedList<Node> nodes)
  ```

  자. 아까 전의 코드에서 `ArrayList` 부분을 `LinkedList`로 바꿔줬다. 이로써 문제를 해결한 것이다....?

  > 만약에 `childNodes`가 method parameter로 1000여곳에서 쓰인다면 어떨까.

  아까 전처럼 다시.... 하나하나 모든 코드를 바꿀 수는 없다. 과연 어떻게 해야할까?

  우리가 지금 '하나하나 코드를 다 바꿔줘야 하는 이유'에 대해 생각해보자. 

  - 왜 바꿔줘야 하는가? → 다른 자료구조 구현체를 쓰기 위해서

  - 그렇다면 `public ArrayList<Node> childNodes = new ArrayList<>();` 부분만 바꾸면 되는거 아닌가? 

    → 당연히 그러면 오류난다. `LinkedList`로 해당부분만 바꾸게 되면 해당 데이터를 parameter로 받는 다른 method에서 오류가 난다.

  - 왜 오류가 나는가?

    → 당연히 'data type'이 달라서 그렇지...

  > 만약 ArrayList, LinkedList 구현체를 같은 data type 아래에 둘 수 있다면...?

  따라서 등장한게 `interface`라는 개념인 것이다. `List` 라는 interface에 `ArrayList, LinkedList`라는 구현체를 만들어서 코드의 유지보수성을 높인 것이다.

  이 말고도 다중 상속이라든지... interface의 장점이 있지만 나중에 더 자세히 다루도록 하겠다.

<br>

> Note that all the core collection interfaces are generic.

그리고 모든 `core collections interfaces` generic 이 쓰였다고 한다. 따라서 Collection instance를 선언할 때 반드시 object data type을 명시해줘야 한다. 왜냐하면 compiler가 compile-time 때 collection 안에 적절한 type이 들어갔는지 확인하기 위해서이다. 따라서 run-time에 오류를 줄일 수 있다. 현재 포스팅은 `Generic`을 다루는게 아니라서 더 자세히는 나중에 포스팅 하도록 하겠다.

<br>

collection interfaces 수를 관리하기 위해 Java 는 각 collection type에 대한 별도의 interface를 제공하지 않는다. 대신 각 interfaces의 수정에 관한 동작은 선택이다. 따라서 구현체가 모든 동작을 지원안할 수도 있다. 만약 지원하지 않는 동작이 호출되려고 하면, collection이 `UnsupportedOperationException`을 던진다고 한다. 따라서 구현체들은 선택적으로 지원하는 동작에 대한 정보를 알려줄 책임이 있다.

<br>

다음은 `core collections interfaces`에 대한 설명이다.

- Collection 
  - collection 계층의 root. 
  - 같은 element type으로 이뤄진 객체의 그룹.
  - 모든 collections가 구현하는 공통 부분이며 collections를 전달하거나 조작하기 위해 사용된다.
  - Java는 Collection interface에 대한 구현체를 제공하지는 않지만, 그 하위의 interface(ex] List, Set...)에 대해서는 구현체를 제공한다.
- Set : 중복되는 요소를 포함하지 않는다.
- List : 순서있는 collection. 중복되는 요소를 포함한다. Insert나 index접근을 위해 사용된다.
- Queue, Deque : processing하기 전 다수의 요소들을 대기시키기 위해 사용된다. 기본적인 Collection 동작 말고도, 추가적인 삽입, 추출, 조회에 관한 동작을 제공한다.
- Map : `key-value` pair의 객체. 중복되는 key를 갖지 못한다.
- SortedSet, SortedMap : Set, Map의 정렬된 버전.

<br>

# Collection Interface

> Collection : 어떤 element type 객체들의 그룹. 객체들의 collections를 전달하기 위해 사용된다. 모든 

```java
public static void main(String[] args) {
    Collection<String> collection = new ArrayList<>(Arrays.asList("Hello", "Java", "Collections", "Frameworks"));
    List<String> list = new ArrayList<>(collection);

    for (String element : list) {
      System.out.print(element + " ");
  }
}
```

간단하게 `Collection`으로 String 객체들의 collections을 전달해본 코드이다.

`Collection Interface`는 기본 동작을 수행하는 method를 제공한다.

- `int size()`, `boolean isEmpty()`, `boolean contains(Object element)`, `boolean add(E element)`, `boolean remove(Object element)`, and `Iterator<E> iterator()`

`Collections` 전체에 대해 동작하는 method 또한 제공된다.

- `boolean containsAll(Collection<?> c)`, `boolean addAll(Collection<? extends E> c)`, `boolean removeAll(Collection<?> c)`, `boolean retainAll(Collection<?> c)`, and `void clear()`.

Array 동작에 관한 추가적인 method도 제공한다.

- `toArray()`

<br>

- JDK 8 이상의 버전에서는 `Stream` 기능을 제공한다.

- `Collection` interface는 얼마나 많은 element가 있는지(`size, isEmpty`), 주어진 객체가 collection에 존재하는지(`contains`), collection에 element를 추가하거나 삭제할 수 있는 method(`add, remove`)를 제공합니다. 그리고 collection을 순회할 수 있는 기능(`iterator`)도 제공합니다.

  - Iterator..? 그런거 본 적 없는데 어떻게 제공하는 것이냐

    써본적이 거의 없어서 있는 줄도 몰랐다. 하지만 애초에 `Collection` interface는 `Iterable` interface를 상속받는다.

    ```java
    public interface Collection<E> extends Iterable<E> {
      ....
    ```

    왜 갑자기 저런 interface를 상속 받는지 궁금했다. 잘 생각해보면 우리는 Collection 구현체에서 element를 추가, 삭제, 조회 할 때 너무나도 당연하게 해당 data들을 순회하여 조사해야 하기 때문에 필요한 것이다.

- `add` method : 중복을 허용하는 것뿐만 아니라 허용안하는 collections 둘 다에 적합하도록 정의된다. 해당 method가 호출이 된 후에 지정된 element를 추가하는 행위가 보장된다. 변경되면 return true.

- `remove` method : collections에서 지정된 element 제거. 제거의 결과로 collections가 수정되면 return true.

<br>

## Traversing Collections

collections를 순회하는데 3가지 방법이 존재한다.

 - aggregate operation

   JDK 8 이상에서 collections traversing에는 collections에 대한 `stream`을 가져와서 `aggregate operation`을 수행하는게 선호된다고 한다.

   - Aggregate operation이란?

     > 여러개의 행의 값들이 하나의 요약된 값을 형성하게 해주는 기능. - [wiki](https://en.wikipedia.org/wiki/Aggregate_function)

     평균, 최대값, 최소값 등등을 구해주는 함수라고 생각하면 될 것 같다.

   ```java
   List<Something> myCollections = new ArrayList<>();
   ...(대충 myCollections에 어떤 정보를 넣는 작업)...
     
   myCollections.stream()
     .filter(element -> myFilteringProcess(element.attribute))
     .forEach(element -> System.out.println(element.getName()))
   ```

   위 코드를 잠깐 설명해보자면, `myCollections`에서 `stream`을 가져와서 내가 원하는 element 정보만 추출하기 위해 `myFilteringProcess()`를 사용했고, 그 결과를 `forEach()`로 출력하는 코드이다. 더 자세한 설명은 나중에 `stream`을 다루게 된다면 해보도록 하겠다. 지금은 코드가 더 명확하고 간결해진다는 장점이 있음을 기억하자.

   <br>

 - For-each

   ```java
   Collection<Object> collections = new 어떤 collections 구현체;
   for(Object obj : collections) {
     System.out.println(obj);
   }
   ```

   `for-each`는 해당 collections의 처음부터 끝까지 순회할 수 있게 해준다. index에 의한 접근을 할 수가 없기에 데이터를 수정하는 일에는 적합하지 않지만, index에 접근을 하여 읽게 될 때는 실수가 생길 수 있기에 데이터 조회 작업에서는 `for-each`를 선호한다고 한다.

   [해당 블로그(effective-java)](https://itstory.tk/entry/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EA%B7%9C%EC%B9%9946-for-%EB%AC%B8%EB%B3%B4%EB%8B%A4%EB%8A%94-foreach-%EB%AC%B8%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)에서는 

   >요약하자면, `for-each` 문은 전통적인 for 문에 비해 명료하고 버그 발생 가능성도 적으며, 성능도 for 문에 뒤지지 않는다. 그러니 가능하면 항상 사용해야 한다. 

   라고 말하고 있다. 조회할 때는 `for-each`를 쓰도록 하자.

   <br>

 - Iterators

   > `Iterator`는 collection을 탐색하면서 선택적으로 element를 제거할 수 있는 객체. - [Java SE 8 docs](https://docs.oracle.com/javase/tutorial/collections/interfaces/collection.html)

   평소에 `Iterator`를 써본적이 없어서 한번 [테스트 코드](https://github.com/xi-jjun/data-structure-and-algorithm/blob/master/src/javastudy/collectionframework/collectionInterface/IteratorTest.java)를 만들어서 실행해봤다.

   Iteration 도중에 collection의 element를 안전하게 제거할 수 있는 방법은 `Iterator`의 `remove()` method라고 한다. iteration 중에 다른 방법으로 collection element를 제거하려고 한다면 되지 않는다.

   > `for-each`는 `iterator`를 숨기고 있기 때문에 `remove` method를 호출할 수 없어서 filtering 작업에 적합하지 않다.

   어떻게 숨겨져 있는지 궁금해서 찾아봤다. [for-each Docs](https://docs.oracle.com/javase/specs/jls/se8/html/jls-14.html#jls-14.14.2)에서는 `for-each`는 아래와 같은 의미를 가진다고 한다.

   ```java
   for (I #i = Expression.iterator(); #i.hasNext(); ) {
       {VariableModifier} TargetType Identifier =
           (TargetType) #i.next();
       Statement
   }
   ```

   `Expression`이 `Iterable`의 subtype일 때 위 와 같이 동작한다고 한다. 따라서 `iterator`가 내부에 숨겨져 있기에 `remove`를 호출할 수 없다.

<br>

## Collection Interface Bulk Operations

- `containsAll` — returns `true` if the target `Collection` contains all of the elements in the specified `Collection`.
- `addAll` — adds all of the elements in the specified `Collection` to the target `Collection`.
- `removeAll` — removes from the target `Collection` all of its elements that are also contained in the specified `Collection`.
- `retainAll` — removes from the target `Collection` all its elements that are *not* also contained in the specified `Collection`. That is, it retains only those elements in the target `Collection` that are also contained in the specified `Collection`.
- `clear` — removes all elements from the `Collection`.

이 중에서 `addAll(), removeAll(), retainAll()`method는 실행결과 해당 Collection에 변화가 생겼다면 모두 return true. [예시](https://docs.oracle.com/javase/tutorial/collections/interfaces/collection.html)도 들어 줬는데 한번 보도록 하자.

```java
Collection<Something> c = new 어떤 collection 구현체;
c.removeAll(Collections.singleton(e));
```

**Collection c에서 e에 해당하는 모든 element를 제거**하는 코드이다

<br>

## Collection Interface Array Operations

`toArray()` method는 옛날 API와 Collections를 이어주는 다리같은 역할로써 제공된다.

<br>

다음 포스팅은 **Set Interface**부터 이어나가겠다.

<br>

## Rerference

[Java JDK 8 docs - Collections](https://docs.oracle.com/javase/tutorial/collections/index.html)

[what is low level plumbing?](https://stackoverflow.com/questions/41670177/what-is-low-level-plumbing-in-programming-field)

[effective-java: for-each vs for](https://itstory.tk/entry/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-%EA%B7%9C%EC%B9%9946-for-%EB%AC%B8%EB%B3%B4%EB%8B%A4%EB%8A%94-foreach-%EB%AC%B8%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC)

[Java SE 8 docs: enhanced for(for-each)](https://docs.oracle.com/javase/specs/jls/se8/html/jls-14.html#jls-14.14.2)

Wikipedia



