# Github Actions 를 이용한 CI/CD 파이프라인 구축

<br/><br/>



## :speech_balloon: 개요
Github Actions 는 Github 에서 제공하는 소프트웨어 개발 Workflow 를 자동화하는 도구입니다.  
CI, CD 파이프라인은 빌드/배포 과정 동안 수행해야 할 테스크가 정의된 것입니다.  
Github Actions 를 통해 CI/CD 파이프라인을 Workflow 에 정의하여 자동화하는 과정을 살펴보겠습니다.


<br/>

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/msa-pattern-cicd-pipeline.png" width="700"/>|
|-|
|CI/CD 파이프라인|

<br/><br/>


### 사용 도구
Github Actions 를 이용한 CI/CD 파이프라인 구축하기 위해서 다음과 같은 도구들을 사용합니다.

- Github Repository
- Docker 이미지 (app 포함)
- GCE (Google Compute Engine) 인스턴스
- GCR (Google Container Registry)
- Slack Repository

<br/><br/>

### 간단한 Workflow 만들기
Github actions 에서 제공하는 Workflow 는 Github 저장소에 저장된 소스코드를 이용한  
`Build`, `Test`, `Release`, `Deploy` 등 다양한 이벤트를 자동화할 수 있습니다.  

<br/>

Workflow 는 Runners 라 불리는 **Github 에서 호스팅된 환경(Linux, macOS, Windows 등)에서** 실행됩니다.  
(사용자가 직접 호스팅하는 환경에서도 구동이 가능합니다; self hosted runner)