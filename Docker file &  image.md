## Dockerfile 작성 시 유의사항 ##

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
