---
layout: post
title: MySQL과 MariaDB
description: >
  2022-08-28-MySQL과 MariaDB
hide_description: false
category: dailydev
---

MySQL은 1995년 오픈소스로 배포된 무료 DBMS이며 오라클로 인수되고 나서 개발 지침과 라이선스 정책의 변화에 따라 MySQL의 핵심 개발자 주도로 오픈소스 정책을 지향하는 MariaDB가 탄생했다. MariaDB가 MySQL의 소스 코드에 기반을 두고 개발된 만큼, SQL문을 사용하는 개발자 입장에서는 별다른 차이는 없다. 5.5 버전 부터 두 DBMS가 버전이 나뉘기 시작했으며 SQL문법이나 실행 계획의 출력 방식은 유사하다. 단 버전에 따라 DB엔진 레벨에서 제공하는 옵티마이저 기능의 차이는 있으므로 데이터베이스 관리자라면 이 점을 염두에 해야 한다.

## 오라클과 MySQL의 구조적 차이
먼저 데이터가 저장되는 스트로지의 구조 측면에서 큰 차이 있다.
오라클DB는 통합된 스토리지 하나를 공유 하여 사용, MySQL은 물리적인 DB 서버마다 독립적으로 스토리지를 할당하여 구성한다. 즉 오라클은 사용자가 어느 DB서버에 접속하여 SQL문을 수행하더라도 같은 결과를 출력하거나 동일한 구문을 처리할 수 있다. MySQL은 물리적으로 여러 대의 MySQL DB 서버에 접속하더라도 동일한 구문이 처리되지 않을 수 있으며 DB 서버마다 각자의 역활이 부여될 수 있다(마스터 노드에서는 쓰기, 슬레이브 노드에서는 읽기를 수행한다.)

## 오라클과 MySQL의 지원 기능차이
MySQL과 오라클 DB에서 제공하는 조인 알고리즘의 기능에는 차이가 있다.
MySQL은 대부분 중첩 루프 조인 방식으로 조인을 수행, 오라클에서는 중첩 루프 조인 방식뿐만 아니라 정렬 병합 조인, 해시 조인 방식도 제공한다.
MySQL은 데이터를 저장하는 스토리지 엔진이라는 개념을 포함하므로 필요한 DBMS를 설정해 사용할 수 있고 오라클에 비해 상대적으로 낮은 메모리 사용으로 저사양PC에서도 손쉽게 설치, 개발할 수 있다.

## SQL 구문차이

__Null 대체__<br>
tab이라는 테이블에서 col1 열 조회, 이때 col1 열은 null값도 포함할 수 있으므로 해당 열에 null이 포함될 때는 'N/A'로 대체 할때<br>
MySQL, MariaDB

```sql
SELECT IFNULL(col1, 'N/A') col1
FROM tab;
```
오라클
```sql
SELECT NVL(col1, 'N/A') col1
FROM tab;
```

__페이징 처리__<br>
tab 테이블에서 5개의 데이터만 조회<br>

MySQL, MariaDB
```sql
SELECT col1
FROM tab
LIMIT 5;
```
오라클
```sql
SELECT col1
FROM tab
WHERE ROWNUM <= 5;
```

__현재 날짜__<br>
오라클에서는 가상 테이블인 dual을 사용해 작성, MySQL에서는 date라는 문자로 별칭을 부여하여 현재 날짜를 출력<br>

MySQL, MariaDB
```sql
SELECT NOW() AS date;
```
오라클
```sql
SELECT SYSDATE AS date
FROM dual;
```

__조건문__<br>
col1 열의 값이 A라면 apple 그렇지 않으면 '-'로 표시<br>

MySQL, MariaDB
```sql
SELECT IF(col1 ='A', 'apple', '-') AS col1
FROM tab;
```
오라클
```sql
SELECT DECODE(col1, 'A', 'apple', '_') AS col1
FROM tab;
```

__날짜 형식__<br>
편의상 현재 날짜를 조회하고 열 형태로 변경하여 출력<br>

MySQL, MariaDB
```sql
SELECT DATE_FORMAT(NOW(), '%Y%m%d %H%i%s') AS date;
```
오라클
```sql
SELECT TO_CHAR(SYSDATE, 'YYYYMMDD HH24MISS') AS date
FROM dual;
```


## 참고 문헌

[업무에 바로 쓰는 SQL 튜닝](http://www.yes24.com/Product/Goods/102382080)