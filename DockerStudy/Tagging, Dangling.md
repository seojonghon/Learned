# **컨테이너 관련 명령어**

동일 이미지로 이름이 다른 두 개의 컨테이너를 실행

```powershell
c:\docker\volume> docker container run -it --rm --name mycontainerv1 myvolume:1.0 /bin/bash
root@b0cf2641360e:/# Ctrl + p + q
c:\docker\volume> docker container run -it --rm --name mycontainer myvolume:2.0 /bin/bash
root@91f006e21d8f:/# Ctrl + p + q
c:\docker\volume> docker container run -it --rm --name othercontainer myvolume:2.0 /bin/bash
root@ed463f718600:/# Ctrl + p + q
```

```powershell
c:\docker\volume> docker container ls
CONTAINER ID   IMAGE          COMMAND       CREATED              STATUS              PORTS     NAMES
b0cf2641360e   myvolume:1.0   "/bin/bash"   24 seconds ago       Up 23 seconds                 mycontainerv1
ed463f718600   myvolume:2.0   "/bin/bash"   56 seconds ago       Up 56 seconds                 othercontainer
91f006e21d8f   myvolume:2.0   "/bin/bash"   About a minute ago   Up About a minute             mycontainer
b0ab57229bd4   myvolume:2.0   "/bin/bash"   30 minutes ago       Up 30 minutes                 myv
```

특정 ID를 가진 컨테이너를 중지

```powershell
docker container stop b0ab57229bd4
```

특정 이름을 가진 컨테이너를 중지

```powershell
docker container stop myvolumecontainer
```

모든 실행 중인 컨테이너를 중지 (윈도우인 경우 PowerShell에서 실행)

```powershell
docker container stop $(docker container ls -q)
```

동일 이미지로 생성, 실행된 컨테이너를 중지

c:\docker\volume> docker container ls --filter "ancestor=myvolume:1.0"

```powershell
CONTAINER ID   IMAGE          COMMAND       CREATED              STATUS              PORTS     NAMES

b0cf2641360e   myvolume:1.0   "/bin/bash"   About a minute ago   Up About a minute             mycontainerv1
```

c:\docker\volume> docker container ls --filter "ancestor=myvolume:2.0"

```powershell
CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS          PORTS     NAMES

ed463f718600   myvolume:2.0   "/bin/bash"   5 minutes ago    Up 5 minutes              othercontainer

91f006e21d8f   myvolume:2.0   "/bin/bash"   5 minutes ago    Up 5 minutes              mycontainer

b0ab57229bd4   myvolume:2.0   "/bin/bash"   34 minutes ago   Up 34 minutes             myvolumecontainer
```

```powershell
PS C:\Users\myanj> docker container stop **$(docker container ls --filter "ancestor=myvolume:2.0" -q)**

ed463f718600

91f006e21d8f

b0ab57229bd4
```

```powershell
PS C:\Users\myanj> docker container ls
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS         PORTS     NAMES
b0cf2641360e   myvolume:1.0   "/bin/bash"   4 minutes ago   Up 4 minutes             mycontainerv1
```

# **이미지 태깅**

동일한 이미지명으로 이미지를 빌드하면 이전 이미지의 이름이 사라짐

## **태그를 명시하지 않으면 자동으로 latest 태그로 이미지가 생성**

```powershell
c:\docker\volume> docker image build -t myvolume .
c:\docker\volume> docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
myvolume     latest    24d31081417b   37 minutes ago   77.8MB
```

****Dockerfile 내용 변경 없이 이미지 빌드 ⇒ 캐시를 사용하므로 새로 이미지를 만들지 않음****

