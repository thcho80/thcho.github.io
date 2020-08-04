---
title:  "아마존 웹서비스(6)"
excerpt: "RDS"

categories:
  - dev
tags:
  - AWS

---

### Parameter Group
characterset : utf8mb4
collation	: utf8mb4
time_zone	: asia/seoul

**직접수정해야하는사항**
show variables like 'c%';  

ALTER DATABASE thchodb  
CHARACTER SET = 'utf8'  
COLLATE = 'utf8_general_ci';  

commit;  


ALTER DATABASE thchodb CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci;  

commit;   

### Option Group

### Security Group
web/was 인스턴스 와 openvpn서버 에서만 접근가능하도록 구성


### mysql
aws에 구축한 rds접속 시 DBMS툴에서 실제 사용할 DB이름으로 접속하고 인코딩변수들을 확인해볼것.

클라이언트접속
sudo apt-get install mysql-client
mysql -V
mysql -u admin -h thcho-db.ch4jyizbufnb.ap-northeast-2.rds.amazonaws.com -p



