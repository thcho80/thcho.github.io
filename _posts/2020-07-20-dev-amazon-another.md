---
title:  "AWS에 OPENVPN설치-작업중"
excerpt: "openVpn서버를 이용하여 private subnet의 EC2에 접속"

categories:
  - dev
tags:
  - AWS

---

QuickStart Amazon Linux 2(기본인스턴스 사용), Elastic IP 할당

### 1. yum - sudo yum update
- 오류발생  
ps -ef | grep yum  (프로세스 확인)
kill -9 15211 (프로세스 죽이기) 
rm -rf /var/run/yum.pid 

### 2. docker 
- 설치
[ec2-user@ip-172-31-39-218 ~]$ sudo yum install docker

- 설정
[ec2-user@ip-172-31-39-218 ~]$ sudo usermod -aG docker ec2-user
[ec2-user@ip-172-31-39-218 ~]$ sudo systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.

- openVpn 이미지 다운로드

```
[ec2-user@ip-172-31-39-218 ~]$ sudo docker pull kylemanna/openvpn
Using default tag: latest
latest: Pulling from kylemanna/openvpn
c9b1b535fdd9: Pull complete
48ae345d34a0: Pull complete
9660e571431b: Pull complete
294766247fac: Pull complete
e0cfc9e9959e: Pull complete
Digest: sha256:f638aff0997e519ab356ea4770d33df08c69cbc8fde6071130e5459e92d266d7
Status: Downloaded newer image for kylemanna/openvpn:latest
docker.io/kylemanna/openvpn:latest
[ec2-user@ip-172-31-39-218 ~]$
```

다음과같은 오류가 발생한다면
> Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running? 오류발생  

아래 커맨드 수행
$sudo systemctl status docker -상태확인

$sudo systemctl start docker

$sudo systemctl enable docker


### 3. 설치

#### 3.1 설정파일 및 log파일이 저장될 위치를 생성

mkdir ovpn-data
OVPN_DATA="/home/ec2-user/ovpn-data"

#### 3.2 docker 실행
```
[ec2-user@ip-172-31-39-218 ~]$ docker run -v $OVPN_DATA:/etc/openvpn --log-driver=none --rm kylemanna/openvpn ovpn_genconfig -u udp://ec2-13-124-146-252.ap-northeast-2.compute.amazonaws.com               docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.40/containers/create: dial unix /var/run/docker.sock: connect: permission denied.
See 'docker run --help'.
[ec2-user@ip-172-31-39-218 ~]$ sudo docker run -v $OVPN_DATA:/etc/openvpn --log-driver=none --rm kylemanna/openvpn ovpn_genconfig -u udp://ec2-3-34-244-248.ap-northeast-2.compute.amazonaws.com
Processing PUSH Config: 'block-outside-dns'
Processing Route Config: '192.168.254.0/24'
Processing PUSH Config: 'dhcp-option DNS 8.8.8.8'
Processing PUSH Config: 'dhcp-option DNS 8.8.4.4'
Processing PUSH Config: 'comp-lzo no'
Successfully generated config
Cleaning up before Exit ...
[ec2-user@ip-172-31-39-218 ~]$
```
udp://ec2-13-124-146-252.ap-northeast-2.compute.amazonaws.com 는 작업하고자 하는 웹서버의 dns이며 이는 aws에서 자동으로 할당해준다.  

DNS hostname이 비활성화되어있는 경우 다음과 같이 활성화시킨다.
1. Services메뉴에서 VPC 선택
2. 왼쪽 메뉴의 Your VPCs 선택
3. 우측 VPC목록에서 사용중인 VPC에서 마우스 우클릭 (또는 선택후 상단 Actions버튼 클릭)
4. 나타나는 팝업 메뉴에서 Edit DNS Hostnames 선택 후 Yes 로 변경
이후 인스턴스를 Stop->Start하시거나 , 신규로 생성시에는 Public DNS가 생성되는걸 보실수 있습니다.

### 4. 환경설정

