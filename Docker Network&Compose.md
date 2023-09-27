## 컨테이너 네트워크 통신 ##
### 네트워크 생성 ###
```
C:\docker>docker network create --driver=bridge network-a
9e1b1db9475ffe358913aca62ba19dbeb100e9f4d3c6544de9334c0749338c25

C:\docker>docker network ls
NETWORK ID     NAME              DRIVER    SCOPE
23c866096dbd   bridge            bridge    local
db15ca27f2df   host              host      local
617c3d299e72   mybridgenetwork   bridge    local
9e1b1db9475f   network-a         bridge    local
00ebb1fdfad5   none              null      local
```

### 컨테이너와 연결 ###
```
C:\docker>docker container run -itd --name cent-networka-a --network network-a centos
26eb41c085fbaa0fd40646ce82d18d743e8d41fb4bca032feb328ad804ec9aac

C:\docker>docker container run -itd --name cent-networka-b --network network-a centos
9c57933b870f8db1045b56380ed771753c302894bbe5e5b8a1a40aa68676a162
```

### network-a 에 두 컨테이너가 들어간 모습 ###
```
C:\docker>docker network inspect network-a

...(생략)...

 "ConfigOnly": false,
        "Containers": {
            "26eb41c085fbaa0fd40646ce82d18d743e8d41fb4bca032feb328ad804ec9aac": {
                "Name": "cent-networka-a",
                "EndpointID": "205d0b6e360a59cedfa3e9bf1d0837d46103a1c893fa34b33b48e9fd1f1ecc8e",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            },
            "9c57933b870f8db1045b56380ed771753c302894bbe5e5b8a1a40aa68676a162": {
                "Name": "cent-networka-b",
                "EndpointID": "9f478b0fde2106a7edfde92c5f1d4ff5fb04471f99ea8f50f3c7f051962f93ee",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
```

### 네트워크를 생성하고 컨테이너를 포함시키면 컨테이너 이름으로도 핑 접근 가능 ###
```
[root@26eb41c085fb /]# ping 172.18.0.3        => cent-networka-b IP 
PING 172.18.0.3 (172.18.0.3) 56(84) bytes of data.
64 bytes from 172.18.0.3: icmp_seq=1 ttl=64 time=0.065 ms
64 bytes from 172.18.0.3: icmp_seq=2 ttl=64 time=0.089 ms
64 bytes from 172.18.0.3: icmp_seq=3 ttl=64 time=0.085 ms


[root@26eb41c085fb /]# ping cent-networka-b    => 네이밍 서비스 제공
PING cent-networka-b (172.18.0.3) 56(84) bytes of data.
64 bytes from cent-networka-b.network-a (172.18.0.3): icmp_seq=1 ttl=64 time=0.046 ms
64 bytes from cent-networka-b.network-a (172.18.0.3): icmp_seq=2 ttl=64 time=0.086 ms
64 bytes from cent-networka-b.network-a (172.18.0.3): icmp_seq=3 ttl=64 time=0.092 ms
```


## 링크 ##
### 기본 네트워크를 사용하는 컨테이너에 --link 옵션을 사용해서 실행 ###
```
C:\docker>docker container run -itd --name cent-default-c --link cent-default-a:cent-a centos
97c2597d0b18dce039a928be972b27c094c4be88e856549ad18bbd285636857d
```

### 이름으로도 핑 접근이 가능 ###
```
[root@97c2597d0b18 /]# ping cent-a
PING cent-a (172.17.0.2) 56(84) bytes of data.
64 bytes from cent-a (172.17.0.2): icmp_seq=1 ttl=64 time=0.085 ms
64 bytes from cent-a (172.17.0.2): icmp_seq=2 ttl=64 time=0.054 ms
64 bytes from cent-a (172.17.0.2): icmp_seq=3 ttl=64 time=0.082 ms
```

