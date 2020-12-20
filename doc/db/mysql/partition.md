---
sort: 2
---

# Table Partitioning

- [https://lannstark.tistory.com/35](https://lannstark.tistory.com/35)
- [https://www.youtube.com/watch?v=seTk790HsIM&ab_channel=SQL%EC%A0%84%EB%AC%B8%EA%B0%80%EC%A0%95%EB%AF%B8%EB%82%98](https://www.youtube.com/watch?v=seTk790HsIM&ab_channel=SQL%EC%A0%84%EB%AC%B8%EA%B0%80%EC%A0%95%EB%AF%B8%EB%82%98)
- [https://soye0n.tistory.com/267](https://soye0n.tistory.com/267)


## 파티셔닝(Partitioning)이란

데이터베이스 분할(Partitioning) 또는 파티셔닝은 데이터베이스를 여러 부분으로 분할하는 것이다.   

데이터베이스 분할은 중요한 튜닝기법으로 데이터가 너무 커져서, 조회하는 시간이 길어질 때 또는 관리 용이성, 성능, 가용성 등의 향상을 이유로 행해지는 것이 일반적이다.   

분할된 각 부분을 ‘파티션’이라고 부른다.   

물리적인 데이터 분할이 있지만 DB에 접근하는 app의 입장에서는 이를 인식하지 못함. 

- 단일 INSERT와 단일 또는 범위 SELECT의 빠른 처리
- 데이터의 물리적인 저장소를 분리
- 이력 데이터의 효율적인 관리

## DB 파티셔닝(Partitioning) 종류

- 수평 분할 (Horizontal Partitioning)
- 수직 분할 (Vertical partitioning)

## 분할 기준

- 범위 분할 (range partitioning) : BY RANGE
- 목록 분할 (list partitioning)
- 해시 분할 (hash partitioning) : BY HASH
- 합성 분할 (Composite partitioning)

### 파티션 테이블의 종류와 특징

#### 레인지 파티션
보통 날짜를 파티션 키로 잡는다. 날짜를 키로 잡을 때 YEAR 또는 TO_DAYS 함수의 사용을 추천한다.
레인지 파티션에서 NULL은 어떤 값보다 작은 값으로 간주된다.

#### 리스트 파티션
레인지 파티션과 달리, 나머지 모든 값을 저장하는 MAXVALUE 파티션은 정의할 수 없다.

#### 해시 파티션
특정 테이블만 DROP하는 것이 불가능하다.
새로운 파티션을 추가하는 작업은 단순히 파티션만 추가하는 것이 아니라 기존의 모든 데이터의 재배치 작업이 필요하다.
해시 파티션은 레인지 파티션이나 리스트 파티션과는 상당히 다른 방식으로 관리하기 때문에 해시 파티션이 용도에 적합한 해결책인지 확인이 필요하다.

#### 키 파티션
해시 파티션과 비슷하나, 선정된 파티션 키의 값을 MD5( ) 함수를 이용해 해시 값을 계산하고, 그 값을 MOD 연산해서 데이터를 각 파티션에 분배한다.  
이것이 해시 파티션과의 차이점이다.

#### 서브파티션
서비스의 요건에 따라 기간 단위로 레인지 파티션을 생성하고, 각 레인지 파티션 내에서 다시 지역별로 리스트 서브 파티션을 구성하는 형태의 파티션이 가능하다.  
하지만 MySQL에서는 최대로 사용 가능한 파티션의 개수가 다른 DBMS보다 상당히 제한적이라 서브 파티션으로 얻을 수 있는 이점은 별로 없다.



## 파티션 테이블의 쿼리

### INSERT

MySQL 서버는 INSERT 되는 칼럼의 값 중 파티션 키를 이용해서 파티션 표현식을 평가하고, 그 결과를 이용해 레코드가 저장될 적절한 파티션을 결정한다.


### UPDATE

UPDATE 쿼리의 WHERE 조건에 파티션 키 칼럼이 조건으로 존재한다면 그 값을 이용해 레코드가 저장된 파티션에서 빠르게 대상 레코드를 검색할 수 있다. 하지만 WHERE 조건에 파티션 키 칼럼의 조건이 명시되지 않았다면 MySQL 서버는 변경 대상 레코드를 찾기 위해 테이블의 모든 파티션을 검색해야 한다.


### SELECT

두 가지 조건이 중요하다.
- WHERE 절의 조건으로 검색해야 할 파티션을 선택할 수 있는가?
- WHERE 절의 조건이 인덱스를 효율적으로 사용할 수 있는가?

위 두 조건에 따라 아래와 같은 조합이 가능하다.

- 파티션 선택 가능 + 인덱스 효율적 사용 가능 : 가장 효율적으로 처리
- 파티션 선택 불가 + 인덱스 효율적 사용 가능 : 파티션 개수만큼의 테이블에 대해 인덱스 레인지 스캔을 한 다음 결과를 병합해서 가져오는 것과 같다
- 파티션 선택 가능 + 인덱스 효율적 사용 가능 : 파티션에 대해 풀 테이블 스캔을 한다.
- 파티션 선택 불가 + 인덱스 효율적 사용 불가 : 모든 파티션에 대해 풀 테이블 스캔 (최악..)

MySQL에서 모든 인덱스는 파티션 단위로 생성되며, 파티션에 관계없이 테이블 전체 단위로 글로벌하게 하나의 통합된 인덱스는 지원하지 않는다.
실제 MySQL 서버는 여러 파티션에 대해 인덱스 스캔을 수행할 때, 각 파티션으로부터 조건에 일치하는 레코드를 정렬된 순서대로 읽으면서 Priroity Queue에 임시로 저장한다. 그리고 Priority Queue에서 다시 필요한 순서대로 데이터를 가져간다.

파티션 프루닝 : 최적화 단계에서 필요한 파티션만 골라내고 불필요한 것들은 실행 계획에서 배제하는 것 (EXPLAIN PARTITIONS로 확인할 수 있다)


## 파티션 테이블 주의사항

MySQL에서는 일반적으로 테이블을 파일 단위로 관리하기 때문에 MySQL 서버에서 동시에 오픈된 파일의 개수가 상당히 많아질 수 있다.  
이를 제한하기 위해 open-files-limit 시스템 변수가 존재한다.  
파티션을 많이 사용하는 경우에는 open-files-limit를 적절히 높은 값으로 다시 설정해 줄 필요가 있다.

MySQL에서는 파티션 테이블이 가지는 파티션의 개수가 늘어날수록 성능이 더 떨어질 수 있다.


## Example

### 테이블 생성시 적용
```sql
CREATE TABLE t1 ( 
  id 
  INT, year_col INT 
) PARTITION BY RANGE (year_col) ( 
  PARTITION p0 VALUES LESS THAN (1991)
  , PARTITION p1 VALUES LESS THAN (1995)
  , PARTITION p2 VALUES LESS THAN (1999) 
);
```
> 1991년 미만이면 p0, 1991~1994면 p1, 1995~1998이면 p2로 분할

### 기존 테이블 파티션 적용
```sql
CREATE TABLE t1 ( 
  id INT
  , year_col INT 
);

ALTER TABLE t1 
  PARTITION BY HASH(id) 
  PARTITIONS 8;
```


```sql
ALTER TABLE t2 ADD PARTITION (
  PARTITION p3 VALUES LESS THAN (2002)
);
```
