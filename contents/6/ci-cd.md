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

위 방법처럼 직접 파일을 생성하지 않아도 됩니다.  
Github 에서 제공하는 Actions UI 를 이용해 보겠습니다.

<br/>

Github Repository 탭에 `Actions` 메뉴로 들어갑니다.  
왼쪽에 `New workflow` 를 눌러 새로운 workflow 를 만들 수 있습니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/cicd/cicd-file-location.png" width="500"/>|
|-|
|workflow 파일 생성|

<br/>

`set up a workflow yourself` 를 눌러 커스텀 workflow 파일을 생성합니다.  

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

위 명령어를 하나하나 살펴보겠습니다.

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

- `jobs`  
    job 은 기본 실행 단위입니다.  
    job 들은 모두 병렬적으로 실행됩니다.
    
    ```yaml
    jobs:
      some-job-id:
        ...
      some-job-id-2:
        ...
    ```
    
    + `runs-on`  
        해당 job 을 가상 실행할 컴퓨팅 환경인 `runner` 를 명시합니다.  
        ubuntu-latest, macos-latest, windows-latest 등으로 실행 가능합니다.
        
        ```yaml
        jobs:
          some-job-1:
            runs-on: ubuntu-latest
        ```
        
    <br/>
    
    + `strategy`  
        여러 환경에서의 실행을 위해 build matrix 를 설정합니다.  
        각기 다른 환경들을 명시하여 같은 job 을 동시에 실행할 수 있습니다.  
        ```yaml
        jobs:
          some-job-1:
            strategy:
              matrix:
                node-version: [10.x, 12.x]
        ```
        
    <br/>
    
    + `steps`  
        job 내부 실행 과정을 명시합니다.  
        + Github 저장소 코드 체크아웃
        + Github Marketplace 에서 import 한 actions 실행
        + shell 명령어 실행
        + 도커 이미지 빌드/배포
        + aws/gcp 등의 인프라에 서비스 배포 등
        
        각 step 은 job 의 컴퓨팅 자원에서 독립적인 프로세스로 동작하며,  
        여러가지 명령들을 순차적으로 실행합니다.  
        (또한 runner 의 파일 시스템에 접근할 수 있습니다)
        
        ```yaml
        jobs:
          some-job-1:
            runs-on: ubuntu-latest
            steps:
              # Github 저장소에 저장된 소스코드를 체크아웃
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
        
        위 workflow 는 Github 에 저장된 소스코드를 체크아웃하고 gradlew 를 통해 build 작업이 순차적으로 실행됩니다.
        
        <br/>
        
        + `steps.name`  
            step 의 이름을 명시합니다.
            
        <br/>
        
        + `steps.uses`  
            해당 스텝에서 사용할 action 을 명시합니다.  
            [Github Marketplace](https://github.com/marketplace) 에 많은 action 들이 있습니다.  
            
        <br/>
        
        + `steps.run`  
            runner 의 shell 을 이용하여 명시된 명령어 나열을 실행합니다.  
            
            ```yaml
            jobs:
              some-job-1:
                steps:
                  - name: My First Step
                    run: | ## 명령어를 여러 줄 사용하기 위해서는 파이프(|) 를 입력합니다.
                      npm install
                      npm test
                      npm build
            ```

<br/>

이외에 Github Actions 문법은 공식문서를 통해 확인할 수 있습니다.  
[Workflow syntax for GitHub Actions](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions)

<br/>

이제 복사한 내용을 `commit` 하면 자동으로 Action workflow 가 실행됩니다.  
Actions UI 에서 workflow 실행과정이 모니터링 됩니다.

<br/><br/>

### CI/CD 파이프라인 Workflow
기본적인 Github Actions 내용을 숙지했다면 CI/CD 자동화 파이프라인을 구축해보겠습니다.

<br/>

Workflow 는 다음과 같습니다.  

```yaml
name: Build-Deploy

on:
  push:
    branches: [ master ]
    paths-ignore:
      - "**.md"
  pull_request:
    branches: [ master ]
    paths-ignore:
      - "**.md"

