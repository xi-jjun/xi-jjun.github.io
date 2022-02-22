---
layout: post
title: "JAVA - Collection Frameworks2"
author: "xi-jjun"
tags: Java


---

# Java

Java 공부를 정리 tag

<br>

[JDK 8 Collections: Java docs](https://docs.oracle.com/javase/tutorial/collections/index.html)를 공부하며 정리하는 글입니다.

<br>

지난 시간까지 `Collection Interfaces`에 대해 알아봤다. 이제 각각의 interface들을 알아볼 것이다

<br>

# Set Interface

> 중복 element를 허락하지 않는 Collection.

- `Set` interface에는 `Collection`으로부터 상속받은 method만 포함되어 있다. (실제로 확인해보니 `of`, `copyOf` 빼고 모두 `Collection` interface로 부터 상속받은 함수들이다) 

- 중복되는 element 금지라는 제한이 추가된다.

- `equals()`와 `hashCode()`연산에 대해 더 강한 계약을 추가한다.

  → 구현 type이 다르더라도 `Set` instance는 의미있게 비교될 수 있다. 따라서 2개의 `Set` instance가 똑같은 element들을 가진다면, 동일한 instance라고 한다. - [Java Set interface docs](https://docs.oracle.com/javase/tutorial/collections/interfaces/set.html)

- Java platform에서는 3가지 `Set` 구현체를 제공한다. `HashSet`, `LinkedHashSet`, `TreeSet`. hash-table에 element를 저장하여 좋은 성능을 제공하는 구현체들이다. 그리고 기본적으로 element의 순서를 보장하지 않는다.

  - TreeSet : `red-black tree`에 element를 저장. element의 value를 기준으로 순서가 정렬된다. `HashSet`보다 실질적으로 느리다.
  - LinkedHashSet : element가 추가되는 순서대로 저장된다. `LinkedList`로 이뤄진 hash-table로 구현됐기 때문이다.

<br>

## Examples

```java
Collection<Type> noDups = new HashSet<Type>(c);
```

Collection c가 있다고 가정하자. c에서 중복을 없애고 싶다면 위와 같은 코드를 쓰면 된다. 그리고 Collection interfaces들의 모든 constructor들은 `Collection` type를 parameter로 받기 때문에 우와 같은 코드가 가능하다.

<br>

```java
Set<String> set = people.stream()
  .map(Person::getName)
  .collect(Collectors.toCollection(TreeSet::new));
```

Java 8 이후의 버전에서는 위와 같이 `TreeSet`으로 set instance를 stream으로 가져와서 초기화해줄 수 있다.

<br>

## Set Interface Basic Operations

- `size` : `Set`에 있는 element 개수를 반환.
- `isEmpty` : 당신이 생각하는 바로 그 기능! element가 1개도 없으면 true 반환. 그 외에는 return false.
- `add` : `Set`에 element 추가. 추가가 됐으면 return true. 이미 해당 element가 존재해서 추가가 안됐다면 return false.
- `remove` : `Set`에서 element 제거. 제거가 됐으면 return true. 이미 해당 element가 존재하지 않아서 제거가 안됐다면 return false.
- `iterator` : 해당 `Set`의 `iterator` 반환.

[Set에 관한 테스트 코드](https://github.com/xi-jjun/data-structure-and-algorithm/blob/master/src/javastudy/collectionframework/setInterface/SetTest.java)를 작성해봤다. 위 method들을 다 사용한건 아니고 궁금한 것 위주로 사용했다.

<br>

## Set Interface Bulk Operations

bulk operation은 `Set`에 적합하다. `s1`과 `s2`를 Set이라고 가정하자. 다음과 같은 동작을 할 수 있다고 한다.

- `s1.containsAll(s2)` — returns `true` if `s2` is a **subset** of `s1`. (`s2` is a subset of `s1` if set `s1` contains all of the elements in `s2`.)
- `s1.addAll(s2)` — transforms `s1` into the **union** of `s1` and `s2`. (The union of two sets is the set containing all of the elements contained in either set.) 두 집합의 합집합이라고 생각하면 쉬울 것 같다.
- `s1.retainAll(s2)` — transforms `s1` into the intersection of `s1` and `s2`. (The intersection of two sets is the set containing only the elements common to both sets.) 두 집합의 교집합이라고 생각하면 쉬울 것 같다.
- `s1.removeAll(s2)` — transforms `s1` into the (asymmetric) set difference of `s1` and `s2`. (For example, the set difference of `s1` minus `s2` is the set containing all of the elements found in `s1` but not in `s2`.) 차집합을 생각하면 쉬울 것 같다.

[bulk operation code](https://github.com/xi-jjun/data-structure-and-algorithm/blob/master/src/javastudy/collectionframework/setInterface/BulkOperationTest.java)를 작성해봤다. (그냥 테스트 겸으로 어떤 느낌인지 알기 위해서 간단하게 작성한 것이다.)

다른 예시들도 있었는데 인상깊었던게 있어서 가져와봤다.

```java
Set<Type> symmetricDiff = new HashSet<Type>(s1);
symmetricDiff.addAll(s2); // symmetricDiff = s1 ∪ s2
Set<Type> tmp = new HashSet<Type>(s1);
tmp.retainAll(s2); // tmp = s1 ∩ s2
symmetricDiff.removeAll(tmp); // symmetricDiff = symmetricDiff - tmp 
```

`s1`, `s2` 둘 중 하나에는 무조건 포함되어 있지만, **동시에 2개의 집합에는 포함안되는 element**들의 집합을 표현한 것이다.

<br>

## Set Interface Array Operations

> The array operations don't do anything special for `Set`s beyond what they do for any other `Collection`.

`Set`에서 array operation은 특별한 동작을 하지 않는다고 한다.

<br>

<br>

# List Interface

> 순서가 있는 `Collection`. 중복되는 element를 가질 수 있다.

`List` interface는 `Collection`으로 부터 상속받은 기능말고도 다음과 같은 기능도 추가된다.

- `Positional access` : list에서 index로 element를 조작할 수 있다.

  ex) `get`, `set`, `add`, `addAll`, `remove`.

- `Search` : index 위치로 list 안의 특정 객체를 찾을 수 있다.

  ex) `indexOf`, `lastIndexOf`.

- `Iteration` : List의 순차적 특성의 장점을 위해 `Iterator`를 확장했다. 

  ex) `listIterator`

- `Range-view` : `sublist` method는 리스트에서 임의의 range 동작을 수행한다. ex) `subList(fromIndex, toIndex)`

