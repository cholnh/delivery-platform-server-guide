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
- [3. 시스템 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [4. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [5. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [6. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [7. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md#배달-서비스-플랫폼-api-서버-가이드)

<br/><br/>



## 2. 도메인 설계
해당 프로젝트는 가상 비즈니스 상황을 가정하고 가상 서비스에 대한 API 서버를 구현합니다.  

<br/><br/>

### 2-1. 왜 API 서버가 필요한가?
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
|POST|`https://api.domain.com/v1/products`|새로운 제품정보 생성|
|GET|`https://api.domain.com/v1/products`|제품정보 목록 조회|
|GET|`https://api.domain.com/v1/products/1`|1번 제품정보 조회|
|PUT|`https://api.domain.com/v1/products/1`|1번 제품정보 수정|
|DELETE|`https://api.domain.com/v1/products/1`|1번 제품정보 삭제|

각각의 HTTP 메서드에 따라 다른 행위가 요청됩니다.  
(마이크로 서비스 REST API 설계에 대한 내용은 [이곳](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/restful-api.md#restful-api-설계)에서 자세하게 다루겠습니다)  

<br/>

위와 같은 요청을 처리하여 반환하는 API 서버를 구현하고 On-Premise 서버(자체적으로 보유한 전산실 서버) 또는 클라우드 서버에서 실행하여 서버를 기동합니다.  
그럼 웹이나 앱의 요청 또는 다른 사용자의 요청에 응답을 하는 API 서버가 완성됩니다.(참 쉽죠)  
이제 **'어떤 서비스'** 를 API 서버를 통해 제공해 볼지 정해보겠습니다.

<br/><br/>

---

### 2-2. 어떤 걸 만들까?
글을 쓰고 있는 지금(2021년) 전후로 스타트업 붐이 일고 있습니다.  
과거에는 창업에 대한 문턱이 높았습니다. 하지만 현재는 플랫폼에 대한 진입장벽에 과거와는 비교불가할 정도로 달라졌습니다. 
특히 IT 창업의 경우, 자신의 노트북과 스마트폰만으로도 창업을 시작할 수 있게 되었습니다. 
때문에 여러 플랫폼 스타트업이 우후죽순 생겨나기 시작했고, 유니콘 기업(기업 가치가 10억 달러 이상인 비상장 스타트업)들이 하나둘씩 생겨나기 시작했습니다.

<br/>

저도 시류에 편승하여 간단한 플랫폼 서비스를 만들어 보겠습니다.  
대한민국 유니콘 기업 중 하나인 배달의 민족과 같은 배달 서비스 플랫폼을 선택하겠습니다.  
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
대학/기관같이 사람들이 밀집해 있는 곳은 **'먹는 시간'** 과 **'취식 장소'** 가 정해져 있습니다.  
그렇다면 음식 주문을 미리 모아 한 번에 묶음 배달을 해주면 어떨까 생각해 보았습니다.  
방식은 다음과 같습니다.

1. [주문 전달]  
    고객의 주문을 미리 받아 업주(음식점 사장님)들에게 전달합니다.
2. [제품 픽업]  
    픽업 시간에 맞춰 픽업트럭이 모든 업체(음식점)를 순회하며 제품(음식)을 수령합니다.
3. [제품 이동]  
    픽업트럭은 배달 받는 장소로 이동합니다.
4. [제품 전달]  
    배달 기사는 배달 장소에 있는 제품 보관대에 제품을 넣어 놓습니다. (다음 배달 장소가 있으면 반복합니다)
5. [배달 완료]  
    배달 기사는 다음 픽업을 준비합니다.

<br/>

**[ 무엇을 해결할까요 ]**  
- 고객들은 제품 할인과 무료 배달을 경험합니다.
- 명량 핫도그 하나만 시켜도 배달이 됩니다.
- 업주들은 바쁘지 않은 시간대에 사전 단체 주문을 받아 추가 매출이 기대됩니다.
- 오토바이 배달보다 상대적으로 안전하고 배달 횟수가 짧으므로 사고율이 현저히 감소됩니다. 

<br/><br/>

---

### 2-3. 어떻게 설계할까?
현시대의 플랫폼 사업은 민첩해야 합니다.  
다양하고 급변하는 시장의 요구에 따라 유연하고 민첩하게 적응할 수 있는 플랫폼을 만들어야 합니다.  
매번 요구가 변경될 때마다 프로젝트를 갈아엎고 다시 시작할까요?  
프로젝트에는 [`다시 시작`](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#배달-서비스-플랫폼-api-서버-가이드) 버튼이 없습니다.ㅎㅎ  

<br/>

설계 방식에 여러 유명한 방법론을 적용하면 어떨까요?  
시간이 금인 초기 스타트업 같은 경우 방법론을 배우는데 많은 시간이 소요되어 머뭇거리게 됩니다.  
또한 막상 배운 방법론의 끝이 자신이 원하던 방향이 아닐 경우의 시간적 리스크도 감내해야 합니다.  
그래서 대충 머릿속에 두루뭉술하게 그려지는 지식으로 프로젝트를 진행하게 됩니다.  
그러다 보면 디자이너의 생각, 기획자의 생각, 개발자의 생각이 조금씩 달라지게 됩니다.  
서로가 이해하는 비즈니스 이해 각도가 틀어지고, 기능이 추가될 때마다 이러한 악순환은 폭발적으로 증가합니다.  
각자가 사용하는 말이 달라 바벨탑 건축이 실패했듯이, 결국엔 프로젝트를 다시 시작하게 됩니다.  
뭐가 문제였던 걸까요?  

<br/>

**'도메인 주도 설계'**(이하 DDD) 방법론을 적용해 보면 어떨까요?  
DDD 는 비즈니스 도메인의 기능들을 가시적으로 명확하게 보여줍니다.  
팀원들은 눈앞에 보이는 도메인 구조 그림을 보며 서로의 의견을 맞춰나갑니다.  
처음 러닝 커브가 두렵다고요? 아래 `그림 1` 을 보세요.  

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/1.jpg" width="700"/>|
|-|
|그림 1 - (출처:PoEAA by Martine Fowler)|

(기존 설계와 같은)Data 또는 Transaction 기반의 설계는 도메인 복잡성이 증가할수록 관리가 힘들어집니다.  
어느 정도의 러닝 커브를 감내한 방법론의 실천은 '골든크로스' 지점을 돌파할 때 그 효과가 나타납니다.  
도메인 복잡성이 증가해도 감당 가능한 범위 내입니다.  

<br/>

따라서 저는 어느 정도 러닝 커브를 감수하고 도메인 위주로 설계를 실천하는 방법론인 DDD 를 프로젝트에 적용하고자 합니다.  
추후 클라우드 기반에서 서비스를 빠르게 적용할 수 있는 방법인 마이크로 서비스 아키텍처(이하 MSA) 를 도입하여 잘게 나누어진 도메인을 서비스 할 것입니다.  
MSA 는 DDD 를 적용할 수 있는 기술적 환경을 마련해 줍니다. (MSA 설계/구현에 DDD 방법론이 제격이라는 말입니다)  
그럼 이제부터 빠른 변경을 포용할 수 있는, 쉽고 도메인 응집성이 있고 단순하며 아름다운 설계를 해보겠습니다.  
(글쓴이는 DDD 관련 실무 지식이 부족할뿐더러 글 작성과 동시에 DDD 공부를 병행하므로 부족한 부분이 많을 수 있습니다)

<br/><br/>

---

### 2-4. 요구사항 정의
위에서([:link:어떤걸 만들까?](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#2-2-어떤-걸-만들까)) 
정한 배달 플랫폼 서비스는 다음과 같은 기능 요건을 가지고 있습니다.  

- 사용자 관리 및 로그인
    + (Create) 사용자를 등록합니다. 등록 시 사내 HR 시스템에 의해 검증됩니다.
    + (Update) 사용자는 개인정보를 수정할 수 있습니다.
    + (Delete) 사용자는 서비스를 탈퇴할 수 있습니다. (강제탈퇴/자진탈퇴)
    + 사용자는 역할이 부여됩니다. (고객, 업주, 기사, 관리자)
    + 사용자는 시스템 사용을 위해 로그인/로그아웃할 수 있습니다.

<br/>

- 업체 관리
    + (Create) 업주/관리자는 업체 정보를 등록할 수 있습니다.
    + (Update) 업주/관리자는 업체 정보를 수정할 수 있습니다.
    + (Delete) 업주/관리자는 업체 정보를 삭제할 수 있습니다.
    + 사용자는 배달지에 해당되는 업체 목록을 조회할 수 있습니다. (sorting, paging 기능이 작동합니다)
    + 사용자는 업체를 선택할 수 있습니다.
    + 추후 업체 별 할인 행사(프로모션)를 고려해야 합니다.
    + 업체는 영업시간이 정해져 있습니다.

<br/>

- 제품 관리
    + (Create) 업주/관리자는 제품 정보를 등록할 수 있습니다.
    + (Update) 업주/관리자는 제품 정보를 수정할 수 있습니다.
    + (Delete) 업주/관리자는 제품 정보를 삭제할 수 있습니다.
    + 사용자는 업체에 판매되는 제품 목록을 조회할 수 있습니다. (sorting, paging 기능이 작동합니다)
    + 제품 카테고리 목록을 조회할 수 있습니다.
    + 사용자는 제품을 장바구니에 추가할 수 있습니다.
    + 업주/관리자는 품절 처리할 수 있습니다.
    + 추후 제품 별 할인 행사(프로모션)를 고려해야 합니다.

<br/>

- 서브 제품 관리
    + (Create) 업주/관리자는 서브 제품 정보를 등록할 수 있습니다.
    + (Update) 업주/관리자는 서브 제품 정보를 수정할 수 있습니다.
    + (Delete) 업주/관리자는 서브 제품 정보를 삭제할 수 있습니다.
    + 사용자는 서브 제품 목록을 조회할 수 있습니다. (sorting, paging 기능이 작동합니다)
    + 서브 제품 카테고리 목록을 조회할 수 있습니다.
    + 사용자는 서브제품을 선택할 수 있습니다.
    + 선택 최소/최대 수량을 검증합니다.
    + 업주/관리자는 품절 처리할 수 있습니다.
    + 추후 제품 별 할인 행사(프로모션)를 고려해야 합니다.

<br/>

- 가격 관리
    + 제품에 해당되는 원가와 판매 수수료를 조회할 수 있습니다.
        + 업체 판매 수수료 (가격/퍼센트)
        + 고객 판매 수수료 (가격/퍼센트)
        + 프로모션 할인가 (가격/퍼센트)

<br/>

- 주문 시간 관리
    + (Create) 업주/관리자는 주문 시간 정보를 등록할 수 있습니다.
    + (Update) 업주/관리자는 주문 시간 정보를 수정할 수 있습니다.
    + (Delete) 업주/관리자는 주문 시간 정보를 삭제할 수 있습니다.
    + 주문 마감 시간, 제품 픽업 시간, 제품 도착 시간으로 나뉩니다.
    + 사용자는 상세 배달지에 해당하는 주문 시간 목록을 조회할 수 있습니다.
    
<br/>

- 주문
    + 사용자는 업체별 주문 가능 수량을 요청할 수 있습니다.
        + 주문 마감 시간에 다다를수록 주문 가능 수량이 줄어듭니다.
        + 시간별 최대 주문 수량이 정해져 있습니다.
    + 사용자는 제품 주문과 동시에 요청사항을 등록할 수 있습니다.
    + 주문은 상태를 갖습니다. ([주문 프로세스](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/order-process.md#주문-프로세스) 참고 바랍니다)
    + 주문 성공시 식별 번호가 반환됩니다.
    + 주문 승인 처리가 된 주문 건은 고객 취소가 불가능합니다. (업체 측 거절/취소는 가능합니다)
    + 모든 주문 상황을 (푸시)알림 해야 합니다.
    + 모든 주문 상황을 기록해야 합니다.
        + 주문 식별 번호
        + 주문자 정보
        + 결제 정보
        + 배달지 정보
        + 주문 시간 정보
        + 주문 제품 정보
    + 사용자는 주문 목록을 조회할 수 있습니다.
    
<br/>
       
- 결제
    + 결제에 실패한 주문은 거래가 성립되지 않습니다. (Atomic Transaction)
    + 고객은 결제 수단 목록 조회가 가능합니다.
    + 추후 결제방식(PG 등) 추가/수정/삭제를 고려해야 합니다.
    + 모든 결제 상황을 (푸시)알림 해야 합니다.
    + 모든 결제 상황을 기록해야 합니다.
        + 결제 수단 정보
        + 사용 포인트
        + 사용 쿠폰
        + 적용된 프로모션
        + 적립 포인트
        + 현금 영수증
        + 결제 요청 금액/최종 결제 금액
        + 결제 실패 원인

<br/>

- 배달지 관리   
    + (Create) 업주/관리자는 배달지 정보를 등록할 수 있습니다.
    + (Update) 업주/관리자는 배달지 정보를 수정할 수 있습니다.
    + (Delete) 업주/관리자는 배달지 정보를 삭제할 수 있습니다.
    + 사용자는 배달지 목록을 조회할 수 있습니다.
    + 고객은 배달지를 선택할 수 있습니다. 
    
<br/>

- 상세 배달지 관리    
    + (Create) 업주/관리자는 배달지 정보를 등록할 수 있습니다.
    + (Update) 업주/관리자는 배달지 정보를 수정할 수 있습니다.
    + (Delete) 업주/관리자는 배달지 정보를 삭제할 수 있습니다.
    + 사용자는 배달지에 해당하는 상세 배달지 목록을 조회할 수 있습니다.
    + 고객은 상세 배달지를 선택할 수 있습니다.

<br/>

- 평가 관리
     + (Create) (해당 업체에서 구매한 고객에 한에) 고객은 업체/제품 평가를 등록할 수 있습니다.
     + (Update) 고객은 자신이 작성한 업체/제품 평가를 수정할 수 있습니다.
     + (Delete) 고객은 자신이 작성한 업체/제품 평가를 삭제할 수 있습니다.
     + 사용자는 업체/제품 평가를 조회할 수 있습니다.
     + 사용자는 제품/제품 평가를 조회할 수 있습니다.
     + 사용자는 업체/제품 평가에 (작성자 외 로그인된 고객/업주에 한에) 좋아요 및 댓글을 작성할 수 있습니다.
     
<br/>

- 이미지 관리
    + 사용자는 업체 이미지를 요청할 수 있습니다.
    + 사용자는 제품 이미지를 요청할 수 있습니다.
    + 사용자는 서브 제품 이미지를 요청할 수 있습니다.
    + 사용자는 평가 이미지를 요청할 수 있습니다.

<br/> 

- 쿠폰 관리
    + 고객은 자신이 소유한 쿠폰/쿠폰 사용 내역을 조회할 수 있습니다.
    + 업주/관리자는 쿠폰을 발급할 수 있습니다.
    + 업주/관리자는 쿠폰 발급 내역을 조회할 수 있습니다. 
    + 쿠폰은 로그인 유저에게 귀속되는 쿠폰과 코드로 발급된 쿠폰으로 나뉩니다.
    + 모든 쿠폰은 유효기간이 있습니다.
    + 모든 쿠폰은 발급자가 등록되어야 합니다.
    + 쿠폰의 유효성을 검증합니다.
    + 고객이 쿠폰을 사용할 경우 사용 완료 처리합니다.
    + 쿠폰 할인 금액만큼 결제 금액이 차감됩니다.
    + 모든 쿠폰 발급/사용/만료 내역을 기록해야 합니다.

<br/>

- 포인트 관리
    + 고객은 자신이 소유한 포인트/포인트 사용 내역을 조회할 수 있습니다.
    + 관리자는 포인트를 발급할 수 있습니다.
    + 관리자는 포인트 발급 내역을 조회할 수 있습니다.
    + 포인트 랭크에 따라 적립되는 포인트가 달라집니다.
    + 모든 포인트는 유효기간이 있습니다.
    + 적립 포인트는 구매금액의 Percentage 로 계산됩니다.
    + 포인트 사용 금액만큼 보유 포인트가 차감되며 결제 금액이 할인됩니다.
    + 포인트 최소 사용 단위는 0원입니다.
    + 포인트의 유효성을 검증합니다.
    + 모든 포인트 적립/사용/만료 내역을 기록해야 합니다.
        + 관리자에 의한 포인트 수정(증가/차감)
        + 구매활동에 의한 포인트 발급
        + 프로모션에 의한 포인트 발급
        + 주문 취소에 의한 사용 포인트 재발급
        + 주문 취소에 의한 적립 포인트 회수
        + 구매 활동에 의한 포인트 사용
   
<br/><br/>

아래 그림은 주문 프로세스를 도식화한 결과입니다.  
각 주문 상태별 설명을 [이곳에](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/order-process.md#주문-프로세스) 
정리해두었습니다.  

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/2.jpg" width="700"/>|
|-|
|그림 2 - 주문 프로세스|

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/3.png" width="900"/>|
|-|
|그림 3 - 주문 프로세스|

<br/><br/>

---

### 2-5. 이벤트 스토밍을 통한 마이크로서비스 도출
앞에서 정의한 요구사항을 기반으로 '이벤트 스토밍'을 통해 비지니스 흐름을 파악하고 
바운디드 컨텍스트 식별을 통해 마이크로 서비스 후보를 찾아내고 서비스 간의 관계를 정의해 보겠습니다.  

<br/>

(DDD 개념은 [이곳](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/ddd-strategy.md#ddd-간단-소개)에서 자세하게 설명하겠습니다)  
(이벤트 스토밍은 [이곳](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/event-storming.md#이벤트-스토밍)에서 자세하게 설명하겠습니다)

<br/>

아래는 배달 서비스의 업무 흐름입니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-process-my.png" width="700"/>|
|-|
|그림 4 - 고객 정보 조회 프로세스|

<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-process-store.png" width="700"/>|
|-|
|그림 5 - 고객 정보 조회 프로세스|

<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-process-billing-2.png" width="1000"/>|
|-|
|그림 6 - 결제 프로세스|

<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-process-delivery-2.png" width="1000"/>|
|-|
|그림 7 - 배달 프로세스|

<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-process-storeowner-1.png" width="1000"/>|
|-|
|그림 8 - 업주 정산 프로세스|

<br/><br/>

위에서 정의한 요구사항을 다음과 같이 나눴습니다.  
- 사용자 관리 및 로그인
- 업체 관리
- 제품/서브 제품 관리
- 구매 관리
- 배달지/상세 배달지 관리    
- 배달 관리
- 알림/이미지 관리
- 프로모션 관리

이것은 비즈니스 해결을 위한 문제영역 입니다. DDD 에서는 문제 영역을 서브도메인이라 부릅니다.  
이제 서브도메인별로 이벤트 스토밍을 진행하겠습니다.  

<br/><br/>

1. 사용자 관리 및 로그인

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-es-user-1.png" width="1000"/>|
|-|
|사용자 관리 및 로그인 서브도메인에 대한 이벤트 스토밍 결과|

<br/><br/>

2. 업체 관리

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-es-store.png" width="1000"/>|
|-|
|업체 관리 서브도메인에 대한 이벤트 스토밍 결과|

<br/><br/>

3. 제품/서브 제품 관리

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-es-product.png" width="1000"/>|
|-|
|제품/서브 제품 관리 서브도메인에 대한 이벤트 스토밍 결과|

<br/><br/>

4. 구매 관리

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-es-order.png" width="1000"/>|
|-|
|구매 관리 서브도메인에 대한 이벤트 스토밍 결과|

<br/><br/>

5. 배달지/상세 배달지 관리

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-es-delivery-site.png" width="600"/>|
|-|
|배달지/상세 배달지 관리 서브도메인에 대한 이벤트 스토밍 결과|

<br/><br/>

6. 배달 관리

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-es-delivery.png" width="1000"/>|
|-|
|배달 관리 서브도메인에 대한 이벤트 스토밍 결과|

<br/><br/>

7. 알림/이미지 관리

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-es-util.png" width="600"/>|
|-|
|알림/이미지 관리 서브도메인에 대한 이벤트 스토밍 결과|

<br/><br/>

8. 프로모션 관리

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-es-promotion.png" width="900"/>|
|-|
|프로모션 관리 서브도메인에 대한 이벤트 스토밍 결과|

<br/><br/>

---

### 2-6. 바운디드 컨텍스트 식별
이벤트 스토밍 결과를 보고 애그리거트 응집도를 고려하여 컨텍스트를 구별해 보겠습니다.  

<br/>

1. 사용자 관리 및 로그인
    + 로그인은 회원이 시스템을 사용하는 시점(로그인 상태)에 발생하는 정보라는 점에서 사용자 정보와 개념상 차이가 있습니다.
    + 또한 회원가입 보다는 로그인이 빈번하게 수행되므로 두 컨텍스트로 분리하였습니다.
    + 분리시, 로그인 정보와 회원정보의 일관성이 유지되어야 하므로 회원 등록/삭제 이벤트를 비동기로 전달합니다.
    
|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-user.png" width="1000"/>|
|-|
|사용자 관리 및 로그인 컨텍스트 분리|

<br/><br/>

2. 업체 관리
    + 정산은 업체 관리 맥락과 다르므로 분리하였습니다.
    + 업체 평가 또한 제품 평가 컨텍스트와 묶어서 분리하였습니다.
    + 업체 검색은 업주/관리자가 아닌 사용자가 접근하는 정보라는 점에서 맥락이 다릅니다.  
        조회(Query)와 생성, 변경(Command)을 분리하는 CQRS 패턴을 활용하여 카탈로그라는 조회를 전담하는 컨텍스트로 분리하였습니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-store.png" width="1000"/>|
|-|
|업체 관리 컨텍스트 분리|

<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-review.png" width="400"/>|
|-|
|평가 컨텍스트 분리|

<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-catalog.png" width="400"/>|
|-|
|카탈로그 컨텍스트 분리|

<br/><br/>

3. 제품/서브 제품 관리
    + 제품조회 및 제품 평가를 분리하였습니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-product.png" width="1000"/>|
|-|
|제품/서브 제품 관리 컨텍스트 분리|

<br/><br/>

4. 구매 관리
    + 결제 정보와 주문 정보는 의미, 책임범위가 다르므로 분리하였습니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-order.png" width="1000"/>|
|-|
|구매 관리 컨텍스트 분리|

<br/><br/>

5. 배달지/상세 배달지 관리
    + 특이점이 없어 서브도메인 그대로 바운디드 컨텍스트로 식별하였습니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-delivery-site.png" width="600"/>|
|-|
|배달지/상세 배달지 관리 컨텍스트 분리|

<br/><br/>

6. 배달 관리
    + 특이점이 없어 서브도메인 그대로 바운디드 컨텍스트로 식별하였습니다.
    
|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-delivery.png" width="1000"/>|
|-|
|배달 관리 서브도메인에 대한 이벤트 스토밍 결과|

<br/><br/>

7. 알림/이미지 관리
    + 특이점이 없어 서브도메인 그대로 바운디드 컨텍스트로 식별하였습니다.
    
|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-util.png" width="600"/>|
|-|
|알림/이미지 관리 컨텍스트 분리|

<br/><br/>

8. 프로모션 관리
    + 특이점이 없어 서브도메인 그대로 바운디드 컨텍스트로 식별하였습니다.
    
|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-promotion.png" width="800"/>|
|-|
|프로모션 관리 컨텍스트 분리|

<br/><br/>

---

### 2-7. 호출 관계 정의
위에서 도출된 바운디드 컨텍스트는 다음과 같습니다.

- 로그인
- 사용자
- 업체
- 제품
- 카탈로그
- 정산
- 평가
- 프로모션
- 주문
- 결제
- 배달
- 배달지
- 알림
- 이미지

<br/> 

이처럼 바운디드 컨텍스트를 도출한 뒤 이전에 도출했던, 외부 시스템과의 연관관계, 정책 등을 살펴보면 컨텍스트 간 호출 관계를 정리할 수 있습니다. 

<br/>

다음은 도출된 컨텍스트와 컨텍스트 간의 호출 관계를 나타낸 다이어그램입니다.  

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-bc-diagram-3.png" width="1000"/>|
|-|
|컨텍스트 간의 호출 관계|

<br/><br/>

이렇게 식별된 컨텍스트가 마이크로 서비스 후보가 됩니다.  
(추후 서비스 배포, 운영 효율성 등을 고려하여 분할되거나 통합될 수 있습니다)  

<br/>

최종적으로 마이크로 서비스로 정의하기 위해 다음과 같은 질문을 고려하여 판단하겠습니다.

<br/>

- (비즈니스 측면) 비즈니스 프로세스를 수행하기 위한 하나의 맥락의 단위로 구분될 수 있는가?
    - [x] 로그인
    - [x] 사용자
    - [x] 업체
    - [x] 제품
    - [x] 카탈로그
    - [x] 정산
    - [x] 평가
    - [x] 프로모션
    - [x] 주문
    - [x] 결제
    - [x] 배달
    - [x] 배달지
    - [x] 알림
    - [x] 이미지
    
<br/>
    
- (데이터 관점) 마이크로 서비스별로 분리된 데이터를 정의할 수 있는가?
    - [ ] 로그인
    - [x] 사용자
    - [x] 업체
    - [x] 제품
    - [x] 카탈로그
    - [x] 정산
    - [x] 평가
    - [x] 프로모션
    - [x] 주문
    - [x] 결제
    - [x] 배달
    - [x] 배달지
    - [ ] 알림
    - [x] 이미지
    
<br/>

- (운영 조직 측면) 하나의 팀이 독립적으로 운영 가능한 단위인가?
    - [ ] 로그인
    - [x] 사용자
    - [x] 업체
    - [x] 제품
    - [x] 카탈로그
    - [x] 정산
    - [x] 평가
    - [x] 프로모션
    - [x] 주문
    - [x] 결제
    - [x] 배달
    - [ ] 배달지
    - [x] 알림
    - [x] 이미지

규모가 커졌다는 가정하에 체크하였습니다.   
    
<br/>

- (배포 측면) 독립적으로 배포 가능한 단위인가?
    - [x] 로그인
    - [x] 사용자
    - [x] 업체
    - [x] 제품
    - [x] 카탈로그
    - [x] 정산
    - [x] 평가
    - [x] 프로모션
    - [x] 주문
    - [x] 결제
    - [x] 배달
    - [x] 배달지
    - [x] 알림
    - [x] 이미지
    
<br/>

- (변경 영향도) 변경 시 영향을 받는 마이크로 서비스가 존재하는가?
    - [x] 로그인 - 사용자 데이터 변경 시
    - [ ] 사용자
    - [ ] 업체
    - [ ] 제품
    - [x] 카탈로그 - 업체/제품 데이터 변경 시
    - [x] 정산 - 업체/제품 데이터 변경 시
    - [ ] 평가
    - [ ] 프로모션
    - [x] 주문 - 사용자/업체/제품 데이터 변경 시
    - [x] 결제 - 프로모션 데이터 변경 시
    - [x] 배달 - 배달지 데이터 변경 시
    - [ ] 배달지
    - [ ] 알림
    - [ ] 이미지
    
<br/>

- (클라우드/MSA 도입 목적 측면) 도입을 통한 기대효과를 충분히 활용할 수 잇는가?
    + 본 서비스는 규모가 작아 MSA 에 비해 모놀리식 아키텍처로 구성할 때 성능, 관리상 이점이 높습니다.  
        하지만 추후 확장 고려, 교육 목적이 크므로 MSA 구조를 선택하였습니다.
    
<br/><br/>

---

### 2-8. 헥사고날 아키텍처
다음은 이벤트 스토밍 결과를 좀 더 상세한 설계로 만들기 위해 헥사고날 아키텍처로 표현해보겠습니다.  
(헥사고날 아키텍처 개념은 [이곳](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/ddd-hex.md#헥사고날-아키텍처)에서 자세하게 설명하겠습니다)  

<br/>

이벤트 스토밍 결과는 아래 표와 같이 매핑되어 여러 후보가 됩니다.  

|이벤트 스토밍|헥사고날 아키텍처 구성요소|
|-|-|
|커멘드|외부 영역의 인바운드 어댑터 :point_right: API 후보|
|이벤트|외부 영역의 아웃바운드 어댑터로 전송되는 메시지 이벤트 후보|
|애그리거트|내부 영역의 도메인 모델의 후보|
|인터페이스|외부 영역의 아웃바운드 어댑터로 연결될 대외 인터페이스 후보|
|정책|내부 영역의 비즈니스 로직 규현 규칙<br>외부 영역의 아웃바운드 어댑터로 연결될 다른 서비스와의 방향을 결정하는 데 도움이 됨|

<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-hex-mapping.png" width="1100"/>|
|-|
|이벤트 스토밍 결과와 헥사고날 아키텍처 구성요소 매핑|

<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/2/ddd-hex-mapping2-1.png" width="1100"/>|
|-|
|이벤트 스토밍 결과와 헥사고날 아키텍처 구성요소 매핑|

<br/><br/>

---

이렇게 DDD (도메인 주도 설계)를 통한 도메인 모델링이 끝났습니다.  
도출된 애그리거트와 도메인 흐름을 좀 더 발전시켜 모델링을 진행할 수 있으나 더 상세한 설계를 진행하기에 앞서 아키텍처 정의 활동부터 살펴보겠습니다.

<br/>

[다음장](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드)
은 시스템 설계를 통해 외부 아키텍처와 내부 아키텍처를 정의해 보겠습니다.

<br/><br/>

---

[[:point_up_2: 위로]](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [1. 다루는 내용](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/1/index.md#배달-서비스-플랫폼-api-서버-가이드) 
- [2. 도메인 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md#배달-서비스-플랫폼-api-서버-가이드) :point_left:
- [3. 시스템 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [4. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [5. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [6. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [7. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md#배달-서비스-플랫폼-api-서버-가이드)