env:
  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}

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

      # Gcloud CLI 세팅
      - name: Set up Gcloud
        uses: google-github-actions/setup-gcloud@master
        with:
          version: '290.0.1'
          service_account_key: ${{ secrets.GCP_SA_KEY }}
          project_id: ${{ secrets.GCP_PROJECT_ID }}
          export_default_credentials: true

      # GCR 연결 위한 인증 작업 실행
      - name: Set Auth GCR
        run: gcloud --quiet auth configure-docker

      # GCR에서 이전 버전 참고하여 다음 버전 만든 후, 이미지 빌드 및 푸쉬
      - name: Build Docker Image And Delivery To GCR
        run: |
          IMAGE=gcr.io/${{ secrets.GCP_PROJECT_ID }}/${{ secrets.REPOSITORY_NAME }}
          INPUT=$(gcloud container images list-tags --format='get(tags)' $IMAGE)
          LATEST_TAG=$(echo ${INPUT[0]} | awk -F ' ' '{print $1}' | awk -F ';' '{print $1}')
          ADD=0.01
          VERSION=$(echo "${LATEST_TAG} $ADD" | awk '{print $1 + $2}')
          NEW_VERSION=$(printf "%.2g\n" "${VERSION}")
          docker build --tag $IMAGE:${NEW_VERSION} .
          docker push $IMAGE:${NEW_VERSION}
          docker tag $IMAGE:${NEW_VERSION} $IMAGE:latest
          docker push $IMAGE:latest

      # 작업 결과 슬랙 전송
      - name: Result to Slack
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          fields: repo,message,commit,author,action,eventName,ref,workflow,job,took
          author_name: MSA Build Result
        if: always()

  deploy:
    needs: [ build ]
    runs-on: ubuntu-latest

    steps:
      # SSH 접속을 통한 직접 배포
      - name: Deploy to GCE
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SSH_HOST }}
          username: ${{ secrets.SSH_USERNAME }}
          key: ${{ secrets.SSH_KEY }}
          passphrase: ${{ secrets.SSH_PASSPHRASE }}
          script: |
            CONTAINER_NAME=hello-container
            IMAGE=gcr.io/${{ secrets.GCP_PROJECT_ID }}/${{ secrets.REPOSITORY_NAME }}
            sudo gcloud --quiet auth configure-docker
            sudo docker ps -q --filter "name=$CONTAINER_NAME" | grep -q . && sudo docker stop $CONTAINER_NAME
            sudo docker system prune -a -f
            sudo docker run -d --name "$CONTAINER_NAME" --rm -p ${{ secrets.CONTAINER_PORT }}:${{ secrets.CONTAINER_PORT }} $IMAGE:latest

      # 작업 결과 슬랙 전송
      - name: Result to Slack
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          fields: repo,message,commit,author,action,eventName,ref,workflow,job,took
          author_name: MSA Deploy Result
        if: always()
```

<br/>

위 명령어 하나하나 살펴보겠습니다.  
우선 트리거 조건을 명시하는 `on` 부분입니다.  
`paths-ignore` 를 통해 commit 시 무시되는 파일 확장자를 명시합니다.

```yaml
on:
  push:
    branches: [ master ]
    paths-ignore:
      - "**.md"
  pull_request:
    branches: [ master ]
    paths-ignore:
      - "**.md"
```

<br/>

`env` 는 workflow 내부에서 사용되는 환경변수입니다.  
아래 action 에서 사용될 각 변수들에 값을 넣어줍니다.  
`${{ secrets.시크릿변수 }}` 문법을 통해 `Github Secret` 에서 설정한 시크릿을 가져올 수 있습니다.  
`GITHUB_TOKEN`, `SLACK_WEBHOOK_URL` 변수들에 대한 설명은 아래에서 다루겠습니다.

```yaml
env:
  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

<br/>

runner 환경은 리눅스 `ubuntu` 를 사용하여 실행되게 합니다.

```yaml
runs-on: ubuntu-latest
```
 
<br/>

Github 저장소에서 소스코드를 체크아웃 합니다.  
체크아웃된 코드는 runner 의 가상 실행 환경에 위치됩니다.

```yaml
# "master" 체크아웃
- name: Checkout
  uses: actions/checkout@v2
```

<br/>

해당 프로젝트는 Java 프로젝트를 기반으로 진행되므로 `JDK 1.8` 환경을 runner 에 세팅해줍니다.  
체크아웃 한 코드를 `gradlew` 를 통해 Test 및 Build 작업을 실행합니다.

