# 헥사고날 아키텍처

<br/><br/>



## :speech_balloon: 개요
현대 애플리케이션에서는 다양한 인터페이스를 필요로 합니다.  
애플리케이션을 호출하는 다양한 (외부, 내부)시스템 유형과 다양한 저장소가 존재합니다.  
헥사고날 아키텍처는 이러한 다양성을 충족하기 위한 아키텍처 입니다.  

<br/><br/>



### 헥사고날
헥사고날 아키텍처 구조는 다음과 같습니다.
- 고수준의 비즈니스 로직을 표현하는 내부 영역 
    + 순수한 비즈니스 로직을 표현하는 기술 독립적인 영역
    + 외부 영역과 연결되는 포트를 가지고 있음
- 인터페이스 처리를 담당하는 저수준의 외부 영역
    + 외부 요청을 처리하는 인바운드 어댑터
    + 비즈니스 로직에 의해 호출되어 외부와 연계되는 아웃바운드 어댑터

<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-hex-arch-1.png" width="900"/>|
|-|
|그림 1 - 헥사고날 아키텍처의 포트와 어댑터|

<br/>

헥사고날 아키텍처 포트의 특징은 다음과 같습니다.
- 고수준의 내부 영역이 외부의 구체 어댑터에 전혀 의존하지 않습니다. (포트에 의해)
- 인바운드 포트는 내부 영역 사용을 위해 표출된 API 입니다.
- 아웃바운드 포트는 외부를 호출하는 방법을 정의합니다.
- 아웃바운드 포트가 외부의 아웃바운드 어댑터를 호출해서 외부 시스템과 연계하는 것이 아니라  
    아웃바운드 어댑터가 아웃바운드 포트에 의존해서 구현됩니다. (DIP 원칙)

<br/>

헥사고날 아키텍처 어댑터의 특징은 다음과 같습니다.
- 인바운드 어댑터의 종류 예시
    + REST API 컨트롤러
    + 웹 페이지 스프링 MVC 컨트롤러
    + 커맨드 핸들러
    + 이벤트 메시지 구독 핸들러 등
- 아웃바운드 어댑터 종류 예시
    + DAO (Jpa, MySQL, NoSQL 등등)
    + 이벤트 메시지 발행 클래스
    + 외부 서비스 호출 프록시 등
    
<br/><br/>

### 도메인 모델링
도메인 모델링은 백엔드 모델링의 한 부분이며, 도메인 주도 마이크로 서비스를 설계하는 데 중요한 역할을 합니다.  
마이크로 서비스 내부 구조는 폴리글랏하게 접근할 수 있습니다.  

> 폴리글랏하다 : 애플리케이션을 구현하는 언어나 데이터를 저장하는 저장소를 서비스마다 다양하게 활용할 수 있다는 의미.  
> 동시에 내부 아키텍처 구조를 서비스 특성에 맞게 다양하게 수립할 수 있다는 의미이고도 합니다.  
> 따라서 서비스 내부 영역의 구조를 도메인 모델 중심으로 만들 수도 있고 트랜젝션 스크립트 형태로 만들 수도 있습니다.  

<br/>

- 도메인 모델 중심의 구조  
    도메인 모델을 중심으로 모델링이 수행됩니다.  
    서비스가 모든 로직을 처리하지 않고 비즈니스 로직이 도메인 모델로 위임되어 적절히 분산됩니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-hex-domain-2.png" width="900"/>|
|-|
|그림 2 - 도메인 모델 형태의 헥사고날 구조|

<br/><br/>

- 트랜젝션 스크립트 중심의 구조  
    도메인 모델이 없는 구조입니다.  
    `DTO` 는 데이터 묶음으로써의 역할만 수행할 뿐 서비스가 많은 로직을 보유하게 됨으로써 시스템이 복잡해질수록 비대해질 수 있습니다.  
    단순한 로직인 경우에는 트랜젝션 스크립트 구조로 만들어도 무방합니다.  
    하지만 비즈니스가 복잡해질수록 도메인 모델 구조가 효과적입니다. 

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-hex-transaction-1.png" width="900"/>|
|-|
|그림 3 - 트랜젝션 스크립트 형태의 헥사고날 구조|

<br/><br/>

마이크로 서비스 내부 구조를 도메인 모델 구조로 정의했을 때 설계하는 방식이 도메인 모델링입니다.  
다음은 DDD 전술적 설계를 통한 객체 모델링 설계를 진행하겠습니다.  

