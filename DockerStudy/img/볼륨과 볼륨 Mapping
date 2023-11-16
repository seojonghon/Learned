# 볼륨과 볼륨 Mapping

## **VOLUME**

- 빌드된 이미지로 컨테이너를 생성했을 때 호스트와 공유할 컨테이너 내부의 디렉터리를 설정
- 컨테이너에서 VOLUME을 사용하면 컨테이너를 실행할 때 -v 또는 --volume 옵션을 사용하여 호스트의 볼륨을 연결하거나 볼륨 컨테이너를 생성해 연결해 줘야 함
- VOLUME 명령을 사용하는 주요 목적
    - 데이터 보존
    - 데이터 공유
    - 데이터 컨테이너와 애플리케이션 코드를 분리
    

```powershell
c:\docker\volume\Dockerfile

FROM    ubuntu
RUN     mkdir  /home/share
RUN     echo test >> /home/share/testfile
```

```powershell
c:\docker\volume> docker image build -t myvolume:1.0 .
c:\docker\volume> docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED             SIZE
myvolume      1.0       d7577d1e6ba7   6 seconds ago       77.8MB
```

```powershell
c:\docker\volume> docker container run -it --rm myvolume:1.0 /bin/bash
root@5fb6540a9eba:/#
root@5fb6540a9eba:/# ls /home/share/
testfile
```

```powershell
root@5fb6540a9eba:/# echo test2 >> /home/share/test2		⇐ (1) 새로운 파일을 생성한 후 컨테이너를 종료
root@5fb6540a9eba:/# ls /home/share/
test2  testfile
root@5fb6540a9eba:/# exit
exit

c:\docker\volume> docker container run -it --rm myvolume:1.0 /bin/bash
root@74879850e770:/# ls /home/share/					⇐ (1)에서 생성한 파일이 존재하지 않음
testfile
root@74879850e770:/# Ctrl + p + q

c:\docker\volume> docker volume ls					⇐ 생성된 볼륨이 없음
DRIVER    VOLUME NAME
```

```powershell
FROM    ubuntu
RUN     mkdir  /home/share
RUN     echo test >> /home/share/testfile
VOLUME  /home/share
```

```powershell
c:\docker\volume> docker image build -t myvolume:2.0 .
c:\docker\volume> docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED             SIZE
myvolume      1.0       d7577d1e6ba7   3 minutes ago       77.8MB
myvolume      2.0       68996d8bf40e   3 minutes ago       77.8MB
```

```powershell
c:\docker\volume> docker container run -it --rm myvolume:2.0 /bin/bash
root@9e1b08e64f1d:/# echo test2 >> /home/share/test2
root@9e1b08e64f1d:/# ls /home/share/test2

/home/share/test2
root@9e1b08e64f1d:/# Ctrl + p + q

c:\docker\volume> docker volume ls
DRIVER    VOLUME NAME
local     9ac5ec7ebabe9e6c6a2a427e2b1c1338198cb72d16df5121afb8dfb7830d1513

c:\docker\volume> docker container run -it --rm myvolume:2.0 /bin/bash
```

# **도커에서 데이터 관리**

<img src = "https://lh3.googleusercontent.com/aiC5GC2-dk0BCxzPgI4v6cJY6ceZByGhHOiCE5kuX0x2oHk2StCnbBX3yLxYOrllNFRtaAYg3ChlNEX51HzGYMBK2Lh_gPharYBug7fH25eNWScxmi5szWY0Vk7kkxPYocaGMKTBvy6OfpMYP-gINqM">

## **volume (컨테이너 내부에서만 관리가능)**

호스트의 파일 시스템 내에 특정 영역(리눅스의 경우, /var/lib/docker/volumes/)을 도커가 사용, 관리

도커가 아닌 다른 프로세스에서는 해당 영역 접근이 불가능

가장 추천하는 방식

## **bind mount (호스트에서 관리 가능)**

호스트의 파일 시스템 자체를 사용

호스트와 컨테이너가 설정 파일을 공유하거나 호스트에서 개발하고 컨테이너로 배포하는 방식으로 사용

## **tmpfs mount**

호스트의 파일 시스템 대신 메모리에 저장하는 방식

non-persistent data를 다룰 때는 tmpfs mount가 가장 좋음

## **bind mount 예시**