```powershell
c:\docker\volume> docker image build -t myvolume .
[+] Building 0.8s (7/7) FINISHED                                           docker:default
 => [internal] load build definition from Dockerfile                                 0.0s
 => => transferring dockerfile: 143B                                                 0.0s
 => [internal] load .dockerignore                                                    0.0s
 => => transferring context: 2B                                                      0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                     0.7s
 => [1/3] FROM docker.io/library/ubuntu@sha256:aabed3296a3d45cede1dc866a24476c4d7e0  0.0s
 => => resolve docker.io/library/ubuntu@sha256:aabed3296a3d45cede1dc866a24476c4d7e0  0.0s
 => CACHED [2/3] RUN     mkdir  /home/share                                          0.0s
 => CACHED [3/3] RUN     echo test >> /home/share/testfile                           0.0s
 => exporting to image                                                               0.0s
 => => exporting layers                                                              0.0s
 => => writing image sha256:24d31081417bf9b71b25e4d21cdcc11310873227809c2bb086f536a  0.0s
 => => naming to docker.io/library/myvolume
```

```powershell
c:\docker\volume> docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
myvolume     latest    24d31081417b   38 minutes ago   77.8MB
```

****--no-cache 옵션과 함께 이미지 빌드 ⇒ 캐시를 사용하지 않고 이미지를 새로 생성 ⇒ 동일한 이미지 이름을 사용하므로 이전 이미지의 이름이 삭제됨 ⇒ dangling 이미지 생성****

```powershell
c:\docker\volume> docker image build --no-cache -t myvolume .
[+] Building 1.7s (7/7) FINISHED                                           docker:default
 => [internal] load .dockerignore                                                    0.0s
 => => transferring context: 2B                                                      0.0s
 => [internal] load build definition from Dockerfile                                 0.0s
 => => transferring dockerfile: 143B                                                 0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                     0.7s
 => CACHED [1/3] FROM docker.io/library/ubuntu@sha256:aabed3296a3d45cede1dc866a2447  0.0s
 => => resolve docker.io/library/ubuntu@sha256:aabed3296a3d45cede1dc866a24476c4d7e0  0.0s
 => [2/3] RUN     mkdir  /home/share                                                 0.4s
 => [3/3] RUN     echo test >> /home/share/testfile                                  0.5s
 => exporting to image     
=> => exporting layers                                                              0.0s
 => => writing image sha256:9bb8c11974a6c38c23a6bec0f1e34db9888faeb74b094ee63ac890e  0.0s
 => => naming to docker.io/library/myvolume                                          0.0s                                                          0.0s
```

```powershell
c:\docker\volume> docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
myvolume     latest    9bb8c11974a6   36 seconds ago   77.8MB
<none>       <none>    24d31081417b   39 minutes ago   77.8MB	⇐ 이미지 이름이 동일하여 기존 이미지의 이름을 삭제
									   → dangling 이미지 생성
```

****dangling 상태의 이미지 조회 ⇒ --filter 옵션 사용****

```powershell
c:\docker\volume> docker image ls --filter "dangling=true"
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
<none>       <none>    24d31081417b   40 minutes ago   77.8MB
```

****dangling 이미지 생성을 방지하기 위해서는 이미지 생성 전에 tag 명령으로 이미지 태그를 변경한 후 이미지를 생성하는 것이 필요****

```powershell
c:\docker\volume> docker image tag myvolume:latest myvolume:1.0
c:\docker\volume> docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
myvolume     latest    9bb8c11974a6   3 minutes ago    77.8MB
myvolume     1.0       9bb8c11974a6   3 minutes ago    77.8MB	⇐ 동일한 ID의 다른 이름(태그)의 이미지가 생성
<none>       <none>    24d31081417b   42 minutes ago   77.8MB
```

```powershell
c:\docker\volume> docker image build --no-cache -t myvolume .
c:\docker\volume> docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
myvolume     latest    1fe19554dfa5   34 seconds ago   77.8MB	⇐ dangling 이미지 생성 없이 
myvolume     1.0       9bb8c11974a6   3 minutes ago    77.8MB	   동일한 이름의 이미지만 변경
<none>       <none>    24d31081417b   42 minutes ago   77.8MB
```

도커 컨테이너 실행

```powershell
docker container run -it seojonghun/myvolume:latest /bin/bash
```