## **파드의 헬스 체크 기능**

파드의 컨테이너에는 애플리케이션이 정상적으로 기동하고 있는지 확인하는 기능(=헬스 체크 기능)을 설정할 있으며, 이상이 감지되면 컨테이너를 강제 종료하고 재시작할 수 있음

kubelet가 컨테이너의 헬스 체크를 담당

kubelet의 헬스 체크는 활성 프로브(Liveness Probe)와 준비 상태 프로브(Readiness Probe)를 사용하여 실행 중인 파드의 컨테이너를 검사

활성 프로브(Liveness Probe)

- 컨테이너의 애플리케이션이 **정상적으로 실행** 중인 것을 검사
- 검사에 실패하면 파드 상의 컨테이너를 강제로 종료하고 재시작
- 이 기능을 사용하기 위해서는 매니페스트에 명시적으로 설정해야 함

준비 상태 프로브(Readiness Probe)

- 컨테이너의 애플리케이션이 **요청을 받을 준비**가 되었는지를 검사
- 검사에 실패하면 서비스에 의한 요청 트래픽 전송을 중지
- 파드가 기동하고 나서 준비가 될 때까지 요청이 전송되지 않기 위해 사용
- 이 기능을 사용하기 위해서는 매니페스트에 명시적으로 설정해야 함

프로브 대응 핸들러

exec

- 컨테이너 내 커맨드를 실행
- exit 코드 0으로 종료하면 진단 결과는 성공으로 간주하고, 그 외는 실패로 간주

tcpSocket

- 지정한 TCP 포트 번호로 연결할 수 있으면 성공으로 간주

httpGet

- 지정한 포트와 경로로 HTTP GET 요청을 정기적으로 실행
- HTTP 상태 코드가 200 이상 400 미만이면 성공으로 간주하고, 그 외는 실패로 간주
- 지정 포트가 열려 있지 않은 경우도 실패로 간주

**nodejs 설치 후 새 명령 프롬프트를 실행해야 실습**

https://nodejs.org/ko/download

실제 컨테이너를 기동하여 프로브의 동작을 확인

```powershell
c:\kubernetes> mkdir webapp
c:\kubernetes> cd webapp
c:\kubernetes\webapp> npm init -y
```

```powershell
Wrote to c:\kubernetes\webapp\package.json:

{
  "name": "webapp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

```powershell
C:\kubernetes\webapp\index.js

const express = require('express')
const app = express()
var start = Date.now()

app.get('/healthz', function(request, response) {
    var msec = Date.now() - start
    var code = 200
    if (msec > 40000) {
        code = 500
    }
    console.log('GET /healthz ' + code)
    response.status(code).send('OK')
});

app.get('/ready', function(request, response) {
    var msec = Date.now() - start
    var code = 500
    if (msec > 20000) {
        code = 200
    }
    console.log('GET /ready ' + code)
    response.status(code).send('OK')
});

app.get('/', function(request, response) {
    console.log('GET /')
    response.send('Hello from Node.js')
});

app.listen(3000);
```

애플리케이션 실행에 필요한 모듈을 추가

```powershell
c:\kubernetes\webapp> npm install express -y
```

의존모듈이 추가된 것을 확인할 수 있음

```powershell
c:\kubernetes\webapp> type package.json
{
  "name": "webapp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "express": "^4.18.2"			⇐ 의존모듈이 추가된 것을 확인할 수 있음
  }
}
```

```powershell
c:\kubernetes\webapp> node index.js
GET /
GET /ready 200
GET /ready 200
GET /healthz 500
```

```powershell
C:\kubernetes\webapp\Dockerfile

FROM    alpine:latest
RUN     apk update && apk add --no-cache nodejs npm
WORKDIR /
ADD     ./package.json /
RUN     npm install
ADD     ./index.js /
CMD     node /index.js
```

이미지 빌드 및 등록

```powershell
c:\kubernetes\webapp> docker image build --tag seojonghun/webapp:1.0 .
c:\kubernetes\webapp> docker login
c:\kubernetes\webapp> docker image push seojonghun/webapp:1.0
```

헬스 체크 설정 매니페스트 작성

```powershell
C:\kubernetes\webapp\webapp-pod.yaml
```

```powershell
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - name: webapp
    image: myanjini/webapp:1.0
    livenessProbe:                       # 어플리케이션이 살이 있는지 확인
      httpGet:
        path: /healthz
        port: 3000
      initialDelaySeconds: 3             # 검사 개시 대기 시간
      periodSeconds: 5                   # 검사 간격(주기)
    readinessProbe:                      # 어플리케이션이 요청을 처리할 준비되었는지 확인
      httpGet:
        path: /ready
        port: 3000
      initialDelaySeconds: 15
      periodSeconds: 6
```

---

파드를 배포하고 헬스 체크가 실행되어 READY 상태가 되는 과정을 확인

```powershell
c:\kubernetes\webapp> kubectl apply -f webapp-pod.yaml
pod/webapp created
```

readinessProbe가 성공하지 못했기 때문에 READY 상태가 아닌 것으로 판단

```powershell
c:\kubernetes\webapp> kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
hello-k8s-75797f94b4-v8gvw   1/1     Running   0          30h
init-sample                  1/1     Running   0          88m
webapp                       0/1     Running   0          18s
                             ~
                             readinessProbe가 성공하지 못했기 때문에 READY 상태가 아닌 것으로 판단

c:\kubernetes\webapp>kubectl get pod
NAME                         READY   STATUS    RESTARTS   AGE
hello-k8s-75797f94b4-v8gvw   1/1     Running   0          30h
init-sample                  1/1     Running   0          88m
webapp                       1/1     Running   0          26s
```

컨테이너 로그에 기록된 헬스 체크 결과를 출력

```powershell
c:\kubernetes\webapp> kubectl logs webapp			⇐ 컨테이너 로그에 기록된 헬스 체크 결과를 출력
GET /healthz 200						⇐ livenessProbe에 의해 액세스 
GET /healthz 200
GET /healthz 200
GET /ready 500							⇐ 20초가 지나기 전에 /ready가 500을 응답
GET /healthz 200
GET /ready 200
GET /healthz 200
GET /ready 200
GET /healthz 200
GET /healthz 200
GET /ready 200
GET /healthz 200
GET /ready 200
GET /healthz 500
GET /ready 200
GET /healthz 500
GET /ready 200
GET /healthz 500
```

활성 프로브(livenessProbe)가 호출하는 /healthz의 응답이 3회 연속해서 500을 반환 = >종료되고 재시작

```powershell
c:\kubernetes\webapp> kubectl logs webapp

GET /healthz 200
GET /healthz 200
GET /healthz 200
GET /ready 500
GET /healthz 200
GET /healthz 200
GET /ready 200
GET /healthz 200
GET /ready 200
GET /healthz 200
GET /ready 200
GET /healthz 200
GET /ready 200
GET /healthz 500	⇐ 활성 프로브(livenessProbe)가 호출하는 /healthz의 응답이 3회 연속해서 500을 반환 
GET /ready 200
GET /healthz 500
GET /healthz 500
GET /ready 200
GET /ready 200
GET /ready 200
GET /ready 200
```