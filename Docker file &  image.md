## Dockerfile 작성 시 유의사항 1 ##

- 사용자 입력이 발생하지 않도록 하고, 포그라운드 프로세스로 실행되도록 해야 함

### 우분투 컨테이너에 아파치 웹 서버를 설치하고 hello.html 파일을 /var/www/html 디렉터리에 추가 ###
**- 작업 디렉터리 생성 및 이동**

![image](https://github.com/xodbs1123/Docker/assets/61976898/07a46225-aa12-461d-a9d2-21a428b6d5de)

**- 우분투 컨테이너 실행 및 아파치 설치**

![image](https://github.com/xodbs1123/Docker/assets/61976898/9d8c6994-f4ca-46c5-ab07-e7e126738167)

**- 아파치 실행 ( apachectl start )**

![image](https://github.com/xodbs1123/Docker/assets/61976898/b7e0a0cb-a16d-4d8e-9855-25768db43b69)

  - root@e988801fd6e7:/# ls /var/www/html
  - index.html

**- hello.html 작성 후 컨테이너 /var/www/html 폴더로 이동**
  - ctrl + p + q 로 dettatch 모드 전환
  - vsvode에서 hello.html 파일 생성 후 작성

    ![image](https://github.com/xodbs1123/Docker/assets/61976898/1a71ea62-0807-40e0-96d0-e607cc7ef561)

  - cp 명령어 이용하여 복사

    ![image](https://github.com/xodbs1123/Docker/assets/61976898/69c906cd-5729-4452-88c1-9f61da3381fa)

**- 동작 확인**

![image](https://github.com/xodbs1123/Docker/assets/61976898/d4694994-30b9-4340-bf98-59c2d8e51f46)


## 동일한 기능을 제공하도록 Dockerfile 작성하여 컨테이너 이미지 생성 ##
**- Dockerfile 파일 작성**

![image](https://github.com/xodbs1123/Docker/assets/61976898/ec59f9b4-c3a7-4f2d-9a55-764896e4965a)

**- 이미지 빌드**
  - C:\docker\webserver> docker image build -t myimage:0.0 .
  - Y의 입력을 요구하므로 오류 발생
  
  ![image](https://github.com/xodbs1123/Docker/assets/61976898/f2fd429e-0fd3-4249-baa4-5370ce38fd98)


**- Dockerfile 수정**
- DEBIAN_FRONTEND=noninteractive => 입력을 요구하지 않도록 함
https://www.cyberciti.biz/faq/explain-debian_frontend-apt-get-variable-for-ubuntu-debian/
  
![image](https://github.com/xodbs1123/Docker/assets/61976898/7c461d73-1ec1-42ba-8e7a-4c924c08cc77)

**- 이미지 빌드**

![image](https://github.com/xodbs1123/Docker/assets/61976898/1599c303-ab4c-41ce-9cd5-f2d8257d9480)

**- 컨테이너 실행**

- 실행과 동시에 종료됨

**- Dockerfile 수정**

![image](https://github.com/xodbs1123/Docker/assets/61976898/92025f5a-d91a-4558-af8e-1a440890be6c)

**- 이미지 빌드 및 컨테이너 실행**

![image](https://github.com/xodbs1123/Docker/assets/61976898/6fc5f45f-cef2-4a81-b609-eade760960c9)

![image](https://github.com/xodbs1123/Docker/assets/61976898/9ae00b44-20e0-4c2a-8688-545eefb6bacb)

**- 컨테이너 내부에서 hello2.html 파일을 추가하도록 Dockerfile을 수정**

![image](https://github.com/xodbs1123/Docker/assets/61976898/72fff26b-efe0-4438-abde-d359235fdb99)

**- 이미지 빌드 후 컨테이너 실행**
- PS C:\docker\webserver> docker image build -t myimage:1.0 .
- PS C:\docker\webserver> docker container run -p 8888:80 -it myimage:1.0

![image](https://github.com/xodbs1123/Docker/assets/61976898/782aced6-e00f-4ae1-9344-837c2ac34f82)

## RUN, CMD, ENTRYPOINT에서 명령어를 기술하는 방법 ##
**- JSON 배열 형식은 쉘을 실행하거나 어떤 정류의 확장도 수행하지 않고 직접 해석**
  
- CMD command_string
- CMD [ "/bin/sh", "-c", "command_string" ]
- CMD mkdir /echo => /bin/sh -c mkdir /echo 형식으로 실행 | -c는 mkdir /echo 명령어를 실행 후 쉘 종료하게 하는 명령어
- CMD ["mkdr", "/echo"] => mkdir /echo 형식으로 실행


## COPY, ADD 차이 ##
- copy는 로컬의 파일만 이미지에 추가할 수 있음
- add는 외부 url 및 tar 파일에서도 파일을 추가할 수 있음 (tar 파일의 경우 자동으로 해제해서 추가)
- add 보다는 **copy 사용**을 권장 (의도하지 않은 파일 추가를 방지)

## WOKRDIR ##
- 작업 디렉터리를 지정

![image](https://github.com/xodbs1123/Docker/assets/61976898/496f841f-95be-4c05-9d5b-9f2c2ce2701d)

## EXPOSE ##
- 이미지에서 노출할 포트를 설정

![image](https://github.com/xodbs1123/Docker/assets/61976898/4a13bace-5eac-4105-9bf4-757130d4ea30)

## CMD vs ENTRYPOINT ##
- entrypoint가 설정되지 않았다면 cmd에 설정된 명령어를 그대로 실행하지만, entrypoint가 설정되었다면 cmd는 단지 entrypoint에 대한 인자의 기능을 수행
- 따라서 image의 명령어를 보호하고 싶을 때 entrypoint를 사용
  
### Dockerfile_cmd 파일 생성 ###

![image](https://github.com/xodbs1123/Docker/assets/61976898/83915ff1-3068-4f4c-8035-1aef0449cfd9)

- 이미지 빌드 후 컨테이너 실행

![image](https://github.com/xodbs1123/Docker/assets/61976898/8de5e9d1-fef7-4341-a9ef-ad4de3f32692)

![image](https://github.com/xodbs1123/Docker/assets/61976898/28c2ff74-462c-46a3-8d6b-a48d88735936)

- dockerfile에 기술되어있는 cmd 명령어를 덮어씀

![image](https://github.com/xodbs1123/Docker/assets/61976898/53ccad5d-b671-4100-a785-1d17f2235592)

### Dockerfile_entrypoint 파일 생성 ###

![image](https://github.com/xodbs1123/Docker/assets/61976898/4d2fb3de-97bf-4b20-abfb-d53b8bff365e)


- 이미지 빌드 후 컨테이너 실행

![image](https://github.com/xodbs1123/Docker/assets/61976898/580caae0-6910-4854-b366-e606a9d457fa)

- /bin/sh -c echo hello echo world 형식으로 실행

![image](https://github.com/xodbs1123/Docker/assets/61976898/950bc677-3754-4f17-92e3-63bc04eabe54)


![image](https://github.com/xodbs1123/Docker/assets/61976898/d9e8ab12-a2ec-437a-bc15-5c256cbbfd7e)

## Dockerfile 작성 시 유의사항 2 ##
- 도커 이미지 크기가 커지지 않도록 유의
- 도커 컨테이너 이미지를 빌드하고 배포하는 시간을 단축하기 위해 도커 이미지 크기를 최대한 작게 유지하는 것이 중요
- Dockerfile의 모든 명령어는 레이어를 생성하고, 생성된 레이어는 저장
- 실제 컨테이너에서 사용하지 않는 (못 하는) 레이어도 저장 공간을 차지하게 되므로, 불필요한 레이어가 생성되지 않도록 중요
**- Dockerfile 작성할 때 명령어를 묶어서(라인을 줄이는 것) 실행하는 것이 유익**
**- 빌더 패턴 또는 다단계 도커 빌드 등의 기법을 이용해서 이미지를 작게 만드는 것이 필요**

### 레이어 최소화 ###
- RUN 지시문을 결합해서 최적화 => Dockerfile의 각 줄은 도커 이미지에서 공간을 차지할 새 레이어를 생성하므로 가능한 지시문을 결합해서 적은 레이어를 생성

- C:\docker\webserver\Dockerfile

![image](https://github.com/xodbs1123/Docker/assets/61976898/1f29e3b5-8bca-45e1-a29c-654e29d2d888)

![image](https://github.com/xodbs1123/Docker/assets/61976898/4c93eb17-0c03-46fd-bc36-d266cb5ca1cb)

- 하나의 명렁어로 모든 명령어를 묶어서 실행하도록 Dockerfile 수정

![image](https://github.com/xodbs1123/Docker/assets/61976898/2e135fa9-1881-449a-ba3d-8788abb704f8)

![image](https://github.com/xodbs1123/Docker/assets/61976898/7ae31218-4bc5-4942-8994-1e9a668796c6)

### 불필요한 도구 설치 금지 ###
- 불필요한 개발 및 디버깅 도구(vim, curl, telnet, .. 등)의 종속성을 제거하면 크기가 작은 효율적인 도커 이미지를 생성할 수 있음

- **--no-install-recommends 플래그를 사용**
  - 추천 (recommends) 및 제안(suggested) 패키지가 자동으로 설치되는 것을 막기 위해서는 apt-get install --no-install-recommends 플래그를 사용

[Dockerfile_before-optimization]

![image](https://github.com/xodbs1123/Docker/assets/61976898/624d26ba-7ec3-44b8-8966-e1511be8fff0)

[Dockerfile_after-optimization]

![image](https://github.com/xodbs1123/Docker/assets/61976898/fe7aaded-01cf-46f1-90bc-2216c1e3b51d)

![image](https://github.com/xodbs1123/Docker/assets/61976898/aaac7c9f-dc20-4eac-b843-50c6eff2656b)

- **apt 패키지 매니저의 캐시 삭제**
[Dockerfile_after-optimization-2]

![image](https://github.com/xodbs1123/Docker/assets/61976898/00c5effa-3791-4d2f-8b7f-98ad5574b109)

![image](https://github.com/xodbs1123/Docker/assets/61976898/32cda6bd-5934-4b70-ad09-56aa720397c7)

