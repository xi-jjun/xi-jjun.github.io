---
layout: post
title: "C-study-4"
author: "xi-jjun"
tags: 동아리

---

# 4주차

아래와 같은 것들을 공부하시면 됩니다. [동빈나 유투브](https://www.youtube.com/watch?v=dh4hdtZ00EU&list=PLRx0vPvlEmdDNHeulKC6JM25MmZVS_3nT)를 참고하였으나 순서가 약간 다를 수 있습니다.

- 문자열
- 구조체
- 동적 메모리

<br>

## 문자열

문자열에 관한 함수들을 알아볼 것이다. 나중에 전전과 전공과목 중 하나인 `C언어및자료구조-(전준현 교수님)`이라는 수업을 듣게 된다면 많이 쓰게 될 수 있다. 

<br>

```c
char input[1001];
gets(input); // scanf와 마찬가지로 사용자의 입력을 받는다.
```

`gets()`라는 함수는 잘 안써봤을 수 있다. 보통 `scanf()`를 먼저 배우기 때문이다. 

그렇다면 `gets()`와 `scanf()`의 차이는 무엇일까? 코딩을 하다보면 이런게 궁금할 수 있다. 그럴 땐 검색을 하는 것을 추천한다.

<br>

### gets() vs scanf()

[해당 사이트](https://www.geeksforgeeks.org/difference-between-scanf-and-gets-in-c/)에서 알 수 있었다. 

- `scanf()` : `int, char...`등 다양한 자료형의 데이터를 받을 수 있다. 그리고 `scanf()`는 데이터를 받을 때 사용자의 **띄워쓰기, 줄바꿈**을 인식하고 받을 데이터를 결정한다.
- `gets()` : 오직 `char` 데이터만을 받을 수 있다. `gets()`는 오직 **줄바꿈**만을 인식하여 받을 데이터를 정한다.

예시는 [해당 사이트](https://www.geeksforgeeks.org/difference-between-scanf-and-gets-in-c/)에 있으니 확인바란다.

<br>

### strlen() 문자열 길이 출력 함수

```c
#include <stdio.h>
#include <string.h> // string.h 를 include안하면 strlen()와 같은 함수를 못쓴다. 

char input[10] = "Hello";
strlen(input);
```

`strlen("문자열 시작 주소")`는 해당 문자열의 길이를 반환해주는 함수이다. 중요한 것은 `#include <string.h>`를 위에 써줘야 한다는 것이다.

<br>

### strcmp() 문자열 비교 함수

```c
char str1 = 'a';
char str2 = 'a';
if(str1 == str2) printf("SAME");
else printf("Different");
```

위 코드는 실제로 실행시켜보면 당연하게도 `SAME`이라는 결과가 나온다. 그러나 다음 예시를 보자.

```c
char str1[5] = "abcd";
char str2[5] = "abcd";
if(str1 == str2) printf("SAME");
else printf("Different");
```

이번에도 `SAME`이라는 결과가 나와야할 것 같지만, 실제로 코드를 실행시켜보면 `Different`라는 결과가 나온다. 분명히 `a="abcd"`이고 우리는 정확하게 같은 문자열을 비교했는데 왜 `SAME`이 안나오는 것일까?

이 코드는 언뜻보면 문자열을 비교하는 것 같지만 절대로 우리가 원하는 결과를 얻을 수가 없다. 왜냐하면 `if(str1 == str2)`라는 코드는 문자열을 비교하는 것이 아니라 문자열의 '주소값'를 비교하는 것이기 때문이다([출처](https://enter.tistory.com/146)). 이 말이 이해가 잘 안간다면 str1과 str2를 출력해봐라.

```c
printf("%x\n", str1);
printf("%x", str2);
```

```shell
7bae4e7e
7bae4e83
```

라는 결과를 얻을 수 있다. 이건 우리가 `pointer`를 공부할 때 배웠던 내용이었다. 따라서 결론적으로 비교가 안되는 이유는,

> **내용물**을 비교해야 하는데 우리는 **껍데기(주소값)**을 비교하고 있었던 것이다.

이것이 바로 우리가 `strcmp()`라는 함수를 쓰는 이유이다.

<br>

```c
#include <stdio.h>
#include <string.h>

int main() {
  char str1[5] = "abcd";
	char str2[5] = "abcd";
  
  // strcmp는 두 문자열의 시작주소를 입력으로 받는다.
  // 두 문자열이 같을경우, 0을 반환. 다를경우 -1를 반환.
	if(strcmp(st1, str2) == 0) printf("SAME");
	else printf("Different");
  
  return 0;
}
```

위 코드와 같이 쓰게 되면 우리가 원하는 결과를 얻을 수 있다. `strcmp(문자열1 시작주소, 문자열2 시작주소)`

<br>

### strcpy() 문자열 복사 함수

```c
#include <stdio.h>
#include <string.h>

int main() {
  char str1[20] = "original";
	char str2[8] = "new one";
  
  strcpy(str1, str2);
  
  printf("%s\n", str1); // 출력 : new one
  
  return 0;
}
```

`strcpy(복사를 당할 문자열 시작주소, 복사하고 싶은 내용의 문자열 시작주소)`의 형태로 사용하면 된다. 

<br>

## Structure(구조체)

> 여러개의 자료형을 묶어서 새로운 자료형을 만들 수 있다. - [출처](https://www.youtube.com/watch?v=WpWSD7QR8EA&list=PLRx0vPvlEmdDNHeulKC6JM25MmZVS_3nT&index=15)

구조체는 아마 `프로그래밍 기초 및 실습`에서도 잘 안다룬다. 이유는 여기까지 진도를 나가기엔 시간이 부족하기 때문이다. 

```c
struct student {
  unsigned int studentID; 
  char name[20];
  double grade;
};
```

위 코드는 '학생'이라는 구조체를 만들고 그 안의 값들을 정의한 것이다. '학생'은 '학번(studentID)'과 '이름(name)'과 '성적(grade)'를 가지기 때문에 우리는 '학생'을 위와 같이 정의한 것이다.

```c
/* str1.c file */
#include <stdio.h>
#include <string.h>

struct student {
  unsigned int studentID; 
  char name[20];
  double grade;
};

int main() {
  
  struct student kjj;
  kjj.studentID = 2016111000;
  strcpy(kjj.name, "Kim Jae Jun");
  kjj.grade = 2.19;
  
  printf("%d\n", kjj.studentID); // 2016111000
  printf("%s\n", kjj.name); // Kim Jae Jun
  printf("%.2lf\n", kjj.grade); // 2.19
  
  return 0;
}
```

```shell
~/D-Coder-Study623$ ./str1
2016111000
Kim Jae Jun
2.19
```

이런식으로 사용할 수 있다. 이렇게 하면 변수의 선언을 일일히 계속 할 필요가 없다. 예를 들어 학생 3명을 선언하고 싶다면,

```c
int studentID1;
char name1[20];
double grade1;

int studentID2;
char name2[20];
double grade2;

int studentID3;
char name3[20];
double grade3;
```

과 같이 선언해줘야했다. 그러나 구조체라는 것을 사용한다면,

```c
struct student {
  unsigned int studentID; 
  char name[20];
  double grade;
};

int main() {
  struct student kimjaejun;
  struct student parkjaejun;
  struct student jingayun;
  // code...
  return 0;
}
```

이런 방식으로 학생 3명을 표현할 수 있다. 보다시피 더욱 효율적이다.

<br>

## Dynamic memory allocation(메모리 동적 할당)

```c
#include <stdlib.h>

int *pi;
pi = (int *)malloc(sizeof(int)); // == (int*)malloc(4);
```

`(datatype *)malloc(할당할 크기 입력)`형식이다. 위 코드는 `int`형으로 `sizeof(int)==4byte`의 크기만큼 메모리를 할당하겠다는 뜻이다. 이해가 잘 안갈 수 있다. 처음에는 그냥 저렇게 쓰인다는 것을 외우면 좋다. 자세한 예제를 보도록 하자.

```c
/* 동빈나 예제 코드 */
#include <stdio.h>
#include <stdlib.h> // 필수

int main() {
  
  int *pi;
	pi = (int *)malloc(sizeof(int)); // == (int*)malloc(4);
  if(pi == NULL) {
    printf("Error"); // 메모리 동적할당 실패.
    exit(1); // program 종료. 코드가 끝난다.
  }
  
  *pi = 100;
  printf("%d\n", *pi);
  
  free(pi); // 메모리 할당 해제
  return 0;
}
```

동적 메모리로 할당을 해주고 다 썼을 때 메모리 해제를 해줘야 한다. `free()`라는 함수를 쓰는 이유는 메모리 해제를 해줄 때 쓰는 것이다.

<br>

## C언어 스터디 끝!!!

이걸로 스터디는 끝났습니다. 스터디이기 때문에 제가 찾아드리는 것보단 여러분들께서 알아서 찾아서 해결하길 바랬었는데 어쩌다 보니 이렇게 4주차까지 글을 쓰고 있네요. 그래도 엄청 친절하게는 글을 안써놨기에 헷갈리는 부분도 많았을 것이라고 생각합니다. 부족한 저를 따라서 C언어 스터디에 참가해주셔서 감사합니다. 스터디가 끝나고 동아리를 나가시더라도 언제나 질문은 환영입니다. 저도 모르는게 많기 때문에 공부해서라도 알려드리겠습니다. 혹시나 이번 스터디가 마음에 드셨다면, 다음에 할 python, Java 스터디도 한번 생각해보시면 감사하겠습니다.

언어를 공부하다보면 제가 일일히 A~Z까지 코드를 적는 것이 아닌, 남의 코드를 복붙하는 경우가 많습니다. 지금까지 하신 것들은 다 간단한 것들이라 그렇지만 나중에 본인이 만들고 싶은 프로그램이 있다면, 상당수를 다른 사람이 만든 코드를 가져오게 되는 경우가 있을 것입니다. 제가 말씀드리고 싶은 것은 이런게 부끄러운게 아니라는 것입니다. 처음 배우는 과정에선 남이 잘 만든 코드를 보고 배우는 것도 매우 중요하다고 생각하기 때문입니다.

결론적으로 여러분들께서 코딩을 하면서 가져야할 자세는 **'검색하는'** 자세라고 전 생각합니다. 물론 전 아직 모르는게 한참 많은 방구석 화석+백수지만 제가 코딩을 하면서 느낀건, **"무엇이든지 모르겠으면 구글로 가라"** 입니다. 이걸 깨닳고 코딩이 막히거나 에러가 발생할 때 저만의 3가지 단계를 만들어 극복해냈습니다.

1. **정확한** 오류 원인을 찾아라. 대부분 코드에 어떤 오류인지 뜹니다. 영어라고 무시하면 안되고 천천히 읽어보세요. 진짜 모르겠으면 그 오류문구 **그대로** 구글 검색창에 입력해보세요. `stackoverflow`에는 뛰어난 사람들이 많습니다.
2. 검색창에 입력한 순간 오류의 70%를 해결할 수 있다고 생각합니다. 그러나 오류의 원인을 알았음에도 코드를 완성 못할 수 있습니다. 왜냐하면 **무슨 소리인지 모르기 때문**입니다. 따라서 이 단계에서는 **무엇을 모르는지 파악**해야 합니다. 예를 들면 어떤 단어를 몰라서 무슨 소리인지 모른다거나 그럴 수 있기 때문입니다.
3. **오류를 알고, 무엇을 모르는지**를 알았다면, 이제 다시 검색입니다. 구글로 가서 제일 먼저 **모르는 것**을 알아내세요. 모르는 것을 알아내셨다면 이제 오류를 해결해보세요. 저는 이 방법으로 코딩을 합니다. 물론 쉬운것들만 해서 엄청 어려운 코드는 만져본적이 없긴 해요 ㅎㅎ.

전전과의 상당수는 반도체공정 직무로 갑니다. 대부분은 "코딩을 왜 해?"라는 반응입니다. 저도 부정은 못합니다. 본인에게 필요한 것들을 해야하니깐요. 

그래도 이러한 C코딩이 어떤식으로 도움이 될지는 모르겠지만, 설마 나중에라도 쓰일 수 있다면 그 때 단 한 순간만이라도 이 스터디가 도움이 되길 바랄뿐입니다.

감사합니다. (과제 있읍니다..)

<br>

## 과제기한(7/12~7/18)

## 문제1

구조체를 써서 Book이라는 구조체를 선언하고 책이름, 저자를 구조체 안에서 선언해라. 그리고 3개의 책을 선언한 뒤 출력하는 함수를 만들어서 출력해봐라. (Malloc, struct, pointer struct, pointer, strcmp 를 써야할 것이다. 안써서 구현해도 상관 안쓴다.)

```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>

struct Book{
  title
  author
};

void showBookInfo(입력 넣으세요) { // 우리가 입력한 책 정보 출력하는 함수
  // printf code...
}

int main(void) { 
  struct Book *books;

	return 0;
}
```

위 코드의 출력을 아래와 같이 만들어라. 2가지의 경우가 있다. 

1번째는 `Yes`를 입력하여 프로그램을 정상적으로 진행시키는 것.

2번째는 `No`를 입력하여 프로그램을 종료시키는 것.

나머지 경우에 대해서는 안만들어도 된다.(`Yes, No`이외의 입력은 없을 거라는 가정 )

**첫번째 힌트**를 주자면, malloc선언과 struct pointer 선언 때에는 [동빈나 유투브 00:00~7:22](https://www.youtube.com/watch?v=_1PiJAjB7Io&list=PLRx0vPvlEmdDNHeulKC6JM25MmZVS_3nT&index=20)부분을 참고 바란다.

```shell
~/D-Coder-Study623$ ./last1 
총 몇권의 책을 등록하시겠습니까? 3
책을 등록하시겠습니까?(Yes or No) : Yes
책 이름 : 김재준 일대기
책 저자 : 김재준

책 이름 : 디코더는 죽었다
책 저자 : 재준킴

책 이름 : 스터디 파이팅
책 저자 : 여러분

---이 밑으로는 출력만 되는 것---
< Book No. 0 >
Book title : 김재준 일대기
Book author : 김재준

< Book No. 1 >
Book title : 디코더는 죽었�재준킴
Book author : 재준킴

< Book No. 2 >
Book title : 스터디 파이팅
Book author : 여러분

~/D-Coder-Study623$ ./last1 
총 몇권의 책을 등록하시겠습니까?3
책을 등록하시겠습니까?(Yes or No) : No
program exit
```

문제를 만드는데 2시간 걸렸습니다. 이 문제에 이번 주차에 공부한 것 대부분과 이때까지 배웠던 것들 대부분이 있을 것 같네요. 많이 어려울 것입니다. 처음하시는 분들이라면 못할 수 있어요. 밑에 **힌트**를 보시고도 못하겠다면 말씀해주세요. 설명해드리겠습니다.

<br>

<br>

<br>

<br>

<br>

<br>

궁금하신 것 있으면 언제든지 물어봐주세요!!

E-mail : rlawowns97@naver.com, rlawowns97@gmail.com

카카오톡 가능

<br>

## 두번째 힌트

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

struct Book{
  char title[20];
  char author[20];
};

void showBookInfo(struct Book *p, int count) {
  int i = 0;
  printf("---이 밑으로는 출력만 되는 것---\n");
  for(i=0;i<??;i++) {
    printf("< Book No. %d >\n", i);
    printf("Book title : %s\n", ??);
    printf("Book author : %s\n\n", ??);
  }
}

int main() {
  struct Book *books;

  int i = 0;
  char mode[5]; // Yes or No
  int bookNum = 0; // how many books do you want to add?
  char pass[2];

  printf("총 몇권의 책을 등록하시겠습니까? ");
  scanf("%d", ????);

  books = (struct Book *)malloc(???? * sizeof(struct Book));

  printf("책을 등록하시겠습니까?(Yes or No) : ");
  scanf(" %s", ??);

  if(???) {
    printf("program exit");
    exit(1);
  }
  else if(???) {
    for(i=0; i<????; i++) {
      if(i==0) fgets(pass, 2, stdin);
      
      printf("책 이름 : ");
      gets(??);
      printf("책 저자 : ");
      gets(??);
      printf("\n");
    }
  }
  
  showBookInfo(??, ??);

  free(books);
  return 0;
}
```



