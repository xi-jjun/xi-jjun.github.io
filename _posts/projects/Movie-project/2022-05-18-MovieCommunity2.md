---
layout: post
title: "영화 커뮤니티 사이트 만들기 2"
author: "xi-jjun"
tags: project-movie-community

---

# 영화 lover들을 위한 커뮤니티 사이트를 만들어보자

> 본 프로젝트는 협업으로 진행되었습니다.

<br>

## 1일차에 설계 했던 DB

![movie1_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/projects/Movie-project/img/movie1_1.png?raw=True){: width="85%"}

- `Member` : 커뮤니티 사용자. 영화에 대한 리뷰를 남기거나, 커뮤니티 게시판에서 게시글을 쓰거나 다른 게시글에 댓글을 달 수 있다.
- `Posting` : 사용자가 게시판에 작성할 수 있는 게시글 Entity. 게시판의 종류는 1개 이기에 따로 Entity를 만들지는 않았다.
- `Comment` : 댓글 Entity. 게시글에 댓글을 달 수 있어야 하기에 만들었다.
- `Review` : 사용자가 한 영화에 대해 리뷰글을 작성할 수 있다. 해당 리뷰 글에는 댓글을 달 수 없다.

....

2일 전에는 위와 같이 DB modeling을 했었다. 하지만 API를 설계하는 과정에서 몇가지 추가하고 싶은 속성과 entity가 있었다. 따라서 API 설계부터 보도록 하겠다.

<br>

# 3일차 진행사항

## API 설계

```text
[ Review API : 영화 리뷰에 대한 API ]
어떤 Movie에 적힌 Review list 조회 : GET - /reviews/movies/{movieId}
Review 1개 조회 : GET - /reviews/{reviewId}
어떤 Movie에 대한 Review 생성 : POST - /reviews/movies/{movieId}
어떤 Movie에 대한 Review 수정 : PATCH - /reviews/movies/{movieId}
어떤 Movie에 대한 Review 삭제 : DELETE - /reviews/movies/{movieId}


[ Movie API : 영화에 대한 API ]
Movie list 조회 : GET - /movies
어떤 Movie detail 조회 : GET - /movies/{movieId}
Movie 목록에 영화 추가 : POST - /movies
어떤 Movie 정보 수정 : PATCH - /movies/{movieId}
어떤 Movie 정보 삭제 : DELETE - /movies/{movieId}


[ Holiday API : 기념일 API ]
Holiday(기념일)에 따른 Movie list 조회 : GET - /holiday/{holidayName}/movies
Holiday 추가 : POST - /holiday
Holiday 수정 : PATCH - /holiday/{holidayId}
Holiday 삭제 : DELETE - /holiday/{holidayId}


[ Posting API (게시글 작성 API) ]
Posting list 조회 : GET - /postings
어떤 Posting 1개 조회 : GET - /postings/{postingId}
Posting 생성 : POST - /postings
어떤 Posting 수정 : PATCH - /postings/{postingId}
어떤 Posting 삭제 : DELETE - /postings/{postingId}


[ Comment API(댓글 작성 API) ]
어떤 Posting 1개에 대한 Comment list 조회 : GET - /comments/postings/{postingId}
어떤 Comment 1개 조회 : GET - /comments/{commentId}
어떤 Posting 1개에 대한 Comment 생성 : POST - /comments/postings/{postingId}
어떤 Posting 1개에 대한 Comment 수정 : PATCH - /comments/postings/{postingId}
어떤 Posting 1개에 대한 Comment 삭제 : DELETE - /comments/postings/{postingId}


[ Member API ]
Member list 조회 : GET - /members
Member 1명 조회 : GET - /members/{memberId}
Member 생성(회원가입) : POST - /members
Member 정보 수정 : PATCH - /members/{memberId}
Member 정보 삭제 : DELETE - /members/{memberId}


Login URL Path
login 화면 조회 : GET - /login
login : POST - /login


[ Movie Lover Test API(덕후력 테스트 API) ]
Test 질문 list 제공(7문항) : GET - /movie-lover-tests
Test 질문 결과 제공 : POST - /movie-lover-tests (테스트 결과 return rank)
Test 질문 DB에 추가 : POST - /movie-lover-tests/questions
Test 질문 DB에 수정 : PATCH - /movie-lover-tests/questions/{questionId}
Test 질문 DB에 삭제 : DELETE - /movie-lover-tests/questions/{questionId}


[ Diary Journal API : Diary(일기장), Journal(그날 일기) ]
사용자 Diary list 조회 : GET - /members/{memberId}/diary
Diary(일기장) 추가 : POST - /members/{memberId}/diary
Diary(일기장) 정보 수정 : PATCH - /members/{memberId}/diary/{diaryId}
Diary(일기장) 삭제 : DELETE - /members/{memberId}/diary/{diaryId}
사용자 Diary의 Journal list 조회 : GET - /members/{memberId}/diary/{diaryId}/journals
사용자 Diary의 Journal 1개 조회 : GET - /members/{memberId}/diary/{diaryId}/journals/{journalId}
Journal(일기) 쓰기 : POST - /members/{memberId}/diary/{diaryId}/journals
Journal 수정 : PATCH - /members/{memberId}/diary/{diaryId}/journals/{journalId}
Journal 삭제 : DELETE - /members/{memberId}/diary/{diaryId}/journals/{journalId}


[ Recommendation API (추천 알고리즘 API) ]
우리가 제공하는 Movie 추천 list 조회 : GET - /recommendations/movies
```

