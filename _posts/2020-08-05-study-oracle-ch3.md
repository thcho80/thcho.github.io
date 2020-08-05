---
title:  "Ch3"
excerpt: "오라클성능관리"

categories:
  - 66Day
tags:
  - 공부일기, oracle

---

## ch3

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