#### 4.1. 클라이언트에서 사용할 ovpn설정파일 생성
```
[ec2-user@ip-172-31-39-218 ~]$ sudo docker run -v $OVPN_DATA:/etc/openvpn --log-driver=none --rm -it kylemanna/openvpn ovpn_initpki

init-pki complete; you may now create a CA or requests.
Your newly created PKI dir is: /etc/openvpn/pki


Using SSL: openssl OpenSSL 1.1.1d  10 Sep 2019

Enter New CA Key Passphrase:	//thcho?1
Re-Enter New CA Key Passphrase:	//thcho?1
Generating RSA private key, 2048 bit long modulus (2 primes)
.......................................+++++
...........................+++++
e is 65537 (0x010001)
Can't load /etc/openvpn/pki/.rnd into RNG
139945506868552:error:2406F079:random number generator:RAND_load_file:Cannot open file:crypto/rand/randfile.c:98:Filename=/etc/openvpn/pki/.rnd
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Common Name (eg: your user, host, or server name) [Easy-RSA CA]: thcho
```
2. 키파일생성 후 위에서 입력한 Passphrase 입력  

```
Using SSL: openssl OpenSSL 1.1.1d  10 Sep 2019
Generating a RSA private key
......+++++
..........................+++++
writing new private key to '/etc/openvpn/pki/private/ec2-13-124-146-252.ap-northeast-2.compute.amazonaws.com.key.XXXXDcHmBh'
-----
Using configuration from /etc/openvpn/pki/safessl-easyrsa.cnf
Enter pass phrase for /etc/openvpn/pki/private/ca.key:
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
commonName            :ASN.1 12:'ec2-13-124-146-252.ap-northeast-2.compute.amazonaws.com'
Certificate is to be certified until Jul  5 06:09:30 2023 GMT (1080 days)

Write out database with 1 new entries
Data Base Updated

Using SSL: openssl OpenSSL 1.1.1d  10 Sep 2019
Using configuration from /etc/openvpn/pki/safessl-easyrsa.cnf
Enter pass phrase for /etc/openvpn/pki/private/ca.key:

An updated CRL has been created.
CRL file: /etc/openvpn/pki/crl.pem
```
3. 생성파일 조회  
```
[ec2-user@ip-172-31-39-218 ~]$ ls -al ovpn-data/
total 12
drwxrwxr-x 4 ec2-user ec2-user   67 Jul 20 06:06 .
drwx------ 4 ec2-user ec2-user  112 Jul 20 06:05 ..
drwxr-xr-x 2 root     root        6 Jul 20 06:05 ccd
-rw-r--r-- 1 root     root      726 Jul 20 06:05 openvpn.conf
-rw-r--r-- 1 root     root      890 Jul 20 06:05 ovpn_env.sh
drwx------ 8 root     root     4096 Jul 20 06:09 pki
[ec2-user@ip-172-31-39-218 ~]$
```

#### 4.2 클라이언트 접속설정

##### 4.2.1. 클라이언트가 서버에 붙을 수 있도록 설정  
```
sudo docker run -v $OVPN_DATA:/etc/openvpn --log-driver=none --rm -it kylemanna/openvpn easyrsa build-client-full THCHO nopass

- 위 명령어를 실행하면 ovpn-data경로에 클라이언트용 인증서를 생성해 주는데 명령어 뒷부분의 "THCHO"은 원하는 것으로 변경

[ec2-user@ip-172-31-39-218 ~]$ sudo docker run -v $OVPN_DATA:/etc/openvpn --log-driver=none --rm -it kylemanna/openvpn easyrsa build-client-full THCHO nopass

Using SSL: openssl OpenSSL 1.1.1d  10 Sep 2019
Generating a RSA private key
.................................................................+++++
.....+++++
writing new private key to '/etc/openvpn/pki/private/THCHO.key.XXXXjGPmLo'
-----
Using configuration from /etc/openvpn/pki/safessl-easyrsa.cnf
Enter pass phrase for /etc/openvpn/pki/private/ca.key:
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
commonName            :ASN.1 12:'THCHO'
Certificate is to be certified until Jul  5 06:16:42 2023 GMT (1080 days)

Write out database with 1 new entries
Data Base Updated
[ec2-user@ip-172-31-39-218 ~]$
```

