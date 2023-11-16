## **임의 포트로 컨테이너를 실행 → 동일한 이미지로 여러 컨테이너를 생성할 경우, 호스트 포트를 다르게 설정해야 함**

```c
C:\docker> docker container run -d **-p 8080** example/echo:latest	⇐ 호스트 포트를 지정하지 않고 컨테이너를 실행
5929ef9d57a5330aa0a3d6a7ee2bf93a7c8b2590dbea25b7f71b9e2f32c0481c
C:\docker> docker container ls
CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                     NAMES
5929ef9d57a5   example/echo:latest   "go run /echo/main.go"   9 seconds ago    Up 7 seconds    0.0.0.0:**52881->8080**/tcp   jolly_goldwasser				⇐ 임의의 포트가 할당된 것을 확인
3cf25dc36a1e   example/echo:latest   "go run /echo/main.go"   42 minutes ago   Up 42 minutes   0.0.0.0:8080->8080/tcp    priceless_pascal
659418fef298   example/echo:latest   "go run /echo/main.go"   47 minutes ago   Up 47 minutes   0.0.0.0:80->8080/tcp      festive_hamilton
C:\docker> docker container run -d **-p 8080** example/echo:latest
716008f1ef785fc2be0289ded67548b5708857805997da1edab41c230aec39cb
C:\docker> docker container ls
CONTAINER ID   IMAGE                 COMMAND                  CREATED              STATUS              PORTS                     NAMES
716008f1ef78   example/echo:latest   "go run /echo/main.go"   4 seconds ago        Up 3 seconds        0.0.0.0:**52887->8080**/tcp   tender_sanderson
5929ef9d57a5   example/echo:latest   "go run /echo/main.go"   About a minute ago   Up About a minute   0.0.0.0:52881->8080/tcp   jolly_goldwasser
3cf25dc36a1e   example/echo:latest   "go run /echo/main.go"   44 minutes ago       Up 44 minutes       0.0.0.0:8080->8080/tcp    priceless_pascal
659418fef298   example/echo:latest   "go run /echo/main.go"   49 minutes ago       Up 49 minutes       0.0.0.0:80->8080/tcp      festive_hamilton
```

```c
C:\docker> docker container ls -a

CONTAINER ID   IMAGE                 COMMAND                  CREATED              STATUS                         PORTS                     NAMES

716008f1ef78   example/echo:latest   "go run /echo/main.go"   About a minute ago   Up About a minute              0.0.0.0:52887->8080/tcp   tender_sanderson

5929ef9d57a5   example/echo:latest   "go run /echo/main.go"   2 minutes ago        Up 2 minutes                   0.0.0.0:52881->8080/tcp   jolly_goldwasser

3cf25dc36a1e   example/echo:latest   "go run /echo/main.go"   45 minutes ago       Up 45 minutes                  0.0.0.0:8080->8080/tcp    priceless_pascal

09b427568e34   example/echo:latest   "go run /echo/main.go"   46 minutes ago       Created                                                  compassionate_morse

659418fef298   example/echo:latest   "go run /echo/main.go"   50 minutes ago       Up 50 minutes                  0.0.0.0:80->8080/tcp      festive_hamilton

4624121b706d   example/echo:latest   "go run /echo/main.go"   56 minutes ago       Exited (1) 55 minutes ago                                festive_pike

d91f6bb54a36   example/echo:latest   "go run /echo/main.go"   About an hour ago    Exited (2) About an hour ago                             eager_bardeen
```

```c
C:\docker> docker container rm d9 46
d9
46
C:\docker> docker container ls -a

CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                     NAMES
716008f1ef78   example/echo:latest   "go run /echo/main.go"   2 minutes ago    Up 2 minutes    0.0.0.0:52887->8080/tcp   tender_sanderson
5929ef9d57a5   example/echo:latest   "go run /echo/main.go"   3 minutes ago    Up 3 minutes    0.0.0.0:52881->8080/tcp   jolly_goldwasser
3cf25dc36a1e   example/echo:latest   "go run /echo/main.go"   46 minutes ago   Up 46 minutes   0.0.0.0:8080->8080/tcp    priceless_pascal
09b427568e34   example/echo:latest   "go run /echo/main.go"   47 minutes ago   Created                                   compassionate_morse
659418fef298   example/echo:latest   "go run /echo/main.go"   51 minutes ago   Up 51 minutes   0.0.0.0:80->8080/tcp      festive_hamilton
```

```c
C:\docker> docker container rm 65			⇐ 실행 상태의 컨테이너는 강제 삭제하거나 중지 후 삭제해야 함

Error response from daemon: You cannot remove a running container 659418fef298cb281d44bd0d1e77bde4c4d65cb384ca79d0a9089f11311466ef. Stop the container before attempting removal or force remove
```

