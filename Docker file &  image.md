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


