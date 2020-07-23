---
title:  "아마존 웹서비스(5)"
excerpt: "route 53"

categories:
  - dev
tags:
  - AWS

---

### 정의

Route 53
- 도메인주소를 IP주소로 변환해주는 DNS서비스
- 사용자의 요청을 Amazon EC2 인스턴스, Elastic Load Balancing 로드 밸런서, Amazon S3 버킷 등 AWS에서 실행되는 인프라에 효과적으로 접속


### 무료도메인
[https://my.freenom.com/](https://my.freenom.com/)
Route 53작업후 생성된 네임서버로 무료도메인의 네임서버를 변경해주는 작업 필요

### Route 53 콘솔
[Create Record Set]
Name - 하위 도메인 설정(ex: www)
Type - A 레코드, 
Alias - Yes
Alias Target - ELB
(ex: thcho-elb-1203759863.ap-northeast-2.elb.amazonaws.com)