### hosts 파일에서 도메인 캐시 기록 확인 ###
```
[root@97c2597d0b18 /]# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.2      cent-a 17765eab7e8d cent-default-a    => 컨테이너 생성시 링크한 컨테이너 IP 정보가 들어가 있음
172.17.0.4      97c2597d0b18
```

## Hostname ##
### 컨테이너 생성 ###
```
C:\docker>docker container run -itd --name cent-default-x --hostname cent-x centos
bc005a294a25ba99ecb2d18bfba43b7146b1d28c6c8460ff613ec3332cc902ed

C:\docker>docker container run -itd --name cent-default-y --hostname cent-y centos
a3555903122b02dd160953c56b1e290004d603412c187ad153974392f1632497
```

### 컨테이너 실행시 머신 이름이 hostname으로 보임 ###
```
C:\docker>docker container exec -it cent-default-x /bin/bash
[root@cent-x /]#
```

```
[root@cent-x /]# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.5      cent-x
```

## Wordpress와 mysql 컨테이너 이미지를 이용한 블로그 서비스 개발 ##
### mysql 이미지를 이용한 데이터베이스 컨테이너 생성 ###
https://hub.docker.com/_/mysql
```
C:\docker>docker container run -d --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7

C:\docker>docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED          STATUS          PORTS                 NAMES
46ba85cc36d4   mysql:5.7   "docker-entrypoint.s…"   17 seconds ago   Up 16 seconds   3306/tcp, 33060/tcp   wordpressdb
```

### Wordpress 이미지를 이용해서 컨테이너 실행 ###
https://hub.docker.com/_/wordpress
```
C:\docker>docker container run -d --link wordpressdb:mysql -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=password --name wordpress -p 80 wordpress
661ded419596f1d7c8c238410c270d385bd624c1add836fb5118babfa2535a7b

C:\docker>docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED          STATUS          PORTS                   NAMES
661ded419596   wordpress   "docker-entrypoint.s…"   5 seconds ago    Up 3 seconds    0.0.0.0:53860->80/tcp   wordpress
46ba85cc36d4   mysql:5.7   "docker-entrypoint.s…"   7 minutes ago    Up 7 minutes    3306/tcp, 33060/tcp     wordpressdb```
```
### 브라우저로 wordpress 컨테이너로 접속 ###

![image](https://github.com/xodbs1123/Docker/assets/61976898/cb469c3d-6d31-4df0-b572-c05c76787c5e)

### wordpressdb 컨테이너로 쉘 접속 ###
```
C:\docker>docker container exec -it wordpressdb /bin/bash    <= 윈도우 프롬프트
bash-4.2# mysql -u root -p                                   <= 배시 쉘 
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 5.7.43 MySQL Community Server (GPL)

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>                                                          <= mysql 쉘
```
### 환경 변수 확인 ###
```
bash-4.2# set								⇐ 환경 변수를 확인
BASH=/bin/bash
BASHOPTS=cmdhist:expand_aliases:extquote:force_fignore:hostcomplete:interactive_comments:progcomp:promptvars:sourcepath
BASH_ALIASES=()
BASH_ARGC=()
BASH_ARGV=()
BASH_CMDS=()
BASH_LINENO=()
BASH_SOURCE=()
BASH_VERSINFO=([0]="4" [1]="2" [2]="46" [3]="2" [4]="release" [5]="x86_64-redhat-linux-gnu")
BASH_VERSION='4.2.46(2)-release'
COLUMNS=98
DIRSTACK=()
EUID=0
GOSU_VERSION=1.16
GROUPS=()
HISTFILE=/root/.bash_history
HISTFILESIZE=500
HISTSIZE=500
HOME=/root
HOSTNAME=622d5be2b9de
HOSTTYPE=x86_64
IFS=$' \t\n'
LINES=16
MACHTYPE=x86_64-redhat-linux-gnu
MAILCHECK=60
MYSQL_DATABASE=wordpress
MYSQL_MAJOR=5.7
MYSQL_ROOT_PASSWORD=p@ssw0rd
MYSQL_SHELL_VERSION=8.0.34-1.el7
MYSQL_VERSION=5.7.43-1.el7
OPTERR=1
OPTIND=1
OSTYPE=linux-gnu
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PIPESTATUS=([0]="0")
PPID=0
PS1='\s-\v\$ '
PS2='> '
PS4='+ '
PWD=/
SHELL=/bin/bash
SHELLOPTS=braceexpand:emacs:hashall:histexpand:history:interactive-comments:monitor
SHLVL=1
TERM=xterm
UID=0
_=-p
```

### mysql 데이터베이스 확인 ###
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| wordpress          |
+--------------------+
5 rows in set (0.00 sec)
```