### **#1 MySQL 이미지를 이용해서 데이터베이스 컨테이너를 실행**

https://hub.docker.com/_/mysql

```powershell
C:\docker> docker container run -d --name wordpressdb_hostvolume -e MYSQL_ROOT_PASSWORD=password -e
MYSQL_DATABASE=wordpress -v c:\docker\mysql_data:/var/lib/mysql mysql:5.7
																									~~~~~~~~~~~~~~
                                                 MySQL 데이터를 저장하는 기본 디렉터리
```

### **#2 워드프레스 이미지를 이용해서 웹 애플리케이션 컨테이너를 실행**

https://hub.docker.com/_/wordpress

```powershell
C:\docker> docker container run -d -e WORDPRESS_DB_PASSWORD=password --name wordpress_hostvolume --link wordpressdb_hostvolume:mysql -p 80 wordpress
```

```powershell
C:\docker> docker container ls
CONTAINER ID   IMAGE        COMMAND                   CREATED             STATUS             PORTS                    NAMES
8e0b1759e31f   wordpress    "docker-entrypoint.s…"   54 seconds ago      Up 45 seconds      0.0.0.0:57555->80/tcp    wordpress_hostvolume
ae39f66eba6d   mysql:5.7    "docker-entrypoint.s…"   9 minutes ago       Up 9 minutes       3306/tcp, 33060/tcp      wordpressdb_hostvolume
```

### **#3 bind mount를 확인**

C:\docker> dir .\mysql_data

C 드라이브의 볼륨에는 이름이 없습니다.

볼륨 일련 번호: D0A5-3978

```powershell
C:\docker\mysql_data 디렉터리

2023-09-26  오전 10:27    <DIR>          .
2023-09-26  오전 10:27    <DIR>          ..
2023-09-26  오전 10:27                56 auto.cnf
2023-09-26  오전 10:27             1,680 ca-key.pem
2023-09-26  오전 10:27             1,112 ca.pem
2023-09-26  오전 10:27             1,112 client-cert.pem
2023-09-26  오전 10:27             1,680 client-key.pem
2023-09-26  오전 10:27        79,691,776 ibdata1
2023-09-26  오전 10:27        12,582,912 ibtmp1
2023-09-26  오전 10:27             1,318 ib_buffer_pool
2023-09-26  오전 10:27        50,331,648 ib_logfile0
2023-09-26  오전 10:27        50,331,648 ib_logfile1
2023-09-26  오전 10:27    <DIR>          mysql
2023-09-26  오전 10:27    <JUNCTION>     mysql.sock [...]
2023-09-26  오전 10:27    <DIR>          performance_schema
2023-09-26  오전 10:27             1,676 private_key.pem
2023-09-26  오전 10:27               452 public_key.pem
2023-09-26  오전 10:27             1,112 server-cert.pem
2023-09-26  오전 10:27             1,680 server-key.pem
2023-09-26  오전 10:27    <DIR>          sys
2023-09-26  오전 10:27    <DIR>          wordpress
              15개 파일         192,949,862 바이트
               6개 디렉터리  72,076,234,752 바이트 남음
```

**#4 컨테이너 삭제 후 데이터 보존 여부를 확인**

```powershell
C:\docker> docker container rm -f wordpress_hostvolume wordpressdb_hostvolume
wordpress_hostvolume
wordpressdb_hostvolume

C:\docker> dir .\mysql_data
 C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: D0A5-3978
```

```powershell
2023-09-26  오전 10:27             1,680 server-key.pem
2023-09-26  오전 10:27    <DIR>          sys
2023-09-26  오전 10:27    <DIR>          wordpress
              15개 파일         192,949,862 바이트
               6개 디렉터리  72,475,029,504 바이트 남음
```

### **#5 디렉터리 단위 공유 뿐 아니라 파일 단위의 공유도 가능하며, -v 옵션을 다중으로 사용하는 것도 가능**

```powershell
C:\docker> echo hello >> hello
```

```powershell
C:\docker> echo hello2 >> hello2
```

```powershell
C:\docker> dir
C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: D0A5-3978

 C:\docker 디렉터리

2023-09-26  오전 10:43                 8 hello
2023-09-26  오전 10:43                 9 hello2
                   ... (생략) ...
              14개 파일               6,550 바이트
              14개 디렉터리  72,731,049,984 바이트 남음
```

