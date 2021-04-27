# 도커

<br/><br/>


## :speech_balloon: 개요
도커는 컨테이너 기반의 오픈소스 가상화 플랫폼입니다.  
다양한 프로그램, 실행환경을 컨테이너로 추상화하고 동일한 인터페이스를 제공하여 프로그램의 배포/관리를 단순하게 만들어 줍니다. 

<br/><br/>

## 컨테이너
컨테이너는 격리된 공간에서 프로세스가 동작하는 기술입니다.  

<br/>

백엔드 프로그램, 데이터베이스 서버 메시지 큐 등.. 어떤 프로그램도 컨테이너로 추상화할 수 있으며  
온프렘 환경, AWS, Azure, Google Cloud 등 어디에서든 실행할 수 있습니다.

<br/>

도커는 기존 VM 방식과는 다르게 프로세스를 격리 시켜 시스템 자원을 프로세스가 필요한 만큼만 추가하고 성능적으로 거의 손실이 없는 환경을 만듭니다.  
(새로운 컨테이너를 만드는데 걸리는 시간은 불과 1~2초입니다)

<br/>

도커에서 가장 중요한 개념은 컨테이너와 함께 이미지라는 개념입니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/docker/msa-pattern-docker.png" width="500"/>|
|-|
|컨테이너 이미지 레이어|

<br/>

- 이미지는 컨테이너 실행에 필요한 파일과 설정값 등을 포함하고 있습니다.  
- 이미지는 불변성(Immutable)을 갖습니다.
- 레이어 단위의 이미지를 포개는 방식으로 구성됩니다.

<br/>

컨테이너는 이러한 이미지를 실행한 상태라고 볼 수 있습니다.  
같은 이미지로 여러개의 컨테이너를 생성할 수 있고 컨테이너 상태가 변경/삭제되어도 이미지는 변하지 않고(불변) 그대로 남아있습니다.

예시
- ubuntu 이미지  
    ubuntu 실행을 위한 모든 파일
    
<br/>
    
- MySQL 이미지  
    ubuntu 이미지를 기반으로 MySQL 을 실행하는데 필요한 파일, 실행 명령어, 포트 정보 등  
    (레이어 단위의 이미지 구성)
    
<br/>

- Gitlab 이미지  
    centos 를 기반으로 ruby, go, database, redis, gitlab source, nginx 등

<br/>

위 예시와 같이 이미지는 컨테이너를 실행하기 위한 모든 정보를 갖습니다.  
(이는 더이상 의존성 파일을 설치하고 컴파일하고 이것저것 할 필요 없음을 의미합니다)

<br/>

도커 이미지의 용량을 보통 수백메가에서 수기가 넘는 경우도 흔합니다.  
이렇게 큰 용량의 이미지를 관리하기 위해 Docker Hub 를 이용합니다.  

<br/><br/>

## 도커 설치
도커는 리눅스 컨테이너 기술이므로 macOS 또는 윈도우에 설치할 경우 가상머신에 설치가 됩니다.  

<br/>

각 개발 환경에 따른 설치를 진행하시면 됩니다.

