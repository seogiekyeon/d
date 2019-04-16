---
layout: post
title: "Amazon Web Service EC2 Instance Setting"
date: 2017-11-30
excerpt: "Amazon Web Service EC2 Instance Setting"
tags: [AWS, SERVER]
feature: {{ site.url }}/assets/img/aws_logo.jpg
comments: true
---

Amazon Elastic Compute Cloud(Amazon EC2)는 Amazon Web Services(AWS) 클라우드에서 확장식 컴퓨팅을 제공합니다. Amazon EC2를 사용하면 하드웨어에 선투자할 필요가 없어 더 빠르게 애플리케이션을 개발하고 배포할 수 있습니다. Amazon EC2를 통해 원하는 만큼 가상 서버를 구축하고 보안 및 네트워크 구성과 스토리지 관리가 가능합니다. 또한 Amazon EC2는 요구 사항이나 갑작스러운 인기 증대 등 변동 사항에 따라 신속하게 규모를 확장하거나 축소할 수 있어 서버 트래픽 예측 필요성이 줄어듭니다.

## 1. AWS 콘솔 로그인

https://aws.amazon.com/ko/

## 2. Instance를 생성 할 국가 선택

우측 상단 Welcome to AWS 옆의 메뉴탭을 누르면 선택 가능한 국가(도시)리스트 출력

## 3. EC2 메뉴로 이동

좌측 상단의 Service 메뉴를 누르면 이동가능한 메뉴 출력

## 4. Instance Launch

EC2 페이지 진입 후, 페이지 중간의 Launch Instance(인스턴스 시작) 로 쓰여진 파란 버튼 클릭

## 5. Instance OS 설정 (작성자 기준)

페이지 진입 후 좌측 메뉴트리의 AWS Marketplace를 선택 후 "CentOS" 검색

현재 CentOS 버전에 맞게 Instance OS 설정

**CentOS 버전 확인 명령어** `cat /etc/redhat-release` 페이지 진입 초기에 등장하는 Amazon Linux는 CentOS를 기반으로 Amazon사에서 만든 Linux이며 대부분의 CentOS 명령어를 그대로 사용
{: .notice}

## 6. Instance 설정

* 6-1. Instance의 사양 설정

이 사양은 Instance를 생성 후 사용중에도 변경 가능

보안설정을 마친 후 Review and Launch 버튼을 클릭

**Watch Out!** 해당 사양으로 1초라도 사용 시 바로 1시간의 요금이 부과됩니다.
{: .notice}

* 6-2. Security Group 설정

Edit Security Group 버튼을 찾아 클릭

Add 버튼을 눌러 원하는 포트를 추가

**Watch Out!** AWS 서버 포트관리는 이 Security Group을 통해 관리하며,  이 또한 인스턴스 생성 후 수시로 변경이 가능합니다.
{: .notice}

* 6-3. Launch

위 단계 중에서 변경해야 할 부분이 없으면 우측 하단의 Launch 버튼 클릭

* 6-4. Key Pair 생성

해당 인스턴스로 접근할 키페어를 생성
 
생성된 private key 형식(.pem)을 [PUITTYgen](https://www.puttygen.com/) 을 이용해 public key 형식(.ppk)으로 변환 한 후 [putty](https://www.putty.org/)를 통해 접속을 시도

**Watch Out!** 키페어는 절대 잃어버리지 마십시오.
{: .notice}

## 7. Elastic IP 설정

* 7-1. Elastic IP 메뉴 진입

EC2 메뉴 초기화면으로 돌아와 좌측 메뉴트리의 elastic IP 메뉴 클릭

해당 메뉴의 Allocate new address 버튼 클릭

* 7-2. Elastic IP 를 사용할 Instance 선택

Instance를 클릭하면 현재 생성된 모든 Instance 목록이 나오며

이 중 고정 IP를 사용할 Instance를 선택 한 뒤 Associate 버튼 클릭

* 7-3. 생성된 Elastic IP

생성 후 목록에 나오는 Elastic IP가 해당 Instance로 접근할 고정 IP 이다.

**Watch Out!** ※ 고정 IP 생성 후 연결된 Instance가 없을 시에는 요금이 부과되니 주의하십시오.
{: .notice}

## 8. 원격접속

`6-4` 에서 생성한 키파일(.ppk )을 이용해 서버에 접속

AWS 서버는 root 접근을 제한하고 있으며,  초기 username은 CentOS로 설치 시에 "centos", AmazonLinux를 설치 시 "ec2-user" 이다.
{: .notice}
