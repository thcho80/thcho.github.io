---
title:  "볼링게임"
excerpt: "junit 학습테스트"
toc: true
toc_sticky: true

categories:
  - dev
tags:
  - [JUNIT, UML, Design Pattern]
---

## 1.볼링게임

### 1.1 개요
볼링게임을 설계하고 junit을 활용하여 refactoring과정을 학습

### 1.2 제약사항
1. 스페어
2. 스트라이크
3. extra roll

### 1.3 학습내용
1. UML
2. 리펙토링(ugly code제거)
	- 테스트 실패 시 추가된 테스트를 제거하고 기존 로직설계를 수정
	- 변수의 이름은 적절하게
	- 로직을 포함한 부분은 별도 적절한 이름의 함수의 boolean 반환값처리