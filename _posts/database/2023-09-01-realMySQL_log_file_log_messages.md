---
layout: post
title: "MySQL log file - 메세지"
author: "xi-jjun"
tags: Database
---

# MySQL log file - 메세지

## 개요

개발할 때 수 많은 오류를 맞이할 때마다 트러블슈팅을해야한다. 그러기 위해서는 어떤 이유에 의해서 에러가 발생했는지를 알아야하는데, 대부분의 경우에서는 '로그'를 확인한다.

MySQL 에서도 에러가 발생하면 관련 로그들을 파일에 남기게되는데, 어떤 로그들이 남고 어떤 파일을 확인해서 트러블슈팅을 할 수 있는지 알기 위해서 정리하는 글이다. 
(에러가 발생하면 로그 파일을 안보고 다른 헛짓거리를 많이해본 사람으로써, 로그 파일의 중요성을 최근 절실히 느끼게 되어 정리하는게 크다.)

<br>

## 에러 로그 파일

- 에러 로그 파일은 `my.cnf` 라는 MySQL 설정파일에서 `log_error`라는 이름의 파라미터로 정의된 경로에 생성된다.

아래와 같은 주요 에러 메세지들을 볼 수 있으니 정리해본다.

### MySQL 시작과정

비정상적으로 종료된 이후 서버 재시작 때, MySQL 에러 로그 파일을 통해 **설정된 변수의 이름이나 값이 명확하게 설정되고 의도한대로 적용됐는지 확인할 필요**가 존재한다.

- `mysql: ready for connections` → 정상 실행
- `ignore` → 관련 파라미터가 MySQL에 적용되지 못했다는 의미

<br>

### InnoDB transaction 복구 메세지

비정상적 또는 강제 종료됐을 때 MySQL이 재시작되면서 `InnoDB` 가 아래의 처리를 한다.

1. 완료되지 못한 transaction 정리
2. 디스크에 기록되지 못한 데이터가 있다면 다시 기록하는 처리

위 과정에서 오류가 난다면, 에러 메세지를 출력하며 MySQL은 종료된다. 해결하기 힘든 경우가 많기에 `innodb_force_recovery` 파라미터를 재설정하는 등의 처리가 필요하다고 하는데, 나중에 알아보도록 하겠다.

<br>

### 쿼리 도중 발생하는 에러 메세지

말 그대로 쿼리 도중에 발생하는 에러 메세지. 

<br>

### 비정상적으로 종료된 connection 메세지

client app에서 정상적인 종료를 못한채로 프로그램이 종료된 케이스에서 발생하고 에러 로그에 기록되는 메세지. (네트워크가 끊어져서 생길수도 있으나, 기본적으로 정상적인 종료가 안됐을 경우에 발생)

아래와 같이 대응해볼 수 있다고 한다.

- Application 내 connection 종료 로직 검토

  - (Rails 기준) 내 기준으로 생각나는 것은, [remove connection과 같은 코드](https://api.rubyonrails.org/classes/ActiveRecord/ConnectionHandling.html#method-i-remove_connection)를 명시적으로 넣어서 해결할 수 있지 않을까... 라는 생각. (안써봄)

  - (Java Spring boot 기준) 

    - db connection pool size https://sg-choi.tistory.com/427
    - 기본 connection timeout 변경 (default: 30s [참고](https://freedeveloper.tistory.com/250))

    근데 기본적으로 idle-timeout이나 max-lifetime 의 설정값도 적절히 설정해주면, 애초에 application 수준에서 굳이 추가적인 로직이 필요없을 수도 있지 않을까... 라는 생각.

  뭐가 됐든지 정상적으로 connection을 종료하도록 하는 로직을 검토해야하는 것 같다.

- `max_connect_errors` 을 높여서 MySQL 서버에 접속이 안되는 경우를 회피

  - 원인 : 원격 MySQL 서버에 단순 connection 한 뒤, close를 하면 MySQL서버에서 비정상적인 접근이라 판단하고 해당 ip를 block한다고 하는 것 같더라...

    > ... After [`max_connect_errors`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_connect_errors) failed requests without a successful connection, the server assumes that something is wrong (for example, that someone is trying to break in), and blocks the host from further connection requests. ...
    >
    > [MySQL8.0 Docs - 5.1.12.3 DNS Lookups and the Host Cache](https://dev.mysql.com/doc/refman/8.0/en/host-cache.html)

  - 생각보다 이런 경우가 많아 보인다. (Spring으로 서버를 올려도, 사용하는 사람이 없었기에.. 부하 테스트도 안해봐서 겪을 일이 없었..)

  - [[mysql] mysql 외부 접속 connection locked 현상](https://velog.io/@usa_dev/mysql-mysql-%EC%99%B8%EB%B6%80-%EC%A0%91%EC%86%8D-connection-locked-%ED%98%84%EC%83%81)

여러가지 방법이 있을 수 있을 것 같으나 일단 해당 포스팅은 어떤 로그가 존재하고, 우리가 뭘 보면 좋을지에 대한 글이기에 여기까지만 적도록 하겠다.

<br>

### InnoDB의 모니터링 또는 상태조회 명령의 결과 메세지

`InnoDB` 의 테이블 모니터링/lock 모니터링, 엔진 상태 조회 명령은 상대적으로 큰 메세지를 에러 로그 파일에 기록한다고 한다. 그래서 모니터링을 활성화 상태로 만들어두고 그대로 유지하는 경우에는 에러 로그 파일이 매우 커져서 파일 시스템 공간을 다 사용해버릴지도 모른다고 한다...

책의 말이 이해가 안가는게, 에러 로그 파일은 무슨 상관인거지? 라는 생각이 들었는데,

> Standard Monitor output is limited to 1MB when produced using the [`SHOW ENGINE INNODB STATUS`](https://dev.mysql.com/doc/refman/8.0/en/show-engine.html) statement. This limit does not apply to output written to server standard error output (`stderr`).
>
> [MySQL8.0 Docs - 15.17.3 InnoDB Standard Monitor and Lock Monitor Output](https://dev.mysql.com/doc/refman/8.0/en/innodb-standard-monitor.html)

상태조회 명령을 사용했을 때, Standard Monitor 파일은 1MB 제한이 걸려있으나, 에러 파일에는 적용되지 않는다는 공식문서를 봐서는... 에러 로그 파일의 크기가 매우 커질 경우에 대한 경고를 한 이유가 납득이되는 것 같다.

<br>

### MySQL 종료 메세지

아무도 모르게 종료되거나 재시작되는 경우에 확인해볼만한 메세지. 이 경우에 에러 로그 파일에서 MySQL이 마지막으로 종료되며 출력한 메세지를 보는 것이 원인을 알 수 있는 유일한 방법이라고 한다.

1. 누군가가 MySQL 서버를 종료시켰을 경우
   `Received SHUTDOWN from user ...` 라는 메시지를 확인가능하다고 한다.

2. 그 외 (`segmentation fault` 등등...)

   Stack trace 내용을 최대한 참조해서 MySQL 버그와 연관이 있는지 여부를 확인 후, 버전 업 또는 다른 방법을 찾아야 한다고 한다...

<br>

주요 로그 메세지가 어떤 것들이 있고, 어떤 부분들을 조심하거나, 주요하게 보면 좋은지에 대해 알 수 있었다. 
