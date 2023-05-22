---
layout: post
title:  "My Beautiful Web Site Project (1)"
summary: "Build web site using AWS"
author: ms9648
date: '2023-05-19 15:35:23 +0530'
category: Web
thumbnail: /assets/img/posts/code.jpg
keywords: web site, 데이터, 프로젝트, 웹사이트, aws
permalink: /blog/web-site-project-1/
usemathjax: true
---

## 들어가며
4-1학기에 들어서며 멀티프로세서컴퓨팅이라는 과목을 수강하고 있습니다. 수업 시간에 배운 AWS 환경 설정 방법과 과거 클라우드 기반 머신러닝 프로젝트에서 경험했던 Elasticsearch 사용 경험을 바탕으로 웹사이트를 구축해보고자 본 포스팅을 작성하게 되었습니다. 처음부터 차근차근 진행해보도록 하겠습니다.

## 가상머신을 작성하기 위한 AWS 선택하기
### EC2 인스턴스 실행하기
먼저, [`https://aws.amazon.com/ko/`](https://aws.amazon.com/ko/)에 접속한 뒤 로그인을 합니다.

계정에는 두 가지가 있습니다.
- 루트 사용자: 모든 리소스에 대한 액세스 권한을 가짐
- IAM 사용자: 특정 권한에 대한 제약을 가짐

EC2 서비스를 클릭하여 인스턴스를 시작합니다.

1. AMI 선택
   
  프리티어 계정이 사용 가능한 `Amazon Linux 2 AMI (HVM) - Kernel 5.10, SSD Volume Type`을 선택합니다.

> AMI: 사용자에게 필요로 하는 운영체제, 애플리케이션, 서버 등 소프트웨어 설정을 담고 있는 템플릿

2. 인스턴스 유형 선택
  
  프리티어 계정이 사용 가능한 `t2.micro: 가상 CPU 1개, 메모리 1GB`를 선택합니다.

3. 키페어 생성
  기존에 사용하고 있는 키페어가 없는 경우 키페어를 새로 생성합니다.
  RSA 유형의 `.pem` 키를 선택하였습니다.

> 저는 putty 클라이언트를 사용할 예정이기 때문에 후에 `.ppk` 파일 형식으로 변경할 것입니다.

키페어란 퍼블릭 키와 프라이빗 키로 구성되며 Amazon EC2 인스턴스에 연결할 때 자격 증명 입증에 사용하는 `보안 자격 증명 집합`입니다.
EC2는 퍼블릭 키를 인스턴스에 저장하며 프라이빗 키는 사용자가 저장합니다.
Linux 인스턴스의 경우 프라이빗 키를 이용하여 인스턴스에 안전하게 SSH로 연결할 수 있습니다.

4. ssh 접속 포트(port: 22) 이외에 앞으로 작성할 홈페이지를 브라우저에서 확인할 수 있도록 HTTP 80, HTTPS 443를 추가합니다.

## SSH 연결

저는 Putty client로 인스턴스에 연결하는 것이 익숙하기 때문에 Putty를 사용하겠습니다.

Putty는 리눅스나 유닉스 계열의 서버에 원격으로 접속할 수 있는 클라이언트 프로그램입니다. PuTTY는 Amazon EC2에서 생성된 프라이빗 키 형식(.pem)을 기본적으로 지원하지 않습니다. 

따라서, PuTTY를 사용하여 인스턴스에 연결하기 전에 프라이빗 키를 .ppk 파일로 변환해야 합니다. 이 변환에는 PuTTYgen 도구를 사용합니다.

### PuttyGen으로 키 형식 변환
1. `Conversions` → `Import key` 버튼을 누릅니다.
2. `.ppk` 파일 형식으로 변환하고 싶은 `.pem` 파일을 선택합니다.
     - 인스턴스 생성 당시 생성하였던 프라이빗 키(`.pem`)를 선택합니다.
3. 파일을 선택하면 `Save private key` 버튼이 활성화됩니다. 버튼을 누르면 Key passphrase를 활성화할 것이냐고 묻는데, 이는 SSH키로 접속 시 암호를 사용하는 옵션으로 리눅스 계정에 대한 암호가 아닌 SSH키에 대한 암호로 보안을 이중으로 하고 싶을 때 사용합니다.
4. 변환하였으면 `로컬`에 잘 저장해둡시다.

### Putty로 연결하기
Putty 클라이언트를 실행하여 인스턴스에 연결합니다.

위에서 `Host Name` 부분에는 AWS에서 발급받은 아래와 같은 형식의 퍼블릭 DNS(IPv4)를 입력합니다.
- `instance-user-name@instance-public-dns-name`

그런 다음 `Connection` **→** `Auth` **→** `Credentials`에서 `.ppk` 파일을 업로드합니다.

결과는 다음과 같습니다.

<img src = "https://i.postimg.cc/XqSDfJvP/Connect-instance.jpg" style="width: 60%; height: 400px; display: block; margin: 0 auto;"/>