# nginx 이미지 생성후 도커 허브에 배포해보기

nginx 이미지를 이용해서 본인만의 웹 서버 이미지를 작성하고, 작성한 이미지를 도커 허브에 등록해 보기

## **방법1. container commit 명령으로 이미지를 작성**

### **#1 nginx 이미지를 이용해서 컨테이너를 실행**

```powershell
c:\docker> mkdir mywebserver
```

```powershell
c:\docker> cd mywebserver
```

```powershell
c:\docker\mywebserver> docker container run -itd -p 80 --name mywebserver nginx
```

```powershell
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
a803e7c4b030: Pull complete
8b625c47d697: Pull complete
4d3239651a63: Pull complete
0f816efa513d: Pull complete
01d159b8db2f: Pull complete
5fb9a81470f3: Pull complete
9b1e1e7164db: Pull complete
Digest: sha256:32da30332506740a2f7c34d5dc70467b7f14ec67d912703568daff790ab3f755
Status: Downloaded newer image for nginx:latest
fc12ce89f88e85c3900a2e7dd5d82fa7d0e5626711b4cc3f073025ea8b41abdf
```

```powershell
c:\docker\mywebserver> docker container ls
CONTAINER ID   IMAGE     COMMAND                   CREATED         STATUS         PORTS                   NAMES
fc12ce89f88e   nginx     "/docker-entrypoint.…"   9 seconds ago   Up 5 seconds   0.0.0.0:53918->80/tcp   mywebserver
```

nginx 기본 웹 루트 디렉터리 아래의 기본 웹 페이지(index.html)가 응답으로 제공되는 것을 확인

https://lh3.googleusercontent.com/Ta_6uif6utpJINacUU-CBhVDa68xFXRk65D0vKwhMN-Y0ct5R7T0a1OhuDsdzV5Kd4f7aipKPhZ1RHjlGB-eM5Vbap9dlTeTe1Uzm25r2c4086assQSqIYzz-9V8OCE8HAXqNqnjrLJC3SDcVtcVgG0

### **#2 index.html**

```powershell
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>Hello Docker</h1>
    <h1>^..^v</h1>
</body>
</html>
```

****#3 #2에서 생성한 index.html 파일을 #1에서 실행한 컨테이너 내부의 nginx 기본 웹 루트 디렉터리로 복사****

```powershell
c:\docker\mywebserver> docker container cp .\index.html mywebserver:/usr/share/nginx/html/
                                                        ~~~~~~~~~~~ 컨테이너 이름 
Successfully copied 2.05kB to fc12:/usr/share/nginx/html/
```

```powershell
c:\docker\mywebserver> docker container exec mywebserver cat /usr/share/nginx/html/index.htm

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>Hello Docker</h1>
    <h1>^..^v</h1>
</body>
</html>
```

### **#4 브라우저로 확인**

https://lh5.googleusercontent.com/2PKYp8iyP189Bxzx1sgK29_q0lJRPkZbUr7pZ_jd2w4DhgvS5bkIjT59i37dAd8waU-DXRNPFcV4GJh_dBBjfHuilaD5qIuV-Qz2QR_teB8kaezahpvlUY79-F3SeQBFeESGJWERZsUiFGwX_VzC6rc

### **#5 컨테이너의 현재 상태를 이미지로 저장**

```powershell
c:\docker\mywebserver> docker container commit mywebserver seojonghun/mywebserver:1.0
                                               ~~~~~~~~~~~ ~~~~~~~~~~~~~~~~~~~~~~~~
                                               |                            |
                                               +---  컨테이너 이름          +--- 이미지 이름
sha256:bb469861021baf178d55d176209697c81b2a1d4fed18aee1688d8d86671ee8af
```

```powershell
c:\docker\mywebserver> docker image ls
REPOSITORY             TAG       IMAGE ID       CREATED         SIZE
seojonghun/mywebserver   1.0       bb469861021b   4 seconds ago   187MB
nginx                  latest    61395b4c586d   4 days ago      187MB
```

****#6 생성한 이미지를 도커 허브에 등록****

