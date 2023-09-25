## 빌드 컨텍스트(build context) ##
- 도커 이미지를 생성하는데 필요한 파일, 소스 코드, 메타 데이터(Dockerfile, Dockerignore) 등을 담고 있는 디렉터리
- Dockerfile이 존재하는 디렉터리
- 빌드 컨텍스트는 docker build 명령어의 마지막에 지정한 위치에 있는 파일 전체를 포함
- 깃 허브와 같은 외부 URL에서 Dockerfile을 읽어 해당 저장소에 있는 파일과 서브 모듈 포함 가능
- 단순 파일뿐 아니라 하위 디렉터리도 전부 포함하게 되므로 빌드에 불필요한 파일이 포함된다면 빌드 속도가 느려지고, 호스트의 메모리를 지나치게 점유하는 문제가 발생
- .dockerignore 파일을 작성하면 명시된 이름의 파일을 빌드할 때 컨텍스트에서 제외
  - 불필요한 파일 --> 빌드 시간을 단축하고 네트워크 대역폭을 절약
  - 기밀성을 요하는 파일
- .dockerignore 파일은 Dockerfile과 동일한 위치에 저장

## Dockerignore 실습 ##
- Dockerfile 작성

![image](https://github.com/xodbs1123/Docker/assets/61976898/f067d8ad-0324-412f-8c84-be8b76fb976d)

- password.txt 작성 (아무 내용)

- c:\docker\tmp 디렉터리 생성

- c:\docker\sample.md 파일 생성 (아무 내용)

- c:\docker\readme.md 파일 생성 (아무 내용)

![image](https://github.com/xodbs1123/Docker/assets/61976898/75540413-5cc0-401a-acc5-1556c56c8c94)

- c:\docker\.dockerignore\ 생성

- readme.md 만 포함하여 이미지 빌드

![image](https://github.com/xodbs1123/Docker/assets/61976898/7fd7cc4c-f2c4-47f2-9e85-53a372471e54)

![image](https://github.com/xodbs1123/Docker/assets/61976898/2ce89202-0e53-45f6-81bc-e4201fc09701)

- 컨테이너 실행 (ignore에 명시된 파일들은 제외, Dockerfile과 readme.md만 실행됨)

![image](https://github.com/xodbs1123/Docker/assets/61976898/8a830b9c-9045-4c24-8fae-34f498c9355d)

## Dockerfile 명령어 ##
### ENV ###
- Dockerfile에서 사용할 환경변수를 지정
- **ENV 환경변수이름=환경변수값** 형식으로 설정
- 설정한 환경변수는 $(환경변수이름) 또는 $환경변수이름 형태로 Dockerfile, 이미지, 이미지로 생성한 컨테이너에서 사용 가능
- docker container run 명령어의 -e 옵션을 이용해서 같은 이름의 환경변수 값을 덮어 쓸 수 있음

### ENV 실습 ###
- c:\docker\env\Dockerfile

![image](https://github.com/xodbs1123/Docker/assets/61976898/21c45976-9065-433a-b379-172b1f953e00)

- env 디렉터리로 이동 후 이미지 빌드

![image](https://github.com/xodbs1123/Docker/assets/61976898/4b7ccf60-b13c-4309-8e79-feb6ca7221f9)

- 컨테이너 실행 (c:\docker\env> docker container run -itd envimage /bin/bash)

![image](https://github.com/xodbs1123/Docker/assets/61976898/81a1cd20-0b3c-4157-b092-08e349021d38)

![image](https://github.com/xodbs1123/Docker/assets/61976898/14874d2c-d4b2-4517-90a9-2415d5b46934)

- -e 명령은 이미지 빌드에는 영향 X, Container 생성되면서 명령이 실행됨
![image](https://github.com/xodbs1123/Docker/assets/61976898/a5ef0bfa-17c2-43dd-b32d-d2ef3881ab2c)

### ENV 실습 2 ###
- C:\docker\env_python\myapp.py

![image](https://github.com/xodbs1123/Docker/assets/61976898/35011303-43ab-47b9-aa96-6bc3518dbfc0)

![image](https://github.com/xodbs1123/Docker/assets/61976898/fb7466c1-fbf8-40ed-a202-eae5190c4d85)

-C:\docker\env_python\Dockerfile

![image](https://github.com/xodbs1123/Docker/assets/61976898/a373bc5d-b336-4d60-9a69-bed7bf928d4f)

- 이미지 빌드

![image](https://github.com/xodbs1123/Docker/assets/61976898/c0e9193f-d73a-4637-a9d2-3c6d4bd52240)

![image](https://github.com/xodbs1123/Docker/assets/61976898/abb2dc18-dd7b-4541-8cea-5cba34a8e916)


## VOLUME ##
- 디렉터리의 내용을 컨테이너에 저장하지 않고 호스트에 저장하도록 설정
- Union File System을 통하지 않고 호스트에 저장하므로, docker commit 명령으로 이미지를 생성해도 이미지에 변경 사항이 포함되지 않음
- 호스트의 특정 디렉터리와는 연결할 수 없으며, 데이터 볼륨을 호스트의 특정 디렉터리와 연결하려면 docker container run 명령어의 -v 옵션을 사용해야 함

## -v 옵션 사용하여 volume 매핑 ##

![image](https://github.com/xodbs1123/Docker/assets/61976898/2c901db7-a2af-4ea4-b886-3eeee97c773b)

![image](https://github.com/xodbs1123/Docker/assets/61976898/4a04d554-895f-4529-a427-8305bfe6f81a)

![image](https://github.com/xodbs1123/Docker/assets/61976898/09c1e2d0-0c26-43b3-b715-8e979ce00f48)

![image](https://github.com/xodbs1123/Docker/assets/61976898/d4e60b42-b3bb-4ee1-ba6b-440710ea7634)


## 컨테이너 관련 명령 ##
- 동일 이미지로 이름이 다른 컨테이너 실행

![image](https://github.com/xodbs1123/Docker/assets/61976898/f67021d7-4e47-4321-94b3-395654e19750)

![image](https://github.com/xodbs1123/Docker/assets/61976898/bc24bb66-27e5-402e-934d-d88f560a04ba)

- **특정 ID를 가진 컨테이너를 중지**
  - docker container stop 컨테이너ID
- **특정 이름을 가진 컨테이너 중지**
  - docker container stop 컨테이너name
- **모든 컨테이너 중지**
  - docker container stop $(docker container ls -aq)
- **실행 중인 모든 컨테이너를 중지**
  - docker container stop $(docker container ls -q)

- **동일 이미지로 생성한 실행중인 컨테이너 검색**

![image](https://github.com/xodbs1123/Docker/assets/61976898/549add5c-61a0-46ed-bec8-1d971e459361)

- **동일 이미지로 생성한 실행중인 컨테이너 삭제**

![image](https://github.com/xodbs1123/Docker/assets/61976898/efffe914-707a-46dd-ad37-3cf4e02321bc)

## 이미지 태깅 ##
- 동일한 이미지명으로 이미지를 빌드하면 이전 이미지의 이름이 사라짐

![image](https://github.com/xodbs1123/Docker/assets/61976898/646ff776-7763-45f1-aced-d6ac6b20578d)

![image](https://github.com/xodbs1123/Docker/assets/61976898/d60038a5-a79e-4180-9b8a-fa67a497de69)

![image](https://github.com/xodbs1123/Docker/assets/61976898/831b6761-ff02-4363-b172-933c708f6fbc)

- image 태그를 이용해서 다른 이름으로 저장

![image](https://github.com/xodbs1123/Docker/assets/61976898/2b6a2415-c27a-462f-891d-0909b0cc74d8)

- dangling 이미지 생성을 방지하기 위해서는 이미지 생성 전에 tag 명령으로 이미지 태그를 변경한 후 이미지를 생성하는 것이 필요

![image](https://github.com/xodbs1123/Docker/assets/61976898/97bf3c73-941f-4490-9636-ea63672986e1)

## Docker Hub에 이미지 올리기 ##

![image](https://github.com/xodbs1123/Docker/assets/61976898/c88db036-048f-4d88-8cdb-387d9ca60fba)

- 네임스페이스에 도커 허브의 계정 태깅

![image](https://github.com/xodbs1123/Docker/assets/61976898/81fc940c-cd21-400e-8679-988763607ad2)

- 도커 로그인

![image](https://github.com/xodbs1123/Docker/assets/61976898/d8c6f081-4e54-41a8-af9b-e74c1f1ef6fb)

- image push

![image](https://github.com/xodbs1123/Docker/assets/61976898/4b3b2b8b-1871-490d-80a2-1b12145dd301)

![image](https://github.com/xodbs1123/Docker/assets/61976898/2c8425b8-0eb7-4d23-97d3-12074336902a)

- docker container run -it tyunme/myvolume:old /bin/bash

![image](https://github.com/xodbs1123/Docker/assets/61976898/ec806787-713f-4607-b522-712881dec284)

## 호스트와 컨테이너 간 파일 복사 ##

![image](https://github.com/xodbs1123/Docker/assets/61976898/36ee2982-4903-447d-a76b-b359ac900e8a)

![image](https://github.com/xodbs1123/Docker/assets/61976898/a4a58efb-2350-4da9-a3a6-c097d6eda115)

![image](https://github.com/xodbs1123/Docker/assets/61976898/93669b36-4804-4938-9ab3-6a9f0de77a13)

- 호스트의 디렉터리를 컨테이너 내부로 복사
  
![image](https://github.com/xodbs1123/Docker/assets/61976898/15d3cd61-f0ff-470c-aaa4-818d9bb419b9)

- 컨테이너 내부의 파일을 호스트로 복사

![image](https://github.com/xodbs1123/Docker/assets/61976898/61176579-57f8-42de-a81d-1cbd606efdb1)


- 컨테이너 내부에 있는 data 디렉터리 아래의 내용을 호스트의 temp 디렉터리 아래로 복사

![image](https://github.com/xodbs1123/Docker/assets/61976898/4c9ec1f9-969a-455d-a3ce-6d20f0b5c889)

## 실습 ##
- **nginx 이미지를 이용해서 본인만의 웹 서버 이미지를 작성하고, 작성한 이미지를 도커 허브에 등록**
- 생성할 이미지 이름은 mywebserver로 설정
- 컨테이너 실행 후 http://localhost:HOST_PORT/로 접속했을 때 아래와 같은 결과(화면)가 출력되어야 함

![image](https://github.com/xodbs1123/Docker/assets/61976898/752cba6c-b41c-40b7-a258-37db897c05ed)

- nginx의 기본 웹 루트 디렉터리는 /usr/share/nginx/html 이며, 기본 페이지는 index.html

### 방법 1. Container commit 명령으로 이미지를 작성 ###
- nginx 이미지를 이용해서 컨테이너를 실행

![image](https://github.com/xodbs1123/Docker/assets/61976898/5a1bc492-6840-474c-a31f-d03829def6e2)

- nginx 기본 웹 루트 디렉터리 아래의 기본 웹 페이지(index.html)가 응답으로 제공되는 것을 확인

![image](https://github.com/xodbs1123/Docker/assets/61976898/9a6d373d-824f-406b-a3f2-ab572f97a90d)

- index.html 생성

- index.html 파일을 컨테이너 실행한 컨테이너 내부의 nginx 기본 웹 루트 디렉터리로 복사

- 브라우저 확인 (정상 결과 출력)

- 컨테이너의 현재 상태를 이미지로 저장

![image](https://github.com/xodbs1123/Docker/assets/61976898/9265cb34-38ad-41bd-ad0e-b87b6ac82b9a)

- 생성한 이미지를 도커 허브에 등록 (c:\docker\mywebserver> docker image push tyunme/mywebserver:1.0)

### 방법 2. Dockerfile을 작성해서 이미지를 빌드 ###
- nginx 이미지의 dockerfile을 확인
https://github.com/nginxinc/docker-nginx/blob/321a13a966eeff945196ddd31a629dad2aa85eda/mainline/debian/Dockerfile

![image](https://github.com/xodbs1123/Docker/assets/61976898/9d33199b-3948-47fa-b590-7376ab308a79)

- Dockerfile 작성

![image](https://github.com/xodbs1123/Docker/assets/61976898/ee94da8d-5fc1-45dd-83d0-60dc2476a96b)

- 이미지 빌드

![image](https://github.com/xodbs1123/Docker/assets/61976898/0bc77ba6-313b-4bf1-95ee-384dfca35b13)

- 컨테이너 실행(docker container run -d -p 80 tyunme/mywebserver:2.0)

- 브라우저로 확인

- 이미지를 도커 허브에 등록

### 방법 3(보류). 볼륨 맵핑을 통해 index.html 파일을 쉽게 편집하도록 구성 ###
- nginx 이미지를 이용해서 컨테이너를 실행(호스트와 볼륨 맵핑을 통해서 웹 루트 디렉터리를 공유)

![image](https://github.com/xodbs1123/Docker/assets/61976898/48bd8d8f-1b01-43fe-88bd-8f93afc0834b)

- c:\docker\mywebserver\html\index.html 파일을 생성

- 브라우저 확인 후 이미지로 저장
- c:\docker\mywebserver> docker container commit mywebserver tyunme/mywebserver:3.0
- 생성한 이미지를 도커 허브에 등록

## 실습 ##
- 구구단을 출력하는 파이썬 프로그램을 작성하고, 해당 프로그램을 실행하는 도커 컨테이너 이미지 생성 후 본인의 도커 허브에 등록
- 컨테이너 이미지 이름은 mygugudan:1.0
- 컨테이너 실행할 때 환경변수의 값으로 출력할 단을 지정하고, 환경변수를 부여하지 않으면 2 ~ 9단까지 모두 출력하도록 작성
- ex) docker container run -e DAN=2 tyunme/mygugudan:1.0

### 방법 ###
- 작업 디렉토리 작성 (mygugudan)
- 파이썬 프로그램 작성

![image](https://github.com/xodbs1123/Docker/assets/61976898/19be2737-d576-4cf2-8b89-720940d66d8f)

- 도커 파일 작성

![image](https://github.com/xodbs1123/Docker/assets/61976898/d9a15e51-2ec5-4587-8bde-2ab6df2b3670)

- c:\docker\mygugudan> docker container run myanjini/mygugudan:1.0

- c:\docker\mygugudan> docker container run -e DAN=3 myanjini/mygugudan:1.0
