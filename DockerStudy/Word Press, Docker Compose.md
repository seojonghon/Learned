# Word Press, Docker Compose

# **wordpress와 mysql 컨테이너 이미지를 이용한 블로그 서비스를 개발**

## **mysql 이미지를 이용한 데이터베이스 컨테이너를 실행**

https://hub.docker.com/_/mysql

```powershell
C:\docker> docker container run -d --name wordpressdb -e MYSQL_ROOT_PASSWORD=p@ssw0rd -e MYSQL_DATABASE=wordpress mysql:5.7
622d5be2b9de96f7c6adb2083980f820e7265561724e5f3cbfb960fdc93f0744

C:\docker> docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED          STATUS          PORTS                 NAMES
622d5be2b9de   mysql:5.7   "docker-entrypoint.s…"   6 seconds ago    Up 5 seconds    3306/tcp, 33060/tcp   wordpressdb
```

**wordpress 이미지를 이용해서 컨테이너를 실행**

```powershell

https://hub.docker.com/_/wordpress

C:\docker> docker container run -d --link wordpressdb:mysql -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=pass --name wordpress -p 80 wordpress
8a8d28c106659b123aff32450ca46afc52fefe97e13bac199798d77d68c0743e

C:\docker> docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED          STATUS          PORTS                   NAMES
8a8d28c10665   wordpress   "docker-entrypoint.s…"   31 seconds ago   Up 30 seconds   0.0.0.0:64286->80/tcp   wordpress
622d5be2b9de   mysql:5.7   "docker-entrypoint.s…"   5 minutes ago    Up 5 minutes    3306/tcp, 33060/tcp     wordpressdb
```

## **브라우저로 wordpress 컨테이너로 접속**

https://lh3.googleusercontent.com/DCkTjo9_fKY7_T8ygLOPjrFRy4KC6IR-3_FEekpXN_aLweLIMxalrxEb1IOmaqjLzmvCz3ZAfH7niPZtU3ExkqYpKEdjR3CoNsmUdCzjQQy4w_SalDhhqwf5tMZVwXtJ2WFZ3DaQ57HHwN1iEPo2jds

## **wordpressdb 컨테이너로 쉘 접속**

```powershell
C:\docker> docker container exec -it wordpressdb /bin/bash	⇐ 윈도우 프롬프트
bash-4.2#								⇐ 배시 쉘

bash-4.2# set								⇐ 환경 변수를 확인
BASH=/bin/bash
.
.
.
MYSQL_DATABASE=wordpress
MYSQL_MAJOR=5.7
MYSQL_ROOT_PASSWORD=passw
```

```powershell
bash-4.2# mysql -u root -p
Enter password: pass
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 66
Server version: 5.7.43 MySQL Community Server (GPL)
```

```powershell
mysql>									⇐ mysql 쉘

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

```powershell
mysql> use wordpress;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```

```powershell
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

```powershell
mysql>
mysql> select * from wp_users;
+----+------------+------------------------------------+---------------+--------------------+------------------------+---------------------+---------------------+-------------+--------------+
| ID | user_login | user_pass                          | user_nicename | user_email         | user_url               | user_registered     | user_activation_key | user_status | display_name |
+----+------------+------------------------------------+---------------+--------------------+------------------------+---------------------+---------------------+-------------+--------------+
|  1 | admin      | $P$Bl54MjHOeOnlY7E19D0iZB9QD3GBIm. | admin         | seojonghun@gmail.com | http://localhost:64286 | 2023-09-27 00:56:23 |                     |           0 | admin        |
+----+------------+------------------------------------+---------------+--------------------+------------------------+---------------------+---------------------+-------------+--------------+
1 row in set (0.00 sec)

mysql> exit
Bye

bash-4.2# exit
exit
```

![Alt text](image-9.png)

## **wordpressdb 컨테이너를 중지했을 때 wordpress 컨테이너 동작을 확인**

```powershell
C:\docker> docker container stop wordpressdb
wordpressdb
```

```powershell
C:\docker> docker container ls -a
CONTAINER ID   IMAGE       COMMAND                   CREATED             STATUS                      PORTS                   NAMES
8a8d28c10665   wordpress   "docker-entrypoint.s…"   36 minutes ago      Up 36 minutes               0.0.0.0:64286->80/tcp   wordpress
622d5be2b9de   mysql:5.7   "docker-entrypoint.s…"   41 minutes ago      Exited (0) 33 seconds ago                           wordpressdb
```

## **wordpressdb 컨테이너를 재시작 후 wordpress 컨테이너 동작을 확인**

```powershell
C:\docker> docker container start wordpressdb
wordpressdb
```

