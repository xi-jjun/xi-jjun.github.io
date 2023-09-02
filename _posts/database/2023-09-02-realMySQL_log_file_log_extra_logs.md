---
layout: post
title: "MySQL log file - General/Slow Query Logs"
author: "xi-jjun"
tags: Database
---

# MySQL log file - General/Slow Query Logs

## 개요

개발할 때 수 많은 오류를 맞이할 때마다 트러블슈팅을해야한다. 그러기 위해서는 어떤 이유에 의해서 에러가 발생했는지를 알아야하는데, 대부분의 경우에서는 '로그'를 확인한다.

이전에는 그런 경우에서 봐야할 주요 로그들에 대해서 정리했고, 간단하게 대응 방법? 까지 얘기했었다. 이번에는 Genernal query log 와 Slow query log에 대해서 알아볼 것이다.

*개인적으로는 slow query에 대한 로그를 어떤 경우에 나타나고, 어떻게 보는지 익혀서 회사 업무에 써먹을 수 있기를 바라며...*

<br>

## General Query Log

- '쿼리 로그' 활성화하여 파일로 기록 후 어떤 쿼리들이 실행되는지 확인을 위해 사용

- 파일에는 시간단위로 실행됐던 쿼리의 내용이 기록됨.

  - MySQL이 쿼리 요청을 받으면 바로 기록하기에 실행 중 에러가 발생해도 일단은 로그 파일에 기록됨.

- 파일 경로 : `general_log_file` 파라미터에 정의된 경로

  → 파일이 아닌, 테이블에 저장할 수도 있다. (`log_output` 파라미터로 설정가능) [MySQL8.0 Docs 참고](https://dev.mysql.com/doc/refman/8.0/en/log-destinations.html)

<br>

## Slow Query Log

- `long_query_time` 파라미터 이상의 시간이 소요된 쿼리를 기록

  → '소요된 시간'을 기준으로 로깅하기에, 반드시 **쿼리가 성공** 해야 기록된다. (`log_output` 설정 가능)

  - 근데 MySQL 테이블에 저장해도, csv storage 엔진을 사용하기 때문에 CSV파일로 저장하는 것과 동일하다고 한다... (`genernal_log`도 마찬가지)

    > By default, the log tables use the `CSV` storage engine that writes data in comma-separated values format. For users who have access to the `.CSV` files that contain log table data, the files are easy to import into other programs such as spreadsheets that can process CSV input.
    >
    > [MySQL 8.0 Docs - Log Table Benefits and Characteristics](https://dev.mysql.com/doc/refman/8.0/en/log-destinations.html)

로그 내용을 [제타위키](https://zetawiki.com/wiki/MySQL_%EC%8A%AC%EB%A1%9C%EC%9A%B0_%EC%BF%BC%EB%A6%AC_%EB%A1%9C%EA%B7%B8_%EC%84%A4%EC%A0%95)에서 가져와봤다.

```log
[root@zetawiki ~]# cat /var/log/mysql/mysql-slow.log

/usr/libexec/mysqld, Version: 5.1.73-log (Source distribution). started with:
Tcp port: 0  Unix socket: /var/lib/mysql/mysql.sock
Time                 Id Command    Argument
# Time: 160102 20:29:51
# User@Host: root[root] @ localhost []
# Query_time: 7.000300  Lock_time: 0.000000 Rows_sent: 1  Rows_examined: 0
SET timestamp=1451734191;
SELECT SLEEP(7);
```

- `Time` : 쿼리 종료된 시점
- `User@host` : 쿼리 실행한 사용자
- `Query_time` : 쿼리가 실행되는데 걸린 시간
- `Lock_time` : MySQL 엔진 레벨에서 관장하는 테이블 잠금에 의한 대기시간 (잠금의 여부를 확인하는 시간도 포함됨)
- `Rows_examined` : 쿼리를 처리하기 위해 몇 건의 record에 접근했는지에 대한 건수
- `Rows_sent` : 실제 몇 건의 처리결과(record)를 클라이언트로 보냈는지에 대한 건수
  - `Rows_examined` 가 `Rows_sent` 보다 상당히 많다면, 해당 쿼리는 조금 더 적은 record에만 접근하도록 튜닝의 대상이 되어야 한다고 함. (집합함수가 아닌 쿼리일 경우에만)

이렇게 로그를 볼 수 있으나, 내용이 너무 많아서 보기 편하게 하기 위해서는 `percona toolkit`의 `pt-query-digest` 스크립트를 사용한다고 한다. (빈도나 처리 성능별로 쿼리를 정렬해준다고 함)

<br>

## References

[MySQL 8.0 Docs - 5.4.1 Selecting General Query Log and Slow Query Log Output Destinations](https://dev.mysql.com/doc/refman/8.0/en/log-destinations.html)

[Zetawiki - MySQL Slow Query Log](https://zetawiki.com/wiki/MySQL_%EC%8A%AC%EB%A1%9C%EC%9A%B0_%EC%BF%BC%EB%A6%AC_%EB%A1%9C%EA%B7%B8_%EC%84%A4%EC%A0%95)

[다크쉐라빔의 주절주절 - MySQL Slow Query Log 정보](https://darksharavim.tistory.com/917)

