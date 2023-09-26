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


c:\docker>docker image ls

REPOSITORY                TAG       IMAGE ID       CREATED       SIZE
ubuntu                    latest    c6b84b685f35   5 weeks ago   77.8MB
localhost:5000/myubuntu   latest    c6b84b685f35   5 weeks ago   77.8MB
registry
```