- Java platform에서는 2가지의 `List` 구현체를 제공한다. 

  - `ArrayList` : 대게 좋은 성능을 보이는 List 구현체.
  - `LinkedList` : 특정 상황에서 더 나은 성능을 보이는 List 구현체.

<br>

## 왜 List를 쓸까

`Set`은 그래도 중복을 허용하지 않는다는 점에 있어서 기존에 있던(Collections Frameworks 이외의) 기능이 아니기에 쓰는 이유를 납득했다. 근데 `List`같은 경우는 `배열`이라는 객체가 존재하는데 굳이 만들어서 쓰는 이유가 뭔지 궁금했다.

```java
int[] arr = new int[4];
arr = addItem(arr, 100);
...

public int[] addItem(int[] arr, int item) {
  int[] newArr = new int[5]; // array size++
  newArr = Arrays.copyOf(arr, arr.length);
  newArr[newArr.length - 1] = item;
  return newArr;
}
```

배열 `arr`에 데이터 하나를 추가해보려고 한다. 따라서 `addItem()`이라는 함수를 만들었다. 다음으로는 `ArrayList`에서 `list`에 데이터 하나를 추가해보려고 한다.

```java
List<Integer> list = new ArrayList<>();
list.add(100);
```

둘의 차이가 느껴지는가? 데이터 하나를 추가하기 위해 배열에서는 직접 함수를 만들어서 크기가 더 큰 배열 선언해서 원래 값 복사하고 마지막 추가할 데이터 추가해준다음 해당 배열 반환... 반면에 리스트에서는 너무나도 간단하게 `Collection`으로부터 상속받은 `add()` method를 써서 해결했다. 개발자가 저런 자잘자잘한 일보다 더 중요한 로직을 신경 쓸 수 있게 해주는게 `Collection Frameworks`이기 때문에 `List`를 쓰는 것이다.

