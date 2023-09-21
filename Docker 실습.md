## Dockerfile을 이용한 도커 이미지 빌드 ##

![image](https://github.com/xodbs1123/Docker/assets/61976898/ba97dac2-b86e-4cd1-9f28-2ef71fef5670)

## Dockerfile ##
https://docs.docker.com/engine/reference/builder/

- 도커 이미지를 빌드하는데 필요한 모든 명령어를 순서대로 기술한 텍스트 파일

## Docker image build(권장) = docker build ##
- Dockerfile 및 컨텍스트(context)에서 Docker 이미지를 빌드할 때 사용하는 명령
- **Dockerfile** => 컨테이너의 설치해야 하는 패키지, 추가해야 하는 소스코드(ADD, COPY), 실행해야 하는 명령어와 쉘 스크립트(RUN, CMD) 등을 기록한 파일
- **Context** => 지정된 경로 또는 URL에 있는 파일 세트, Build 프로세스는 컨텍스트에 있는 모든 파일을 참조할 수 있음
- Dockerfile을 사용하면 직접 컨테이너를 생성하고 이미지로 커밋해야 하는 번거로움을 덜 수 있을 뿐 아니라 깃과 같은 개발 도구를 통해 애플리케이션의 빌드 및 배포를 자동화할 수 있음

## 작업 디렉터리 생성, main.go 파일 작성 ## 
- 디렉터리 생성

![image](https://github.com/xodbs1123/Docker/assets/61976898/6deb1385-e995-42d6-bac6-d57d08088cff)

- main.go 파일 생성 후 코드 입력
  - 8080 포트로 HTTP 요청을 대기하다, /로 요청이 들어오면 Hello Docker!!!를 응답으로 반환하는 프로그램

![image](https://github.com/xodbs1123/Docker/assets/61976898/42a9c0be-a83a-4c9e-87cd-6223c8a82f9b)


## 개발 환경(PC)에서 main.go 실행 ##
- go 다운로드 설치
https://go.dev/dl/
- 명령 프로프트 재실행 후 작업 디렉토리로(docker) 이동하여 main.go 실행

![image](https://github.com/xodbs1123/Docker/assets/61976898/479c1a7b-76a1-47cb-b328-2291bcb575eb)

- http://localhost:8080/ 으로 접속 

![image](https://github.com/xodbs1123/Docker/assets/61976898/b7d9701c-f794-4a06-b507-096722675d28)

![image](https://github.com/xodbs1123/Docker/assets/61976898/c1beccbb-cf0b-4826-8473-58ade14885c4)

## Dockerfile 생성 및 작성 ##

![image](https://github.com/xodbs1123/Docker/assets/61976898/b21f2507-2df5-44d5-8087-ceb08903b54b)

- **FROM** => 내가 만드는 이미지를 실행하기 위한 베이스 이미지(go를 실행하기 위한)를 가져옴
  - 레지스트리가 명시되어있지 않으면 Docker Hub로부터 공식 이미지를 가져오는 것
  - 동일한 이미지는 **tag(버전을 뜻함, : 뒤 표시)** or **namespace(소유권을 뜻함, 이미지 이름 앞 위치)**으로 구분함
- **RUN** => 컨테이너 내부에서 실행할 명령어를 지정, **golang 컨테이너 안에서 root 밑에 echo 디렉토리를 생성해라**
- **COPY** => 호스트의 main.go 파일을 컨테이너의 /echo/main.go로 복사
- **CMD** => 만든 컨테이너를 실행할 때 실행할 명령어 (go run /echo/main.go)

## Docker 이미지 빌드 및 확인 ##
- C:\docker> docker image build --help

![image](https://github.com/xodbs1123/Docker/assets/61976898/9cf0491e-172a-40a7-9ee6-8bbb4257c9a4)

## dangling image ##
- 이름을 가지지 않는 이미지

![image](https://github.com/xodbs1123/Docker/assets/61976898/56a7defe-cd91-41b9-836b-af9414a6e5a6)

![image](https://github.com/xodbs1123/Docker/assets/61976898/7a8684b1-1740-4b40-8de6-77433878d7f1)

- no-cache 조건으로 build시 동일한 이미지가 생성되면서 기존 이미지 이름은 없어짐

![image](https://github.com/xodbs1123/Docker/assets/61976898/bd187545-4394-488b-a8d2-da00411e5cba)

- 필터 옵션을 이용해서 dangling 이미지 조회

![image](https://github.com/xodbs1123/Docker/assets/61976898/f7d2e103-634f-4004-b145-99d066081384)

- dangling image ID를 이용해서 삭제

![image](https://github.com/xodbs1123/Docker/assets/61976898/a4a50104-7c94-41ce-a856-cc7134ca0716)

## main.go 내용을 변경한 후 도커 빌드 ##

![image](https://github.com/xodbs1123/Docker/assets/61976898/e78c3b7d-3ee5-47ca-9cea-454e8874cddb)

- 변경된 부분만 새롭게 레이아웃을 만듦 = 변경된 부분만 새롭게 빌드

![image](https://github.com/xodbs1123/Docker/assets/61976898/c2779d32-9ed0-41e0-ab08-63a9459e21bd)


## 도커 컨테이너 실행 ##
- C:\docker> docker container run --help => 명령어 옵션 확인
- **docker container run(권장) = docker run** 명령어 사용

![image](https://github.com/xodbs1123/Docker/assets/61976898/c227ce3e-9b23-4540-ad02-9aab2e4635bc)