<br/>

### DDD 전술적 설계 (도메인 모델링 구성요소)
기존 객체 모델링 방식은 자유도가 높아 비즈니스 복잡도가 높아질수록 계층 구조 또한 복잡해집니다. (진흙 덩어리)  
DDD 전술적 설계는 객체들 각각의 역할에 따른 유형을 정의하고, 이러한 규칙에 따라 객체를 모델링 합니다.

<br/>

다음은 DDD 전술적 설계를 이루는 다양한 구성요소 입니다.

- 엔티티 (Entity)  
    도메인의 실체 개념을 표현하는 객체입니다.
    + 다른 엔티티와 구별할 수 있는 식별자를 지님 (개별성 individuality)
    + 엔티티의 속성 및 상태는 변함 (변화 가능성 mutability)
    
|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-entity.png" width="700"/>|
|-|
|그림 4 - 도메인 모델 예시|
    
<br/>

- 값 객체 (VO)  
    각 속성이 개별적으로 변화하지 않는 객체입니다. (immutability)
    + 값 객체를 구성하는 하나 이상의 특성들이 서로 연관되어 전체 의미를 이룹니다. (개념적 완전성, 응집도 UP)
    + 개별 속성이 하나하나 수정되지 않고, 전체가 한 번에 생성/삭제되는 객체입니다.
    + 속성의 값이 모두 일치하면 같은 ('값') 객체 입니다.
    
> 참고: SUN 의 J2EE 커뮤니티에서 언급된 Value Object (Transfer Object) 패턴은 DDD 의 VO 와 다릅니다.  
> 전자는 DTO (Data Transfer Object)로 부르는 것이 적절합니다.

<br/> 

- 표준 타입  
    대상의 타입을 나타내는 서술적 객체입니다.  
    + Entity/VO 속성을 구분하는 용도로 사용
    + 예시로, 전화번호를 VO 로 모델링 했다면 전화번호가 집 전화인지 핸드폰 전화인지 회사 전화인지 구분하는 용도로 사용가능합니다.
    + 보통 자바 에서는 `enum` 으로 정의합니다.
    
<br/>

- 애그리거트  
    Entity, VO 모델링을 하게 되면 자연스럽게 객체 간 계층구조가 만들어집니다.  
    이처럼 연관된 Entity, VO 묶음이 애그리거트 입니다.
    + 내부 Entity, VO, 표준 타입 등 들은 서로 간 비즈니스 의존관계를 맺고 있습니다.
    + 애그리거트 단위는 트랜젝션의 기본 단위입니다.
    + 애그리거트 내 Entity 중 가장 상위 Entity 를 애그리거트 루트로 정합니다.
    + 애그리거트 루트를 통해서만 외부와 소통(내부 값 변경)이 가능합니다.
    + 보통 바운디드 컨텍스트를 마이크로 서비스로 식별하게 되는데, 애그리거트 또한 별도의 마이크로 서비스 후보가 될 수 있습니다.
    + 다른 애그리거트 사이 일관성이 필요할 시, 도메인 이벤트를 통한 결과적 일관성을 사용하여 다른 애그리거트를 갱신한다.
    
|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-hex-aggregate.png" width="600"/>|
|-|
|그림 5 - 식별자를 통한 애그리거트 간 참조|

<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-hex-aggregate2.png" width="600"/>|
|-|
|그림 6 - 결과적 일관성을 통한 애그리거트 간 갱신|

<br/>

- 도메인 서비스  
    도메인의 비즈니스 로직 처리가 특정 Entity 나 VO 에 속하지 않을 때 단독 객체를 만들어서 처리하는 것.
    + 상태를 관리하지 않고 행위만 존재합니다.
    + 특정 작업을 처리한 뒤 상태를 본인이 가지고 있지 않고 Entity, VO 에 전달합니다.

<br/>

- 도메인 이벤트  
    도메인 이벤트의 구현 객체입니다.  
    + (서비스 간 정합성을 일치시키기 위해) 단위 애그리거트 주요 상태 값을 담아 전달되도록 모델링합니다.
    + 메시지(이벤트) 처리 메커니즘을 통해 처리됩니다.
     
|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-hex-event.png" width="700"/>|
|-|
|그림 7 - 도메인 이벤트 발행 예시|