<br>

## Collection Operations

`List`의 동작은 `Collection`으로부터 상속받은 동작들이며 우리가 이전에 `Collection` interface를 공부하며 봤었던 기능이라 생각하면 된다고 한다.

- `remove` : 특정 index위치의 element 삭제. 삭제된 element를 return.

- `add`, `addAll` : 새로운 element또는 element들을 리스트의 끝에 추가. 추가에 성공하면 return true.

- `List`는 `equals`, `hashCode`method에 대한 요구조건을 강화하여 **'2개의 리스트가 동일한 순서의 element를 가졌다면 같다'**라고 판단한다.

- Java 8 이후부터는 `List`에서 `stream`을 가져와서 생성할 수도 있다.

  ```java
  List<String> list = people.stream()
    .map(Person::getName)
    .collect(Collectors.toList());
  ```

  `Person` type을 element로 하는 `people` 리스트에서 `이름`부분만 추출하여 다시 리스트를 만드는 과정이다.

<br>

## Positional Access and Search Operations

positional access에는 기본적으로 `get`, `set`, `add`,  `remove` 가 있다. 나머지에 대해서는 이미 `Set`에서 했었어서 알기 쉽지만 `set`이라는 method는 생소할 수 있다. 순서에 대한 정보를 가지는 `List`는 index로 접근이 가능하기에 '원하는 index에 element를 삽입'할 수 있는 기능인 `set` method가 있는 것이다.

```java
// ArrayList.java
public boolean addAll(Collection<? extends E> c) // 리스트 끝 부터 Collection c의 모든 element 삽입
public boolean addAll(int index, Collection<? extends E> c) // 리스트의 index부터 Collection c의 모든 element 삽입
```

`addAll` method는 특정 index부터 다른 리스트를 추가할 수 있는 기능도 있다. 그리고 `Collection` c 에서 제공하는 `iterator`의 순서를 기준으로 리스트에 c의 element들이 삽입된다고 한다.

<br>

## Arrays.asList

`Arrays` class에는 `asList()`라는 method가 있다. 잠깐 알아보고 가도록 하자.

```java
// Arrays.java
...
public static <T> List<T> asList(T... a) {
        return new ArrayList<>(a);
}


private static class ArrayList<E> extends AbstractList<E> implements RandomAccess, java.io.Serializable {...}
```

`ArrayList`를 생성하여 반환하는 것 처럼 보이지만, `List` interface 구현체인 `ArrayList`가 아닌, `Arrays` 내에서 선언된 `ArrayList`를 반환하는 것이다.

>The resulting List is not a general-purpose `List` implementation, because it doesn't implement the (optional) `add` and `remove` operations.

저번 포스팅에서 잠깐 언급했던 말인데, `add`, `remove` method가 `Arrays`의 `ArrayList`에는 구현되지 않았다. 따라서 

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
Iterator<Integer> iterator = numbers.iterator();

while (iterator.hasNext()) {
    // logic has remove method
    iterator.remove()
}

/* Exception in thread "main" java.lang.UnsupportedOperationException: remove
	 	at java.base/java.util.Iterator.remove(Iterator.java:102)
*/
```

```shell
Exception in thread "main" java.lang.UnsupportedOperationException: remove
	 	at java.base/java.util.Iterator.remove(Iterator.java:102)
