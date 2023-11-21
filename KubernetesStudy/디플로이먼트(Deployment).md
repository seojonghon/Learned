# **디플로이먼트(Deployment)**

레플리카셋, 파드의 배포를 관리

애플리케이션의 **배포와 업데이트**를 편하게 하기 위해서 사용

쿠버네티스에서 **상태가 없는(stateless) 애플리케이션을 배포**할 때 사용하는 가장 기본적인 컨트롤러

디플로이먼트는 스케일, 롤아웃, 롤백, 자동복구 기능이 있음


|       |             +--  노드 수준에서 장애가 발생했을 때 파드를 복구하는 것이 가능

|       +--  서비스를 유지하면서 파드를 교체

+-- 파드의 개수를 늘리거나 줄일 수 있음

## **디플로이먼트 생성 및 삭제**

https://kubernetes.io/ko/docs/concepts/workloads/controllers/deployment/

### **#1 디플로이먼트 생성**

```powershell
/home/vagrant/deployment-nginx.yaml
```

```powershell
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      name: my-nginx-pod
      labels:
        app: my-nginx
    spec:
      containers:
      - name: nginx
        image: docker.io/nginx
        ports:
        - containerPort: 80
      imagePullSecrets:
      - name: regcred
```

```powershell
vagrant@master-node:~$ kubectl apply -f deployment-nginx.yaml

deployment.apps/my-nginx-deployment created
```

### **#2 디플로이먼트, 레플리카셋, 파드 생성을 확인**

```powershell
vagrant@master-node:~$ kubectl get deployments,replicasets,pods
NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-nginx-deployment   3/3     3            3           40s
                                      ~~~~~   ~~~~~~~~~~   ~~~~~~~~~   ~~~~
                                      |       |            |           +--  파드가 실행되고 있는 지속 시간
                                      |       |            +-- 서비스 가능한 파드의 개수 
                                      |       +-- 최신 상태로 업데이트된 파드의 개수  
                                      +-- 파드의 개수        

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-deployment-66bcdb4565   3         3         3       40s

NAME                                       READY   STATUS    RESTARTS   AGE
pod/my-nginx-deployment-66bcdb4565-4k4lx   1/1     Running   0          39s
pod/my-nginx-deployment-66bcdb4565-59shk   1/1     Running   0          39s
pod/my-nginx-deployment-66bcdb4565-lqdlp   1/1     Running   0          39s
```

****#3 디플로이먼트를 삭제 → 레플리카셋, 파드 또한 함께 삭제되는 것을 확인****

```powershell
vagrant@master-node:~$ kubectl delete deployment my-nginx-deployment
deployment.apps "my-nginx-deployment" deleted

vagrant@master-node:~$ kubectl get deployments,replicasets,pods
No resources found in default namespace.
```

## **디플로이먼트를 사용하는 이유 1) 스케일**

레플리카의 값을 변경해서 파드의 개수를 조절 → 처리 능력을 높이고 낮추는 기능

파드의 개수를 늘리는 중에 쿠버네티스 클러스터의 자원(CPU, 메모리, ...)이 부족해지면 ... 노드를 추가하여 자원이 생길 때까지 파드 생성을 보류

### **#1 레플리카 값을 3으로 설정해서 디플로이먼트를 생성**

```powershell
/home/vagrant/web-deploy-replicas-3.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
      - name: nginx
        image: docker.io/nginx
        ports:
        - containerPort: 80
      imagePullSecrets:
      - name: regcred
```

디플로이먼트를 이용해 래플리카3개 생성

```powershell
vagrant@master-node:~$ kubectl apply -f web-deploy-replicas-3.yaml
deployment.apps/web-deploy created

vagrant@master-node:~$ kubectl get deploy,rs,po -o wide
NAME                         READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES            SELECTOR
deployment.apps/web-deploy   3/3     3            3           16s   nginx        docker.io/nginx   app=web

NAME                                    DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES            SELECTOR
replicaset.apps/web-deploy-6dc9946879   3         3         3       16s   nginx        docker.io/nginx   app=web,pod-template-hash=6dc9946879

NAME                              READY   STATUS    RESTARTS   AGE   IP              NODE            NOMINATED NODE   READINESS GATES
pod/web-deploy-6dc9946879-4pq7x   1/1     Running   0          16s   172.16.158.34   worker-node02   <none>           <none>
pod/web-deploy-6dc9946879-76tq5   1/1     Running   0          16s   172.16.158.35   worker-node02   <none>           <none>
pod/web-deploy-6dc9946879-gw7r7   1/1     Running   0          16s   172.16.87.205   worker-node01   <none>           <none>
```

### **#2 레플리카 값을 5로 변경해서 적용**

```powershell
vagrant@master-node:~$ cp web-deploy-replicas-3.yaml web-deploy-replicas-5.yaml
```

