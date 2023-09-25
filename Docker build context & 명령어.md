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
- 빌드된 이미지로 컨테이너를 생성했을 때 호스트와 공유할 컨테이너 내부의 디렉터리를 설정
- 컨테이너에서 VOLUME을 사용하면 컨테이너를 실행할 때 -v 또는 --volume 옵션을 사용하여 호스트의 볼륨을 연결하거나 볼륨 컨테이너를 생성해 연결해줘야 함
- VOLUME 명령을 사용하는 주요 목적
  - 데이터 보존
  - 데이터 공유
  - 데이터 컨테이너와 애플리케이션 코드르 분리

- c:\docker\volume\Dockerfile

![image](https://github.com/xodbs1123/Docker/assets/61976898/a8af6eb8-0b43-4342-b9f3-706231d77ea1)

- 이미지 빌드 & 컨테이너 실행 

![image](https://github.com/xodbs1123/Docker/assets/61976898/4f03770d-9435-4516-a9a8-0d2d286cf260)

- dockerfile 수정 (volume 추가)

![image](https://github.com/xodbs1123/Docker/assets/61976898/178ebaa0-cb2c-4fad-a548-43f6af22dc2b)

- 이미지 빌드 & 컨테이너 실행

![image](https://github.com/xodbs1123/Docker/assets/61976898/5fb0ca79-1aaf-495f-b1a6-98ec6cf3ccbc)


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

- 다시 myvolume:2.0 을 이미지 빌드하면 새로운 이미지로 생성됨 ( ID 바뀜 )
![image](https://github.com/xodbs1123/Docker/assets/61976898/5154a29c-1374-404f-b744-00c82b14361a)