```

위와 같은 코드는 오류가 발생하게 된다. `new ArrayList(Arrays.asList(1, 2, 3, 4, 5))`로 생성해주면 오류를 해결 할 수 있다.

마찬가지로 `add` method에서도 오류가 나타난다.

```java
List<Integer> asList = Arrays.asList(1, 2, 3 ,4);
asList.add(3000);
```

```shell
Exception in thread "main" java.lang.UnsupportedOperationException
	at java.base/java.util.AbstractList.add(AbstractList.java:153)
	at java.base/java.util.AbstractList.add(AbstractList.java:111)
	at javastudy.collectionframework.listInterface.ListTest.main(ListTest.java:85)
```

따라서 `Arrays.asList`는 배열을 `List`로 볼 수 있게 해주는 method이다. 그러나 그저 배열의 내용을 복사해주는 method가 아니라 리스트의 변경사항을 배열에 반영할 수 있고, 그 반대도 가능하다고 한다. 또한 배열의 크기는 바뀔 수가 없기 때문에 `add`, `remove` method가 구현되지 못한 것이다. [테스트 코드](https://github.com/xi-jjun/data-structure-and-algorithm/blob/master/src/javastudy/collectionframework/collectionInterface/IteratorTest.java)

> The [`Arrays`](https://docs.oracle.com/javase/8/docs/api/java/util/Arrays.html) class has a static factory method called `asList`, which allows an array to be viewed as a `List`. This method does not copy the array. Changes in the `List` write through to the array and vice versa. The resulting List is not a general-purpose `List` implementation, because it doesn't implement the (optional) `add` and `remove` operations: Arrays are not resizable. 

<br>

## Iterators

`List`의 iterator에 의해 반환된 Iterator는 리스트의 element를 적절한 순서로 반환하는 역할을 한다. 또한 `List`에서는 `ListIterator`라는 것을 제공하는데 양방향 탐색, 반복 도중의 수정, iterator의 현재 index위치도 얻을 수 있다.

- ListIterator
  - `hasNext`, `next`, `remove`는 `Iterator` class로 부터 상속받는 method이다.
  - `hasPrevious`, `previous` : `hasNext`, `next` 와 반대되는 동작이다. `cursor`를 뒤로 움직인다.
  - `set`, `add` method를 추가로 제공.

```java
// ArrayList.java
/* 
  Returns a list iterator over the elements in this list (in proper sequence), 
  starting at the specified position in the list.
*/
public ListIterator<E> listIterator(int index) 
...

/*
  Returns a list iterator over the elements in this list (in proper sequence).
*/
public ListIterator<E> listIterator()
```

2가지 종류의 `ListIterator`method를 제공하는데 `index`가 parameter로 있는 method는 특정 index를 시작으로 하는 list iterator를 반환한다.

<br>

## Range-View Operation

range-view operation인 `subList(int fromIndex, int toIndex)`는 지정한 구간에 해당하는 리스트를 반환한다. `view` operation이라는 말 답게 어떤 리스트의 subList는 원래 리스트가 변경되면 그 변경사항이 동일하게 subList에도 반영된다.

```java
System.out.println("=== subList modify Test ===");
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10));
List<Integer> subListOfList = list.subList(3, 7);
System.out.println("list = " + list);
System.out.println("subListOfList = " + subListOfList);
list.set(4, 1000); // 원래 리스트 변경
System.out.println(" << modify list's index 4th (5=>1000) >> ");
System.out.println("list = " + list);
System.out.println("subListOfList = " + subListOfList);
/**
 * === subList modify Test ===
 * list = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
 * subListOfList = [4, 5, 6, 7]
 *  << modify list's index 4th (5=>1000) >>
 * list = [1, 2, 3, 4, 1000, 6, 7, 8, 9, 10]
 * subListOfList = [4, 1000, 6, 7]
 */
