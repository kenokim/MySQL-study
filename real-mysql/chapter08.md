# 08. 인덱스

인덱스는 쿼리 튜닝의 기본이다.

### 1. 디스크 읽기 방식

디스크 I/O 는 여전히 느리다. 데이터베이스 성능 튜닝은 디스크 I/O 를 줄이는 것이 관건이다.

SSD 는 전자식 장치이며 HDD 보다 훨씬 빠르다. (OLTP 환경에서 100배 가량 차이) 요즘의 DBMS 용 서버에서는 대부분 SSD 를 채택하고 있다.

랜덤 I/O 가 순차 I/O 보다 부하가 더 크다. 쿼리를 튜닝해서 필요없는 랜덤 I/O 를 줄여야 한다.

### 2. 인덱스란?

인덱스는 INSERT, UPDATE, DELETE 의 성능을 희생하고 그 대신 읽기 속도를 높이는 기능이다.

MySQL 의 key 와 index 는 같은 개념이다.

인덱스의 역할적 구분은 primary key, secondary key 로 나뉠 수 있다.

- Primary key 는 레코드를 대표하는 column 의 값으로 만든 인덱스다.

- Secondary key 는 그 외의 모든 인덱스다.

### 3. B-Tree 인덱스

InnoDB 는 BTREE 인덱스의 leaf node 에 index key 와 primary key 를 저장한다. 따라서 읽기 기능에서 leaf 에서 찾은 primary key 로 탐색을 한 번 더 해야 한다. MyISAM 등은 ROWID (물리적 주소) 를 저장한다.

- INSERT : table insert 가 1일 때 index insert 는 1.5다. InnoDB 에서 unique 하지 않은 key 에 대해 change buffer 를 통해 지연된 쓰기를 한다.
- DELETE : 삭제 대상으로 mark 하고 나중에 지운다. (버퍼링)
- UPDATE : 인덱스 키가 변경되면 삭제 후 다시 생성한다.
- 검색 : 인덱스를 사용하는 검색인지 확인을 해야 한다.

InnoDB 에서 인덱스는 추가적인 쓰기 작업 뿐 아니라 lock 을 걸 때 index 먼저 걸기 때문에 설계를 잘 해야 한다.

* 인덱스 성능에 영향을 미치는 요소
  - 키 값의 크기 : InnoDB 는 page 단위로 저장하는데, key 값이 커지면 한 page 에 적은 수의 노드가 저장된다.
  - BTREE 깊이 : 깊이가 깊어지면 탐색이 오래 걸린다.
  - Cardinality : unique 함이 높아야 인덱스가 효율적일 것
  - 읽어야 하는 레코드의 건수 : index 는 index 를 읽고 table 을 추가로 또 읽기 때문에 (random i/o) 건수가 많아지면 인덱스를 읽지 않는게 성능에 유리하다.

* BTREE 인덱스 읽기 방법 3가지 (EXPLAIN 에서 나오는 값)
  1. index range scan (성능적으로 좋은 방법)
     - 키의 위치를 찾고 PK 를 읽고 데이터를 읽는다. 
  2. index full scan (비효율적)
     - index 를 full 로 읽어서 키의 위치를 찾는다.
  3. loose index scan (좋은 방법)
 
WHERE 조건, GROUP BY, ORDER BY clause 에서 어떤 경우에 인덱스를 사용할 수 있는지 식별할 수 있어야 한다. 
 - left-most 비교가 아닐 경우 사용할 수 없다.
 - multi-column 인덱스의 경우 순서가 맞지 않으면 사용할 수 있다.

### 8. 클러스터링 인덱스

InnoDB 에서만 클러스터링 인덱스를 지원한다. 

Primary key 값에 의해 레코드의 저장 위치가 결정된다. Primary key 기반의 검색이 빠르며 저장과 변경이 느리다. 또한 커버링 인덱스로 사용할 수 있는 장점이 있다. 