```
mysql> use wordpress;
Database changed
mysql> show tables;
Empty set (0.00 sec)        => 워드프레스 웹에서 회원가입 및 설치 후 다시 입력

mysql> show tables;
+-----------------------+
| Tables_in_wordpress   |
+-----------------------+
| wp_commentmeta        |
| wp_comments           |
| wp_links              |
| wp_options            |
| wp_postmeta           |
| wp_posts              |
| wp_term_relationships |
| wp_term_taxonomy      |
| wp_termmeta           |
| wp_terms              |
| wp_usermeta           |
| wp_users              |
+-----------------------+
12 rows in set (0.00 sec)
```

```
mysql> select * from wp_users;
+----+------------+------------------------------------+---------------+------------------+------------------------+---------------------+---------------------+-------------+--------------+
| ID | user_login | user_pass                          | user_nicename | user_email       | user_url               | user_registered     | user_activation_key | user_status | display_name |
+----+------------+------------------------------------+---------------+------------------+------------------------+---------------------+---------------------+-------------+--------------+
|  1 | tyunme     | $P$BusS1w.JIEfoHyuO/BPW8zsbqdDe1z1 | tyunme        | ty6593@naver.com | http://localhost:53860 | 2023-09-27 01:04:52 |                     |           0 | tyunme       |
+----+------------+------------------------------------+---------------+------------------+------------------------+---------------------+---------------------+-------------+--------------+
1 row in set (0.00 sec)
```

### wordpressdb 컨테이너 중지시 wordpress 컨테이너 동작을 확인 ###
```
C:\docker>docker container stop wordpressdb
wordpressdb
```