```

다른 테스트 코드는 [여기](https://github.com/xi-jjun/data-structure-and-algorithm/blob/master/src/javastudy/collectionframework/listInterface/SubListTest.java)에 있으니 한번 봐도 좋을 것 같다.

<br>

### 왜 굳이 subList를 쓰는 것일까

처음에 굳이..? 라는 생각이 들었다.

```java
for(int i = fromIndex; i < toIndex; i++) {
  ...
}
```

위같이 (아주 조금은 귀찮지만) 간단하게 subList를 흉내낼 수 있는데 왜 굳이 사용할까 라는 의문이었다. Java docs에서는 다음과 같이 말한다.

> This method eliminates the need for explicit range operations (of the sort that commonly exist for arrays). Any operation that expects a `List` can be used as a range operation by passing a `subList` view instead of a whole `List`. 

전체 리스트에서 할 작업을 `subList`를 써서 할 수 있기에 우리가 위처럼 `for`를 직접 만들어가며 명시적으로 사용할 일을 없애준다고 한다... 아직까지 와닿지는 않지만 아래와 같은 활용 예시를 보여주고 있었다.

```java
list.subList(1, 3).clear(); // list의 1번, 2번 index element 제거
```

```java
int index = list.subList(5, 9).indexOf(Obj); // list의 5~9 사이의 element 중에 Obj에 해당하는 값이 있으면 해당 index 위치 반환
```

확실히 `indexOf`와 같은 method를 쓸 때에는 더 깔끔하고 간결한 코드가 나올 것 같다.

근데 위 예시를 보면... 과연 **'`subList`를 수정하면 원본에 반영이 될까?'**라는 생각이 들었다. 결과적으로는 반영이 된다. 왜일까?

```java
// ArrayList.java
...
public List<E> subList(int fromIndex, int toIndex) {
    subListRangeCheck(fromIndex, toIndex, size);
    return new SubList<>(this, fromIndex, toIndex);
}
...
```

`ArrayList`를 기준으로 설명하자면, 위와 같은 method가 존재한다. 따라서 우리는 `List` interface의 구현체인 `ArrayList`에서 `subList`라는 method를 호출할 수 있는 것이다. 하지만 위 method는 `new SubList<>(...)`를 반환하는 것을 볼 수 있다. 한번 따라가서 보도록 해보자.

```java
// ArrayList.java
public List<E> subList(int fromIndex, int toIndex) {
    subListRangeCheck(fromIndex, toIndex, size);
    return new SubList<>(this, fromIndex, toIndex);
}   
...
  
    private static class SubList<E> extends AbstractList<E> implements RandomAccess {
        private final ArrayList<E> root;
        private final SubList<E> parent;
        private final int offset;
        private int size;
      ...
```

`ArrayList` 안에 static class로 선언된 `SubList` class를 볼 수 있었다. 생성자를 또 찾아 볼 수 있는데 아래와 같다.

```java
// constructor
public SubList(ArrayList<E> root, int fromIndex, int toIndex) {
    this.root = root;
    this.parent = null;
    this.offset = fromIndex;
    this.size = toIndex - fromIndex;
    this.modCount = root.modCount;
}
```

그러니깐 어떤 흐름이냐면,

1. ```java
   List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5, 6, 7));
   List<Integer> subList = list.subList(2, 5);
   ```
   먼저 `list`에서 subList 를 호출했다.

2. ```java
   // ArrayList.java
   public List<E> subList(int fromIndex, int toIndex) {
       subListRangeCheck(fromIndex, toIndex, size);
       return new SubList<>(this, fromIndex, toIndex);
   } 
   ```
   `ArrayList`의 subList method가 호출될 때 `new SubList` 로 다른 생성자를 반환한다. 이 때 생성자의 첫번째 parameter로 넘어간 값을 잘 보면, `this` 키워드가 있음을 알 수 있다.
   
3. ```java
   // SubList constructor in ArrayList.java
   public SubList(ArrayList<E> root, int fromIndex, int toIndex) {
       this.root = root;
       this.parent = null;
       this.offset = fromIndex;
       this.size = toIndex - fromIndex;
       this.modCount = root.modCount;
   }
   ```
   `ArrayList` type으로 들어온 parameter는 아까 `this` 즉, 원래 `list`를 참조하는 위치가 그대로 들어온 것이다. 따라서 `subList`를 변경하면 원래 리스트에도 반영되는 것이다.

<br>

### 좋은거 알았는데 쓸 때는 조심!!

`subList` method로 생성된 리스트는 back up list에서 element의 추가 또는 제거의 작업이 발생하면 더이상 정의되지 않는다. 말이 이해가 잘 안갔는데 코드로 직접 보면서 어느 부분에서 오류가 나는지 보면 이해가 더 쉽다.

```java
System.out.println("=== subList adding Test ===");
List<Integer> listA = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10));
List<Integer> subListA = listA.subList(3, 7);
System.out.println("listA = " + listA);
System.out.println("subListA = " + subListA);
listA.add(1000); // back up list 에 add 작업 진행. <= 에러 발생 원인 코드
System.out.println("listA = " + listA);
System.out.println("subListA = " + subListA); // 여기서 에러 발생!
```

```shell
Exception in thread "main" java.util.ConcurrentModificationException
	at java.base/java.util.ArrayList$SubList.checkForComodification(ArrayList.java:1445)
	at java.base/java.util.ArrayList$SubList.listIterator(ArrayList.java:1314)
	at java.base/java.util.AbstractList.listIterator(AbstractList.java:311)
	at java.base/java.util.ArrayList$SubList.iterator(ArrayList.java:1310)
	at java.base/java.util.AbstractCollection.toString(AbstractCollection.java:465)
	at java.base/java.lang.String.valueOf(String.java:2951)
	at javastudy.collectionframework.listInterface.SubListTest.main(SubListTest.java:78)