2. 명령어를 실행하면 Docker 컨테이너 안에만 파일이 생성되므로 아래 명령어를 실행해서 EC2 장비로 가져오자.
```
sudo docker run -v $OVPN_DATA:/etc/openvpn --log-driver=none --rm kylemanna/openvpn ovpn_getclient THCHO > THCHO.ovpn
```
역시 CLIENTNAME은 방금전 클라용 인증서 생성 시 넣었던 것과 같은걸 입력해준다.


현재 경로에 ovpn 파일이 생성됐음을 알 수 있다. 
```
[ec2-user@ip-172-31-39-218 ~]$ sudo docker run -v $OVPN_DATA:/etc/openvpn --log-driver=none --rm kylemanna/openvpn ovpn_getclient THCHO > THCHO.ovpn
[ec2-user@ip-172-31-39-218 ~]$ ls -al
total 24
drwx------ 4 ec2-user ec2-user  130 Jul 20 06:19 .
drwxr-xr-x 3 root     root       22 Jul 20 05:42 ..
-rw------- 1 ec2-user ec2-user  911 Jul 20 06:05 .bash_history
-rw-r--r-- 1 ec2-user ec2-user   18 Jan 16  2020 .bash_logout
-rw-r--r-- 1 ec2-user ec2-user  231 Jul 20 06:00 .bash_profile
-rw-r--r-- 1 ec2-user ec2-user  231 Jan 16  2020 .bashrc
drwxrwxr-x 4 ec2-user ec2-user   67 Jul 20 06:06 ovpn-data
drwx------ 2 ec2-user ec2-user   29 Jul 20 05:42 .ssh
-rw-rw-r-- 1 ec2-user ec2-user 4937 Jul 20 06:19 THCHO.ovpn
```
이를 WinSCP와 같은 SFTP 툴로 다운로드를 하면 된다.


### 5. ovpn서버 기동

3. 서버와 클라이언트 설정이 모두 완료되었다. 이제 VPN 서버를 띄워보자.
```
sudo docker run -v $OVPN_DATA:/etc/openvpn -d -p 1199:1194/udp --cap-add=NET_ADMIN kylemanna/openvpn  
```
위와 같이 실행하면 1194 포트로 VPN 서버가 구동된다. 그러나 어떤 인터넷 서비스 업체들은 잘 알려진 VPN 포트를 막기도 하고, 외부에서의 접속 시도도 빈번할 수 있으니 바꾸는 것을 권장한다.
바꾸려면 1194:1194 중 왼쪽 포트 번호만 변경해야 한다.
해당포트를 바꾼뒤 클라이언트용 ovpn 설정파일의 접속포트를 바꾼 포트로 변경해야한다
```
docker run -v $OVPN_DATA:/etc/openvpn -d -p 1977:1194/udp --cap-add=NET_ADMIN kylemanna/openvpn
```
4. Security Group 추가
OpenVPN으로 사용할 포트를 Security Group에 추가를 해주어야만 모바일, 노트북 등에서 접속이 가능하다. 
EC2 인스턴스를 선택 후 하단에 나오는 Descriptions에서 Security groups를 통해 바로 설정으로 이동하자.


기본적으로 리눅스용 Security Groups에는 SSH를 위한 22번 포트만 열려 있다.


여기서 Edit 버튼을 눌러 편집을 해보자.


Add Rule 클릭 후 아래와 같이 정보를 입력 후 Save 버튼을 누르면 된다.

### 6. ovpn 접속

접속클라이언트는 openvpn-install-2.4.9-I601-Win10.exe 사용
[다운로드](https://openvpn.net/community-downloads/)

### 7. openVpn 구동시 에러 및 경고
1. 접속이 안될때
- 뭔격포트를 1194를 Security Group에 등록한 접속IP로 변경

2. openvpn log에 warnning발생
> WARNING: 'link-mtu' is used inconsistently, local='link-mtu 1541', remote='link-mtu 1542'
> WARNING: 'comp-lzo' is present in remote config but missing in local config, remote='comp-lzo'  

comp-lzo no를 클라이언트의 config/.ovpn 파일에 추가해준다

### 8. 결과
클라이언트에서 public vpc에 구축한 openvpn을 통하여 공인아이피가 할당되지 않는 private subnet 내의 ec2 인스턴스에 자유롭게 접속할 수 있다
NAT인스턴스와 openVPN서버는 별도로 구성해야 동작한다.
- NAT 시큐리티그룹에 