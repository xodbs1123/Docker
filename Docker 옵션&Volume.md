## 사용 현황 조회 ##
### 컨테이너 리소스 사용 현황 ###
- c:\docker> docker container run -itd --name myubuntu ubuntu
- c:\docker> docker container run -d -p 80 --name mynginx nginx

- c:\docker> docker container stats

![image](https://github.com/xodbs1123/Docker/assets/61976898/babaf583-0c2e-4dd5-91ba-4a88e1afbe48)

### 디스크 이용 현황 ###

![image](https://github.com/xodbs1123/Docker/assets/61976898/92bb14f5-e996-4f50-abc1-40fa955f2fa5)

- 세부 현황
  
![image](https://github.com/xodbs1123/Docker/assets/61976898/d967cb43-83e4-419d-9fd2-824f9266e497)

## Formatting ##

````
c:\docker>docker container ps
CONTAINER ID   IMAGE     COMMAND                   CREATED          STATUS          PORTS                   NAMES
3052098a5541   nginx     "/docker-entrypoint.…"   8 minutes ago    Up 8 minutes    0.0.0.0:59801->80/tcp   mynginx
e127372948b0   ubuntu    "/bin/bash"               11 minutes ago   Up 11 minutes                           myubuntu
````

```
c:\docker>docker container ls --format "{{.ID}} : {{.Command}}"

3052098a5541 : "/docker-entrypoint.…"
e127372948b0 : "/bin/bash"
```

```
c:\docker> docker container ls --format "table {{.ID}}\t{{.Command}}"

CONTAINER ID   COMMAND
df404e39ffe5   "/docker-entrypoint.…"
d1c08ec35bfa   "/bin/bash"
```

- --no-trunc 옵션을 활용해서 전체 내용 확인
```
c:\docker>docker container ls --no-trunc --format "table {{.ID}}\t{{.Command}}"

CONTAINER ID                                                       COMMAND
3052098a55413129d9d50832203ccaac8ac941d4bc162dff38d44c1902943aa8   "/docker-entrypoint.sh nginx -g 'daemon off;'"
e127372948b08ecfa0c799d040dc0743b372c401e10652319ac2e9fdb711a1a0   "/bin/bash"
```

## 레지스트리 운영 ##
### 레지스트리 실행 ###
```
c:\docker>docker container run -d -p 5000:5000 --name myregistry registry:2

Unable to find image 'registry:2' locally
2: Pulling from library/registry
7264a8db6415: Already exists
c4d48a809fc2: Pull complete
88b450dec42e: Pull complete
121f958bea53: Pull complete
7417fa3c6d92: Pull complete
Digest: sha256:d5f2fb0940fe9371b6b026b9b66ad08d8ab7b0d56b6ee8d5c71cb9b45a374307
Status: Downloaded newer image for registry:2
3563e607c7df97bac76c2accb12c7f1e1855ce5172c6d58475d8afe063f64e57
```

### 도커 허브에서 ubuntu 이미지를 pull ###
```
c:\docker>docker image pull ubuntu

Using default tag: latest
latest: Pulling from library/ubuntu
Digest: sha256:aabed3296a3d45cede1dc866a24476c4d7e093aa806263c27ddaadbdce3c1054
Status: Image is up to date for ubuntu:latest
docker.io/library/ubuntu:latest
```

### 이미지에 레지스트리 정보를 태깅 ###
```
c:\docker>docker image tag ubuntu localhost:5000/myubuntu

c:\docker>docker image ls

REPOSITORY                TAG       IMAGE ID       CREATED       SIZE
ubuntu                    latest    c6b84b685f35   5 weeks ago   77.8MB
localhost:5000/myubuntu   latest    c6b84b685f35   5 weeks ago   77.8MB
registry                  2         0030ba3d620c   6 weeks ago   24.1MB
```

### 로컬 레지스트리로 이미지를 push ###
```
c:\docker>docker image push localhost:5000/myubuntu

Using default tag: latest
The push refers to repository [localhost:5000/myubuntu]
dc0585a4b8b7: Pushed
latest: digest: sha256:1f77fcf82cc387f6e40e7ca28ba98f5c7241d4211481d9e5532e8ea54e0d9ad5 size: 529
```

### 로컬 레지스트리에서 이미지를 pull ###
- 기존 localhost:5000/myubuntu 삭제
- 다시 pull
```
c:\docker>docker image pull localhost:5000/myubuntu

Using default tag: latest
latest: Pulling from myubuntu
Digest: sha256:1f77fcf82cc387f6e40e7ca28ba98f5c7241d4211481d9e5532e8ea54e0d9ad5
Status: Downloaded newer image for localhost:5000/myubuntu:latest
localhost:5000/myubuntu:latest
```

```
c:\docker>docker image ls

REPOSITORY                TAG       IMAGE ID       CREATED       SIZE
ubuntu                    latest    c6b84b685f35   5 weeks ago   77.8MB
localhost:5000/myubuntu   latest    c6b84b685f35   5 weeks ago   77.8MB
registry
```

## 도커에서의 데어터 관리 ##

![image](https://github.com/xodbs1123/Docker/assets/61976898/290426ca-6288-42cd-abdb-510ea2b66e83)

### Volume ###
- Docker 컨테이너에서 생성되고 사용되는 데이터를 유지하기 위한 메커니즘
- Docker에서 완전히 관리
- 호스트의 파일 시스템 내에 특정 영역(리눅스의 경우, /var/lib/docker/volumes/)을 도커가 사용, 관리
- 도커가 아닌 다른 프로세스에서는 해당 영역 접근이 불가능 
- 장점
  - 볼륨은 바인드 마운트보다 백업 또는 마이그레이션이 더 용이
  - Docker CLI 명령 또는 Docker API를 사용하여 볼륨 관리 가능
  - 볼륨은 Linux 및 Windows 컨테이너 모두에서 작동
  - 여러 컨테이너 간에 볼륨을 더욱 안전하게 공유
  - 볼륨 드라이버를 사용하면 원격 호스트 또는 클라우드 공급자에 볼륨을 저장하고, 볼륨 내용을 암호화하거나 다른 기능을 추가하는 것이 가능
  - 새 볼륨에는 컨테이너에 의해 콘텐츠가 미리 채워질 수 있음
  - Docker Desktop의 볼륨은 Mac 및 Windows 호스트의 바인드 마운트보다 성능이 훨씬 높음


### bind mount ##
- 호스트 파일 시스템 자체를 사용
- 호스트와 컨테이너가 설정 파일을 공유하거나 호스트에서 개발하고 컨테이너로 배포하는 방식으로 사용

### tmpfs mount ###
- 호스트의 파일 시스템 대신 메모리에 저장하는 방식
- non-persistent data를 다룰 때는 tmpfs mount가 가장 좋음

### -v 또는 --mount 옵션 ###

![image](https://github.com/xodbs1123/Docker/assets/61976898/d82ec00b-e4a7-4625-98fe-29b35dfceb50)


## 볼륨 예시 ##

### 볼륨 생성 ###
```
c:\docker>docker volume create myvolume
myvolume

c:\docker>docker volume ls
DRIVER    VOLUME NAME
local     myvolume
```

- \\wsl.localhost\docker-desktop-data\data\docker\volumes 에서 확인 가능

![image](https://github.com/xodbs1123/Docker/assets/61976898/746a0618-4bc4-4f82-a5d9-7fd13b88e2f7)

### 생성한 볼륨을 사용하는 컨테이너 실행 ###
```
c:\docker>docker container run -it --name myvolumecontainer -v myvolume:/temp/ ubuntu

root@1b65df9ed843:/#
```

- 파일 만든 후 컨테이너 빠져나옴
```
c:\docker>docker container run -it --name myvolumecontainer -v myvolume:/temp/ ubuntu
root@1b65df9ed843:/# echo hello, volume >> /temp/hello_volume
```

### 동일 볼륨을 사용하는 컨테이너 추가 실행 ###
```
c:\docker>docker container run -it --name ourvolumecontainer -v myvolume:/temp/ ubuntu

root@d8768f87586c:/#

root@d8768f87586c:/# ls /temp/      <= 위에서 생성한 파일이 존재(공유) 함
hello_volume
root@d8768f87586c:/# cat /temp/hello_volume
hello, volume
root@d8768f87586c:/#
```

### volume 세부 정보 확인 (inspect) ###
```
c:\docker>docker inspect --type volume myvolume      
[
    {
        "CreatedAt": "2023-09-26T02:26:15Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/myvolume/_data",
        "Name": "myvolume",
        "Options": null,
        "Scope": "local"
    }
]

c:\docker>docker volume inspect myvolume
[
    {
        "CreatedAt": "2023-09-26T02:26:15Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/myvolume/_data",
        "Name": "myvolume",
        "Options": null,
        "Scope": "local"
    }
]
```

### 도커 컨테이너 실행 시 볼륨 옵션을 주면 자동으로 볼륨 생성 ###
```
C:\docker> docker container run -it --name myvolumecontainer -v newvolume:/root/ ubuntu
root@a8012617075d:/# echo Hello, Volume >> /root/hello_volume
root@a8012617075d:/# exit
exit

C:\docker> docker container run -it --name ourvolumecontainer -v newvolume:/temp/ ubuntu
root@314028d04a4c:/# ls /temp/
hello_volume
root@314028d04a4c:/# cat /temp/hello_volume
Hello, Volume
root@314028d04a4c:/#
```

### Bind Mount 공유 예시 ###
- MySQL 이미지를 이용한 데이터베이스 컨테이너 실행
```
c:\docker>docker container run -d ^

More? --name wordpressdb_hostvolume ^
More? -e MYSQL_ROOT_PASSWORD=password ^
More? -e MYSQL_DATABASE=wordpress ^
More? -v c:\docker\mysql_data:/var/lib/mysql ^
                              ~~~~~~~~~~~~~~ MySQL 데이터를 저장하는 기본 디렉터리      
More? mysql:5.7
```

- 워드프레스 이미지를 이용해서 웹 애플리케이션 컨테이너를 실행
```
C:\docker> docker container run -d -e WORDPRESS_DB_PASSWORD=password --name
wordpress_hostvolume --link wordpressdb_hostvolume:mysql -p 80 wordpress
```

```
C:\docker> docker container ls

CONTAINER ID   IMAGE        COMMAND                   CREATED             STATUS             PORTS                    NAMES
8e0b1759e31f   wordpress    "docker-entrypoint.s…"   54 seconds ago      Up 45 seconds      0.0.0.0:57555->80/tcp    wordpress_hostvolume
ae39f66eba6d   mysql:5.7    "docker-entrypoint.s…"   9 minutes ago       Up 9 minutes       3306/tcp, 33060/tcp      wordpressdb_hostvolume
```

- Bind Mount 확인
```
c:\docker>dir .\mysql_data
 C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: D0A5-3978

 c:\docker\mysql_data 디렉터리

2023-09-26  오전 10:27    <DIR>          .
2023-09-26  오전 10:27    <DIR>          ..
2023-09-26  오전 10:27                56 auto.cnf
2023-09-26  오전 10:27             1,676 ca-key.pem
2023-09-26  오전 10:27             1,112 ca.pem
2023-09-26  오전 10:27             1,112 client-cert.pem
2023-09-26  오전 10:27             1,676 client-key.pem
2023-09-26  오전 10:27        79,691,776 ibdata1
2023-09-26  오전 10:27        12,582,912 ibtmp1
2023-09-26  오전 10:27             1,318 ib_buffer_pool
2023-09-26  오전 10:27        50,331,648 ib_logfile0
2023-09-26  오전 10:27        50,331,648 ib_logfile1
2023-09-26  오전 10:27    <DIR>          mysql
2023-09-26  오전 10:27    <JUNCTION>     mysql.sock [...]
2023-09-26  오전 10:27    <DIR>          performance_schema
2023-09-26  오전 10:27             1,680 private_key.pem
2023-09-26  오전 10:27               452 public_key.pem
2023-09-26  오전 10:27             1,112 server-cert.pem
2023-09-26  오전 10:27             1,676 server-key.pem
2023-09-26  오전 10:27    <DIR>          sys
2023-09-26  오전 10:27    <DIR>          wordpress
              15개 파일         192,949,854 바이트
               6개 디렉터리  65,160,855,552 바이트 남음
```

- 컨테이너 삭제 후 데이터 보존 여부 확인 (데이터 살아 있음)
```
C:\docker> docker container rm -f wordpress_hostvolume wordpressdb_hostvolume

wordpress_hostvolume
wordpressdb_hostvolume
```

- 디렉터리 단위 공유 뿐 아니라 파일 단위의 공유도 가능하며, -v 옵션을 다중으로 사용하는 것도 가능
```
c:\docker>echo hello >> hello

c:\docker>echo hello >> hello2

c:\docker>dir
 C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: D0A5-3978

 c:\docker 디렉터리

2023-09-26  오전 10:43                 8 hello
2023-09-26  오전 10:43                 8 hello2
```

```
c:\docker>docker container run -it --name file_volume -v c:\docker\hello:/hello -v c:\docker\hello2:/hello2 ubuntu
root@060ec98507c4:/#

root@060ec98507c4:/# ls -l hello*
-rwxrwxrwx 1 root root 8 Sep 26 01:43 hello
-rwxrwxrwx 1 root root 8 Sep 26 01:43 hello2

root@060ec98507c4:/# cat hello && cat hello2
hello
hello

root@060ec98507c4:/# echo HELLO >> hello
root@060ec98507c4:/# echo HELLO2 >> hello2
root@060ec98507c4:/# cat hello && cat hello2
hello
HELLO       <= /hello 파일에 새로운 내용이 추가된 것을 확인
hello
HELLO2      <= /hello2 파일에 새로운 내용이 추가된 것을 확인
```

- exit 하면 file_volume 컨테이너는 중지됨
- 컨테이너 내부에서 파일에 추가한 내용이 컨테이너가 중지되어도 그대로 유지됨
```
c:\docker>type hello
hello
HELLO

c:\docker>type hello2
hello
HELLO2
```

## 접근 통제 volume 생성 ##
```
c:\docker>docker container run -itd -v testvolume:/temp/ --name rwcontainer ubuntu
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
445a6a12be2b: Already exists
Digest: sha256:aabed3296a3d45cede1dc866a24476c4d7e093aa806263c27ddaadbdce3c1054
Status: Downloaded newer image for ubuntu:latest
f5d36449f60df239d754218b59884cf3f6087589f9e69a154be117f2b7c31676


c:\docker>docker container run -itd -v testvolume:/temp/:ro --name rocontainer ubuntu
c6b0c4d71e1908941ff552bad75971cceaf847e5741aad2fae16cd913aa4aa1b
```

- rwcontainer에서 /temp/hello.txt 생성 및 읽기 가능
```
c:\docker>docker container exec -it rwcontainer /bin/bash
root@f5d36449f60d:/# echo hello >> /temp/hello.txt
root@f5d36449f60d:/# cat /temp/hello.txt
Hello

```

- rocontainer은 읽기만 가능 (쓰기 불가)
```
c:\docker>docker container exec -it rocontainer /bin/bash
root@c6b0c4d71e19:/# echo hi > /temp/hi.txt
bash: /temp/hi.txt: Read-only file system

root@c6b0c4d71e19:/# ls /temp/
hello.txt

root@c6b0c4d71e19:/# cat /temp/hello.txt
hello
```

## 실습 ##
- 두 개의 컨테이너로 구성된 어플리케이션 개발
- 첫번째 컨테이너는 주기적으로 github에서 컨텐츠(html)을 다운로드하는 쉘 스크립트가 동작
  - 깃 허브 주소는 외부에서 컨테이너를 실행할 환경 변수로 전달 받아서 사용
- 두번째 컨테이너는 nginx 서버가 동작

### c:\docker\sidecar 생성 ###
```
C:\docker> mkdir sidecar

C:\docker> cd sidecar

C:\docker\sidecar>
```

### cloner 파일 작성 ###
- 깃 허브로부터 60초 단위로 컨텐츠를 가져오는 쉘 스크립트
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
while tre
do
  date
  sleep 60
  git pull
done
```

### 첫번째 컨테이너 Dockerfile 및 이미지 생성 ###

### 깃 허브 레포지터리 생성 ###