```powershell
C:\docker> docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED          STATUS          PORTS                   NAMES
8a8d28c10665   wordpress   "docker-entrypoint.s…"   38 minutes ago   Up 38 minutes   0.0.0.0:64286->80/tcp   wordpress
622d5be2b9de   mysql:5.7   "docker-entrypoint.s…"   43 minutes ago   Up 18 seconds   3306/tcp, 33060/tcp     wordpressdb
```

## **wordpressdb 컨테이너를 삭제하고 다시 실행했을 때 wordpress 컨테이너의 동작을 확인**

![Alt text](image-8.png)

```powershell
C:\docker> docker container rm -f wordpressdb
wordpressdb
```

```powershell
C:\docker> docker container ls -a
CONTAINER ID   IMAGE       COMMAND                   CREATED          STATUS          PORTS                   NAMES
8a8d28c10665   wordpress   "docker-entrypoint.s…"   39 minutes ago   Up 39 minutes   0.0.0.0:64286->80/tcp   wordpresswordpressdb
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1b8cc88d-9972-478f-9c35-51d6c2e63848/2d5a2638-2eb9-442f-b0ec-597a130b2d0a/Untitled.png)

## **wordpressdb 컨테이너를 다시 실행**

```powershell
C:\docker> docker container run -d --name wordpressdb -e MYSQL_ROOT_PASSWORD=pass -e MYSQL_DATABASE=wordpress mysql:5.7

4029992b8e696f1380703c1df5d5a8c6d179fd5a53affbf6c0d1e148c713ef54

C:\docker> docker container ls

CONTAINER ID   IMAGE       COMMAND                   CREATED          STATUS          PORTS                   NAMES

4029992b8e69   mysql:5.7   "docker-entrypoint.s…"   6 seconds ago    Up 5 seconds    3306/tcp, 33060/tcp     wordpressdb

8a8d28c10665   wordpress   "docker-entrypoint.s…"   41 minutes ago   Up 41 minutes   0.0.0.0:64286->80/tcp   wordpress
```

정상적으로 실행

![Alt text](image-7.png)

## **컨테이너의 데이터를 영속적(persistent)인 데이터로 활용하는 방법**

### **wordpressdb 컨테이너를 삭제**

```powershell
C:\docker> docker container rm -f wordpressdb
wordpressdb
```

```powershell
C:\docker> docker container ls -a
CONTAINER ID   IMAGE       COMMAND                   CREATED         STATUS         PORTS                   NAMES
a5b34d0fd822   wordpress   "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   0.0.0.0:64561->80/tcp   wordpress
```

### **컨테이너의 데이터를 저장할 디렉터리를 생성**

```powershell
C:\docker> mkdir wordpressdb_data
```

### **v 옵션을 추가해서 wordpressdb 컨테이너를 실행**

```powershell
C:\docker> docker container run -d --name wordpressdb **-v c:\docker\wordpressdb_data:/var/lib/mysql** 
-e MYSQL_ROOT_PASSWORD=p@ssw0rd -e MYSQL_DATABASE=wordpress mysql:5.7
1c3d21e1d84165c819fe0ee7a6d5dc8fe99a4ec869fd7050503db92c2048777c

-v c:\docker\wordpressdb_data:/var/lib/mysql
   ~~~~~~~~~~~~~~~~~~~~~~~~~~ ~~~~~~~~~~~~~~
   호스트 디렉터리            컨테이너 내부 디렉터리 ⇒ mysql 설정 파일이 저장되는 곳
⇒ 호스트와 컨테어니는 각 디렉터리를 통해서 파일을 공유하는 것이 가능
```

```powershell
C:\docker> dir .\wordpressdb_data
 C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: D0A5-3978
C:\docker\wordpressdb_data 디렉터리

2023-09-27  오전 10:43    <DIR>          .
2023-09-27  오전 10:43    <DIR>          ..
2023-09-27  오전 10:43                56 auto.cnf			⇐ wordpressdb 컨테이너에서 
2023-09-27  오전 10:43             1,680 ca-key.pem		   mysql 서비스를 시작하면서 생성한 파일
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
               6개 디렉터리  66,869,510,144 바이트 남음
```

**브라우저로 wordpress 컨테이너로 접속해서 설정을 진행**

```powershell

