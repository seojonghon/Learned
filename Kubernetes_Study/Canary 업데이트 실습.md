# **Canary 업데이트 실습**

# **Blue/Green 업데이트 및 Canary 업데이트 실습**

## **Blue/Green 업데이트 실습 ⇒ 디플로이먼트 전략에서 제공하는 것이 아니고 service를 이용해서 구현**

****#1 BLUE 디플로이먼트를 생성****

```powershell
/home/vagrant/sample-deployment-blue.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-deployment-blue
spec:
  selector:
    matchLabels:
      app: blue-green-deployment
      version: v1.16
  replicas: 3
  template:
    metadata:
      labels:
        app: blue-green-deployment
        version: v1.16
    spec:
      containers:
      - name: my-webserver
        image: docker.io/alicek106/rr-test:echo-hostname
      imagePullSecrets:
      - name: regcred
```

```powershell
vagrant@master-node:~$ kubectl apply -f sample-deployment-blue.yaml
deployment.apps/sample-deployment-blue created

vagrant@master-node:~$ kubectl get pod --show-labels
NAME                                      READY   STATUS    RESTARTS   AGE   LABELS
sample-deployment-blue-7774598f45-brfzh   1/1     Running   0          12s   app=blue-green-deployment,pod-template-hash=7774598f45,version=v1.16
sample-deployment-blue-7774598f45-dtw7z   1/1     Running   0          12s   app=blue-green-deployment,pod-template-hash=7774598f45,version=v1.16
sample-deployment-blue-7774598f45-xg72k   1/1     Running   0          12s   app=blue-green-deployment,pod-template-hash=7774598f45,version=v1.16
```

****#2 서비스 생성****

```powershell
/home/vagrant/sample-service-nodeport.yaml
apiVersion: v1
kind: Service
metadata:
  name: blue-green-service
spec:
  type: NodePort
  ports:
  - name: http
    port: 8080
    targetPort: 80
  selector:
    app: blue-green-deployment
    version: v1.16
```

```powershell
vagrant@master-node:~$ kubectl apply -f sample-service-nodeport.yaml
service/blue-green-service created

vagrant@master-node:~$ kubectl get service
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
blue-green-service   NodePort    172.17.4.186    <none>        8080:30038/TCP   22s
kubernetes           ClusterIP   172.17.0.1      <none>        443/TCP          6d16h
```

****#3 노드 IP와 노드 PORT로 서비스 동작을 확인****

```powershell
vagrant@master-node:~$ wget -q -O - http://10.0.0.10:30038 | grep Hello
                                           ~~~~~~~~~ ~~~~~
                                            |         +-- NodePort 서비스로 접속할 수 있는 공개 포트  
                                            +-- 마스터 노드의 IP 주소 (node01, node02 노드의 IP 주소를 사용해도 동일)

vagrant@master-node:~$ wget -q -O - http://10.0.0.10:30038 | grep Hello			⇐ 마스터 노드에서 
        <p>Hello,  sample-deployment-blue-7774598f45-dtw7z</p>  </blockquote>
vagrant@master-node:~$ wget -q -O - http://10.0.0.10:30038 | grep Hello
        <p>Hello,  sample-deployment-blue-7774598f45-brfzh</p>  </blockquote>
vagrant@master-node:~$ wget -q -O - http://10.0.0.10:30038 | grep Hello
        <p>Hello,  sample-deployment-blue-7774598f45-xg72k</p>  </blockquote>
```

클러스터 외부에서는 클러스터 IP로 접근할 수 없음

****#4 GREEN 디플로이먼트를 생성****

```powershell
/home/vagrant/sample-deployment-green.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-deployment-green		<<<
spec:
  selector:
    matchLabels:
      app: blue-green-deployment
      version: v1.17			<<<
  replicas: 3
  template:
    metadata:
      labels:
        app: blue-green-deployment
        version: v1.17			<<<
    spec:
      containers:
      - name: my-webserver
        image: docker.io/alicek106/rr-test:echo-hostname
      imagePullSecrets:
      - name: regcred
```

```powershell
vagrant@master-node:~$ kubectl apply -f sample-deployment-green.yaml
deployment.apps/sample-deployment-green created

vagrant@master-node:~$ kubectl get pod
NAME                                       READY   STATUS    RESTARTS   AGE   IP              NODE            NOMINATED NODE   READINESS GATES
sample-deployment-blue-7774598f45-brfzh    1/1     Running   0          18m   172.16.158.28   worker-node02   <none>           <none>
sample-deployment-blue-7774598f45-dtw7z    1/1     Running   0          18m   172.16.87.222   worker-node01   <none>           <none>
sample-deployment-blue-7774598f45-xg72k    1/1     Running   0          18m   172.16.158.30   worker-node02   <none>           <none>
sample-deployment-green-7774598f45-6pr8q   1/1     Running   0          98s   172.16.87.220   worker-node01   <none>           <none>
sample-deployment-green-7774598f45-r9xsm   1/1     Running   0          98s   172.16.158.26   worker-node02   <none>           <none>
sample-deployment-green-7774598f45-z55xk   1/1     Running   0          98s   172.16.87.219   worker-node01   <none>           <none>
```

두 가지 버전의 애플리케이션이 함께 실행되고 있으며,

BLUE 버전은 NodePort 서비스를 통해서 외부에 노출되어 있고, GREEN 버전은 내부에서만 접근이 가능

### **#5 BLUE 버전에서 GREEN 버전으로 서비스 대상을 변경**

/home/vagrant/**sample-service-nodeport.yaml 수정**

```powershell
apiVersion: v1
kind: Service
metadata:
  name: blue-green-service
spec:
  type: NodePort
  ports:
  - name: http
    port: 8080
    targetPort: 80
  selector:
    app: blue-green-deployment
    version: v1.17
```

```powershell
vagrant@master-node:~$ kubectl apply -f sample-service-nodeport.yaml
service/blue-green-service configured

vagrant@master-node:~$ kubectl get service
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
blue-green-service   NodePort    172.17.4.186    <none>        8080:30038/TCP   17m	⇐ 공개 포트가 그대로 유지
kubernetes           ClusterIP   172.17.0.1      <none>        443/TCP          6d16h     → 클라이언트는 
       동일한 방식으로 이용
```

****#6 노드 IP와 PORT로 서비스를 확인 ⇒ GREEN 버전의 애플리케이션이 응답하는 것을 확인****
****을 확인****

```powershell
vagrant@master-node:~$ wget -q -O - http://10.0.0.10:30038 | grep Hello			
        <p>Hello,  sample-deployment-green-7774598f45-6pr8q</p>  </blockquote>
vagrant@master-node:~$ wget -q -O - http://10.0.0.10:30038 | grep Hello
        <p>Hello,  sample-deployment-green-7774598f45-r9xsm</p>  </blockquote>
vagrant@master-node:~$ wget -q -O - http://10.0.0.10:30038 | grep Hello
        <p>Hello,  sample-deployment-green-7774598f45-z55xk</p>  </blockquote>
```