```powershell
spec:
replicas: 5				<<<<<<< 3에서 5로 변경
```

```powershell
vagrant@master-node:~$ kubectl apply -f web-deploy-replicas-5.yaml
deployment.apps/web-deploy configured

vagrant@master-node:~$ kubectl get deploy,rs,po -o wide
NAME                         READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES            SELECTOR
deployment.apps/web-deploy   5/5     5            5           2m59s   nginx        docker.io/nginx   app=web

NAME                                    DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES            SELECTOR
replicaset.apps/web-deploy-6dc9946879   5         5         5       2m59s   nginx        docker.io/nginx   app=web,pod-template-hash=6dc9946879

NAME                              READY   STATUS    RESTARTS   AGE     IP              NODE            NOMINATED NODE   READINESS GATES
pod/web-deploy-6dc9946879-4pq7x   1/1     Running   0          2m59s   172.16.158.34   worker-node02   <none>           <none>
pod/web-deploy-6dc9946879-76tq5   1/1     Running   0          2m59s   172.16.158.35   worker-node02   <none>           <none>
pod/web-deploy-6dc9946879-gw7r7   1/1     Running   0          2m59s   172.16.87.205   worker-node01   <none>           <none>
pod/web-deploy-6dc9946879-m55t8   1/1     Running   0          20s     172.16.158.36   worker-node02   <none>           <none>
pod/web-deploy-6dc9946879-v9cg6   1/1     Running   0          20s     172.16.87.206   worker-node01   <none>           <none>
```

**#3 kubectl scale 명령으로 레플리카 값을 변경**

```powershell
vagrant@master-node:~$ kubectl scale deployments web-deploy --replicas=10
deployment.apps/web-deploy scaled
```

```powershell
vagrant@master-node:~$ kubectl get deploy,rs,po -o wide
NAME                         READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES            SELECTOR
deployment.apps/web-deploy   10/10   10           10          4m36s   nginx        docker.io/nginx   app=web

NAME                                    DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES            SELECTOR
replicaset.apps/web-deploy-6dc9946879   10        10        10      4m36s   nginx        docker.io/nginx   app=web,pod-template-hash=6dc9946879

NAME                              READY   STATUS    RESTARTS   AGE     IP              NODE            NOMINATED NODE   READINESS GATES
pod/web-deploy-6dc9946879-4pq7x   1/1     Running   0          4m36s   172.16.158.34   worker-node02   <none>           <none>
pod/web-deploy-6dc9946879-76tq5   1/1     Running   0          4m36s   172.16.158.35   worker-node02   <none>           <none>
pod/web-deploy-6dc9946879-7qqzg   1/1     Running   0          19s     172.16.87.209   worker-node01   <none>           <none>
pod/web-deploy-6dc9946879-b8j8r   1/1     Running   0          19s     172.16.87.207   worker-node01   <none>           <none>
pod/web-deploy-6dc9946879-gw7r7   1/1     Running   0          4m36s   172.16.87.205   worker-node01   <none>           <none>
pod/web-deploy-6dc9946879-khtx9   1/1     Running   0          19s     172.16.87.208   worker-node01   <none>           <none>
pod/web-deploy-6dc9946879-m55t8   1/1     Running   0          117s    172.16.158.36   worker-node02   <none>           <none>
pod/web-deploy-6dc9946879-rt6bd   1/1     Running   0          19s     172.16.158.37   worker-node02   <none>           <none>
pod/web-deploy-6dc9946879-s5k95   1/1     Running   0          19s     172.16.158.38   worker-node02   <none>           <none>
pod/web-deploy-6dc9946879-v9cg6   1/1     Running   0          117s    172.16.87.206   worker-node01   <none>           <none>
```

```powershell
vagrant@master-node:~$ kubectl delete deployment web-deploy
deployment.apps "web-deploy" deleted
```

## **디플로이먼트를 사용하는 이유 2) 롤아웃, 롤백**

애플리케이션을 업데이트할 때 레플리카셋의 변경 사항을 저장하는 **리비전(revision)을 남겨 롤백을 가능**하게 해 주고, 무중단 서비스를 위해 **파드의 롤링 업데이터 전략을 지정**할 수 있음

****#1 --record 옵션을 추가해 디플로이먼트를 생성****

```powershell
vagrant@master-node:~$ kubectl apply -f deployment-nginx.yaml --record
Flag --record has been deprecated, --record will be removed in the future
deployment.apps/my-nginx-deployment created
```

