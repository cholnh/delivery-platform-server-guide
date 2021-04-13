# 이벤트 스토밍

<br/><br/>



## :speech_balloon: 개요
이벤트 스토밍은 DDD 설계를 가속화할 수 있는 설계 기법입니다.  
이벤트 스토밍 워크숍은 이벤트 중심으로 이해관계자들이 모여 브레인 스토밍하는 워크숍을 의미합니다.  
모든 이해관계자가 모여 서로의 관점을 논의하며 지식을 공유하고 협업을 가속화하고 시각화 합니다.  
다양한 스티커를 표현 도구로 사용하여 시스템을 분석하게 됩니다.

<br/><br/>

## :athletic_shoe: 준비
워크숍을 진행하려면 다음과 같은 사항을 준비합니다.
- 공간  
    + 깨끗한 벽이 있는 넓은 워크숍 공간

<br/>
    
- 참가자  
    + 고객
    + 도메인 전문가
    + 설계자 
    + 개발자
    + 테스터
    + 등등 모든 이해관계자
    
<br/>
    
- 준비물  
    + 벽에 붙일 흰색 A0 전지
    + 마커 펜
    + 다양한 색의 스티커
    + 라인테이프
    + 스카치 테이프
    
<br/>

- 열린 분위기로 활동을 촉진하고 리딩할 수 있는 퍼실리테이터 (참여 유도하는 MC 같은 사람..)
    
<br/><br/>


    
## :runner: 방식
이벤트 스토밍을 진행하는 순서는 다음과 같습니다.  
(각 진행 순서와 스티커 색상은 프로젝트마다 조금씩 다를 수 있습니다)

<br/>

1. 도메인 이벤트 정의  
    업무 처리 흐름별로 도메인 이벤트를 정의합니다. (왼쪽에서 오른쪽 방향)  
    + 오렌지색 스티커
    + 과거형으로 기술 (ex. 포인트 적립됨)
    + 이슈/개선 사항/관심/재논의 는 빨간색
    + 중간중간 중복된 내용을 제거합니다.
    + 동시에 수행되는 내용은 수직으로 내립니다.

<br/>

2. Tell the Story  
    업무 흐름을 설명하고 논의합니다.
    
<br/>

3. 커멘드 정의  
    각 도메인 이벤트를 발생시키는 명령(사용자 명령)을 현재형으로 정의합니다.
    + 파란색 스티커
    + 도메인 이벤트 왼쪽에 붙입니다.
    + 커멘드 하나에 1개 이상의 이벤트가 발생할 수 있습니다.
    
<br/>

4. 액터 정의  
    커멘드를 발생시키는 주체를 정의합니다.
    + 노란색 작은 원형 스티커
    + 커멘드 왼쪽 아래에 붙입니다.
    
<br/>

5. 외부 인터페이스 식별  
    이벤트와 연관된 외부 시스템(인터페이스)을 정의합니다.
    + 핑크색 스티커

<br/>

6. 애그리거트 찾기  
    커멘드를 수행하기 위해 CRUD 해야 하는 데이터 객체를 정의합니다.  
    애그리거트는 한 단위로 취급 가능한 경계 내부의 도메인 객체입니다.
    + 노란색 스티커
    + 데이터 객체란 엔티티, VO 집합입니다.
    
<br/>

7. 정책 정의  
    이벤트 발생 시 타 영역의 커멘드를 트리거하는 정책을 정의합니다.
    + 보라색 스티커
    + 이름, 이메일 validation, 주민 번호 뒷자리 마스킹 검사 등 이벤트 발생 시 타 영역 커멘드 발동