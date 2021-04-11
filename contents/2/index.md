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

- [1. 다루는 내용](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/1/index.md)
- [2. 도메인 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/2/index.md) :point_left:
- [3. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/index.md)
- [4. 시스템 설계](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md)
- [5. 마일스톤](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md)
- [6. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md)
- [7. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md)
- [8. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/8/index.md)
- [9. 회고](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/9/index.md)

<br/><br/>



## 2. 도메인 설계
해당 프로젝트는 가상 비즈니스 상황을 가정하고 가상 서비스에 대한 API 서버를 구현합니다.  

### 왜 API 서버가 필요한가?
**API 서버란**   
Web 또는 Mobile Application 에서 필요한 기능을 제공하는 서버입니다.  
여기서 필요한 기능이란 
- 회원가입 요청
- 제품 리스트 요청
- 회원탈퇴 요청 등등 
다양한 요청을 뜻합니다.  
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
대학/기관 같이 사람들이 밀집해 있는 곳은 **'먹는 시간'** 과 **'취식 장소'** 가 정해져 있습니다. 
그렇다면 음식 주문을 미리 모아 한 번에 묶음 배달을 해주면 어떨까 생각해 보았습니다.  
방식은 다음과 같습니다.

1. [주문 전달] 고객의 주문을 미리 받아 업주(음식점 사장님)들에게 전달합니다.
2. [제품 픽업] 픽업 시간에 맞춰 픽업 트럭이 모든 업체(음식점)를 순회하며 제품(음식)을 수령합니다.
3. [제품 이동] 픽업 트럭은 배달 받는 장소로 이동합니다.
4. [제품 전달] 배달 기사는 배달 장소에 있는 제품보관대에 제품을 넣어 놓습니다. (다음 배달 장소가 있으면 반복합니다)
5. [배달 완료] 배달 기사는 다음 픽업을 준비합니다.

<br/>

**[ 무엇을 해결할까요 ]**
- 고객들은 제품 할인과 무료 배달을 경험합니다.
- 명량핫도그 하나만 시켜도 배달이 됩니다.
- 업주들은 바쁘지 않은 시간대에 사전 단체 주문을 받아 추가 매출이 기대됩니다.
- 오토바이 배달보다 상대적으로 안전하고 배달횟수가 짧으므로 사고율이 현저히 감소됩니다. 

<br/>