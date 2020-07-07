---
title:  "MEMO"
excerpt: "MEMO"

toc: true
toc_sticky: true
header:
  teaser: "/assets/image/IMG_0081.JPG"

categories:
  - dev
---

## 1. linux 실행파일 만들기
chmod +x filename  
chmod -x filename

## 2. eclipse theme setting
eclipse / install New Software...  
http://eclipse-color-theme.github.com/update

## 3. 스프링부트 포트사용 프로세스 제거
- 윈도우즈  
cmd > netstat -ano | findstr 8080  
cmd > taskkill /F /PID 8964  

## 4. 스프링부트 재기동시 에러