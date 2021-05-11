# GCE 위에 모놀리식 스프링부트 실행시키기

<br/><br/>



## :speech_balloon: 개요
GCP 에서 제공하는 GCE(Google Compute Engine)를 사용하여 Monolithic 스프링 부트 프로젝트를 실행시켜 보겠습니다.

<br/><br/>



### GCP 가입하기
[Google Cloud Platform](https://cloud.google.com/?hl=ko) (이하 GCP) 가입을 진행하겠습니다.  
가입에 앞서 GCP 에서 제공하는 여러 무료 제품군을 설명하겠습니다.

<br/>

GCP 는 처음 가입자에게 무료 크레딧(300불)을 3개월 기한으로 제공합니다.  
이는 3개월 동안 어떤 서비스든 $300 무료 크레딧을 가지고 다양한 테스팅을 할 수 있게 합니다.  
그리고 많은 분들이 이미 알고 계시는 것처럼 `Gmail ID 새로 생성하기` 신공을 통해 3개월이라는 짧은 테스트 기한을 극복할 수 있습니다.  

<br/>

> 참고로 VM(GCE) 의 경우 테스트를 진행하다가 사용이 없는 경우(야간이나 주말 등)  
> 해당 인스턴스를 정지 시켜두면 디스크 비용말고는 별도의 비용이 발생되지 않습니다.

<br/>

무료 크레딧 외에도 free tier 라는 '항상 무료' 제품군이 존재합니다.  
제약 조건이 있기는 하지만 조건이 맞는다면 제한 없이 계속 무료로 사용할 수 있는 오퍼입니다.  
GCE Region 을 `US` 로 설정하고 머신타입을 `f1-micro` 로 선택하시면 월 1개 인스턴스는 무상으로 사용 가능하게 됩니다.  
(참고로 북 버지니아(us-east4)는 US 지역에 포함되지 않습니다)  

<br/>

GCE free tier 에서 제공하는 스팩은 다음과 같습니다.

- HDD 30GB/월
- 스냅샷 5GB/월
- 네트워크 송신 1GB/월 (북아메리카에서 모든 지역의 대상위치로 네트워크 송신)

위 스팩을 보시면 아시겠지만 간단한 개발/테스트는 `f1-micro` 만으로도 충분히 가능하다고 생각합니다.

> f1-micro 머신은 0.2vCPU/0.6GB MEMORY 기본 제공입니다.  
> 0.2 수치가 작아보일 수 있지만 서버급 cpu 제공(Intel Xeon) 이므로 개발/테스트 목적에서는 큰 이슈없이 활용 가능합니다.  
> 또한 CPU Busting 기능을 제공하여 일정 기간동안 추가적인 물리적 CPU 파워를 땡겨와 순간적인 성능을 제공합니다.  
> (얼마나 효과적인지는 테스트해보지 않았습니다)

<br/>

만약 Region 을 Asia 로 두고 개발/테스트 해보고 싶으시다면 Google App Engine (GAE) 선택하는 것도 좋은 대안이 될 수 있습니다.  
GAE standard free tier 에서 제공하는 스팩은 다음과 같습니다.

- 28 인스턴스 시간/일 (28 인스턴스 시간의 의미는 하나 이상의 인스턴스를 사용할 수 있다는 뜻입니다)
- Cloud Storage 5GB
- Memcache 등

<br/>

위에서 설명드린 GCE, GAE free tier 또는 무료 크레딧을 이용한 자유로운 제품군을 선택하시면 되겠습니다.  
다음 단계로 Database 제품군인 Google Cloud Datastore (GCD) 를 알아보겠습니다.  

<br/>

GCD free tier 에서 제공하는 스팩은 다음과 같습니다.

- 1GB 저장소
- 읽기 50,000회/일
- 쓰기 20,000회/일
- 삭제 20,000회/일

<br/>

GCD 의 경우 NoSQL Database 로 완전관리형 Database 입니다.  

> 자동 분할 및 복제 처리를 통해 자동으로 확장되는 가용성과 내구성이 높은 데이터베이스 입니다.  
> 이런 복잡한 기능들을 Google Cloud 가 알아서 관리해주며 기본 ACID 트랜젝션, 색인 등의 기능을 제공합니다.  
> RESTful 인터페이스를 사용해 데이터에 액세스 합니다.

<br/>

이제 회원가입을 진행해 보겠습니다.  
[Google Cloud Platform](https://cloud.google.com/?hl=ko)에 접속한 후 가입하기를 통해 구글에 가입을 하여줍니다.  
구글 가입 부분은 생략하겠습니다.

<br/>

가입 후 결제 수단에서 신용카드를 등록해줍니다.  
(무료 크레딧 기간 종료 후에도 돈이 빠져나가진 않습니다)

<br/><br/>

### GCE 인스턴스 생성

- 결제 등록을 마쳤다면 왼쪽 메뉴를 열어 컴퓨팅 카테고리에서 `Compute Engine` 을 선택해 줍니다.  

    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-sidebar.png" width="400"/>|
    |-|
    |Compute Engine 선택|

<br/>

- 위 메뉴에서 `인스턴스 만들기` 를 선택하여 새 인스턴스를 생성해보겠습니다.  

    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-new-instance.png" width="900"/>|
    |-|
    |인스턴스 만들기 선택|

<br/>

- `인스턴스 이름`을 적고 `리전`을 선택해줍니다.  
    해당 프로젝트에서는 무료크레딧을 사용하여 서울 리전을 선택하겠습니다.  
- 아래 `머신 유형`은 해당 인스턴스의 스팩을 나타냅니다.  
    역시 적절한 머신 스팩을 선택해줍니다.  
- 컨테이너 아래 선택란을 선택해줍니다.  
    해당 머신이 컨테이너에 적합한 머신으로 세팅됩니다.  
    아래 컨테이너 이미지란에 초기 인스턴스에 배포할 이미지를 설정해줍니다.  
    구글에서 제공하는 busybox 이미지를 기본으로 넣어주겠습니다.  
    `gcr.io/google-containers/busybox` 를 적어 넣습니다.  

    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-vm-2.png" width="500"/>|
    |-|
    |인스턴스 만들기 폼 작성|

<br/>

- 아래 폼 마저 작성 후 `만들기` 버튼을 눌러 인스턴스를 생성합니다.  

    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-vm-3.png" width="500"/>|
    |-|
    |인스턴스 만들기 폼 작성|

<br/>

- 생성된 인스턴스를 확인할 수 있습니다.

    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-instance-view.png" width="900"/>|
    |-|
    |인스턴스 생성|

<br/><br/>

### 인스턴스 컨테이너에 접속하기

#### GCP 웹에서 접속하기

- 연결탭에 SSH 화살표를 누르면 여러 SSH 연결 기능을 살펴볼 수 있습니다.  

    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-instance-ssh.png" width="500"/>|
    |-|
    |인스턴스 SSH 연결|

<br/>  

- `브라우저 창에서 열기` 시 간단하게 SSH 연결을 하여 터미널을 사용할 수 있습니다.  
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-instance-ssh-view.png" width="500"/>|
    |-|
    |인스턴스 SSH 브라우저 연결|
    
<br/><br/>

#### mac 터미널에서 접속하기
- RSA KEY 생성  
    + 터미널에 다음과 같이 입력하여 KEY 생성을 합니다.
    
    `
    $ ssh-keygen -t rsa -f [KEY 위치+이름] -C "[유저 아이디@gmail.com]" 
    `
    
    예시  
    
    ```
    ssh-keygen -t rsa -f ~/.ssh/rsa-gcp-key -C "nzzi.dev@gmai.com" 
    ```
    
    + 비밀번호 추가 옵션은 선택입니다.

<br/>

- 생성된 ~/.ssh/rsa-gcp-key.pub 내용을 GCP 메타데이터에 등록해줍니다.  
        
    + `cat` 으로 rsa-gcp-key.pub 내용을 확인해줍니다.  
    
    ```
    cat ~/.ssh/rsa-gcp-key.pub
    ```
    
    + GCP 왼쪽 메뉴에서 메타데이터 탭으로 들어가줍니다.  
    
        |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-metadata-menu.png" width="250"/>|
        |-|
        |GCP 메타데이터 메뉴|
    
    + `SSH 키` 탭을 선택해주고 `수정` - `항목 추가`를 선택해줍니다.  
        위에서 cat 으로 확인한 공개키(~/.ssh/rsa-gcp-key.pub) 내용을 복사합니다.  
    
        |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-metadata-input.png" width="600"/>|
        |-|
        |GCP SSH 공개키 등록 예시|
    
<br/>

- 터미널에서 ssh 명령어를 통해 접속해줍니다.  

    `-i` 옵션뒤에 위에서 생성한 `private key` 위치를 적습니다. 

    `
    $ ssh -i [KEY 위치+이름] [유저 아이디]@[외부 접속 IP]
    `

    예시  
    
    ```
    ssh -i ~/.ssh/rsa-gcp-key nzzi.dev@34.64.154.29
    ```
    
    + 위에서 비밀번호 추가 옵션을 적으셨다면 추가로 기입해줍니다.

<br/><br/>

#### window putty 에서 접속하기  
윈도우 접속은 [putty 프로그램](https://putty.softonic.kr/)을 사용합니다.  
링크를 통해 putty 를 다운로드 합니다.

- PuTTYgen 을 통해 key 를 생성합니다.  
    + `Generate` 를 누르면 key 가 생성됩니다.  
    
        |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-puttygen-generate.png" width="400"/>|
        |-|
        |PuTTYgen key 생성|

    + Key comment 부분에 구글 아이디를 적습니다.
    + 비밀번호를 추가 하고 싶으시면 Key passphrase 에 비밀번호를 적습니다.
    + `Save private key` 를 눌러 비밀키를 저장합니다.  
    
        |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-puttygen-save.png" width="400"/>|
        |-|
        |PuTTYgen 비밀키 key 저장|
        
    + 위 네모칸에 적힌 공개키 내용을 복사해둡니다.
    
        |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-puttygen-copy.png" width="400"/>|
        |-|
        |PuTTYgen 공개키 key 복사|
<br/>

- 복사한 공개키 내용을 GCP 메타데이터에 등록해줍니다.  
    + GCP 왼쪽 메뉴에서 메타데이터 탭으로 들어가줍니다.  
    
        |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-metadata-menu.png" width="250"/>|
        |-|
        |GCP 메타데이터 메뉴|
    
    + `SSH 키` 탭을 선택해주고 `수정` - `항목 추가`를 선택해줍니다.  
        위에서 복사한 공개키 내용을 붙여넣기합니다.  
    
        |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-metadata-input.png" width="600"/>|
        |-|
        |GCP SSH 공개키 등록 예시|
    
<br/>

- puTTY 프로그램을 통해 접속해줍니다.
    + 우선 왼쪽 탭에서 `SSH` - `Auth` 를 선택하고 `Private ket file for authentication` 부분에 비밀키 파일을 등록해줍니다.
    
        |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-putty-privatekey.png" width="400"/>|
        |-|
        |puTTY 비밀키 등록|  
    
    + 왼쪽 탭 `Session` 으로 돌아와 `Host Name` 에 외부 접속 IP 를 적고 `Open` 을 눌러 접속해줍니다.
    
        |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-putty-open.png" width="400"/>|
        |-|
        |puTTY 외부 접속 IP|  
    
    + 터미널에서 요구하는 로그인정보를 입력해줍니다.
    
        |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-putty-login.png" width="600"/>|
        |-|
        |puTTY 로그인|     

<br/>

#### (참고) 파일 전송
- 로컬에서 원격으로 파일 전송  
    (로컬 터미널에서)   
    `
    $ scp -i [private key 위치] -r [로컬의 원본 경로 및 파일] [유저 아이디]@[원격 외부 IP]:[전송받을 원격지 경로]
    `
    
    예시
    
    ```
    scp -i ~/.ssh/rsa-gcp-key -r ~/Desktop/testdir nzzi.dev@34.64.154.29:~/testscp/
    ```
    
    <br/>
    
    + `-i` : 연결용 rsa key  
    + `-p` : 원본파일 수정/사용시간 및 권한 유지  
    + `-P` : 포트번호 지정  
    + `-r` : 디렉터리 recursive 복사  
        (testdir 디렉터리 전체 -> testscp 디렉터리 안으로 전체 복사)
        
<br/>

- 원격에서 로컬로 파일 전송  
    (로컬 터미널에서)  
    `
    $ gcp -i [private key 위치] -r [유저 아이디]@[원격 외부 IP]:[원격지의 원본 경로 및 파일] [전송받을 로컬 경로]
    `
    
    예시
    
    ```
    scp -i ~/.ssh/rsa-gcp-key -r nzzi.dev@34.64.154.29:~/testscp/ ~/Desktop/testdir 
    ```

<br/><br/>

### 스프링부트 프로젝트 생성
[IntelliJ IDE](https://www.jetbrains.com/ko-kr/idea/) 를 사용하여 스프링부트 프로젝트를 생성해보겠습니다.

<br/>

- `File` 탭 - `New` - `Project...` 를 선택하여 새 프로젝트를 생성합니다.  
    왼쪽 탭에서 `Spring Initializer` 를 선택하고 Java SDK 버전 선택 후 다음을 누릅니다.  

    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-springboot-menu.png" width="700"/>|
    |-|
    |Spring Initializer 프로젝트 생성|  

<br/>

- Metadata 를 적어줍니다.
    + `Group` 에 java 패키지 네이밍을 기입해줍니다. (URL 형식을 반대)  
        group id 는 프로젝트마다 구별할 수 있는 고유한 이름입니다.  
    + `Artifact` 는 jar 파일에서 버전 정보를 뺀 어플리케이션 이름입니다.  
    + `Type` 에서 Gradle Project 를 선택해줍니다. (Maven 사용하시는 분들은 Maven Project 를 선택해줍니다)
    + `Language` 와 사용언어 버전을 선택해줍니다.
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-springboot-metadata.png" width="700"/>|
    |-|
    |Spring Initializer Metadata|  
    
<br/>

- 필요한 Dependency 를 설정해줍니다.  
    기본적인 `Lombok` 과 `Spring Web` 을 선택해주겠습니다.  
    (추가적으로 필요한 Dependency 를 선택하여 구성하셔도 됩니다)  
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-springboot-dependency-1.png" width="700"/>|
    |-|
    |Spring Initializer Lombok Dependency|  
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-springboot-dependency-2.png" width="700"/>|
    |-|
    |Spring Initializer Spring Web Dependency|
    
<br/>

- 프로젝트 저장 경로를 설정해줍니다.  
    `Finish` 를 눌러 프로젝트 생성을 마칩니다.

    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-springboot-location.png" width="700"/>|
    |-|
    |Spring Initializer location|  
    
<br/>

- 프로젝트가 빌드되며 Gradle 설정 창이 뜨게됩니다.  
    Use auto-import 를 선택하여 자동 gradle import 기능을 활성화 시켜줍니다. 
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-springboot-gradle.png" width="700"/>|
    |-|
    |Spring Initializer Gradle 설정|  
    
<br/>

- 기본적인 API 컨트롤러를 생성해줍니다.  

    ```java
    @RestController
    @RequestMapping("/hello")
    public class HelloApi {
        @GetMapping
        public ResponseEntity<?> hello() {
            return ResponseEntity.ok("success hello world!");
        }
    }
    ```
    
    + `@RestController` 어노테이션을 통해 해당 Class 가 Spring 에서 제공되는 Controller Bean 이라는 것을 알립니다.  
        (초기 IoC 컨테이너의 Bean 스캔/등록 과정에서 해당 Class 가 Controller Bean 으로 등록됩니다)
    + `@RequestMapping` 어노테이션을 통해 컨트롤러의 경로를 매핑해줍니다.  
        해당 컨트롤러는 `/hello` 경로로 진입되는 모든 요청을 전달 받게됩니다. 
    + `@GetMapping` 어노테이션은 비즈니스 로직을 처리하는 메서드에 HTTP Method 의 `GET` 요청을 매핑해줍니다.
    + `ResponseEntity` 클래스는 Spring 프레임워크에서 제공하는 클래스 중 `HttpHeader` 와 `HttpBody` 를 포함하는 클래스인 `HttpEntity` 를 상속받아 구현한 클래스입니다.  
        요청에 대한 응답을 전달할 때 `상태코드`, `헤더`, `응답데이터`를 묶어 제공합니다.
        
<br/>

- 스프링 설정 파일인 `application.properties` 을 변경해줍니다.  
    스프링 프로젝트에 연관된 설정 정보(Configuration)를 관리하는 파일입니다.
    
    + .properties 확장자를 .yml(yaml 확장자) 로 변경해줍니다.  
        (선택사항 입니다. 조금 더 설정 정보를 구조적으로 파악할 수 있습니다.)
    
    + 파일 안에 서버의 포트번호를 변경하는 설정 내용을 적어줍니다.  
        (기본 포트번호는 8080 입니다. 변경하지 않으셔도 됩니다.)  
        
        ```yaml
        server:
          port: 8085
        ```

<br/>

- Run 메뉴에서 어플리케이션 실행을 시켜봅니다.

    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-springboot-run.png" width="400"/>|
    |-|
    |Spring 프로젝트 실행|  
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-springboot-run-console.png" width="1000"/>|
    |-|
    |Spring 프로젝트 실행결과|  
    
    + 내장 Tomcat(버전 9.0)이 내부포트 8085 에서 실행되었다는 콘솔 메시지를 확인할 수 있습니다.
        
<br/>

- [Postman 프로그램](https://www.postman.com/downloads/)을 사용하여 API 요청이 잘 반환되는지 확인해볼 수 있습니다.
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-postman.png" width="900"/>|
    |-|
    |Postman 프로그램을 통한 API 요청 확인|  
    
<br/>

- 프로젝트를 실행파일로 build 해줍니다.  
    오른쪽 탭에서 `Gradle` 창을 열어주시고 `Tasks` - `build` - `bootJar` 를 실행해줍니다. 
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-gradle-build.png" width="400"/>|
    |-|
    |Gradle build|  
    
    |<img src="https://github.com/cholnh/delivery-platform-server-guide/blob/main/assets/images/gcp/gce-msa/gcp-gradle-build-output.png" width="300"/>|
    |-|
    |Gradle build output|  
    
<br/>

이렇게 어플리케이션 실행 파일(.jar)을 완성하였습니다.  
이제 어플리케이션 서비스를 인스턴스에 올리는 방법을 알아보겠습니다.
    
<br/><br/>

### Dockerfile

1. 도커파일 생성  
2. 이미지 빌드  
3. 이미지 실행
4. 실행 확인 

### GCP 방화벽 설정

<br/><br/>

### 도커 컨테이너 종료