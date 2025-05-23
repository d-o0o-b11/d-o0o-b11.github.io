---
title: 4장 정리 - 완강
description: 집약과 자르기
# author: d_o0o_b
categories: [Reading, SQL-Level-Up]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 12강: 집약

SQL에는 집약 함수(aggregate function) 라는 특별한 함수들이 있다. 이 함수들은 여러 개의 레코드를 하나의 레코드로 요약해주는 기능을 한다. 대표적인 집약 함수는 다음과 같다.

- COUNT
- SUM
- AVG
- MAX
- MIN

표준 SQL에는 위의 5개만 집약 함수로 포함되어 있다. 이 외에도 다양한 집약 함수가 존재하지만, 확장 기능에 해당하므로 구현체에 따라 다를 수 있다.


### 여러 개의 레코드를 한 개의 레코드로 집약
비집약 테이블에서는 관련 정보가 여러 레코드에 흩어져 있어서 데이터를 조회하려면 여러 번 WHERE, SELECT 문을 사용해야 한다. 하지만 애플리케이션에서는 보통 한 번의 조회로 한 사람의 정보를 한 번에 가져오는 방식이 편리하다.

그렇다고 여러 쿼리 결과를 UNION으로 합치는 방식은 성능상 비효율적이며 안티패턴이다. (3장에서 다룸)

따라서 이럴 때는 관련된 정보를 하나의 레코드에 담는 집약 테이블을 설계하는 것이 좋다. 이는 데이터 모델링 관점에서도 바람직하며, 예를 들어 “사람”이라는 엔티티는 하나의 레코드로 표현되어야 정보 접근이 편리해진다.


#### CASE 식과 GROUP BY 응용
비집약 테이블을 집약 테이블로 만들기 위해선 다음과 같은 SQL 구조를 사용한다.

- 사람 단위로 집약 → GROUP BY에 id 사용
- SELECT 문에서는 집계할 항목을 집약 함수로 감싼다

GROUP BY구로 집약했을 때 SELECT 구에 입력할 수 있는 것은 다음 세 가지 뿐이다.

- 상수
- GROUP BY에 사용된 필드
- 집약 함수

필요하다면 이 결과를 INSERT하여 새로운 집약 테이블로 저장할 수 있고, 성능에 큰 문제가 없다면 뷰(View)로 저장해두는 것도 좋은 선택이다.


#### 집약, 해시, 정렬
많은 사람들이 GROUP BY는 정렬 기반일 거라 생각하지만, 최근의 DBMS는 정렬보다 해시 알고리즘을 더 많이 사용한다.

- GROUP BY 필드를 해시 키로 변환하고, 같은 해시 키를 가진 그룹끼리 집약
- 정렬보다 빠르며, 유일성이 높을수록 더 효율적
- 단점: 해시도 정렬도 많은 메모리를 사용한다

충분한 워킹 메모리가 확보되지 않으면 디스크 스왑이 발생하고, SQL이 매우 느려지거나 비정상 종료될 수도 있다. 따라서 집약 SQL은 실제 환경에서 부하 테스트를 꼭 실행해야 한다.


## 13강: 자르기
GROUP BY는 단순히 집약만 하는 구문이 아니다. 모집합을 작은 부분집합으로 나누는 "자르기" 기능도 함께 가지고 있다.

즉, GROUP BY는 다음 두 가지를 동시에 수행한다.

- 자르기 (파티셔닝)
- 집약

### 자르기와 파티션

#### 파티션
GROUP BY로 잘라낸 각 부분집합을 수학적으로 파티션(partition) 이라고 부른다. 파티션은 서로 중복되지 않는 집합으로, 자르기 기준에 따라 여러 방식으로 나뉠 수 있다.

- GROUP BY와 SELECT 양쪽에 자르기 기준 필드를 입력해야 한다.
- PostgreSQL 등에서는 SELECT 구에 붙인 별칭(alias) 을 GROUP BY에서 사용할 수 있다. (표준 SQL에는 없는 기능이므로 주의)

또한 CASE 문이나 계산식도 GROUP BY 기준으로 사용할 수 있다. 식이 복잡해질수록 CPU 오버헤드는 생기겠지만, 이는 데이터를 읽어온 이후의 이야기이므로 데이터 접근 경로에는 큰 영향을 주지 않는다.

### PARTITION BY - 윈도우 함수의 자르기 역할
PARTITION BY는 윈도우 함수에서 사용하는 구문으로, GROUP BY에서 집약 기능만 뺀 것이라 할 수 있다.

- GROUP BY: 데이터를 나누고 → 집약
- PARTITION BY: 데이터를 나누기만 하고 → 원본을 유지하며 계산

즉, GROUP BY는 데이터를 요약하여 출력 레벨이 바뀌지만, PARTITION BY는 정보를 덧붙이는 수준으로 출력 레벨은 그대로 유지된다.

그리고 PARTITION BY도 CASE, 계산식 등 다양한 기준으로 자르기가 가능하다. SQL 쿼리를 눈으로 보면 "이런 복잡한 것도 되는구나!" 하고 감탄할 수 있는 부분이다.