---
layout: page
title: Projects
permalink: /projects/
---
* content
{:toc}

## 1. [Cloudike SaaS/On-premise](https://www.cloudike.kr)

Cloudike(B2B용 클라우드 스토리지 서비스) 서비스의 백엔드 프로그래밍 및 서버 관리.

### 1.1. 서버 시스템 관리

* 서버 모니터링 및 장애 대처.
* 모니터링 시스템 구축.

### 1.2. 스토리지 서비스의 부가 기능 API 구현

![Untitled Diagram (2)](https://user-images.githubusercontent.com/54487021/66366439-5da15d80-e9cb-11e9-8655-9d6c0ef4bc8b.png)

S3, Openstack swift storage 등 스토리지 서비스의 RESTFUL-API를 이용하여 클라우드 스토리지 서비스 제공 및 클라우드 스토리지 서비스의 편의성 기능 구현.

* 외부 뷰어 서비스의 RESTFUL_API를 이용한 Autocad, HWP 프리뷰 API 구현.
* 사용자 권한 관리(접속 디바이스 제한, 다운로드 또는 업로드 제한, 접속 IP 제한 등), 사용자 로그 메일링 기능, 공유 폴더 부가 기능 등의 API 구현
* 미디어 파일 변환 오픈소스를 이용한 파일 프리뷰 및 썸네일 파일 생성의 worker task 버그 픽스.
* 파일에 대한 코멘트, 버전 관리 등 이용자 편의 시스템 버그 픽스.

### 1.3. 서비스 관련 스크립트 작성

배치 프로세싱 작업에 필요한 리눅스 쉘 스크립트 및 Python, Javascript 언어의 스크립트 작성.

### 1.4. 결제 시스템 관리

결제 대행 서비스의 REST-API를 통해 결제를 진행 후 데이터베이스 업데이트 후 유저에게 서비스 제공. 버그 픽스를 담당.

### 1.5. 서비스 관리자 편의 시스템 구축

통계 metric 생성 cron job 개발 구현 및 metric 가시화 등의 작업을 담당.
