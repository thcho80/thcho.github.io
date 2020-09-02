---
title:  "함수호출순서"
excerpt: "컴퓨터공학"

categories:
  - 66Day
tags:
  - [공부일기, 컴퓨터공학]

---

## 스택포인터, 프레임포인터, 스택

### (1) 스택포인터
FP레지스터에 저장된 값을 스택에 쌓아 모든 스택프레임의 경계정보를 저장
- 스택프레임 : 함수 호출시 할당되는 메모리 블록(지역변수의 선언으로 인해 할당되는 메모리 블럭)
- 스택포인터(SP)레지스터 : 스택프레임을 가르키는 레지스터
- 프레임포인터(FP)레지스터 : 되돌아갈 SP레지스터의 정보를 저장하는 레지스터


### 레지스터
> 64비트 : R로 시작, 32비트 : E로 시작  

RAX : 누산기(Accumulator)레지스터
RBX : 베이스 레지스터
RCX : 카운터 레지스터
RDX : 데이터 레지스터
RSP : 스택포인터 레지스터
RBP : 베이스포인터 레지스터(스택의 시작점)

RSI : 근원지(source) 인덱스 레지스터
RDI : 목적지(destination) 인덱스 레지스터
- 이 레지스터들은 포인터나 인덱스라고 부르기도함
- 포인터 레지스터 : 메모리위치를 가르키는 64비트 주소정보 저장
- 인덱스 레지스터 : 데이터 R/W시 근원지와 목적지 정보를 가르키는 포인터
  
  
RIP : 프로세서가 읽고있는 현재 명령의 위치를 가리키는 명령포인터(Instruction Pointer)레지스터

### ret와 stp
ret : 다음에 실행해야하는 명령이 위치한 메모리 주소
sfp : 스택 베이스 값. 스택주소값 계신시 현재 스택의 바닥, 즉 기준이되는 프레임포인터 값을 저장하는 곳, 4Byte

### 어셈블리어
- 상수 : $ ex) $0x4
- 문자열 : .string 디렉티브선언 ex) str: .string "hello world\n"
- 레지스터 : % ex) %eax

### 쉘코드(shell code)
정의 : 쉡을 실행시키는 기계어 코드. 어셈블리어는 기계어와 1:1대응.
생성방법: 어셈블리어 -> 기계어코드 추출
종류 : 
- 로컬쉘 : 대상시스템에 제한혹은 완전한 접근권한을 가진 경우 BOF취약점이용하여 root권한 프로세스를 공격하여 해당프로세스와같은권한 획득
- 원격쉘 : 네트워크상의 대른 대상 시스템에 대한 취약점이 존해하는 프로세스 공격시 사용
- 리버스쉘 : 목표시스템으로부터 공격자에게 연결 요청하도록 하는 쉘코드

### Nop sled
No Operation의 약자. 
아무동작도 하지 않는다는 어셈블리 명령어
Nop 10개는 10줄 skip을 의미하며, Nop개수만큼 주소공간을 확보
> bash쉘 실행 쉘코드의 시작주소를 약간 부정확하게 확인하더라도 Nop개수많큼의 오차범위 안에있는 주소로 리턴주소를 덮어씌우면 공격성공

### strcpy 취약점 공격절차
> strcpy : null byte를 만날때까지 문자열을 복사하는 함수. 즉 크기를 검증하지않는 취약한 함수

``` c
int main(int argc, char *argv[]){
	char buf[256];
	
	strcpy(buf, argv[1]);
	printf("%s\n", buf);
}
```
- 공격절차
1. gdb로 BOF.c 파일 분석
```
gdb -q BOF
(gdb) disassemble main
dump of asembler code for fuction main:
0x0804843b <+0> : 	push	ebp
0x0804843c <+1> : 	mov		ebp, esp
0x0804843e <+3> :	sub		esp,0x100
...
```
0x100은 십진수로 256. buf의 크기

|항목		|
|---	|
|RET(4)	|
|SFP(4)	|
|buf(256)	|
- 260 Byte넘게 입력하면 RET가 덮일것이다
  
  
2. 공격 - 쉘 코드를 메모리 어딘가에 저장
BOF파일은 argv[1]을 buf로 복사하므로 argv[1]에 넣고 Return address를 buf의 주소로 덮어씌우면 된다
```
gdb -q BOF
(gdb) disas main
dump of asembler code for fuction main:
0x0804843b <+0> : 	push	ebp
0x0804843c <+1> : 	mov		ebp, esp
0x0804843e <+3> :	sub		esp,0x100
...
```






### 다른 공격예시
1. buffer의 size 조사
- main함수 disassemble : buffer공간확보위해 16진수로 0x18(10진수24) 빼주는것 확인
- ```sub	rsp,0x18```
- ret는 24 + 4 바아트 위치에 존재

2. hack함수의 시작주소 조사
(gdb) info function




