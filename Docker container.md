o## Dockerfile을 이용한 도커 이미지 빌드 ##

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

- C:\docker> docker container run -p 80:8080 example/echo:latest
- 브라우저를 통해서 localhost:80 으로 요청해서 응답 받는 것은 가능하나, 명령 입력은 전달되지 않음(attach, 포그라운드 상태이기 때문)
  
![image](https://github.com/xodbs1123/Docker/assets/61976898/d078e835-f612-4b24-b9bf-dab461baf818)

- 새로운 터미널을 열어서 컨테이너 실행을 확인

![image](https://github.com/xodbs1123/Docker/assets/61976898/d3ee0989-afc9-4807-a1ea-461aa7cf790a)

- 실행 중인 컨테이너를 중지 ( 중지할 컨테이너 이름 또는 컨테이너 ID ( ID의 경우 식별 가능한 부분만 입력해도 중지 가능 )

![image](https://github.com/xodbs1123/Docker/assets/61976898/5cb5516e-242d-4ca8-8d9b-b79dff83107d)

- C:\docker> docker container run -it -p 80:8080 example/echo:latest
- 명령 입력이 가능해지므로 ctrl + c 명령(프로세스 종료)도 가능해짐

![image](https://github.com/xodbs1123/Docker/assets/61976898/ec8b7885-7647-4851-9fbc-98edf244b85d)

- 모든 상태의 컨테이너를 조회 ( docker container ls -a )

![image](https://github.com/xodbs1123/Docker/assets/61976898/320502a2-1883-45ac-84b5-a05b37a3cbb0)

- detach 모드로 전환

![image](https://github.com/xodbs1123/Docker/assets/61976898/ad27b2e6-01dd-4caa-b5f4-e21176d08e8a)

- 포트가 사용중이면 중복 오류 발생

![image](https://github.com/xodbs1123/Docker/assets/61976898/e5432556-c913-4155-a6fa-dba3935b33f5)

- 포트 변경 후 detach 모드로 실행 ( 제어권을 호스트가 가져감 )

![image](https://github.com/xodbs1123/Docker/assets/61976898/5f3243aa-3aad-4d73-98dd-62189c460a4c)

![image](https://github.com/xodbs1123/Docker/assets/61976898/c84c6d47-c30e-43d4-bd0c-5cf52c1ae2d2)

- 컨테이너 내용을 자르지 않고 출력 ( docker container ls --no-trunc )

![image](https://github.com/xodbs1123/Docker/assets/61976898/df9b6e91-fb96-4c85-885d-482e3850cf3a)

- 다양한 옵션 추가하여 컨테이너 생성
  
![image](https://github.com/xodbs1123/Docker/assets/61976898/dd8f0ddd-f9f1-43a1-b7b7-5d8ccbb83dcf)

![image](https://github.com/xodbs1123/Docker/assets/61976898/1cfde6e9-8c25-4af7-a1b7-680765aea261)

![image](https://github.com/xodbs1123/Docker/assets/61976898/9eebead7-45f6-43c3-8454-ff1578e7582e)

- --rm 옵션이 있는 컨테이너 중지

![image](https://github.com/xodbs1123/Docker/assets/61976898/7ea86d54-a5f8-4e4b-af7d-ad051687520c)

- 컨테이너가 사라져도 볼륨 매핑 되어있던 호스트에는 데이터가 살아있음

## 임의 포트로 컨테이너 실행 ##
- 동일한 이미지로 여러 컨테이너를 생성할 경우, 호스트 포트를 다르게 설정해야 함
- 호스트 포트를 지정하지 않고 컨테이너 실행 => 임의의 포트 할당

![image](https://github.com/xodbs1123/Docker/assets/61976898/835700b9-fb0f-4da3-8426-f004733e739b)

## 컨테이너 삭제 ##
- C:\docker> docker container rm ID1, ID2 ...
  
![image](https://github.com/xodbs1123/Docker/assets/61976898/6ea19895-a9b5-48eb-b42a-671ee31b4d91)

- 실행중인 컨테이너는 삭제할 수 없음 (강제 삭제 or 중지 후 삭제)
  - docker container **rm -f** ID ...

- 모든 상태의 컨테이너 ID 조회 (-q는 실행중인 컨테이너만)

![image](https://github.com/xodbs1123/Docker/assets/61976898/6335298a-5be9-4f97-8120-db44ab908ab6)

- 여러 컨테이너 한번에 삭제 => 윈도우인 경우 파워셀에서 스크립트 실행

![image](https://github.com/xodbs1123/Docker/assets/61976898/d95d6a8a-b236-40ae-82c7-5f20daa300d9)

## -i -t -d 옵션 활용: ubuntu 이미지를 이용해서 컨테이너 실행 ##

- bash shell 이미지는 입출력이 있어야 실행, 아니면 바로 종료
  
![image](https://github.com/xodbs1123/Docker/assets/61976898/e20436bb-8f47-4f58-bf28-a4379a017498)

![image](https://github.com/xodbs1123/Docker/assets/61976898/fb369c4a-a556-4ea8-8439-3d4f07c92ec7)

- -i -t -d 옵션 부여하면 실행 유지
  
![image](https://github.com/xodbs1123/Docker/assets/61976898/10728764-51c8-4057-bc48-f9ca9bc2fb19)

- -it 옵션만 부여하면 attach로 실행돼서 ubuntu 실행됨

![image](https://github.com/xodbs1123/Docker/assets/61976898/038df980-b2bc-483a-b783-c733a505b140)

- attach 모드에서 dettach 모드로 전환

![image](https://github.com/xodbs1123/Docker/assets/61976898/6646aaa9-5e74-4322-9230-ba4aa9f2e3bd)

### example/echo:latest 이미지를 이용해서 아래 옵션을 조합해서 실행하고 각각의 동작 차이를 확인 ###
  - -d
  - -it -> ctrl + c
  - -it -> ctrl + p + q
  - itd 옵션 모두 설정하지 않았을 때

## 실행 컨테이너에 표준 입출력 연결 ##
### 쉘이 없는 컨테이너 ###

![image](https://github.com/xodbs1123/Docker/assets/61976898/6c3f28e2-1168-4938-996d-917d14a3ffea)

- docker container exec -it 컨테이너ID /bin/bash 로 shell 실행시켜주면 됨
  
![image](https://github.com/xodbs1123/Docker/assets/61976898/2624b362-5e7f-4612-93db-62963435e305)

- exit해도 실행시킨 shell 만 종료

### 쉘이 있는 컨테이너 ⇒ attach 또는 exec ... /bin/bash ###
![image](https://github.com/xodbs1123/Docker/assets/61976898/5145b044-7954-4350-b7d8-9b8d1b0c7d36)

![image](https://github.com/xodbs1123/Docker/assets/61976898/9fe5e7a4-9623-46ae-b839-d9fbb774ac9b)


## 모든 컨테이너와 이미지를 삭제(Windows의 경우 Powershell에서 작업) ##

![image](https://github.com/xodbs1123/Docker/assets/61976898/91775d59-1237-4872-a65f-a43b23375719)

![image](https://github.com/xodbs1123/Docker/assets/61976898/d866ddc2-9480-404a-84f4-1c7971595ef8)