```powershell
c:\docker\mywebserver> docker image push myanjini/mywebserver:1.0
The push refers to repository [docker.io/myanjini/mywebserver]
9bc35f76be3b: Pushed
d26d4f0eb474: Mounted from library/nginx
a7e2a768c198: Mounted from library/nginx
9c6261b5d198: Mounted from library/nginx
ea43d4f82a03: Mounted from library/nginx
1dc45c680d0f: Mounted from library/nginx
eb7e3384f0ab: Mounted from library/nginx
d310e774110a: Mounted from library/nginx
1.0: digest: sha256:54525cb333f4c8a57ea39179df37c8d73a5d5654a63854d77526dc3541d192b9 size: 1986
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1b8cc88d-9972-478f-9c35-51d6c2e63848/ffe5db93-099a-4070-8d38-7e2894a93d87/Untitled.png)

## **방법2. Dockerfile을 작성해서 이미지를 빌드**

### **#1 nginx 이미지의 Dockerfile을 확인 ⇒ https://github.com/nginxinc/docker-nginx/blob/321a13a966eeff945196ddd31a629dad2aa85eda/mainline/debian/Dockerfile**

:

COPY docker-entrypoint.sh /

COPY 10-listen-on-ipv6-by-default.sh /docker-entrypoint.d

COPY 15-local-resolvers.envsh /docker-entrypoint.d

COPY 20-envsubst-on-templates.sh /docker-entrypoint.d

COPY 30-tune-worker-processes.sh /docker-entrypoint.d

ENTRYPOINT ["/docker-entrypoint.sh"]

EXPOSE 80

STOPSIGNAL SIGQUIT

CMD ["nginx", "-g", "daemon off;"]			⇐ 컨테이너 실행 시 실행할 명령

****#2 Dockerfile 작성****

```powershell
FROM  nginx
COPY  index.html /usr/share/nginx/html/	⇐ 방법1에서 생성한 index.html 파일을 컨테이너 내부로 복사
CMD   ["nginx", "-g", "daemon off;"]
```

****#3 이미지 빌드****

```powershell
c:\docker\mywebserver> docker image build -t seojonghun/mywebserver:2.0 .
[+] Building 0.3s (7/7) FINISHED                              docker:default
 => [internal] load .dockerignore                                       0.0s
 => => transferring context: 2B                                         0.0s
 => [internal] load build definition from Dockerfile                    0.0s
 => => transferring dockerfile: 127B                                    0.0s
 => [internal] load metadata for docker.io/library/nginx:latest         0.0s
 => [internal] load build context                                       0.1s
 => => transferring context: 295B                                       0.0s
 => [1/2] FROM docker.io/library/nginx                                  0.1s
 => [2/2] COPY  index.html /usr/share/nginx/html/                       0.0s
 => exporting to image                                                  0.0s
 => => exporting layers                                                 0.0s
 => => writing image sha256:17880d7e941914335bca495549d3839cb25283cf31  0.0s
 => => naming to docker.io/myanjini/mywebserver:2.0                     0.0s
```

```powershell
c:\docker\mywebserver> docker image ls
REPOSITORY             TAG       IMAGE ID       CREATED          SIZE
seojonghun/mywebserver   2.0       17880d7e9419   18 seconds ago   187MB
seojonghun/mywebserver   1.0       bb469861021b   7 minutes ago    187MB
nginx                  latest    61395b4c586d   4 days ago       187MB
```

****#4 컨테이너 실행****

```powershell
c:\docker\mywebserver> docker container run -d -p 80 myanjini/mywebserver:2.0
0ad184113fbd27699b51d1f21f2a2cce28787d0510e49af0bc3a5e09b2088742
```

```powershell
c:\docker\mywebserver> docker container ls
CONTAINER ID   IMAGE                      COMMAND                   CREATED          STATUS          PORTS                   NAMES
0ad184113fbd   myanjini/mywebserver:2.0   "/docker-entrypoint.…"   5 seconds ago    Up 4 seconds    0.0.0.0:54051->80/tcp   wizardly_mirzakhani
fc12ce89f88e   nginx                      "/docker-entrypoint.…"   15 minutes ago   Up 15 minutes   0.0.0.0:53918->80/tcp   mywebserver
```

### **#5 브라우저로 확인**

https://lh3.googleusercontent.com/8de323ZVjZeCI14CUe03a5xgOpHcZNnoHGrdicvFGfdhsQ0z6NoYpBusaeGcXBExJmLVIfVCtoQo1t3K3rOQ9eU9eavog1g4fVCTKmz6xmzMJ5kvrdFphJR_8ZK3hUvUZZESQzuuFEj-yiOvV2ZpNM4

### **#6 생성한 이미지를 도커 허브에 등록**

```powershell
c:\docker\mywebserver> docker image push myanjini/mywebserver:2.0
The push refers to repository [docker.io/myanjini/mywebserver]
96676e06b177: Pushed
d26d4f0eb474: Layer already exists
a7e2a768c198: Layer already exists
9c6261b5d198: Layer already exists
ea43d4f82a03: Layer already exists
1dc45c680d0f: Layer already exists
eb7e3384f0ab: Layer already exists
d310e774110a: Layer already exists
2.0: digest: sha256:65a4a0ad15554df1cf8fe14f47c6612b4608ef4ebe8280446b9b1978cb14f366 size: 1985
```

****#7 도커 허브에서 이미지를 확인****