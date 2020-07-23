---
title:  "ubuntu"
excerpt: "개발환경설정"

categories:
  - dev
tags:
  - ubuntu

---

우분투 개발환경설정

### 1. apt-get update

### 2. java

sudo apt-get install openjdk-8-jre
sudo apt-get install openjdk-8-jdk

### 3. apache
우분투 apt-get install apache2 로 설치했을 경우  

아파치 서버 시작
sudo /etc/init.d/apache2 start  

재 시작  
sudo /etc/init.d/apache2 restart  

멈춤  
sudo /etc/init.d/apache2 stop