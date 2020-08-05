---
title:  "Ch3"
excerpt: "오라클성능관리"

categories:
  - 66Day
tags:
  - [공부일기, oracle]

---

## CH3. 오라클 성능관리

1. Explain plan
	1. explain plan for *QUERY
	2. utlxplan.sql, utlxls.sql, utlxlp.sql
2. Autotrace

3. Sql trace
	1. 자기세션 트레이스
		- TKProf
		- 10046 이벤트트레이스
	2. 다른세션 트레이스
	3. service, module, action단위로 트레이스
	
4. DBMS_XPLAN 패키지
	1. 예상실행계획 출력
	2. 캐싱된 커서의 실제 실행계획 출력
	3. 캐싱된 커서의 row source별 실행계획 출력
	
5. V$SYSSTAT
	- v$sysstat, v$sesstat, v$mystat
	1. 시스템 수행 통계 수집 및 분석
	2. Ratio 기반 성능 분석
	
6. V$SYSTEM_EVENT
	1. v$system_event
	2. v$session_event
	3. v$session_wait
	
7. Response Time Analysis 분석과 OWI

8. Statspack/AWR(Automatic Workload repository)
	- 주기적으로 동적 성능 뷰를 수집
	- Oracle Database에 대한 부하, resource 사용량의 trend 분석, 성능 문제 분석
	- Statspack : 8i~, SQL을 이용한 딕셔너리 조회
	- AWR : 10g~, DMA(Direct Memory Access)방식으로 SGA를 직접 액세스
	
9. ASH(Active Session History)

10. V$SQL

11. end-to-end 성능관리

12. 데이터베이스 성능고도화 정석해법
	- 튜닝 절차
	- 튜닝 3대요소
	- 튜닝 범위
	
	


### 실행계획 plan 테이블
select * from plan_table;
실행계획 로그기록
set lines 200;
explain plan for *QUERY  
실행계획 로그조회
```
@?/rdbms/admin/utlxpls;
```
utlxpls.sql
- select plan_table_output from table(dbms_xplan.display('plan_table',null,'serial'));
utlxplp.sql 
- select * from table(dbms_xplan.display())  

set autotrace on error발생
> SP2-0618: 세션 식별자를 찾을 수 없습니다. PLUSTRACE 롤이 사용으로 설정되었는지 점검하십시오

```
다음에 접속됨:
Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - 64bit Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options

SQL> conn / as sysdba
연결되었습니다.
SQL> @?/sqlplus/admin/plustrce
SQL>
SQL> drop role plustrace;
drop role plustrace
          *
1행에 오류:
ORA-01919: 롤 'PLUSTRACE'(이)가 존재하지 않습니다


SQL> create role plustrace;

롤이 생성되었습니다.

SQL>
SQL> grant select on v_$sesstat to plustrace;

권한이 부여되었습니다.

SQL> grant select on v_$statname to plustrace;

권한이 부여되었습니다.

SQL> grant select on v_$mystat to plustrace;

권한이 부여되었습니다.

SQL> grant plustrace to dba with admin option;

권한이 부여되었습니다.

SQL>
SQL> set echo off
SQL>

```

실행
set lines 200;  
select * from emp;  
실행하면 조회결과와 실행계획이 함께 출력된다

### 10046 trace
10046 Trace 사용 순서 [참고](http://www.gurubee.net/lecture/2130)  

DBMS_XPLAN 패키지 [참고](http://wiki.gurubee.net/pages/viewpage.action?pageId=23429144)