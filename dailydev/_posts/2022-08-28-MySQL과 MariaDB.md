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

__자동 증갓값__<br>
MariaDB와 오라클에서는 각각 시퀀스라는 오브젝트가 있다. 먼저 시퀀스 오브젝트를 생성한 뒤, 해당 시퀀스명으로 함수를 호출하여 신규 숫자를 채번할 수 있다. MySQL에서는 두 가지 방법으로 자동 증갓값을 저장한다.
첫 번째는 특정 열의 속성으로 자동 증가하는 값을 설정하는 auto_increment를 명시하는 방법, 즉 테이블마다 특정한 하나의 열에만 해당 속성을 정의하여 자동 증가하는 숫자를 저장할 수 있다. 두 번째는 오라클과 마찬가지로 시퀀스 오브젝트를 생성한 뒤 호출하여 활용하는 방법이다.<br>

MySQL, MariaDB auto_increment
```sql
CREATE TABLE TAB
(SEQ INT NOT NULL AUTO_INCREMENT,
 TITLE VARCHAR(20) NOT NULL);
```
MySQL, MariaDB 시퀀스
```sql
CREATE SEQUENCE [시퀀스명]
INCREMENT BY [증감숫자]
START WITH [시작숫자]
NOMINVALUE OR MINVALUE [최솟값]
NOMAXVALUE OR MAXVALUE [최댓값]
CYCLE OR NOCYCLE
CACHE OR NOCACHE
```
다음 값 채번: SELECT NEXTVAL(시퀀스명);<br>

오라클
```sql
CREATE SEQUENCE [시퀀스명]
INCREMENT BY [증감숫자]
START WITH [시작숫자]
NOMINVALUE OR MINVALUE [최솟값]
NOMAXVALUE OR MAXVALUE [최댓값]
CYCLE OR NOCYCLE
CACHE OR NOCACHE
```
다음 값 채번: SELECT 시퀀스명.NEXTVAL FROM DUAL;<br>

__문자 추출__<br>
특정 구간 및 특정 위치의 문자열을 추출할 때 MySQL에서는 SUBSTRING() 오라클에서는 SUBSTR() 함수를 사용<br>
MySQL, MariaDB<br> 
SUBSTRING(열값 또는 문자열, 시작위치, 추출하려는 문자 갯수)
```sql
SELECT SUBSTRING('ABCDE', 2,3);
```
오라클 SUBSTR(열값 또는 문자열, 시작위치, 추출하려는 문자 갯수)
```sql
SELECT SUBSTR('ABCDE',2,3) FROM DUAL;
```

## MySQL과 MariaDB 튜닝의 중요성
최근 MySQL과 MariaDB에 기반을 둔 안정적인 운영서비스를 제공하는 사례를 흔히 접할 수 있다. 지속적인 DBMS 엔진 가동을 위한 소프트웨어 안전성 등이 상용 DBMS와 크게 다르지 않다. 그러나 MySQL 과 MariaDB 에는 기능적인 제약사항이 있기 때문에 DBMS가 제공하느 기능을 더 자세히 알아야하며 제공되는 실행 계획정보를 해석하고 문제점을 인지하거나 대응할 수 있는 능력을 갖춘 뒤 쿼리 튜닝을 진행해야 한다. 요약하면 MySQL과 MariaDB는 기본적으로 무료이고 경량화된 소프트웨어이므로 활용하기 편리하고 유용한 반면 수행 가능한 알고리즘이 적어서 성능적으로 불리하다. 대신 다양한 스토리지 엔진을 적극적으로 사용할 수 있다.

## 참고 문헌

[업무에 바로 쓰는 SQL 튜닝](http://www.yes24.com/Product/Goods/102382080)