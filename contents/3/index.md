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
- [5. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드) 
- [6. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [7. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md#배달-서비스-플랫폼-api-서버-가이드)

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

<br/><br/>

### MSA 패턴
앞에서 설명한 아키텍처는 문제 영역에 대한 솔루션을 제공하는 것입니다.  
그렇다면 어떤 문제영역이 있고 어떤 해법으로 그 문제를 해결할까요?  

<br/>

이처럼 어떤 문제 영역에 대해 여러 사람들에 의해 검증되어 정리된 유용한 해법을 패턴(Pattern)이라고 합니다.  
MSA 에도 이러한 설계 패턴들이 존재합니다.  

<br/>

마이크로 서비스 패턴 종류는 다음과 같습니다.

> 패턴을 편하게 구현하도록 도와주는 여러 오픈소스들을 아래에 같이 적어놨습니다.  
> 넷플릭스 OSS 는 MSA 관련 패턴들을 정형화하여 오픈소스화 한 프로젝트입니다.  
> 하지만 넷플릭스 OSS 또한 각 패턴별 러닝커브가 존재하고 알아야 할 종류들이 많습니다.  
> 보통 대규모 시스템을 운영하는 기업들은 쿠버네티스를 사용하여 여러 패턴에 쓰이는 기술들을 안정적이고 효율적으로 운영합니다.  

쿠버네티스에 관한 설명은 아래 링크를 참조해주세요.  
[ [쿠버네티스 알아보기](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/msa-kubernetes.md#쿠버네티스) ]

<br/>

- 라우팅 패턴
    1. 서비스 디스커버리 패턴
        + 스프링 클라우드 + 넷플릭스 유레카
        + 쿠버네티스
    2. 서비스 라우팅 패턴
        + 스프링 클라우드 + 넷플릭스 주울
        + 쿠버네티스
          
|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/msa-routing-pattern.jpg" width="700"/>|
|-|
|그림 1 - 라우팅 패턴|

<br/>

- 회복성 패턴
    1. 클라이언트 부하 분산
        + 스프링 클라우드 + 넷플릭스 리본
        + 쿠버네티스
    2. 회로차단기 패턴
        + 스프링 클라우드 + 넷플릭스 히스트릭스
    3. 폴백 패턴
        + 스프링 클라우드 + 넷플릭스 히스트릭스
    4. 벌크 헤드 패턴
        + 스프링 클라우드 + 넷플릭스 히스트릭스

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/msa-mediation-pattern.jpg" width="700"/>|
|-|
|그림 2 - 회복성 패턴|

<br/>

- 보안 패턴
    + 스프링 클라우드 시큐리티
    + OAuth2, JWT

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/msa-security-pattern.jpg" width="700"/>|
|-|
|그림 3 - 보안 패턴|

<br/>

- 로그 패턴
    + 스프링 클라우드
    + 슬루스
    + 페이퍼 트레일
    + 집킨

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/msa-log-pattern.jpg" width="700"/>|
|-|
|그림 4 - 로그 패턴|

<br/>

- 빌드/배포 패턴
    1. 지속적 통합(CI)
        + Travis CI
        + 쿠버네티스
    2. 코드형 인프라스트럭처 (IaC)
        + 도커
    3. 불변서버
        + 도커
    4. 피닉스서버
        + Travis CI
        + 도커
        
|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/msa-deploy-pattern.jpg" width="700"/>|
|-|
|그림 5 - 빌드/배포 패턴|

<br/>

- 개발 패턴
    1. 핵심 마이크로서비스 패턴
        + 스프링 부트
    2. 구성 관리
        + 스프링 클라우드 컨피그
    3. 비동기 메시징
        + 스프링 클라우드 스트림
        
<br/>

각 패턴에 관한 자세한 설명은 아래 링크에서 확인해주세요.  
[ [마이크로 서비스 패턴 알아보기](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/msa-pattern.md#MSA-패턴) ]

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

내부 아키텍처도 설계 패턴이 존재합니다.  
내부 아키텍처 패턴에 관한 자세한 설명은 아래 링크에서 확인해주세요.  
[ [마이크로 서비스 어플리케이션 패턴 알아보기](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/msa-pattern-app.md#마이크로-서비스-어플리케이션-패턴) ]

<br/><br/>

### 코드와 설계의 중요성

> 소프트웨어의 가치는 행위 가치와 구조 가치로 나뉘고, 소프트웨어를 정말로 부드럽게(Soft) 만드는 것은  
> 구조가치이다. - 로버트 C. 마틴 저서 클린아키텍처 中
> (여기서 행위 가치는 소프트웨어의 기능을 말하며, 구조 가치는 소프트웨어 아키텍처를 말합니다) 

프로젝트 동안 어플리케이션 구조나 설계에 신경 쓰지 않고 오직 기능 구현에만 몰두한 소프트웨어는 유지보수가 어렵습니다.
새로운 형태의 UI 나 기술을 추가해야 한다고 했을 때 거의 처음부터 새로운 시스템을 만드는 것과 같은 수준으로 수정해야 하고, 
사소한 기능 변경에도 사이드 이펙트를 알 수 없어 그에 따른 실제 변경 작업보다 다른 모듈의 영향도를 파악하기 위한 테스트에 더 많은 시간을 투자해야 하곤 합니다.  

<br/>

소프트웨어를 좀 더 부드럽게(Soft) 만들기 위해서는 바람직한 어플리케이션 아키텍처를 적용할 필요가 있습니다.  
내부 설계 시 고려해야 할 각종 어플리케이션 아키텍처와 관련 패턴, 코드 스타일들을 살펴보겠습니다.  

<br/>

#### 관심사 분리

<br/>

#### 헥사고날 아키텍처

<br/>

#### 클린 아키텍처

<br/>

#### 마이크로 서비스 내부 구조

<br/>

#### 코드 스타일
마이크로 서비스 내부 아키텍처 코드 스타일입니다.  
소스코드는 아래 저장소를 확인해주세요.  
[ [마이크로 서비스 내부 아키텍처 코드 스타일](https://github.com/cholnh/spring-best-practice-todo) ]

<br/><br/>

---

[[:point_up_2: 위로]](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [1. 다루는 내용](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/1/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [2. 도메인 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [3. 시스템 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드) :point_left: 
- [4. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [5. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드) 
- [6. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [7. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md#배달-서비스-플랫폼-api-서버-가이드)