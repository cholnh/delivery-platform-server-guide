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
- [2. 도메인 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#배달-서비스-플랫폼-api-서버-가이드) :point_left:
- [3. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [4. 시스템 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [5. 마일스톤](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [6. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [7. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [8. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/8/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [9. 회고](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/9/index.md#배달-서비스-플랫폼-api-서버-가이드)

<br/><br/>



## 2. 도메인 설계
해당 프로젝트는 가상 비즈니스 상황을 가정하고 가상 서비스에 대한 API 서버를 구현합니다.  

<br/><br/>

### 왜 API 서버가 필요한가?
**API 서버란**   
Web 또는 Mobile Application 에서 필요한 기능을 제공하는 서버입니다.  
여기서 필요한 기능이란 
- 회원가입 요청
- 제품 리스트 요청
- 회원탈퇴 요청 등등 

다양한 요청을 뜻합니다.  

<br/>

API 서버가 어떤 기능을 제공하지는 인터페이스를 통해 알 수 있습니다.  
API 서버 인터페이스 엔드 포인트는 URL 형식으로 제공되며 http 메서드 와 URI 를 통해 요청이 결정됩니다.

|HTTP 메서드|URI|요청|
|-|-|-|
|POST|https://api.domain.com/v1/products|새로운 제품정보 생성|
|GET|https://api.domain.com/v1/products|제품정보 목록 조회|
|GET|https://api.domain.com/v1/products/1|1번 제품정보 조회|
|PUT|https://api.domain.com/v1/products/1|1번 제품정보 수정|
|DELETE|https://api.domain.com/v1/products/1|1번 제품정보 삭제|

각각의 HTTP 메서드에 따라 다른 행위가 요청됩니다.  

<br/>

위와 같은 요청을 처리하여 반환하는 API 서버를 구현하고 On-Premise 서버(자체적으로 보유한 전산실 서버) 또는 클라우드 서버에서 실행하여 서버를 기동합니다.  
그럼 웹이나 앱의 요청 또는 다른 사용자의 요청에 응답을 하는 API 서버가 완성됩니다.(참 쉽죠)  
이제 **'어떤 서비스'** 를 API 서버를 통해 제공해 볼지 정해보겠습니다.

<br/><br/>

### 어떤걸 만들까?
글을 쓰고 있는 지금(2021년) 전후로 스타트업 붐이 일고있습니다.  
과거에는 창업에 대한 문턱이 높았습니다. 하지만 현재는 플랫폼에 대한 진입장벽에 과거와는 비교불가할 정도로 달라졌습니다. 
특히 IT 창업의 경우, 자신의 노트북과 스마트폰만으로도 창업을 시작할 수 있게 되었습니다. 
때문에 여러 플랫폼 스타트업이 우후죽순 생겨나기 시작했고, 유니콘 기업(기업 가치가 10억 달러 이상인 비상장 스타트업)들이 하나 둘씩 생겨나기 시작했습니다.

<br/>

저도 시류에 편승하여 간단한 플랫폼 서비스를 만들어 보겠습니다.  
대한민국 유니콘 기업 중 하나인 배달의민족과 같은 배달 서비스 플랫폼을 선택하겠습니다.  
너무 똑같으면 재미없으니 약간의 변형을 주겠습니다.  
(아래와 같은 방식으로 문제점, 해결방안, 기대효과 순으로 자신의 아이디어를 간단하게 정리합니다)

<br/>

**[ 현재 배달 시장의 문제점 ]**
- 점점 높아지는 배달비
- 최소 주문 금액
- 배달 오토바이 사고율 증가

<br/>

**[ 문제 해결 방식 ]**  
<br/>
대학/기관 같이 사람들이 밀집해 있는 곳은 **'먹는 시간'** 과 **'취식 장소'** 가 정해져 있습니다.  
그렇다면 음식 주문을 미리 모아 한 번에 묶음 배달을 해주면 어떨까 생각해 보았습니다.  
방식은 다음과 같습니다.

1. [주문 전달]  
    고객의 주문을 미리 받아 업주(음식점 사장님)들에게 전달합니다.
2. [제품 픽업]  
    픽업 시간에 맞춰 픽업 트럭이 모든 업체(음식점)를 순회하며 제품(음식)을 수령합니다.
3. [제품 이동]  
    픽업 트럭은 배달 받는 장소로 이동합니다.
4. [제품 전달]  
    배달 기사는 배달 장소에 있는 제품보관대에 제품을 넣어 놓습니다. (다음 배달 장소가 있으면 반복합니다)
5. [배달 완료]  
    배달 기사는 다음 픽업을 준비합니다.

<br/>

**[ 무엇을 해결할까요 ]**  
- 고객들은 제품 할인과 무료 배달을 경험합니다.
- 명량핫도그 하나만 시켜도 배달이 됩니다.
- 업주들은 바쁘지 않은 시간대에 사전 단체 주문을 받아 추가 매출이 기대됩니다.
- 오토바이 배달보다 상대적으로 안전하고 배달횟수가 짧으므로 사고율이 현저히 감소됩니다. 

<br/><br/>

### 어떻게 설계할까?
현시대의 플랫폼 사업은 민첩해야합니다.  
다양하고 급변하는 시장의 요구에 따라 유연하고 민첩하게 적응할 수 있는 플랫폼을 만들어야 합니다.  
매번 요구가 변경될 때마다 프로젝트를 갈아 엎고 다시 시작할까요?  
프로젝트에는 [`다시 시작`](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#배달-서비스-플랫폼-api-서버-가이드) 버튼이 없습니다.ㅎㅎ  

<br/>

설계방식에 여러 유명한 방법론을 적용하면 어떨까요?  
시간이 금인 초기 스타트업 같은 경우 방법론을 배우는데 많은 시간이 소요되어 머뭇거리게 됩니다.  
또한 막상 배운 방법론의 끝이 자신이 원하던 방향이 아닐 경우의 시간적 리스크도 감내해야 합니다.  
그래서 대충 머리속에 두루뭉술하게 그려지는 지식으로 프로젝트를 진행하게 됩니다.  
그러다보면 디자이너의 생각, 기획자의 생각, 개발자의 생각이 조금씩 달라지게 됩니다.  
서로가 이해하는 비즈니스 이해 각도가 틀어지고, 기능이 추가될 때마다 이러한 악순환은 폭발적으로 증가합니다.  
결국엔 프로젝트를 다시 시작하게 됩니다.  
뭐가 문제였던 걸까요?  

<br/>

**'도메인 주도 설계'**(이하 DDD) 방법론을 적용해 보면 어떨까요?  
DDD 는 비즈니스 도메인의 기능들을 가시적으로 명확하게 보여줍니다.  
팀원들은 눈앞에 보이는 도메인 구조 그림을 보며 서로의 의견을 맞춰나갑니다.  
처음 러닝 커브가 두렵다고요? 아래 `그림 1` 을 보세요.  

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/1.jpg" width="700"/>|
|-|
|그림 1 - (출처:PoEAA by Martine Fowler)|

(기존 설계와 같은)Data 또는 Transaction 기반의 설계는 도메인 복잡성이 증가할 수록 관리가 힘들어집니다.  
어느정도의 러닝커브를 감내한 방법론의 실천은 '골든크로스' 지점을 돌파할 때 그 효과가 나타납니다.  
도메인 복잡성이 증가해도 감당가능한 범위내 입니다.  

<br/>

따라서 저는 어느정도 러닝커브를 감수하고 도메인 위주로 설계를 실천하는 방법론인 DDD 를 프로젝트에 적용하고자 합니다.  
추후 클라우드 기반에서 서비스를 빠르게 적용할 수 있는 방법인 마이크로서비스 아키텍처(이하 MSA) 를 도입하여 잘게 나누어진 도메인을 서비스 할 것입니다.  
MSA 는 DDD 를 적용할 수 있는 기술적 환경을 마련해 줍니다. (MSA 설계/구현에 DDD 방법론이 제격이라는 말입니다)  
그럼 이제부터 빠른 변경을 포용할 수 있는, 쉽고 도메인 응집성이 있고 단순하며 아름다운 설계를 해보겠습니다.  
(글쓴이는 DDD 관련 실무 지식이 부족할 뿐더러 글작성과 동시에 DDD 공부를 병행하므로 부족한 부분이 많을 수 있습니다)

<br/><br/>

### 요구사항 정의
위에서([:link:어떤걸 만들까?](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#어떤걸-만들까)) 
정한 배달 플랫폼 서비스는 다음과 같은 기능 요건을 가지고 있습니다.  

- 사용자 관리 및 로그인
    + (Create) 사용자를 등록합니다. 등록 시 사내 HR 시스템에 의해 검증됩니다.
    + (Update) 사용자는 개인정보를 수정할 수 있습니다.
    + (Delete) 사용자는 서비스를 탈퇴할 수 있습니다. (강제탈퇴/자진탈퇴)
    + 사용자는 역할이 부여됩니다. (고객, 업주, 기사, 관리자)
    + 사용자는 시스템 사용을 위해 로그인/로그아웃할 수 있습니다.

<br/>

- 업체 관리
    + (Create) 업체 정보를 등록합니다.
    + (Update) 업체 정보를 수정합니다.
    + (Delete) 업체 정보를 삭제합니다.
    + 추후 업체 별 할인 행사를 고려해야 합니다.

<br/>

- 주문 시간 관리
    + (Create) 주문 시간 정보를 등록합니다.
    + (Update) 주문 시간 정보를 수정합니다.
    + (Delete) 주문 시간 정보를 삭제합니다.

<br/>

- 제품 관리
    + (Create) 제품 정보를 등록합니다.
    + (Update) 제품 정보를 수정합니다.
    + (Delete) 제품 정보를 삭제합니다.
    + 추후 제품 별 할인 행사를 고려해야 합니다.

<br/>

- 장바구니 관리

<br/>

- 주문/결제
    + 
    + 주문은 상태를 갖습니다. (아래 주문 프로세스 참고 바랍니다)
    + 주문 승인 처리가 된 주문건은 고객 취소가 불가능합니다. (업체측 거절/취소는 가능합니다)
    + 결제에 실패한 주문은 거래가 성립되지 않습니다. (Atomic Transaction)
    + 모든 주문/결제 상황을 (푸시)알림해야 합니다.
    + 모든 주문/결제 상황을 기록해야 합니다.
    + 추후 PG 추가/수정/삭제를 고려해야 합니다.

<br/>

- 배달지 관리
    + (Create) 배달지 정보를 등록합니다.
    + (Update) 배달지 정보를 수정합니다.
    + (Delete) 배달지 정보를 삭제합니다.
    
<br/>

- 배달

   
<br/><br/>

아래 그림은 주문 프로세스를 도식화한 결과입니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/2.jpg" width="700"/>|
|-|
|그림 2 - 주문 프로세스|

각 주문 상태별 설명을 [이곳에](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/order-process.md#주문-프로세스) 
정리해두었습니다.  

<br/><br/>

### 설계 전략



<br/><br/>

### 이벤트 스토밍을 통한 마이크로서비스 도출



<br/><br/>

### 외부 아키텍처 정의



<br/><br/>

### 내부 아키텍처 정의



<br/><br/>