C:\docker> docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED         STATUS         PORTS                   NAMES
1c3d21e1d841   mysql:5.7   "docker-entrypoint.s…"   3 minutes ago   Up 3 minutes   3306/tcp, 33060/tcp     wordpressdb
a5b34d0fd822   wordpress   "docker-entrypoint.s…"   8 minutes ago   Up 8 minutes   0.0.0.0:64561->80/tcp   wordpress
```

### **wordpressdb 컨테이너를 삭제한 후 재시작해서 wordpress 서비스를 확인**

```powershell
C:\docker> docker container rm -f wordpressdb
wordpressdb
```

```powershell
C:\docker> docker container run -d --name wordpressdb -v c:\docker\wordpressdb_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=p@ssw0rd -e MYSQL_DATABASE=wordpress mysql:5.7
9f2d6cafbc09286d54da441d60723fa7182028b844748d998a807fb370055a5e

C:\docker> docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED          STATUS          PORTS                   NAMES
9f2d6cafbc09   mysql:5.7   "docker-entrypoint.s…"   19 seconds ago   Up 18 seconds   3306/tcp, 33060/tcp     wordpressdb
a5b34d0fd822   wordpress   "docker-entrypoint.s…"   10 minutes ago   Up 10 minutes   0.0.0.0:64561->80/tcp   wordpress설정 정보(상태)를 유지하고 있는 것을 확인할 수 있음
```

****설정 정보(상태)를 유지하고 있는 것을 확인할 수 있음****

![Alt text](image-6.png)

# **Docker Compose**

https://docs.docker.com/compose/

Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application's services. Then, with a single command, you create and start all the services from your configuration.

## **docker-compose.yaml (docker-compose.yml) 파일 구조**

- Docker Compose를 사용하여 여러 도커 컨테이너를 정의하고 관리하는 데 사용되는 설정 파일
- YAML(YAML Ain't Markup Language) 형식으로 작성
- 여러 서비스, 네트워크, 볼륨 및 환경 변수와 같은 컨테이너 관련 설정을 지정

![Alt text](image-5.png)

version 	Docker Compose 파일 버전 (현재는 3이 가장 일반적으로 사용)

services 	컨테이너 서비스를 정의

서비스 이름과 해당 서비스에 대한 설정을 정의

각 서비스는 독립적인 컨테이너로 실행

networks 	서비스에서 사용할 네트워크를 설정

volumes	서비스에서 사용할 볼륨을 설정
![Alt text](image-4.png)
![Alt text](image-3.png)

## **wordpress 애플리케이션을 docker-compose.yaml 파일로 정의**

```powershell
C:\docker> mkdir docker-compose
C:\docker> cd docker-compose
C:\docker\docker-compose>
```

---

yaml 파일

```powershell
version: "3.3"
services:
  wordpressdb:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=pass
      - MYSQL_DATABASE=wordpress
    volumes:
      - wordpressdb_data:/var/lib/mysql
  wordpress:
    depends_on:
      - wordpressdb
    image: wordpress
    ports:
      - 80
    environment:
      WORDPRESS_DB_HOST: wordpressdb:3306
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: pass
volumes:
  wordpressdb_data: {}
```

## **컨테이너 실행**

```powershell
C:\docker\docker-compose> docker-compose up		⇐ attach 모드로 실행
													→ 별도의 명령 프롬프트를 실행해서 제어해야 함
```

****별도의 명령 프롬프트를 실행해서 컨테이너 실행을 확인****

```powershell
C:\Users\User> docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED              STATUS              PORTS                   NAMES
52a9c1206509   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:65101->80/tcp   docker-compose-wordpress-1
ff29b65f4642   mysql:5.7   "docker-entrypoint.s…"   About a minute ago   Up About a minute   3306/tcp, 33060/tcp     docker-compose-wordpressdb-1
9f2d6cafbc09   mysql:5.7   "docker-entrypoint.s…"   About an hour ago    Up About an hour    3306/tcp, 33060/tcp     wordpressdb
a5b34d0fd822   wordpress   "docker-entrypoint.s…"   About an hour ago    Up About an hour    0.0.0.0:64561->80/tcp   wordpress
```

```powershell
C:\Users\User> cd c:\docker\docker-compose

c:\docker\docker-compose> docker-compose down
[+] Running 3/3
 ✔ Container docker-compose-wordpress-1    Removed                  1.4s
 ✔ Container docker-compose-wordpressdb-1  Removed                  1.9s
 ✔ Network docker-compose_default          Removed                  0.7s
       0.0.0.0:64561->80/tcp   wordpress
