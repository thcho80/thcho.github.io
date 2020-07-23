---
title:  "아마존 웹서비스(4)"
excerpt: "Elastic Load Balancer(ELB)설치"

categories:
  - dev
tags:
  - AWS

---

|| ALB | NLB | CLB |
|:--:|:---:|:---:|:---:|
|프로토콜|HTTP, HTTPS|TCP,TLS,UDP|for HTTP, HTTPS, and TCP	|
||||범용적|

### 작업
1. 생성해놓은 서브넷 중 public subnet을 ELB로 묶어준다
2. 보안그룹설정
- 웹서버이므로 TCP 80, 0.0.0.0/0 등록
3. Health check기능
해당인스턴스 Apache웹서버의 기본경로의 index.html파일을 읽어들임
4. 생성된 elb의 DNS name으로 접속

향후 추가작업
아마존 웹서비스(5)
생성된 DNS name을 무료 도메인으로 연결
