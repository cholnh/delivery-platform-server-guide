# 배달 서비스 플랫폼 API 서버 가이드

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

- [1. 다루는 내용](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/1/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [2. 도메인 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [3. 시스템 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [4. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [5. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드) :point_left: 
- [6. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [7. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md#배달-서비스-플랫폼-api-서버-가이드)

<br/><br/>



## 5. 인프라 구현
해당 프로젝트는 가상 비즈니스 상황을 가정하고 가상 서비스에 대한 API 서버를 구현합니다.  

<br/>

### 5-1. 클라우드 인프라

Google Cloud Platform (이하 GCP) 에서는 여러 인프라 서비스를 제공합니다.  
가상 단일 인스턴스를 제공하는 `Google Compute Engine` (이하 `GCE`) 부터,  
Kubernetes 를 쉽게 사용하도록 관리형으로 제공하는 솔루션인 `Google Kubernetes Engine` (이하 `GKE`),  
서버 인프라를 관리할 필요 없이 개발자는 코드만 제공하면 되는 서버리스 플랫폼인 `Google App Engine` 까지 다양한 제품군들이 있습니다.

<br/>

GCE 는 가상 인스턴스를 제공하지만 컨테이너 기술에 적합한 컴퓨팅환경(Container-Optimized OS(COS))으로 설정할 수 있습니다.  
GCE 환경에 가장 적합한 아키텍처는 모노리식 아키텍처라 생각합니다.  
(참고 : [GCE 위에 모놀리식 스프링부트 실행시키기](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/gcp-gce-msa.md#gcp-gce-msa.md#gce-위에-모놀리식-스프링부트-실행시키기))

<br/>

물론 GCE 인프라 위에 Netflix OSS/Spring Cloud 를 사용하거나 Kubernetes 를 사용하여 MSA 를 구성할 수는 있습니다.  
하지만 해당 프로젝트는 오케스트레이션 플랫폼인 Kubernetes 를 사용하기로 했으며 이를 지원하는 `GKE` 를 사용하겠습니다.

<br/><br/>

### 5-2. CI/CD 파이프라인

Github 에서 제공하는 `Github Actions` 은 소프트웨어 개발 Workflow 를 자동화하는 도구입니다.  
마이크로 서비스의 통합, 배포 환경을 자동화하여 빠르고 안정적인 운영 환경을 보장해야 합니다.  

<br/>

아래 링크에 `Github Actions` 사용법을 정리해두었습니다.  
[Github Actions 를 이용한 CI/CD 파이프라인 구축하기](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/ci-cd.md/#github-actions-를-이용한-cicd-파이프라인-구축)



<br/><br/>

---

[[:point_up_2: 위로]](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [1. 다루는 내용](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/1/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [2. 도메인 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [3. 시스템 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [4. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [5. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드) :point_left: 
- [6. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [7. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md#배달-서비스-플랫폼-api-서버-가이드)