Entity 에 대한 API를 위와 같이 정리해봤다. 결국엔 DB를 바꿔야 했고,

1. 덕후력 테스트 결과를 `Member`에 저장하기 위해서 `rank` column을 `Member` DB table에 추가해야 함.
2. `Memeber` 에게 권한을 부여하기 위해서 `role` column을 DB table에 추가해야 함.
3. `Movie` Entity를 만들 것. - 과제의 요구사항에 DB를 활용하라고 했었기에 `TMDB API`로 data만 받아와서 우리의 DB에 저장한 후 사용할 계획이다.
4. `Holiday` entity를 만들 것. 
   - 요구사항에 '기념일에 따른 영화 목록 추천' 이 있었기에 기념일에 관한 영화 API를 제공할 것이다.
   - `Movie` entity에 안넣은 이유 : 향후 `Holiday` 에 다른 속성을 넣을 수 있기에 유연한 설계가 가능하고, `Movie` 와 `Holiday` 의 역할이 다르다고 판단했음.
5. `Diary`, `Journal` entity 추가.
   - 일기장은 여러개다.
   - 1개의 일기장에는 여러개의 일기를 쓸 수 있다. 일기에는 일기내용, 그날의 기분 을 쓸 수 있다는 요구사항이 존재하기에 추가.

<br>

## 바뀐 DB table 설계

![movie2_1](https://github.com/xi-jjun/xi-jjun.github.io/blob/master/_posts/projects/Movie-project/img/movie2_1.png?raw=True){: width="85%"}

- `Member` : 커뮤니티 사용자. 영화에 대한 리뷰를 남기거나, 커뮤니티 게시판에서 게시글을 쓰거나 다른 게시글에 댓글을 달 수 있다.
- `Posting` : 사용자가 게시판에 작성할 수 있는 게시글 Entity. 게시판의 종류는 1개 이기에 따로 Entity를 만들지는 않았다.
- `Comment` : 댓글 Entity. 게시글에 댓글을 달 수 있어야 하기에 만들었다.
- `Review` : 사용자가 한 영화에 대해 리뷰글을 작성할 수 있다. 해당 리뷰 글에는 댓글을 달 수 없다.
- `Movie` : 영화 entity. 사용자는 어떤 영화에 리뷰를 작성할 수 있다.
- `Holiday` : 기념일 entity. 요구사항 중, 기념일에 따른 영화 추천을 하기위해 만들었다.
- `Diary` : 일기장 entity. 일기는 1개의 글이지만, 일기장은 일기를 담고있는 하나의 묶음이라고 생각하고 만들었다. 사용자가 여러개의 일기장을 갖질 수 있게 하였다.
- `Journal` : 일기 entity. 일기는 직접 우리가 쓴 일기 내용이다.

뭐가 좀 더 많이 추가됐다. API설계와 DB설계가 끝났기에 내일부터는 본격적으로 개발에 들어갈 것이다.

<br>

## 일정 계획

1. **도메인 DB 설계 + 커뮤니티,리뷰 REST API 개발 5/16-18(수) 까지 완료하기**
   - **현재 API 설계까지 완료함. 개발은 5/19부터 3일간 끝내는 것으로...목표를...**
   - **JWT 인증 구현 시간을 더 줄여야겠다.**
2. Jwt 로그인 인증 구현 + 회원가입 REST API 개발 ~5/21(토) 까지 완료하기
3. 기념일에 따른 영화추천 REST API 개발 ~5/22(일) 까지 완료하기
4. 명대사 월드컵 REST API 개발 ~5/22(일) 까지 완료하기
5. 덕후력 테스트 REST API 개발 ~5/22(일) 까지 완료하기
6. 기분 매일 물어보게 하고 데이터 쌓는 REST API 개발 ~5/22(일) 까지 완료하기
7. 만든 API 기반으로 front UI/UX 구현 ~5/25(수) 까지 완료하기
8. 5/26 테스트 진행.
9. 5/27 마감일

<br>

## Next

API 설계가 생각보다 규모가 컸다. 그래도 차근차근 정리하면서 진행했더니 빨리 끝났던 것 같다. 페어와 해당 API를 상의한 이후에 실제 domain 개발부터 시작할 것이다.

<br>

## 느낀점

JWT가 문제가 아니라 설계가 더 중요함을 느꼈다. 이전에 `REST API` 라이브 코딩테스트를 