```yaml
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

GCP 를 제어하기 위한 Gcloud CLI 를 세팅합니다.  
`service_account_key` 와 `project_id` 는 GCP 인증을 위해 GCP 서비스 계정에서 받아와 설정해줘야 하는 정보들입니다.  
Secret 설정은 아래에서 자세하게 다루겠습니다.

```yaml
# Gcloud CLI 세팅
- name: Set up Gcloud
  uses: google-github-actions/setup-gcloud@master
  with:
    version: '290.0.1'
    service_account_key: ${{ secrets.GCP_SA_KEY }}
    project_id: ${{ secrets.GCP_PROJECT_ID }}
    export_default_credentials: true
```

<br/>

GCR (Google Container Registry) 은 GCP 에서 제공하는 컨테이너 이미지 레지스트리(저장소) 입니다.  
Docker 이미지를 빌드하여 저장하기 위해 GCR 을 사용합니다.  
아래 명령어는 GCR 연결을 위한 선 인증 작업을 실행합니다.  
`run` 명령으로 실행되는 `gcloud` 사용을 위해 위에서 Gcloud CLI 세팅작업이 선결되어야 합니다.

```yaml
# GCR 연결 위한 인증 작업 실행
- name: Set Auth GCR
  run: gcloud --quiet auth configure-docker
```

<br/>

shell 명령을 사용하여 GCR 에 도커 이미지를 push 하는 과정입니다.  
GCR 에 저장되어 있는 최근 이미지의 버전을 얻어와 버전업한 후 도커 이미지를 빌드하여 저장소로 푸시하는 과정이 이뤄집니다.

```yaml
# GCR에서 이전 버전 참고하여 다음 버전 만든 후, 이미지 빌드 및 푸쉬
- name: Build Docker Image And Delivery To GCR
  run: |
    IMAGE=gcr.io/${{ secrets.GCP_PROJECT_ID }}/${{ secrets.REPOSITORY_NAME }}
    INPUT=$(gcloud container images list-tags --format='get(tags)' $IMAGE)
    LATEST_TAG=$(echo ${INPUT[0]} | awk -F ' ' '{print $1}' | awk -F ';' '{print $1}')
    ADD=0.01
    VERSION=$(echo "${LATEST_TAG} $ADD" | awk '{print $1 + $2}')
    NEW_VERSION=$(printf "%.2g\n" "${VERSION}")
    docker build --tag $IMAGE:${NEW_VERSION} .
    docker push $IMAGE:${NEW_VERSION}
    docker tag $IMAGE:${NEW_VERSION} $IMAGE:latest
    docker push $IMAGE:latest
```

<br/>

Slack 채널로 해당 작업 결과를 전송합니다.  

```yaml
# 작업 결과 슬랙 전송
- name: Result to Slack
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    fields: repo,message,commit,author,action,eventName,ref,workflow,job,took
    author_name: MSA Build Result
  if: always()
```

<br/>

GCE (Google Compute Engine) 로 SSH 접속하여 도커 이미지를 pull 하여 컨테이너에 어플리케이션이 포함된 이미지를 실행합니다.  
`script` 부분은 SSH 접속후 해당 컨테이너에서 실행할 스크립트를 명시합니다.  
컨테이너 내부에서 GCR 접속을 위해 `gcloud --quiet auth configure-docker` 명령을 통해 접근 권한을 얻습니다.  
기존 컨테이너를 정지/삭제한 뒤 버전업된 새로운 이미지를 받아와 실행시킵니다.  

```yaml
# SSH 접속을 통한 직접 배포
- name: Deploy to GCE
  uses: appleboy/ssh-action@master
  with:
    host: ${{ secrets.SSH_HOST }}
    username: ${{ secrets.SSH_USERNAME }}
    key: ${{ secrets.SSH_KEY }}
    passphrase: ${{ secrets.SSH_PASSPHRASE }}
    script: |
      CONTAINER_NAME=hello-container
      IMAGE=gcr.io/${{ secrets.GCP_PROJECT_ID }}/${{ secrets.REPOSITORY_NAME }}
      sudo gcloud --quiet auth configure-docker
      sudo docker ps -q --filter "name=$CONTAINER_NAME" | grep -q . && sudo docker stop $CONTAINER_NAME
      sudo docker system prune -a -f
      sudo docker run -d --name "$CONTAINER_NAME" --rm -p ${{ secrets.CONTAINER_PORT }}:${{ secrets.CONTAINER_PORT }} $IMAGE:latest
```

<br/><br/>

### Github Secret 설정

### Slack 알림 설정