```powershell
vagrant@master-node:~$ kubectl get deploy,rs,po -o wide
NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES            SELECTOR
deployment.apps/my-nginx-deployment   3/3     3            3           31s   nginx        docker.io/nginx   app=my-nginx

NAME                                             DESIRED   CURRENT   READY   AGE   CONTAINERS   IMAGES            SELECTOR
replicaset.apps/my-nginx-deployment-66bcdb4565   3         3         3       31s   nginx        docker.io/nginx   app=my-nginx,pod-template-hash=66bcdb4565

NAME                                       READY   STATUS    RESTARTS   AGE   IP              NODE            NOMINATED NODE   READINESS GATES
pod/my-nginx-deployment-66bcdb4565-7gf4q   1/1     Running   0          31s   172.16.158.40   worker-node02   <none>           <none>
pod/my-nginx-deployment-66bcdb4565-kbl6r   1/1     Running   0          31s   172.16.87.210   worker-node01   <none>           <none>
pod/my-nginx-deployment-66bcdb4565-kfscs   1/1     Running   0          31s   172.16.158.39   worker-node02   <none>           <none>
```

****#2 kubectl set image 명령으로 파드의 이미지를 변경****

```powershell
vagrant@master-node:~$ kubectl set image deployments my-nginx-deployment nginx=docker.io/nginx:1.11 --reco
rd
Flag --record has been deprecated, --record will be removed in the future
deployment.apps/my-nginx-deployment image updated

vagrant@master-node:~$ kubectl get deploy,rs,po -o wide
NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES                 SELECTOR
deployment.apps/my-nginx-deployment   3/3     1            3           2m36s   nginx        docker.io/nginx:1.11   app=my-nginx

NAME                                             DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES                 SELECTOR
replicaset.apps/my-nginx-deployment-595b6754f6   1         1         0       8s      nginx        docker.io/nginx:1.11   app=my-nginx,pod-template-hash=595b6754f6
replicaset.apps/my-nginx-deployment-66bcdb4565   3         3         3       2m36s   nginx        docker.io/nginx        app=my-nginx,pod-template-hash=66bcdb4565

NAME                                       READY   STATUS              RESTARTS   AGE     IP              NODE            NOMINATED NODE   READINESS GATES
pod/my-nginx-deployment-595b6754f6-5xq95   0/1     ContainerCreating   0          8s      <none>          worker-node02   <none>           <none>
pod/my-nginx-deployment-66bcdb4565-7gf4q   1/1     Running             0          2m36s   172.16.158.40   worker-node02   <none>           <none>
pod/my-nginx-deployment-66bcdb4565-kbl6r   1/1     Running             0          2m36s   172.16.87.210   worker-node01   <none>           <none>
pod/my-nginx-deployment-66bcdb4565-kfscs   1/1     Running             0          2m36s   172.16.158.39   worker-node02   <none>           <none>
```

결과적으로 3개의 래플리카셋이 롤링방식으로  업데이트 됨

```powershell
vagrant@master-node:~$ kubectl get deploy,rs,po -o wide
NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES                 SELECTOR
deployment.apps/my-nginx-deployment   3/3     3            3           6m45s   nginx        docker.io/nginx:1.11   app=my-nginx

NAME                                             DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES                 SELECTOR
replicaset.apps/my-nginx-deployment-595b6754f6   3         3         3       4m17s   nginx        docker.io/nginx:1.11   app=my-nginx,pod-template-hash=595b6754f6
replicaset.apps/my-nginx-deployment-66bcdb4565   0         0         0       6m45s   nginx        docker.io/nginx        app=my-nginx,pod-template-hash=66bcdb4565

NAME                                       READY   STATUS    RESTARTS   AGE     IP              NODE            NOMINATED NODE   READINESS GATES
pod/my-nginx-deployment-595b6754f6-2jj82   1/1     Running   0          3m9s    172.16.158.42   worker-node02   <none>           <none>
pod/my-nginx-deployment-595b6754f6-5xq95   1/1     Running   0          4m17s   172.16.158.41   worker-node02   <none>           <none>
pod/my-nginx-deployment-595b6754f6-sj8kt   1/1     Running   0          3m44s   172.16.87.211   worker-node01   <none>           <none>
```

****가상머신 실행 및 동작 확인****

```powershell
c:\kubernetes\vagrant-kubeadm-kubernetes> vagrant up
c:\kubernetes\vagrant-kubeadm-kubernetes> vagrant status
Current machine states:
master                    running (virtualbox)
node01                    running (virtualbox)
node02                    running (virtualbox)
c:\kubernetes\vagrant-kubeadm-kubernetes> vagrant ssh master

vagrant@master-node:~$ kubectl get node
NAME            STATUS   ROLES           AGE     VERSION
master-node     Ready    control-plane   4d14h   v1.27.1
worker-node01   Ready    worker          4d14h   v1.27.1
worker-node02   Ready    worker          4d14h   v1.27.1
```

전에 **생성한 deplyment가 존재하는지 확인**

