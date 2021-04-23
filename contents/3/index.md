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
- [3. 시스템 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드) :point_left:
- [4. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [5. 마일스톤](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [6. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [7. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [8. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/8/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [9. 회고](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/9/index.md#배달-서비스-플랫폼-api-서버-가이드)

<br/><br/>



## 3. 시스템 설계
해당 프로젝트는 가상 비즈니스 상황을 가정하고 가상 서비스에 대한 API 서버를 구현합니다.  

<br/>

이전 장에서는 도메인 설계를 통해 마이크로 서비스가 될 후보들을 선정하였습니다.  
이번 장은 마이크로 서비스들을 중심으로 커다란 골격, 즉 시스템 구성을 설계해보도록 하겠습니다.

<br/><br/>

### 외부 아키텍처 정의
우선 마이크로 서비스 아키텍처의 예시입니다.  
(각 구성요소들은 변경/대체 가능합니다)

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/outer-arch-2.png" width="1100"/>|
|-|
|그림 1 - 마이크로 서비스 외부 아키텍처 구조|

<br/>

- 인프라  
    서비스 운용의 기반이 되는 하드웨어 인프라입니다.  
    온프렘(On-premise) 환경이나 클라우드 서버에 플랫폼를 올려 구동/운영합니다.  

<br/>

- 플랫폼  
    인프라 영역 위에 어플리케이션을 구동/운영하기 위한 플랫폼이 올라갑니다.  
    코어 서비스 어플리케이션이 잘 작동하도록 도와주는 플랫폼 서비스들(메시지 브로커, 데브옵스 파이프라인 등)이 해당됩니다.
    
<br/>

- 어플리케이션  
    마이크로 서비스의 핵심을 담당하는 각 비즈니스 서비스와 데이터가 올라갑니다.  
    서로다른 언어로 개발된 여러 어플리케이션이 구동될 수 있습니다 (폴리그랏).
    
<br/><br/>

위 각 영역에 있는 구성요소 및 그것들의 관계, 서비스 운영 환경을 정의하는 것을 MSA 외부 아키텍처라 합니다.  
(마이크로 서비스를 관리하고 운영하기 위한 어플리케이션도 모두 포함됩니다)

<br/>





<br/><br/>

### 내부 아키텍처 정의
실제로 비즈니스가 실행되는 비즈니스 어플리케이션의 구조를 정의하는 것을 MSA 내부 아키텍처라 합니다.

<br/>

내부 아키텍처를 정의하기 위해 구조화해야 할 것으로는 다음과 같습니다.  

- 마이크로 서비스가 제공하는 API
- 비즈니스 로직
- 이벤트 발행
- 패키지 구조
- 데이터 저장 처리 등등

외부 아키텍처와 같이 내부 아키텍처 또한 변화에 적응 가능하도록 유연하고 확장성 있게 구현해야 합니다.

<br/>

마이크로 서비스 내부 아키텍처 코드 스타일을 저장해놓은 저장소는 [이곳을](https://github.com/cholnh/spring-best-practice-todo) 참조해주세요.



<br/>

:construction: **작성중입니다!** :construction:

<br/><br/>

---

[[:point_up_2: 위로]](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [1. 다루는 내용](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/1/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [2. 도메인 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [3. 시스템 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드)  :point_left:
- [4. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [5. 마일스톤](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [6. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [7. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [8. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/8/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [9. 회고](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/9/index.md#배달-서비스-플랫폼-api-서버-가이드)