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