```powershell
C:\docker> docker container run -it --name file_volume -v c:\docker\hello:/hello -v c:\docker\hello2:/hello2 ubuntu
root@d27a5aab0c53:/#

root@d27a5aab0c53:/# ls -l hello*
-rwxrwxrwx 1 root root 8 Sep 26 01:43 hello
-rwxrwxrwx 1 root root 9 Sep 26 01:45 hello2

root@d27a5aab0c53:/# cat hello && cat hello2
hello
hello2

root@d27a5aab0c53:/# echo HELLO >> hello
root@d27a5aab0c53:/# echo HELLO2 >> hello2
root@d27a5aab0c53:/# cat hello && cat hello2
hello
HELLO					⇐ /hello 파일에 새로운 내용이 추가된 것을 확인
hello2
HELLO2					⇐ /hello2 파일에 새로운 내용이 추가된 것을 확인
```

```powershell
root@d27a5aab0c53:/# exit
exit

C:\docker> docker container ls -a
CONTAINER ID   IMAGE     COMMAND                   CREATED         STATUS                          PORTS                   NAMES
d27a5aab0c53   ubuntu    "/bin/bash"               4 minutes ago   Exited (0) About a minute ago                           file_volume
df404e39ffe5   nginx     "/docker-entrypoint.…"   2 hours ago     Up 2 hours                      0.0.0.0:56953->80/tcp   mynginx
d1c08ec35bfa   ubuntu    "/bin/bash"               2 hours ago     Up 2 hours                                              myubuntu

C:\docker> type hello
hello
HELLO					⇐ 컨테이너 내부에서 hello 파일에 추가한 내용이 그대로 유지

C:\docker> type hello2
hello2
HELLO2					⇐ 컨테이너 내부에서 hello2 파일에 추가한 내용이 그대로 유지
```

## **volume 예**

### **#1 볼륨 생성**

```powershell
C:\docker> docker volume create myvolume
myvolume
```

```powershell
C:\docker> docker volume ls
DRIVER    VOLUME NAME
local     580596f517eea98b2dd830647d9181da58eb3ecc9c5b50563ce2235f5c1d125a
local     f1e85a73ef43cca39281041cfc941ef77d80fb33e94e933ee4dc1a757db1a440
local     myvolume
```

<img src = "https://prod-files-secure.s3.us-west-2.amazonaws.com/1b8cc88d-9972-478f-9c35-51d6c2e63848/70663c0a-9238-452d-95ca-f94f01bbc8b2/Untitled.png">

****#2 생성한 볼륨을 사용하는 컨테이너를 실행****

```powershell
C:\docker> docker container run -it --name myvolumecontainer -v myvolume:/temp/ ubuntu
root@88ecce5d4fd4:/#

root@88ecce5d4fd4:/# echo hello, volume >> /temp/hello_volume
root@88ecce5d4fd4:/# exit
exit
```

```powershell
C:\docker> docker container ls -a
CONTAINER ID   IMAGE     COMMAND                   CREATED          STATUS                     PORTS                   NAMES
88ecce5d4fd4   ubuntu    "/bin/bash"               57 seconds ago   Exited (0) 9 seconds ago                           myvolumecontainer
```

****#3 동일 볼륨을 사용하는 컨테이너를 추가로 실행****

```powershell
C:\docker> docker container run -it --name ourvolumecontainer -v myvolume:/temp/ ubuntu
root@6c3f0cfbfc94:/#

root@6c3f0cfbfc94:/# ls /temp/			
hello_volume							⇐ #2에 생성한 파일이 존재(공유)하는 것을 확인

root@6c3f0cfbfc94:/# cat /temp/hello_volume
hello, volume

root@6c3f0cfbfc94:/# exit
exit
```

****#4 docker inspect 명령으로 볼륨의 저장 위치를 확인****

```powershell
C:\docker> docker inspect --type volume myvolume		⇐ docker volume inspect myvolume 동일
[
    {
        "CreatedAt": "2023-09-26T02:26:16Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/myvolume/_data",
        "Name": "myvolume",
        "Options": null,
        "Scope": "local"
    }
]
```

\\wsl.localhost\docker-desktop-data\data\docker\volumes\myvolume\_data		⇐ 윈도우의 경우