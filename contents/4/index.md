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
- [4. 전략과 도구](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/4/index.md#배달-서비스-플랫폼-api-서버-가이드) :point_left:
- [5. 마일스톤](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/5/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [6. 인프라 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/6/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [7. 서비스 구현](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/7/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [8. 마무리](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/8/index.md#배달-서비스-플랫폼-api-서버-가이드)
- [9. 회고](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/9/index.md#배달-서비스-플랫폼-api-서버-가이드)

<br/><br/>



## 4. 전략과 도구
해당 프로젝트는 가상 비즈니스 상황을 가정하고 가상 서비스에 대한 API 서버를 구현합니다.  

<br/>