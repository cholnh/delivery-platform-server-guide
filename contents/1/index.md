# :truck: 배달 서비스 플랫폼 API 서버 가이드

<br/><br/>



## :speech_balloon: 개요

비즈니스 예시(배달 서비스)를 토대로 **도메인 설계부터 배포까지** 
직접 구현해 보고 작동 원리를 자세하게 공부하는 프로젝트입니다. 
처음 어떤 아이템을 만들지 고민하는 과정부터 문제 해결(아이템 개발)을 하기 위한 전략과 도구 선택, 
프로젝트 관리, 구현 및 배포까지 프로젝트가 진행되는 모든 발자취를 기록합니다. 
더 좋은 구현 방법, 더 효율적인 설계, 잘못된 내용이 있다면 **[이곳을](https://github.com/cholnh/delivery-platform-server-guide/issues)** 
통해 언제든 리뷰해 주시면 감사하겠습니다.

<br/><br/>



## :memo: Table of Contents

- [1. 다루는 내용](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/1/index.md) :point_left: **여기**
- [2. 도메인 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md)
- [3. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md)
- [4. 시스템 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md)
- [5. 마일스톤](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md)
- [6. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md)
- [7. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md)
- [8. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/8/index.md)
- [9. 회고](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/9/index.md)

<br/><br/>



## 1. 다루는 내용
해당 프로젝트는 아래와 같은 기술 스택을 다루게 됩니다.  
상황에 따라 추가/수정/삭제될 수 있습니다.

<br/>

### 설계 / 개발 패러다임
- DDD
- MSA
- TDD
- REST

### 클라우드
- GKE
- Kubernetes
- Docker
- NginX

### 핵심 마이크로 서비스
- Spring Boot
    + Spring Batch
    + EHCache

### 데이터 관리
- Spring Data Jpa
- ElasticSearch

### 구성 관리
- Spring Cloud Config

### 비동기 메시징
- Spring Cloud Stream
- RabbitMQ

### 회로차단/폴백/벌크헤드
- Spring Cloud
- Spring Netflix OSS

### 빌드/배포
- Travis CI

### 로깅
- Spring Cloud Sleuth
- Papertrail
- Zipkin
~~- ELK (ElasticSearch, Logstash, kibana)~~

### 보안
- Spring Oauth2
- Spring Cloud Security

### 모니터링
- Spring Actuator
- Grafana
- Prometheus

### SCM
- Git
- GitHub