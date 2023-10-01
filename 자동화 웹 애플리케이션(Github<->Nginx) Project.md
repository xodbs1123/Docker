## 개요 ##
- 두 개의 컨테이너로 구성된 어플리케이션 개발
- 첫번째 컨테이너는 주기적으로 github에서 컨텐츠(html)을 다운로드하는 쉘 스크립트가 동작
- 깃 허브 주소는 외부에서 컨테이너를 실행할 환경 변수로 전달 받아서 사용
- 두번째 컨테이너는 nginx 서버가 동작
- 두 컨테이너를 Compose하여 실행

### github 저장소에 html 파일 작성 ###
![image](https://github.com/xodbs1123/Docker/assets/61976898/f6a9af1c-6c43-48fc-b504-2146464ba59c)

- **index.html**
  
```html
<!DOCTYPE html>
<html>
<head>
    <title>Docker Project</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
        }

        h1 {
            text-align: center;
            color: #333;
        }

        .container {
            max-width: 400px;
            margin: 0 auto;
            text-align: center;
            padding: 20px;
            background-color: #fff;
            border-radius: 5px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }

        button {
            background-color: #007BFF;
            color: #fff;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
        }

        button:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Docker Project</h1>
        <!-- 버튼 추가 -->
        <button onclick="goToDockerPage()">Go to Docker Page</button>
    </div>

    <script>
        // JavaScript 함수를 사용하여 페이지 이동 처리
        function goToDockerPage() {
            window.location.href = "docker.html"; // 이동할 페이지의 URL을 여기에 입력
        }
    </script>
</body>
</html>
```
- **docker.html**
  
```html
<!DOCTYPE html>
<html>
<head><meta charset="UTF-8">
    <title>도커에 대한 상세한 설명</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f5f5f5;
            margin: 0;
            padding: 0;
        }

        h1 {
            background-color: #333;
            color: #fff;
            text-align: center;
            padding: 20px;
        }

        .container {
            background-color: #fff;
            border-radius: 10px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
            margin: 20px;
            padding: 20px;
        }

        h2 {
            color: #333;
        }

        p {
            line-height: 1.5;
        }

        code {
            background-color: #f0f0f0;
            font-family: "Courier New", monospace;
            padding: 2px 5px;
            border-radius: 3px;
        }
    </style>
</head>
<body>
    <h1>도커에 대한 상세한 설명</h1>

    <div class="container">
        <h2>도커란 무엇인가요?</h2>
        <p>도커(Docker)는 컨테이너 기반의 가상화 플랫폼으로, 애플리케이션을 포함한 모든 종속성을 격리된 환경에 패키징하고 실행하는 데 사용됩니다.</p>

        <h2>도커의 주요 특징</h2>
        <ul>
            <li>컨테이너화: 애플리케이션과 종속성을 가볍고 이식 가능한 컨테이너로 패키징합니다.</li>
            <li>이식성: 도커 컨테이너는 어디에서든 실행할 수 있으며 호스트 시스템과 독립적입니다.</li>
            <li>환경 격리: 각 컨테이너는 독립된 환경에서 실행되어 충돌을 방지합니다.</li>
            <li>스케일링: 컨테이너는 쉽게 스케일링되어 애플리케이션의 확장이 간편합니다.</li>
        </ul>

        <h2>도커 명령어 예제</h2>
        <p>도커 명령어를 사용하여 컨테이너를 관리할 수 있습니다. 아래는 몇 가지 명령어 예제입니다:</p>
        <pre><code>docker pull ubuntu:latest
docker run -d --name my-container ubuntu:latest
docker exec -it my-container bash
docker ps
docker stop my-container</code></pre>

        <h2>도커 이미지와 컨테이너 관리</h2>
        <p>도커 이미지와 컨테이너를 관리하려면 다음 명령어를 사용합니다:</p>
        <ul>
            <li><code>docker images</code>: 현재 사용 가능한 이미지 목록을 표시합니다.</li>
            <li><code>docker ps</code>: 실행 중인 컨테이너 목록을 표시합니다.</li>
            <li><code>docker build</code>: 커스텀 이미지를 만들기 위해 Dockerfile을 사용합니다.</li>
            <li><code>docker stop</code>: 컨테이너를 중지합니다.</li>
            <li><code>docker rm</code>: 컨테이너를 삭제합니다.</li>
            <li><code>docker rmi</code>: 이미지를 삭제합니다.</li>
        </ul>
    </div>
</body>
</html>
```

### 디렉토리 생성 ###
```
C:\Users\User>cd \docker

C:\docker>mkdir myproject
```


### 깃 허브의 html 파일을 가져오는 스크립트 작성 ###
```bash
#!/bin/bash

# GITHUB_URL 환경변수가 설정되어 있는지 확인

# 설정되지 않은 경우 에러 종료
if [ -z $GITHUB_URL ] ; then
  exit 1
fi

# 처음 git clone을 통해서 컨텐츠를 가져옮
git clone $GITHUB_URL /data

# 이후 60초 마다 git hub에서 컨텐츠를 가져옮
cd /data
while true
do
  date
  sleep 60
  git pull
done
```

### Docker file 및 이미지 생성 ###
- **dockerfile 작성**
  
```dockerfile
FROM    ubuntu
RUN     apt-get update && apt-get install -y git
RUN     mkdir /myapp
WORKDIR /myapp
COPY    cloner /myapp/
ENV     GITHUB_URL https://github.com/xodbs1123/sidecar.git
RUN     chmod a+x /myapp/cloner
CMD     ["/myapp/cloner"]
```

- **이미지 빌드**
```
C:\docker\myproject>docker image build -t myproject:1.0 .
[+] Building 3.2s (12/12) FINISHED
```

### 첫번째 컨테이너(쉘 스크립트 이미지) 실행 ###
- myvolume이라는 volume을 컨테이너의 /data 디렉토리와 매핑
```
C:\docker\myproject>docker container run -d -v myvolume:/data --name myproject myproject:1.0
2403fcd5ab9c549ea57561058423272d7d00c877b25f14b2a9d13cdb3d0c52c7```
```

- 로그를 통해 정상 작동 확인
```
C:\docker\myproject>docker container logs myproject
Cloning into '/data'...
Sun Oct  1 07:19:14 UTC 2023
Already up to date.
Sun Oct  1 07:20:15 UTC 2023
Already up to date.
Sun Oct  1 07:21:16 UTC 2023
```

### 두번째 컨테이너(nginx) 실행 ###
- 첫번째 컨테이너와 매핑 한 myvolume을 nginx 기본 웹 루트 디렉토리와 매핑
- 호스트 포트와 컨테이너 포트를 동일하게 설정
```
C:\docker\myproject>docker container run -d -v myvolume:/usr/share/nginx/html -p 80:80 --name nginx nginx
Unable to find image 'nginx:latest' locally
```

### 브라우저 접속해서 확인 ###

- 기본 페이지
![image](https://github.com/xodbs1123/Docker/assets/61976898/a6b796d9-b229-45f6-a521-737a2128c4bc)

- 버튼 클릭시 docker.html 파일로 이동
![image](https://github.com/xodbs1123/Docker/assets/61976898/b6b4a687-2a96-4dd9-87c1-6f8f461b31df)

- 코드 수정 후 확인
![image](https://github.com/xodbs1123/Docker/assets/61976898/f4c0eace-de48-4716-b707-7da68d019466)


### Docker Compose 작성 ###
```dockerfile
version: '3.3'
services:
  myproject: 
    image: myproject:1.0
    volumes:
      - myvolume:/data
  
  nginx:
    image: nginx
    ports:
      - 80:80
    volumes:
      - myvolume:/usr/share/nginx/html

volumes:
  myvolume: {}
```

- 컨테이너 실행 및 확인
```
C:\docker\myproject>Docker-compose up -d

C:\docker\myproject>docker container ls
CONTAINER ID   IMAGE           COMMAND                   CREATED         STATUS         PORTS                NAMES
446a08b8c913   nginx           "/docker-entrypoint.…"   8 seconds ago   Up 5 seconds   0.0.0.0:80->80/tcp   myproject-nginx-1
7681f68e3d91   myproject:1.0   "/myapp/cloner"           8 seconds ago   Up 6 seconds                        myproject-myproject-1
```

### 브라우저 정상 작동 확인 ###

- 기본 페이지
![image](https://github.com/xodbs1123/Docker/assets/61976898/a6b796d9-b229-45f6-a521-737a2128c4bc)

- 버튼 클릭시 docker.html 파일로 이동
![image](https://github.com/xodbs1123/Docker/assets/61976898/b6b4a687-2a96-4dd9-87c1-6f8f461b31df)