```

`ConcurrentModificationException`이 발생했음을 볼 수 있다. (`set`과 같은 작업은 에러가 발생하지 않는다.)

따라서 `subList`는 일시적인 작업에서 매우 추천된다고 한다.

<br>

## List Algorithm

`Collections` class에 있는 대부분의 알고리즘은 `List`에 적용된다. 이 알고리즘들을 잘 사용하면 더 쉽게 리스트를 조작할 수 있다.

- `sort` : `List`에서는 빠르고 안정적인 `merge sort`를 사용한다. (왜 빠르고 안정적인지에 대해서는 알고리즘을 다룰 때 말하겠다)

- `shuffle` : 리스트에서 element들의 순서를 임의로 바꿔준다.

- `reverse` : 리스트에서 element들의 순서를 뒤집어 준다.

- `rotate` : 특정 방향으로 리스트의 모든 element들을 회전시켜 준다.

- `swap` : 리스트에서 특정 위치들로 element들을 바꿔준다.

- `replaceAll` : 어떤 특정한 값인 리스트의 모든 element들을 다른 값으로 바꿔준다.

- `fill` : 특정 값으로 리스트의 모든 element를 덮어쓰기 한다.

- `copy` : source list를 destination list에 복사한다.

  ```java
  if (srcSize > dest.size())
      throw new IndexOutOfBoundsException("Source does not fit in dest");
  ```

  `Collections.copy()`내에 위와 같은 조건문이 있다. 따라서 source size는 복사할 list 크기보다 작거나 같아야 한다.

- `binarySearch` : binary search algorithm으로 element를 탐색한다.

- `indexOfSubList` 리스트에 해당 target 리스트가 처음으로 나타나는 index 반환. 없으면 -1 반환.

- `lastIndexOfSubList` — returns the index of the last sublist of one `List` that is equal to another.

간단하게 몇가지는 테스트를 진행해봤었다. [여기](https://github.com/xi-jjun/data-structure-and-algorithm/blob/master/src/javastudy/collectionframework/listInterface/ListAlgorithmTest.java)에서 볼 수 있다.

다음 포스팅에서는 Queue에 대해서 알아보도록 하자.

<br>

## Rerference

[Java JDK 8 docs - Collections](https://docs.oracle.com/javase/tutorial/collections/index.html)

[Java JDK 8 docs - Set Interface](https://docs.oracle.com/javase/tutorial/collections/interfaces/set.html)

[Java JDK 8 docs - List Interface](https://docs.oracle.com/javase/tutorial/collections/interfaces/list.html)