```c
C:\docker> docker container rm **-f** 65			⇐ 컨테이너를 강제로 삭제
65
C:\docker> docker container ls -a
CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                     NAMES
716008f1ef78   example/echo:latest   "go run /echo/main.go"   5 minutes ago    Up 5 minutes    0.0.0.0:52887->8080/tcp   tender_sanderson
5929ef9d57a5   example/echo:latest   "go run /echo/main.go"   6 minutes ago    Up 6 minutes    0.0.0.0:52881->8080/tcp   jolly_goldwasser
3cf25dc36a1e   example/echo:latest   "go run /echo/main.go"   49 minutes ago   Up 49 minutes   0.0.0.0:8080->8080/tcp    priceless_pasc
09b427568e34   example/echo:latest   "go run /echo/main.go"   50 minutes ago   Created                                   compassionate_morse
```

윈도우인 경우, 파워쉘을 실행

```c
PS C:\Users\myanj> docker container ls -a -q	⇐ 모든 상태의 컨테이너 ID를 조회
716008f1ef78
5929ef9d57a5
3cf25dc36a1e
09b427568e34
PS C:\Users\myanj> docker container rm -f **$(**docker container ls -aq**)**
716008f1ef78
5929ef9d57a5
3cf25dc36a1e
09b427568e34
PS C:\Users\myanj> docker container ls -a

CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

## **i -t -d 옵션 : ubuntu 이미지를 이용해서 컨테이너를 실행**

```c
PS C:\Users\myanj> docker container run **-d** --name ubuntu-d ubuntu
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
445a6a12be2b: Pull complet
Digest: sha256:aabed3296a3d45cede1dc866a24476c4d7e093aa806263c27ddaadbdce3c105
Status: Downloaded newer image for ubuntu:latest
635c332bb014e280e0619400ca7193bfbc4c03eeedaddf598bf43c2caeac9cef
```

```c
PS C:\Users\myanj> docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
PS C:\Users\myanj> docker container ls -a
CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS                          PORTS     NAMES
635c332bb014   ubuntu    "/bin/bash"   About a minute ago   Exited (0) About a minute ago             ubuntu-d
```

```c
PS C:\Users\myanj> docker container run --name ubuntu-a ubuntu
PS C:\Users\myanj> docker container ls -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                      PORTS     NAMES
e9b199be8514   ubuntu    "/bin/bash"   29 seconds ago   Exited (0) 28 seconds ago             ubuntu-a
635c332bb014   ubuntu    "/bin/bash"   4 minutes ago    Exited (0) 4 minutes ago              ubuntu-d
```

```c
PS C:\Users\myanj> docker container run **-itd** --name ubuntu-itd ubuntu
e136de07fab4fab024ee84fd5d0a7ce0760100c7ea3ed45bf942565ce51ed639
```

```c
PS C:\Users\myanj> docker container ls
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
e136de07fab4   ubuntu    "/bin/bash"   9 seconds ago   Up 8 seconds             ubuntu-itd
PS C:\Users\myanj> docker container run **-it** --name ubuntu-it-exit ubuntu
root@e310e7220f46:/# exit
exit
```

```c
PS C:\Users\myanj> docker container ls -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                          PORTS     NAMES
e310e7220f46   ubuntu    "/bin/bash"   3 minutes ago    Exited (0) About a minute ago             ubuntu-it-exit
e136de07fab4   ubuntu    "/bin/bash"   5 minutes ago    Up 5 minutes                              ubuntu-itd
e9b199be8514   ubuntu    "/bin/bash"   7 minutes ago    Exited (0) 7 minutes ago                  ubuntu-a
635c332bb014   ubuntu    "/bin/bash"   11 minutes ago   Exited (0) 11 minutes ago                 ubuntu-d
```

```c
PS C:\Users\myanj>
PS C:\Users\myanj> docker container run -it --name ubuntu-it-ctrlpq ubuntu
root@43ca348fc434:/# *CTRL+p+q*
```

```c
PS C:\Users\myanj> docker container ls
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
43ca348fc434   ubuntu    "/bin/bash"   16 seconds ago   Up 15 seconds             ubuntu-it-ctrlpq
e136de07fab4   ubuntu    "/bin/bash"   8 minutes ago    Up 8 minutes              ubuntu-itd
```

```c
PS C:\Users\myanj> docker container run --name ubuntu-without-it ubuntu
PS C:\Users\myanj> docker container ls -a
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS                      PORTS     NAMES
43d98d4f1a4f   ubuntu    "/bin/bash"   9 seconds ago    Exited (0) 8 seconds ago              ubuntu-without-it
43ca348fc434   ubuntu    "/bin/bash"   2 minutes ago    Up 2 minutes                          ubuntu-it-ctrlpq
e310e7220f46   ubuntu    "/bin/bash"   7 minutes ago    Exited (0) 5 minutes ago              ubuntu-it-exit
e136de07fab4   ubuntu    "/bin/bash"   9 minutes ago    Up 9 minutes                          ubuntu-itd
e9b199be8514   ubuntu    "/bin/bash"   11 minutes ago   Exited (0) 11 minutes ago             ubuntu-a
635c332bb014   ubuntu    "/bin/bash"   16 minutes ago   Exited (0) 16 minutes ago             ubuntu-d
```

```c
PS C:\Users\myanj>
PS C:\Users\myanj> docker container attach ubuntu-it-ctrlpq
root@43ca348fc434:/# ls
bin   dev  home  lib32  libx32  mnt  proc  run   srv  tmp  var
boot  etc  lib   lib64  media   opt  root  sbin  sys  usr
root@43ca348fc434:/# read escape sequence
PS C:\Users\myanj> docker container exec -it ubuntu-it-ctrlpq /bin/bash
root@43ca348fc434:/# ls
bin   dev  home  lib32  libx32  mnt  proc  run   srv  tmp  var
boot  etc  lib   lib64  media   opt  root  sbin  sys  usr
root@43ca348fc434:/# exit
exit
```

```c
PS C:\Users\myanj> docker container ls
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
43ca348fc434   ubuntu    "/bin/bash"   7 minutes ago    Up 7 minutes              ubuntu-it-ctrlpq
e136de07fab4   ubuntu    "/bin/bash"   14 minutes ago   Up 14 minutes             ubuntu-itd
PS C:\Users\myanj>
```

# **실행 컨테이너에 표준 입출력을 연결**

## **쉘이 없는 컨테이너 ⇒ exec ... /bin/bash**

```c
PS C:\Users\myanj> dock**e**r container run -d -p 8080 --rm example/echo:lates
7856bbf0d6c49cfeed69ea6afd7bcc10d123e27f33f4545f538deb14f08fa3c1
PS C:\Users\myanj> docker container ls
CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                     NAMES
7856bbf0d6c4   example/echo:latest   "go run /echo/main.go"   9 seconds ago    Up 8 seconds    0.0.0.0:53099->8080/tcp   competent_panini
43ca348fc434   ubuntu                "/bin/bash"              32 minutes ago   Up 32 minutes                             ubuntu-it-ctrlpq
e136de07fab4   ubuntu                "/bin/bash"              40 minutes ago   Up 40 minutes                             ubuntu-itd
PS C:\Users\myanj> docker container attach 7856bbf0d6c4
```

⇐ 해당 컨테이너가 쉘을 제공하지 않기 때문에 아무런 작업도 동작도 할 수 없음

```c
C:\docker> docker container exec -it 7856bbf0d6c4 /bin/bash
root@7856bbf0d6c4:/go# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 07:23 ?        00:00:00 go run /echo/main.go
root        47     1  0 07:23 ?        00:00:00 /tmp/go-build449724855/command-line-arguments/_obj/exe/main
root        58     0  0 07:27 pts/0    00:00:00 /bin/bash
root        64    58  0 07:28 pts/0    00:00:00 ps -ef
root@7856bbf0d6c4:/go# exit
exit
```

```c
C:\docker> docker container ls
CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                     NAMES
7856bbf0d6c4   example/echo:latest   "go run /echo/main.go"   5 minutes ago    Up 5 minutes    0.0.0.0:53099->8080/tcp   competent_panini
43ca348fc434   ubuntu                "/bin/bash"              37 minutes ago   Up 37 minutes                             ubuntu-it-ctrlpq
e136de07fab4   ubuntu                "/bin/bash"              45 minutes ago   Up 45 minutes                             ubuntu-itd
```

## **쉘이 있는 컨테이너 ⇒ attach 또는 exec ... /bin/bash**

```c
C:\docker> docker container run -d -it -p 8080 --rm example/echo /bin/bash
c98e0c2d8303958fc7a97926731e72853d20e585815498c3a783694a95624dad
```

```c
C:\docker> docker container ls
CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                     NAMES
c98e0c2d8303   example/echo          "/bin/bash"              10 seconds ago   Up 9 seconds    0.0.0.0:53115->8080/tcp   pensive_shockley
7856bbf0d6c4   example/echo:latest   "go run /echo/main.go"   8 minutes ago    Up 8 minutes    0.0.0.0:53099->8080/tcp   competent_panini
43ca348fc434   ubuntu                "/bin/bash"              41 minutes ago   Up 41 minutes                             ubuntu-it-ctrlpq
e136de07fab4   ubuntu                "/bin/bash"              49 minutes ago   Up 49 minutes                             ubuntu-itd
```

```c
C:\docker> docker container attach c98e0
root@c98e0c2d8303:/go# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 07:32 pts/0    00:00:00 /bin/bash
root         7     1  0 07:35 pts/0    00:00:00 ps -ef
root@c98e0c2d8303:/go# exit
exit
```

```c
C:\docker> docker container ls			⇐ 컨테이너가 종료되어 삭제되고 없는 것을 확인

CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                     NAMES

7856bbf0d6c4   example/echo:latest   "go run /echo/main.go"   13 minutes ago   Up 13 minutes   0.0.0.0:53099->8080/tcp   competent_panini

43ca348fc434   ubuntu                "/bin/bash"              45 minutes ago   Up 45 minutes                             ubuntu-it-ctrlpq

e136de07fab4   ubuntu                "/bin/bash"              53 minutes ago   Up 53 minutes
```

```powershell
C:\docker> docker container run -d -it -p 8080 --rm example/echo /bin/bash

5dc4025b8683885b086eb9e72a2b884c61afac269b257d34271a71540b0fdc21

C:\docker> docker container ls

CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                     NAMES

5dc4025b8683   example/echo          "/bin/bash"              9 seconds ago    Up 8 seconds    0.0.0.0:53145->8080/tcp   cranky_newton

7856bbf0d6c4   example/echo:latest   "go run /echo/main.go"   14 minutes ago   Up 14 minutes   0.0.0.0:53099->8080/tcp   competent_panini

43ca348fc434   ubuntu                "/bin/bash"              47 minutes ago   Up 47 minutes                             ubuntu-it-ctrlpq

e136de07fab4   ubuntu                "/bin/bash"              54 minutes ago   Up 54 minutes                             ubuntu-itd
```

```c
C:\docker> docker container exec -it 5dc40 /bin/bash
root@5dc4025b8683:/go# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 07:38 pts/0    00:00:00 /bin/bash
root         8     0  0 07:38 pts/1    00:00:00 /bin/bash
root        14     8  0 07:39 pts/1    00:00:00 ps -ef
root@5dc4025b8683:/go# exit
exit
```

```c
C:\docker> docker container ls
CONTAINER ID   IMAGE                 COMMAND                  CREATED              STATUS              PORTS                     NAMES
5dc4025b8683   example/echo          "/bin/bash"              About a minute ago   Up About a minute   0.0.0.0:53145->8080/tcp   cranky_newton
7856bbf0d6c4   example/echo:latest   "go run /echo/main.go"   15 minutes ago       Up 15 minutes       0.0.0.0:53099->8080/tcp   competent_panini
43ca348fc434   ubuntu                "/bin/bash"              48 minutes ago       Up 48 minutes                                 ubuntu-it-ctr
e136de07fab4   ubuntu                "/bin/bash"              56 minutes ago       Up 56 minutes                                 ubuntu-itd
```

# **모든 컨테이너와 이미지를 삭제 (windows의 경우 powershell에서 작업)**

```c
PS C:\Users\myanj> docker container rm -f $(docker container ls -aq)
5dc4025b8683
43d98d4f1a4f
43ca348fc434
e310e7220f46
e136de07fab4
e9b199be8514
635c332bb014
```

PS C:\Users\myanj> docker container ls -a

```c
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
PS C:\Users\myanj> docker image rm $(docker image ls -q)
Untagged: example/echo:latest
Deleted: sha256:199bae77e82afe1091a393f1bd5f1b27303df900280818c68a3425db94d963f9
Deleted: sha256:c3663f350588480e7f92671637c1b84d83f017e172d11f89979553d17ae4563d
Untagged: ubuntu:latest
Untagged: ubuntu@sha256:aabed3296a3d45cede1dc866a24476c4d7e093aa806263c27ddaadbdce3c1054
Deleted: sha256:c6b84b685f35f1a5d63661f5d4aa662ad9b7ee4f4b8c394c022f25023c907b65
Deleted: sha256:dc0585a4b8b71f7f4eb8f2e028067f88aec780d9ab40c948a8d431c1aeadeeb5
Untagged: golang:1.9
Untagged: golang@sha256:8b5968585131604a92af02f5690713efadf029cc8dad53f79280b87a80eb1354
Deleted: sha256:ef89ef5c42a90ec98bda7bbef0495c1ca6f43a31d059148c368b71858de463d2
PS C:\Users\myanj> docker image ls
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```