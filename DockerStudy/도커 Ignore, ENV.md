# **빌드 컨텍스트(build context)**

- 도커 이미지를 생성하는데 필요한 파일, 소스 코드, 메타 데이터 등을 담고 있는 디렉터리

~~~~~~~~~~~

Dockerfile, .dockerignore 등

- Dockerfile이 존재하는 디렉터리
- 빌드 컨텍스트는 docker build 명령어의 마지막에 지정한 위치에 있는 파일 전체를 포함
- 깃 허브과 같은 외부 URL에서 Dockerfile을 읽어 들인다면 해당 저장소에 있는 파일과 서브 모듈을 포함
- 단순 파일뿐 아니라 하위 디렉터리도 전부 포함하게 되므로 빌드에 불필요한 파일이 포함된다면 빌드 속도가 느려지고, 호스트의 메모리를 지나치게 점유하는 문제가 발생
- .dockerignore 파일을 작성하면 명시된 이름의 파일을 빌드 시 컨텍스트에서 제외
    - 불필요한 파일 → 빌드 시간을 단축하고 네트워크 대역폭을 절약
    - 기밀성을 요하는 파일
- .dockerignore 파일은 Dockerfile과 동일한 위치에 저장

test2.html

- .html
- /*.html

test.htm?

!test*.html

```powershell
C:\Users\myanj> cd c:\docker
```

```powershell
c:\docker> code .
```

```powershell
C:\docker\Dockerfile
```

```powershell
FROM    ubuntu
RUN     apt-get update
RUN     mkdir /myapp
WORKDIR /myapp
COPY    * /myapp/
CMD     ["/bin/bash"]
```

C:\docker\README.md 파일 생성

https://lh3.googleusercontent.com/W3wvd3gHmuUP0xJcjQ7X9AzJZXF8O_mTuFC4-AUNQAkOMc6hvfMPtU4Jh0gNn5W2ZdVOkR_dLSgzYBg7FyuSH5FmI3nDTw1EuARVvcPoBlA5dA9ewNi92x-p5L7_N1ab0qfFLobHzwdqirSHjS--BDQ

c:\docker\.dockerignore 파일을 생성

```powershell
build-pattern/
multi-state-build/
tmp/
webserver/
*.go
*.md
!README.md
password.txt
```

```powershell
c:\docker> docker image build -t user-test .
[+] Building 0.6s (10/10) FINISHED                               docker:default
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 155B                                       0.0s
 => [internal] load .dockerignore                                          0.0s
```

```powershell
c:\docker> docker container run -it --rm user-test /bin/bash
root@cb35ac559cba:/myapp# ls
Dockerfile  README.md
```

## **ENV**

- Dockerfile에서 사용할 환경변수를 지정
- ENV 환경변수이름 환경변수값 형식으로 설정
- 설정한 환경변수는 ${환경변수이름} 또는 $환경변수이름 형태로 Dovkerfile, 이미지, 이미지로 생성한 컨테이너에서 사용이 가능
- docker container run 명령어의 -e 옵션을 이용해서 같은 이름의 환경변수 값을 덮어 쓸 수 있음

c:\docker\env\Dockerfile

```powershell
FROM    ubuntu
ENV     WORKSPACE /myapp
RUN     mkdir ${WORKSPACE}
WORKDIR ${WORKSPACE}
RUN     touch ${WORKSPACE}/mytouchfile
```

```powershell
c:\docker> cd env

c:\docker\env> docker image build -t envimage .

c:\docker\env> docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED          SIZE
envimage      latest    c1edc51450ae   18 seconds ago   77.8MB

```

```powershell
c:\docker\env> docker container run -itd envimage /bin/bash

086921606bede9d3e6d2dcb25858cbf1a14b61019a36c9c1b63a938dfd7b5501⇐ 컨테이너 ID를 출력
```

```powershell
c:\docker\env> docker container ls
CONTAINER ID   IMAGE      COMMAND       CREATED          STATUS          PORTS     NAMES
086921606bed   envimage   "/bin/bash"   15 seconds ago   Up 14 seconds             crazy_jones
```

```powershell
c:\docker\env> docker container attach 0869		⇐ 실행되고 있는 컨테이너로 attach 
root@086921606bed:/myapp# 
                  ~~~~~~
                  해당 컨테이너로 접속(attach)했을 때 WORKDIR로 설정한 디렉터리로 이동
```

```powershell
root@086921606bed:/myapp# ls
mytouchfile					⇐ Dockerfile에 RUN touch ${WORKSPACE}/mytouchfile 명령어로 만들어진 파일
root@086921606bed:/myapp# echo $WORKSPACE	⇐ Dockerfile에 ENV WORKSPACE /myapp로 설정한 환경변수를 확인
/myapp
```

```powershell
root@086921606bed:/myapp# exit
exit

c:\docker\env> docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

```powershell
c:\docker\env> docker container run -itd -e WORKSPACE=/temp envimage /bin/bash
56a1102ca1bb4b02cc965e8f75b66a3a7be7ae4addab2f0823aa785ff7b58532

c:\docker\env> docker container attach 56a1
root@56a1102ca1bb:/myapp# ls
mytouchfile
```

```powershell
root@56a1102ca1bb:/myapp# ls /temp
ls: cannot access '/temp': No such file or directory
root@56a1102ca1bb:/myapp# echo $WORKSPACE
/temp

root@56a1102ca1bb:/myapp# exit
exit
```

```python
C:\docker\env_python\myapp.py
import os

uname_value = os.environ.get('UNAME')
uage_value = os.environ.get('UAGE')

if uname_value is not None:
    print(f"UNAME is {uname_value}")
else:
    print("UNAME is not set")

if uage_value is not None:
    print(f"UAGE is {uage_value}")
else:
    print("UAGE is not set")
```

```powershell
c:\docker\env_python> python myapp.py
UNAME is not set
UAGE is not set
```

```powershell
c:\docker\env_python> set UNAME=홍길동			⇐ 윈도우에서 환경변수를 설정

c:\docker\env_python> python myapp.py
UNAME is 홍길동
UAGE is not set
```

```powershell
C:\docker\env_python\Dockerfile

FROM    python
ENV     UNAME Hong Gil-Dong
WORKDIR /app
COPY    myapp.py .
CMD     ["python", "myapp.py"]
```

```powershell
c:\docker\env_python> docker image build -t myapp:py .
c:\docker\env_python> docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED              SIZE
myapp         py        7fdeab046bb7   About a minute ago   1.01GB
```

```powershell
c:\docker\env_python> docker container run myapp:py
UNAME is Hong Gil-Dong					⇐ Dockerfile의 ENV에서 설정한 값이 사용
UAGE is not set
```

```powershell
c:\docker\env_python> docker container run -e UNAME="Go Gil-Dong" -e UAGE=40 myapp:py
UNAME is Go Gil-Dong						⇐ -e 옵션에 설정한 값이 사용 (덮어쓰기)
UAGE is 40
```

```powershell
c:\docker\env_python> docker container run -it -e UNAME="Go Gil-Dong" -e UAGE=40 myapp:py /bin/bash
root@bb2f2cab96e7:/app# set					⇐ 환경 변수를 확인
BASH=/bin/bash
	... (생략) ... 
SHLVL=1
TERM=xterm
UAGE=40
UID=0
UNAME='Go Gil-Dong'
_=']'
root@bb2f2cab96e7:/app# exit
```