```powershell
vagrant@master-node:~$ kubectl get all				⇐  생성한 deplyment가 존재하는지 확인
NAME                                       READY   STATUS    RESTARTS   AGE
pod/my-nginx-deployment-595b6754f6-2jj82   1/1     Running   1          3d16h
pod/my-nginx-deployment-595b6754f6-5xq95   1/1     Running   1          3d16h
pod/my-nginx-deployment-595b6754f6-sj8kt   1/1     Running   1          3d16h

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.17.0.1   <none>        443/TCP   4d14h

NAME                                  READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-nginx-deployment   3/3     3            3           3d16h

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-deployment-595b6754f6   3         3         3       3d16h
replicaset.apps/my-nginx-deployment-66bcdb4565   0         0         0       3d16h
```

### **#3 리비전 정보 확인**

- -recode=true 옵션으로 디플로이먼트를 변경하면 변경 사항을 기록하여 해당 버전의 레플리카셋을 보존할 수 있음

```powershell
vagrant@master-node:~$ kubectl rollout history deployment my-nginx-deployment
deployment.apps/my-nginx-deployment
REVISION  CHANGE-CAUSE
1         kubectl apply --filename=deployment-nginx.yaml --record=true
2         kubectl set image deployments my-nginx-deployment nginx=docker.io/nginx:1.11 --record=true
```

### **#4 이전 버전의 레플리카셋으로 롤백**

```powershell
vagrant@master-node:~$ kubectl rollout undo deployment my-nginx-deployment --to-revision=1
deployment.apps/my-nginx-deployment rolled back
```

```powershell
vagrant@master-node:~$ kubectl get rs,pod
NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-deployment-595b6754f6   1         1         1       3d17h	⇐ 두번째 생성한 레플리카셋
replicaset.apps/my-nginx-deployment-66bcdb4565   3         3         2       3d17h	⇐ 처음 생성한 레플리카셋
```

```powershell
vagrant@master-node:~$ kubectl get rs,pod
NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-deployment-595b6754f6   1         1         1       3d17h	⇐ 두번째 생성한 레플리카셋
replicaset.apps/my-nginx-deployment-66bcdb4565   3         3         2       3d17h	⇐ 처음 생성한 레플리카셋

NAME                                       READY   STATUS    RESTARTS   AGE
pod/my-nginx-deployment-66bcdb4565-4g99d   1/1     Running   0          10s
pod/my-nginx-deployment-66bcdb4565-8w4mz   1/1     Running   0          15s
pod/my-nginx-deployment-66bcdb4565-lwr8d   1/1     Running   0          19s
```

### **#5 디플로이먼트 상세 정보 출력**

```powershell
vagrant@master-node:~$ kubectl describe deployment my-nginx-deployment

Name:                   my-nginx-deployment
Namespace:              default
CreationTimestamp:      Fri, 06 Oct 2023 06:42:57 +0000
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 3
.....
OldReplicaSets:  my-nginx-deployment-595b6754f6 (0/0 replicas created)
NewReplicaSet:   my-nginx-deployment-66bcdb4565 (3/3 replicas created)
```

### **#6 스케일 변경 및 롤백**

```powershell
vagrant@master-node:~$ kubectl scale --replicas=10 deployment my-nginx-deployment --record=true
Flag --record has been deprecated, --record will be removed in the future
deployment.apps/my-nginx-deployment scaled
```

```powershell
vagrant@master-node:~$ kubectl get all

NAME                                       READY   STATUS    RESTARTS   AGE
pod/my-nginx-deployment-66bcdb4565-4g99d   1/1     Running   0          7m40s
pod/my-nginx-deployment-66bcdb4565-4sm5q   1/1     Running   0          19s
pod/my-nginx-deployment-66bcdb4565-8w4mz   1/1     Running   0          7m45s
pod/my-nginx-deployment-66bcdb4565-glnvl   1/1     Running   0          18s
pod/my-nginx-deployment-66bcdb4565-j6k8b   1/1     Running   0          18s
pod/my-nginx-deployment-66bcdb4565-lwr8d   1/1     Running   0          7m49s
pod/my-nginx-deployment-66bcdb4565-n8g8j   1/1     Running   0          18s
pod/my-nginx-deployment-66bcdb4565-r69wd   1/1     Running   0          18s
pod/my-nginx-deployment-66bcdb4565-t55b7   1/1     Running   0          19s
pod/my-nginx-deployment-66bcdb4565-tf7n7   1/1     Running   0          19s
...
NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/my-nginx-deployment-595b6754f6   0         0         0       3d17h
replicaset.apps/my-nginx-deployment-66bcdb4565   10        10        10      3d17h⇐ 스케일을 변경해도
																																									   레플리카셋은 그대로 유지
```

스케일을 변경해도 레플리카셋은 그대로 유지