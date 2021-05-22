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
(사용자가 직접 호스팅하는 환경에서도 구동이 가능합니다. self hosted runner)

<br/>

또한 다른 사람들이 제작한 Workflow 가 [Github Marketplace](https://github.com/marketplace) 에 공유되어 있습니다.  
Github 을 통해 공식인증된 Workflow 를 사용할 수도 있고, 커스텀된 Workflow 를 제작할 수도 있습니다.  
간단한 커스텀 Workflow 를 만들어보겠습니다.

<br/>

Workflow 만드는 방법은 간단합니다.  
우선 자신의 프로젝트가 저장되어 있는 Github Repository 에 `.yml` 파일을 하나 생성합니다.  
위치는 프로젝트 폴더의 최상위에 `.github/workflow/*.yml` 로 위치시킵니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/cicd/cicd-file-location.png" width="500"/>|
|-|
|workflow 파일 생성|

<br/>

파일 내용은 다음을 복사합니다.

```yaml
name: Java Gradle Build

on:
  push:
    branches: [ master ]
  pull_request:
    branches: [ master ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # "master" 체크아웃
      - name: Checkout
        uses: actions/checkout@v2

      # Java + Gradle 기반 앱 테스트 및 빌드
      - name: Set up JDK 1.8
        uses: actions/setup-java@v1
        with:
          java-version: 1.8

      - name: Source Code Test And Build
        run: |
          chmod +x gradlew
          ./gradlew build
```

<br/>

- `name`  
    workflow 의 이름을 명시합니다.
    
    ```yaml
    name: Java Gradle Build
    ```
    
<br/>

- `on`  
    workflow 의 이벤트 조건을 명시합니다.  
    `push` 또는 `pull request` 동작시 작동하게 하거나 `cron` 문법을 이용해 시간을 설정할 수도 있습니다.
    
    ```yaml
    on:
      schedule:
        - cron: '*/10 * * * *'
      push:
        branches: [ master ]
      pull_request:
        branches: [ master ]
    ```

    또한 `paths` 를 이용하여 특정 패턴에 해당하는 파일이 변경되었을 때 트리거가 되도록할 수 있습니다.  
    
    ```yaml
    on:
      push:
        branches: [ master, dev ]
        paths:
          - "**.java"
          - "**.js"
        paths-ignore:
          - "doc/**"
          - "**.md"
    ```
    
<br/>

