---
title:  "Ch1"
excerpt: "oracle 성능고도화원리"

categories:
  - 66Day
tags:
  - [공부일기, oracle]

---


## CH1. 기본아키텍쳐

1. 기본구조
	1) 구성도
	2) 접속절차
	
2. DB버퍼캐시
	1) 블럭단위 I/O
	2) 버퍼캐시구조
	3) 캐시버퍼체인
	4) 캐시버퍼LRU체인
	
3. 버퍼락
	1) 정의및 필요성
	2) 버퍼핸들
	3) 버퍼pinning
	4) 클러스터링팩터
	
4. Redo
	1) 종류
	2) 사용목적
	3) 기록시점
	
5. Undo
	1) Undo세그먼트 트랜잭션테이블슬롯
	2) 블럭헤더 ITL슬롯
	3) Lock Byte
	
6. 문장수준읽기일관성
	1) 문장수준읽기일관성 정의
	2) Consistent mode
	3) Current mode
	4) Consistent vs Current
	
7. 블럭클린아웃
	1) Delayed 블럭클린아웃
	2) Fast 블럭클린아웃(Commit 클린아웃)
	3) ITL과 블럭클린아웃
	
8. Snapshot too old
	1) 발생원인
	2) 해결책
	
9. 대기이벤트
	1) 대기이벤트란?
	2) 생성시기
	3) 소멸시기
	4) 

10. Shared pool
	1) 라이브러리 캐시
	2) 데이터딕셔너리 캐시
