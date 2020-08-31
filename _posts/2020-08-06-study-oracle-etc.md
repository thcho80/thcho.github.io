---
title:  "ETC"
excerpt: "오라클성능관리"

categories:
  - 66Day
tags:
  - [공부일기, oracle]

---

## 용어정리

### Block < Extent < Segment < Table-space < Database  

block
- 블록은 데이터 파일 I/O 의 가장 작은 단위이자 할당될 수 있는 공간의 가장 작은 단위
- OS Block을 한 개 이상 합쳐서 생성되며, DB_BLOCK_SIZE 로 지정
- fragmentation

extent
- 여러 연속적인 데이터 블록으로 만들어진 데이터베이스 저장장소 공간 할당의 논리적 단위
- 기본 단위는 64K
- High Water Mark(HWM) : Extent에 데이터를 어디까지 사용했는지를 표시하기 위해 책갈피 

segment
- 테이블스페이스 내의 특정 논리적 저장구조에 대한 모든 데이터를 포함하는 익스텐트의 집합


### PL/SQL (Procedural Language extension to SQL)
종류
- Procedure, Function, Trigger
장점
- 프로시저 생성자와 SQL의 통합
- 성능 향상 : 잘 만들어진 PL/SQL 명령문이라는 가정하에 좋아진다.
- 모듈식 프로그램 개발 가능 : 논리적인 작업 을 진행하는 여러 명령어들을 하나의 블록을 만들 수 있다.
- 이식성이 좋다
- 예외 처리 가능

특징
- 블록 단위의 실행을 제공한다. 이를 위해 BEGIN과 END;를 사용한다. 그리고 마지막 라인에 /를 입력하면 해당 블록이 실행된다.
- 변수, 상수 등을 선언하여 SQL과 절차형 언어에서 사용
- 변수의 선언은 DECLARE절에서만 가능하다. 그리고 BEGIN 섹션에서 새 값이 할당될 수 있다.
- IF문을 사용하여 조건에 따라 문장들을 분기 가능
- LOOP문을 사용하여 일련의 문장을 반복 가능
- 커서를 사용하여 여러 행을 검색 및 처리
- PL/SQL에서 사용 가능한 SQL은 Query, DML, TCL이다.   
	DDL (CREATE, DROP, ALTER, TRUNCATE …), DCL (GRANT, REVOKE) 명령어는 동적 SQL을 이용할 때만 사용 가능하다.
- PL/SQL의 SELECT문은 해당 SELECT의 결과를 PL/SQL Engine으로 보낸다.   
	이를 캐치하기 위한 변수를 DECLARE해야 하고, INTO절을 꼭 선언하여 넣을 변수를 꼭 표현해주어야 SELECT 문장은 반드시 한 개의 행이 검색되어야 한다.   
	그리고 이를 INTO절을 꼭 사용해야한다. 또한 검색되는 행이 없으면 문제가 발생한다.

PL/SQL Block 구조   
|영역						|설명														|옵션/필수	|	|
|-----------------------|-------------------------------------------------------|-------|---|
|DECLARE (선언부)			|PL/SQL에서 사용하는 모든 변수나 상수를 선언하는 부분으로서 DECLARE로 시작	|옵션		|	|
|						|변수/상수/커서 등 을 선언 										|		|	|	
|BEGIN (실행부)			|절차적 형식으로 SQL문을 실행할수 있도록 							|필수		|	|
|						|절차적 언어의 요소인 제어문, 반복문, 함수 정의 등 로직을 기술				|옵션		|	|
|EXCEPTION (예외 처리부)	|PL/SQL문이 실행되는 중에 에러처리								|		|	|
|END (실행문 종료)			|														|필수		|	|
 