```

```powershell
c:\docker\docker-compose> docker container ls -a		⇐ docker-compose를 이용해서 실행한 컨테이너가 모두 삭제
CONTAINER ID   IMAGE       COMMAND                   CREATED             STATUS             PORTS                   NAMES
9f2d6cafbc09   mysql:5.7   "docker-entrypoint.s…"   About an hour ago   Up About an hour   3306/tcp, 33060/tcp     wordpressdb
a5b34d0fd822   wordpress   "docker-entrypoint.s…"   2 hours ago         Up 2 hours
```

```powershell
C:\docker\docker-compose> docker volume ls
DRIVER    VOLUME NAME
local     docker-compose_wordpressdb_data			⇐ 볼륨은 유지되고 있음
```

****detach 모드로 컨테이너 실행****

```powershell
C:\docker\docker-compose> docker-compose up -d		⇐ detach 모드로 실행
[+] Running 3/3
 ✔ Network docker-compose_default          Created    0.8s
 ✔ Container docker-compose-wordpressdb-1  Started    0.0s
 ✔ Container docker-compose-wordpress-1    Started    0.0s
```

```powershell
C:\docker\docker-compose>
C:\docker\docker-compose> docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED              STATUS              PORTS                   NAMES
8bb0d0842a18   wordpress   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:65153->80/tcp   docker-compose-wordpress-1
517be5dbe25c   mysql:5.7   "docker-entrypoint.s…"   About a minute ago   Up About a minute   3306/tcp, 33060/tcp     docker-compose-wordpressdb-1
9f2d6cafbc09   mysql:5.7   "docker-entrypoint.s…"   About an hour ago    Up About an hour    3306/tcp, 33060/tcp     wordpressdb
a5b34d0fd822   wordpress   "docker-entrypoint.s…"   2 hours ago          Up 2 hours          0.0.0.0:64561->80/tcp   wordpress
```

```powershell
C:\docker\docker-compose> docker-compose ls
NAME                STATUS              CONFIG FILES
docker-compose      running(2)          C:\docker\docker-compose\docker-compose.yaml
```

## **docker-compse-wordpress-1 컨테이너의 서비스 포트로 접속**

![Alt text](image-2.png)
## **컨테이너 중지**

```powershell
C:\docker\docker-compose> docker-compose down
[+] Running 3/3
 ✔ Container docker-compose-wordpress-1    Removed   1.5s
 ✔ Container docker-compose-wordpressdb-1  Removed   1.9s
 ✔ Network docker-compose_default          Removed   0.7s

C:\docker\docker-compose> docker-compose up -d --scale wordpress=3
[+] Running 5/5
 ✔ Network docker-compose_default          Created                                                          0.7s
 ✔ Container docker-compose-wordpressdb-1  Started                                                          0.0s
 ✔ Container docker-compose-wordpress-1    Started                                                          0.1s
 ✔ Container docker-compose-wordpress-2    Started                                                          0.1s
 ✔ Container docker-compose-wordpress-3    Started                                                          0.1s
```

```powershell
C:\docker\docker-compose> docker container ls
CONTAINER ID   IMAGE       COMMAND                   CREATED          STATUS          PORTS                   NAMES
78d136c04c4a   wordpress   "docker-entrypoint.s…"   36 seconds ago   Up 33 seconds   0.0.0.0:65188->80/tcp   docker-compose-wordpress-3
d8000fbd46bc   wordpress   "docker-entrypoint.s…"   36 seconds ago   Up 32 seconds   0.0.0.0:65189->80/tcp   docker-compose-wordpress-2
e8e765a84010   wordpress   "docker-entrypoint.s…"   36 seconds ago   Up 31 seconds   0.0.0.0:65190->80/tcp   docker-compose-wordpress-1
86f185080fc4   mysql:5.7   "docker-entrypoint.s…"   36 seconds ago   Up 35 seconds   3306/tcp, 33060/tcp     docker-compose-wordpressdb-1
9f2d6cafbc09   mysql:5.7   "docker-entrypoint.s…"   2 hours ago      Up 2 hours      3306/tcp, 33060/tcp     wordpressdb
a5b34d0fd822   wordpress   "docker-entrypoint.s…"   2 hours ago      Up 2 hours      0.0.0.0:64561->80/tcp   wordpress
```

```powershell
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

기존의 컨테이너에서 7개 추가

```powershell
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

## **scale in**

```powershell
[+] Running 6/6r-compose> docker-compose up -d --scale wordpress=5

✔ Container docker-compose-wordpressdb-1  Running                  0.0s

✔ Container docker-compose-wordpress-1    Running                  0.0s

✔ Container docker-compose-wordpress-3    Running                  0.0s

✔ Container docker-compose-wordpress-2    Running                  0.0s

✔ Container docker-compose-wordpress-5    Running                  0.0s

✔ Container docker-compose-wordpress-8    Running                  0.0s
```

기존에 생성된 컨테이너 5개 삭제