![image](https://github.com/xodbs1123/Docker/assets/61976898/bd005c9a-f402-4005-ad02-5945dd0a263d)

### wordpress 컨테이너 재시작 후 wordpress 컨테이너 동작 확인 ###
```
C:\docker>docker container start wordpressdb
wordpressdb
```
![image](https://github.com/xodbs1123/Docker/assets/61976898/d6d3d87a-abee-4f57-b6ff-7b3526c0aad6)


### wordpressdb 컨테이너를 삭제하고 다시 실행했을 때 wordpress 컨테이너의 동작을 확인 ###
```
C:\docker>docker container rm -f wordpressdb
wordpressdb
```

![image](https://github.com/xodbs1123/Docker/assets/61976898/bd005c9a-f402-4005-ad02-5945dd0a263d)

### wpordpressdb 컨테이너 다시 생성 및 실행 ###
```
C:\docker>docker container run -d --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7
85e62d2e33624d67a0cf590ceb40994a55fcae57867a003cae0849e2f9e8f9f2
```

- mysql 컨테이너가 삭제되면서 컨테이너의 데이터도 함께 삭제됨
![image](https://github.com/xodbs1123/Docker/assets/61976898/a89706b9-8ba9-414e-b080-bfd161544237)

### 컨테이너의 데이터를 영속적(Persistent)인 데이터로 활용하는 방법 ###
**- 컨테이너 데이터를 저장할 디렉터리 생성**
```
C:\docker>mkdir wordpressdb_data
```

**- -v 옵션을 추가해서 wordpressdb 컨테이너 실행**
```
C:\docker>docker container run -d --name wordpressdb -v c:\docker\wordpressdb_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7
787514da9f49ec1e1ccc18029f743239fa393c83278e4335752f581457b058f7
```
**- wordpressdb 컨테이너에서 mysql 서비스를 시작하며 생성한 파일들**
```
C:\docker>dir .\wordpressdb_data
 C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: D0A5-3978

 C:\docker\wordpressdb_data 디렉터리

2023-09-27  오전 10:43    <DIR>          .
2023-09-27  오전 10:43    <DIR>          ..
2023-09-27  오전 10:43                56 auto.cnf
2023-09-27  오전 10:43             1,680 ca-key.pem
2023-09-27  오전 10:43             1,112 ca.pem
2023-09-27  오전 10:43             1,112 client-cert.pem
2023-09-27  오전 10:43             1,680 client-key.pem
2023-09-27  오전 10:43        79,691,776 ibdata1
2023-09-27  오전 10:43        12,582,912 ibtmp1
2023-09-27  오전 10:43             1,318 ib_buffer_pool
2023-09-27  오전 10:43        50,331,648 ib_logfile0
2023-09-27  오전 10:43        50,331,648 ib_logfile1
2023-09-27  오전 10:43    <DIR>          mysql
2023-09-27  오전 10:43    <JUNCTION>     mysql.sock [...]
2023-09-27  오전 10:43    <DIR>          performance_schema
2023-09-27  오전 10:43             1,676 private_key.pem
2023-09-27  오전 10:43               452 public_key.pem
2023-09-27  오전 10:43             1,112 server-cert.pem
2023-09-27  오전 10:43             1,676 server-key.pem
2023-09-27  오전 10:43    <DIR>          sys
2023-09-27  오전 10:43    <DIR>          wordpress
              15개 파일         192,949,858 바이트
               6개 디렉터리  60,716,171,264 바이트 남음
```

### 브라우저로 wordpress 컨테이너로 접속해서 설정을 진행 ###
- data 입력 후 wordpress 설치

### wordpresssdb 컨테이너를 삭제 후 재시작해서 wordpress 서비스 확인 ###
```
C:\docker>docker container rm -f wordpressdb
wordpressdb

C:\docker>docker container run -d --name wordpressdb -v c:\docker\wordpressdb_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7
1c14c4eb4a3bdf187d85bf654dd82e394972ca861af9e87451f16cc86be9ab1a
```

**- 데이터가 유지됨**
![image](https://github.com/xodbs1123/Docker/assets/61976898/ae75156c-e3b0-4343-9e2e-fb4301ead53b)

## Docker Compose ##
https://docs.docker.com/compose/

```
Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application's services. Then, with a single command, you create and start all the services from your configuration.
```
### docker-compose.yaml (dcoker-compose.yml) 파일 구조 ###
- Docker Compose를 사용하여 여러 도커 컨테이너를 정의하고 관리하는 데 사용되는 설정 파일- 
- YAML(YAML Ain't Markup Language) 형식으로 작성
- 여러 서비스, 네트워크, 볼륨 및 환경 변수와 같은 컨테이너 관련 설정을 지정

![image](https://github.com/xodbs1123/Docker/assets/61976898/a83b90fa-bad8-4663-9c61-b17291a03ebe)

- version   => docker-compose 파일의 구문 버전을 정의
- services  => 컨테이너 서비스 정의 ,서비스 이름과 해당 서비스에 대한 설정 정의, 각 서비스는 독립적인 컨테이너로 실행
 - 컨테이너 이미지를 빌드하는 방법
 - 레지스트리에서 도커 이미지를 사용하는 방법
- networks  => 서비스에서 사용할 네트워크에 대한 설명
- volume    => 서비스의 컨테이너에서 마운트될 데이터 볼륨에 대한 설명

![image](https://github.com/xodbs1123/Docker/assets/61976898/8120d360-6c18-48ac-b703-a3d6fc9165c6)

![image](https://github.com/xodbs1123/Docker/assets/61976898/b667f8cd-a4d1-4e7a-b075-57e8ceefee5f)

### mysql wordpress 애플리케이션을 docker-compose.yaml 파일로 정의 ###
**- docker-compose 디렉터리**
```
C:\docker>mkdir docker-compose

C:\docker>cd docker-compose

C:\docker\docker-compose>
```

**- vscode에 docker-compose.yaml 생성후 작성**

![image](https://github.com/xodbs1123/Docker/assets/61976898/5e60d3be-23ec-4838-9764-f909b4262eba)


**- 컨테이너 실행**

```
C:\docker\docker-compose> docker-compose up		⇐ attach 모드로 실행 
							   → 별도의 명령 프롬프트를 실행해서 제어해야 함
```

**- 별도의 명령 프롬프트를 실행해서 컨테이너 실행을 확인**
```
C:\Users\myanj> docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED              STATUS              PORTS                   NAMES
52a9c1206509   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:65101->80/tcp   docker-compose-wordpress-1
ff29b65f4642   mysql:5.7   "docker-entrypoint.s…"   About a minute ago   Up About a minute   3306/tcp, 33060/tcp     docker-compose-wordpressdb-1
9f2d6cafbc09   mysql:5.7   "docker-entrypoint.s…"   About an hour ago    Up About an hour    3306/tcp, 33060/tcp     wordpressdb
a5b34d0fd822   wordpress   "docker-entrypoint.s…"   About an hour ago    Up About an hour    0.0.0.0:64561->80/tcp   wordpress
```
**- docker-compose-down**
```
c:\docker\docker-compose> docker-compose down
[+] Running 3/3
 ✔ Container docker-compose-wordpress-1    Removed                  1.4s
 ✔ Container docker-compose-wordpressdb-1  Removed                  1.9s
 ✔ Network docker-compose_default          Removed                  0.7s

c:\docker\docker-compose> docker container ls 			⇐ docker-compose를 이용해서 실행한 컨테이너가 모두 삭제
CONTAINER ID   IMAGE       COMMAND                   CREATED             STATUS             PORTS                   NAMES
9f2d6cafbc09   mysql:5.7   "docker-entrypoint.s…"   About an hour ago   Up About an hour   3306/tcp, 33060/tcp     wordpressdb
a5b34d0fd822   wordpress   "docker-entrypoint.s…"   2 hours ago         Up 2 hours         0.0.0.0:64561->80/tcp   wordpress
```

**- detach 모드로 컨테이너 실행**
```
C:\docker\docker-compose>docker-compose down
[+] Running 1/1
[+] Running 1/2ocker-compose-wordpress-1    Removed                                                                                               1.5s
[+] Running 1/2ocker-compose-wordpress-1    Removed                                                                                               1.5s
[+] Running 1/2ocker-compose-wordpress-1    Removed                                                                                               1.5s
[+] Running 1/2ocker-compose-wordpress-1    Removed                                                                                               1.5s
[+] Running 1/2ocker-compose-wordpress-1    Removed                                                                                               1.5s
[+] Running 1/2ocker-compose-wordpress-1    Removed                                                                                               1.5s
```
```
C:\docker\docker-compose>docker-compose ls
NAME                STATUS              CONFIG FILES
docker-compose      running(2)          C:\docker\docker-compose\docker-compose.yaml
```

**- docker-compse-wordpress-1 컨테이너의 서비스 포트로 접속**

![image](https://github.com/xodbs1123/Docker/assets/61976898/9579b662-887f-4c6c-bd8a-2ea22d20e603)

**- docker-compose에서 여러 서비스 실행**
```
C:\docker\docker-compose>docker-compose up -d --scale wordpress=3
[+] Running 5/1
 ✔ Network docker-compose_default          Created                                                                                               0.9s
 ✔ Container docker-compose-wordpressdb-1  Created                                                                                               0.0s
```
```
C:\docker\docker-compose>docker container ls

CONTAINER ID   IMAGE       COMMAND                   CREATED              STATUS              PORTS                   NAMES
30e95fc99a6c   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:54607->80/tcp   docker-compose-wordpress-1
9c8383495a41   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:54608->80/tcp   docker-compose-wordpress-3
292585a9dee3   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:54609->80/tcp   docker-compose-wordpress-2
```

**- 한 포트에서 워드프레스 설치하면 다른 포트의 워드프레스는 설치 필요 없음 (volume으로 매핑되어 있기 때문에)**

![image](https://github.com/xodbs1123/Docker/assets/61976898/794a1e7c-f7ee-4e81-b1b9-325a4ddfbe2f)

**- scale 10개로 확장시 기존 컨테이너 이외에 7개가 새로 추가됨** 
```
C:\docker\docker-compose> docker-compose up -d --scale wordpress=10
 ✔ Container docker-compose-wordpressdb-1  Running                                                          0.0s
 ✔ Container docker-compose-wordpress-3    Running                                                          0.0s
 ✔ Container docker-compose-wordpress-2    Running                                                          0.0s
 ✔ Container docker-compose-wordpress-1    Running                                                          0.0s
 ✔ Container docker-compose-wordpress-4    Started                                                          0.1s
 ✔ Container docker-compose-wordpress-10   Started                                                          0.1s
 ✔ Container docker-compose-wordpress-8    Started                                                          0.1s
 ✔ Container docker-compose-wordpress-6    Started                                                          0.1s
 ✔ Container docker-compose-wordpress-7    Started                                                          0.1s
 ✔ Container docker-compose-wordpress-9    Started                                                          0.1s
 ✔ Container docker-compose-wordpress-5    Started                                                          0.1s
```
```

C:\docker\docker-compose> docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED              STATUS              PORTS                   NAMES
ce1ddbc0b611   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:65276->80/tcp   docker-compose-wordpress-10
aa9c14e9af2f   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:65277->80/tcp   docker-compose-wordpress-7
8d7b5eb93264   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:65278->80/tcp   docker-compose-wordpress-5
88921a235734   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:65279->80/tcp   docker-compose-wordpress-9
cfc346c308dd   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:65280->80/tcp   docker-compose-wordpress-8
f83d1a002af8   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:65281->80/tcp   docker-compose-wordpress-6
b2ac823eb867   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:65282->80/tcp   docker-compose-wordpress-4
78d136c04c4a   wordpress   "docker-entrypoint.s…"   7 minutes ago        Up 7 minutes        0.0.0.0:65188->80/tcp   docker-compose-wordpress-3
d8000fbd46bc   wordpress   "docker-entrypoint.s…"   7 minutes ago        Up 7 minutes        0.0.0.0:65189->80/tcp   docker-compose-wordpress-2
e8e765a84010   wordpress   "docker-entrypoint.s…"   7 minutes ago        Up 7 minutes        0.0.0.0:65190->80/tcp   docker-compose-wordpress-1
86f185080fc4   mysql:5.7   "docker-entrypoint.s…"   7 minutes ago        Up 7 minutes        3306/tcp, 33060/tcp     docker-compose-wordpressdb-1
```

**- scale 10개에서 5개로 변경하면 자동으로 컨테이너 갯수가 줄어듦**
```
C:\docker\docker-compose> docker-compose up -d --scale wordpress=5
 ✔ Container docker-compose-wordpressdb-1  Running                                                          0.0s
 ✔ Container docker-compose-wordpress-3    Running                                                          0.0s
 ✔ Container docker-compose-wordpress-2    Running                                                          0.0s
 ✔ Container docker-compose-wordpress-1    Running                                                          0.0s
 ✔ Container docker-compose-wordpress-7    Running                                                          0.0s
 ✔ Container docker-compose-wordpress-5    Running                                                          0.0s
```