- [윈도우 설치](https://hub.docker.com/editions/community/docker-ce-desktop-windows)
- [macOS 설치](https://hub.docker.com/editions/community/docker-ce-desktop-mac)
- [리눅스 설치](https://hub.docker.com/search?q=&type=edition&offering=community&operating_system=linux)

<br/>

도커가 설치됩니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/docker/gcp-docker-1.png" width="500"/>|
|-|
|도커 설치중|

> 리눅스의 경우 도커를 실행하기 위한 kernel 버전은 3.10.x 이상입니다.  
> (ubuntu 14.04 이상)

<br/><br/>

[도커 허브](https://hub.docker.com/)는 도커 이미지를 저장하는 클라우드입니다.  
회원가입/로그인을 합니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/docker/docker-login.png" width="700"/>|
|-|
|도커 허브 로그인|

<br/><br/>

`Create Repository` 버튼을 눌러 이미지를 저장할 Repository 를 만듭니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/docker/docker-repo.png" width="700"/>|
|-|
|도커 허브 저장소|

<br/><br/>

적당한 Repository 이름과 Visibility(공개/비공개) 를 선택한 뒤 `Create` 버튼을 눌러 Repository 를 생성합니다.

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/docker/docker-repo-create.png" width="700"/>|
|-|
|도커 허브 저장소|

<br/><br/>

도커 설치가 마무리 되었다면 간단한 도커 파일을 만들어 도커 허브에 푸시 해보겠습니다.  
도커 파일은 컨테이너에 설치해야 하는 패키지, 소스코드, 명령어, 환경변수 설정 등을 기록한 하나의 파일입니다.  
매번 반복되는 과정을 단순하게 간소화 시켜줍니다.  

|<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/docker/docker-file-process.png" width="500"/>|
|-|
|이미지 출처 : https://data-newbie.tistory.com/516|

<br/><br/>

1. 도커 파일 생성  
    (윈도우의 경우) 빈 file 을 열거나  
    (리눅스의 경우) vim 이나 cat 을 통해 도커 파일을 작성합니다.  
    
    <br/>
    
    윈도우 docker file
    
    ```dockerfile
    FROM busybox
    CMD echo "Hello world! This is my first Docker image."
    ```
    
    <br/>
    
    리눅스/macOS docker file
    
    ```
    ~ $ cat > Dockerfile <<EOF
    > FROM busybox
    > CMD echo "Hello world! This is my first Docker image."
    > EOF
    ```
    
    <br/>
    
    - `FROM busybox` : busybox 를 base image 로 지정하겠다는 명령어 입니다.   
        busybox 는 여러 유닉스 standard utilities 도구들을 담아놓은 실행 파일입니다.
    - `CMD echo` : 컨테이너가 시작할 때 실행할 커멘드를 지정합니다.  
        docker image 를 빌드할 때 실행되는 것이 아니라 docker container 가 시작될 때 실행됩니다.  
        주로 docker image 로 빌드된 application 을 실행할 때 쓰입니다.
    
    도커 파일 명령어는 [아래](https://github.com/cholnh/delivery-platform-server-guide/blob/main/contents/3/msa-docker.md#자주쓰는-도커-파일-명령어)에서 자세하게 다뤄보겠습니다.
    
<br/>

2. 도커 파일 빌드  
    도커 파일을 빌드하면 이미지가 생성됩니다.  
    아래와 같이 콘솔에 입력합니다.
    
    `docker build -t hellobuild:0.1 .`
    
    + `-t` 옵션은 생성될 이미지의 이름을 지정합니다.
    + `:0.1` 은 태그 버전을 지정합니다.
    + `.` 은 현재 디렉토리를 의미합니다. (현 디렉토리의 Dockerfile 을 찾아 build 합니다)
    
    <br/><br/>
    
    아래와 같이 빌드가 진행됩니다.
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/docker/docker-build.png" width="700"/>|
    |-|
    |도커 파일 빌드|
    
    <br/><br/>
    
    `docker images` 를 입력해보면 이미지가 성공적으로 생성되어 등록된 것을 볼 수 있습니다.
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/docker/docker-build.png" width="700"/>|
    |-|
    |도커 파일 빌드|
    
<br/><br/>

3. 도커 이미지 실행  
    빌드한 이미지를 실행시켜 보겠습니다.  
    아래와 같이 콘솔에 입력합니다.
    
    `docker run hellobuild:0.1` 
    
    <br/><br/>
    
    `echo` 명령어가 실행되는 모습을 볼 수 있습니다.
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/3/docker/docker-run.png" width="500"/>|
    |-|
    |도커 이미지 실행|
    
<br/><br/>

### 자주쓰는 도커 파일 명령어

- `FROM`  
    생성할 이미지의 베이스가 될 이미지를 뜻합니다.  
    반드시 한번 이상 입력해야 합니다.  
    베이스 이미지를 지정할 때는 OS와 버전까지 정확하게 지정해주는 것이 좋습니다.  
    + `FROM ubuntu:16.04`
    
<br/>

- `LABEL`  
    이미지에 메타데이터를 추가합니다.  
    추후 이 라벨을 통해 조건을 검색하여 원하는 컨테이너, 이미지 등을 쉽게 찾을 수 있습니다.  
    + `LABEL "purpose"="test"`
    
<br/>

- `RUN`  
    이미지를 만들기 위해 컨테이너 내부에서 명령어를 실행합니다.  
    주로 (apt-get, wget 과 같은)설치/업데이트에 사용되곤 합니다.  
    여기서 주의할 점은 설치과정에서 별도의 입력이 불가능하기 때문에 `-y` 와 같은 입력을 붙여줘야 합니다.  
    + `RUN apt-get install apache2 -y`  
    + `RUN ["/bin/bash", "-c", "echo hello > test2.html"]`
    
<br/>

- `ADD` \[from\] \[to\]  
    파일을 이미지에 추가합니다.  
   + `ADD test.html /var/www/html`
    
<br/>

- `WORKDIR`  
    명령어를 실행할 디렉토리를 지정합니다.  
    bash 쉘에서 `cd` 명령어와 동일한 기능입니다.  
    + `WORKDIR /var/www/html`
    
<br/>

- `EXPOSE`  
    이미지에서 노출할 포트를 설정합니다.  
    + `EXPOSE 80`
    
<br/>
    
- `CMD`  
    컨테이너가 시작될 때 시작할 명령어를 지정합니다.  
    한 번만 사용할 수 있으며, 주로 docker image 로 빌드된 application 을 실행할 때 쓰입니다.  
    + `CMD echo "hello world!"`
    + `CMD apachectl -DFOREGROUND`