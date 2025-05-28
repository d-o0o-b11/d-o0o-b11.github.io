---
title: 8장 정리 - (23강)
description: SQL의 순서
# author: d_o0o_b
categories: [Reading, SQL-Level-Up]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 23강: 레코드에 순번 붙이기
일단 순서 조작의 기초로 레코드에 순번을 붙이는 방법을 살펴봅시다.

기본 키가 한 개의 필드일 경우 기본키가 여러 개의 필드로 구성되는 경우 그리고 테이블을 그룹으로 분할했을 떄 그룹 내부의 레코드에 순번을 붙이는 ㄴ경우 각각의 방법을 차례대로 소개하겠습니다.

### 1. 기본 키가 한 개의 필드일 경우
기본키는 학생 ID(student_id) 입니다.

#### 윈도우 함수 사용
학생 ID를 오름차수능로 순번을 붙여보겠습니다. ROW_NUMBER 함수를 사용할 수 있는 환경이라면 간단하게 구현할 수 있습니다.

```

SELECT student_id, ROW_NUMBER() OVER(ORDER BY student_id) AS seq
FROM Weights;

```


#### 상관 서브쿼리 사용
MySQL처럼 ROW_NUMBER 함수를 사용할 수 없는 환경에서는 상관 서브쿼리를 사용해야 합니다.

```

SELECT student_id, 
    (
        SELECT COUNT(*) 
        FROM Weights W2
        WHERE W2.student_id <= W1.student_id
    ) AS seq
FROM Weights W1;

```

이 서브쿼리는 재귀 집합을 만들고 요소 수를 COUNT 함수로 셉니다. 기본 키 student_id를 비교 키로 사용하므로 재귀 집합의 요소가 한 개씩 증가합니다. 순번을 사용할 떄 자주 사용하는 트릭입니다.

이런 두 가지 방법은 기능적으로는 동일하지만 성능 측면에서는 7장에서도 설명했던 것철머 윈도우 함수를 사용하는 편이 좋습니다. 윈도우 함수에서는 스캔 횟수가 1회입니다. 또 인덱스 온리 스캔을 사용하므로 테이블에 직접적인 접근을 회피합니다. 한편 상관 서브쿼리를 사용하는 방법에서는 2회의 스캔이 실행됩니다.


### 2. 기본 키가 여러 개의 필드로 구성되는 경우
이번에는 학급(class), 학생 ID(student_id)가 기본키입니다.

#### 윈도우 함수 사용
ROW_NUMBER를 사용하는 경우는 머리를 굴릴 필요가 전혀 없스빈다. ORDER BY의 키를 필드에 추가하기만 하면 됩니다.

```

SELECT student_id, ROW_NUMBER() OVER(ORDER BY class,student_id) AS seq
FROM Weights;

```


#### 상관 서브쿼리를 사용
여러가지 방법이 있지만 가장 간단한 방법은 다중 필드 비교를 사용하는 것입니다. 다중 필드 비교는 이름 그대로 복합적인 필드를 하나의 값으로 연결하고 한꺼번에 비교하는 기능입니다.

```

SELECT class, student_id, 
    (
        SELECT COUNT(*) 
        FROM Weights W2
        WHERE (W2.class, W2.student_id) <= (W1.class, W1.student_id)
    ) AS seq
FROM Weights W1;

```

이 방법의 장점은 필드 자료형을 원하는대로 지정할 수 있다는 것입니다. 숫자와 문자열, 문자열과 숫자라도 가능합니다. 암묵적인 자료형 변환도 발생하지 않으므로 기본 키 인덱스도 사용할 수 있습니다. 또한 필드가 3개 이상일 때도 간단하게 확장할 수 있습니다.

### 3. 그룹마다 순번을 붙이는 경우
학급마다 순번을 붙이는 경우입니다. 테이블을 그룹으로 나누고 그룹마다 내부 레코드에 순번을 붙이는 것입니다.

#### 윈도우 함수 사용
class 필드에 PARTITION BY를 적용해줍니다.

```

SELECT class, student_id, ROW_NUMBER() OVER(PARTITION BY class ORDER BY student_id) AS seq
FROM Weights;

```


#### 상관 서브쿼리 사용

```

SELECT class, student_id, 
    (
        SELECT COUNT(*) 
        FROM Weights W2
        WHERE W2.class = W1.class AND W2.student_id <= W1.student_id
    ) AS seq
FROM Weights W1;

```


### 4. 순번과 갱신
