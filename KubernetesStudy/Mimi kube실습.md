# Mimi kube실습

# Minikube : 로컬 개발 환경에서 Kubernetes 클러스터를 실행하고 관리하기 위한 도구로, 주로 개발자 및 테스터들이 Kubernetes 애플리케이션을 개발 및 테스트하는 데 사용

# **minikube를 활용한 단일 노드 쿠버네티스 클러스터 구성**

Docker Desktop Kubernetes를 비활성화한 상태에서 실습


설치 방법

https://minikube.sigs.k8s.io/docs/start/

1번 2번 과정을 모두 수행 후 별도의 명령 프롬프트 실행

```powershell
C:\Users\User>minikube
W1004 10:34:40.232649   12676 main.go:291] Unable to resolve the current Docker CLI context "default": context "default": context not found: open C:\Users\User\.docker\contexts\meta\37a8eec1ce19687d132fe29051dca629d164e2c4958ba141d5f4133a33f0688f\meta.json: The system cannot find the path specified.
minikube는 개발 워크플로우에 최적화된 로컬 쿠버네티스를 제공하고 관리합니다.

기본 명령어:
  start            로컬 쿠버네티스 클러스터를 시작합니다
  status           로컬 쿠버네티스 클러스터의 상태를 가져옵니다
  stop             실행 중인 로컬 쿠버네티스 클러스터를 중지합니다
  delete           로컬 쿠버네티스 클러스터를 삭제합니다
  dashboard        minikube 클러스터 내의 쿠버네티스 대시보드에 접근합니다
  pause            쿠버네티스를 잠시 멈춥니다
  unpause          잠시 멈췄던 쿠버네티스를 재개합니다

이미지 명령어
  docker-env       Provides instructions to point your terminal's docker-cli to the Docker Engine inside minikube. (Useful for building docker images directly inside minikube)
  podman-env       Configure environment to use minikube's Podman service
  cache            Manage cache for images
  image            Manage images

환경 설정 및 관리 명령어:
  addons           Enable or disable a minikube addon
  config           Modify persistent configuration values
  profile          Get or list the current profiles (clusters)
  update-context   Update kubeconfig in case of an IP or port change

Networking and Connectivity Commands:
  service          서비스에 연결된 URL을 반환합니다
  tunnel           Connect to LoadBalancer services

고급 명령어:
  mount            특정 디렉토리를 minikube 에 마운트합니다
  ssh              (디버깅을 위해) minikube 환경에 접속합니다
  kubectl          클러스터 버전에 맞는 kubectl 바이너리를 실행합니다
  node             노드를 추가하거나 삭제, 나열합니다
  cp               Copy the specified file into minikube

Troubleshooting Commands:
  ssh-key          Retrieve the ssh identity key path of the specified node
  ssh-host         Retrieve the ssh host key of the specified node
  ip               지정된 노드의 IP 주소를 가져옵니다
  logs             로컬 쿠버네티스 클러스터를 디버그하기 위해 로그를 반환합니다
  update-check     현재 그리고 최신 버전을 출력합니다
  version          minikube 의 버전을 출력합니다
  options          Show a list of global command-line options (applies to all commands).

Other Commands:
  completion       Generate command completion for a shell
  license          Outputs the licenses of dependencies to a directory

Use "minikube <command> --help" for more information about a given command.
```

```powershell
minikube start
.....
.....
* Configuring bridge CNI (Container Networking Interface) ...
* Kubernetes 구성 요소를 확인...
  - Using image gcr.io/k8s-minikube/storage-provisioner:v5
* 애드온 활성화 : default-storageclass
* 끝났습니다! kubectl이 "minikube" 클러스터와 "default" 네임스페이스를 기본적으로 사용하도록 구성 되었습니다.
```

```powershell
C:\Users\User> kubectl get nodes
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   28s   v1.27.4
```

```powershell
C:\Users\User> kubectl get nodes -o wide
NAME       STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION                      CONTAINER-RUNTIME
minikube   Ready    control-plane   48s   v1.27.4   192.168.49.2   <none>        Ubuntu 22.04.2 LTS   5.10.16.3-microsoft-standard-WSL2   docker://24.0.4
```

mini kube 컴포넌트 확인

```powershell
C:\Users\User> kubectl get pods -A
NAMESPACE     NAME                               READY   STATUS    RESTARTS       AGE
kube-system   coredns-5d78c9869d-5kz5q           1/1     Running   0              114s
kube-system   etcd-minikube                      1/1     Running   0              2m7s
kube-system   kube-apiserver-minikube            1/1     Running   0              2m7s
kube-system   kube-controller-manager-minikube   1/1     Running   0              2m7s
kube-system   kube-proxy-ncvt2                   1/1     Running   0              114s
kube-system   kube-scheduler-minikube            1/1     Running   0              2m8s
kube-system   storage-provisioner                1/1     Running   2 (112s ago)   2m5s
```

대시보드 실행

```powershell
C:\Users\User>minikube addons list
| dashboard                   | minikube | disabled     | Kubernetes
```

```powershell
C:\Users\User>minikube dashboard
....
* Verifying dashboard health ...
* 프록시를 시작하는 중 ...
* Verifying proxy health ...
....

```

```powershell
C:\Users\User>kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4

deployment.apps/hello-minikube created
```

![Alt text](image-2.png)

```powershell
kubectl get pod,replicaset,deployment
NAME                                  READY   STATUS    RESTARTS   AGE
pod/hello-minikube-67b54d89dd-f5n5h   1/1     Running   0          58s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-minikube-67b54d89dd   1         1         1       58s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-minikube   1/1     1            1           58s
```

방법 **1 : 클러스터 외부에서 파드로 접근 1. NodePort 타입의 서비스 생성 후 해당 포트로 포트 포워딩**

```powershell
C:\Users\User>kubectl expose deployment hello-minikube --type=NodePort --port=8080
service/hello-minikube exposed

C:\Users\User>kubectl get service -o wide
NAME             TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE     SELECTOR
hello-minikube   NodePort    10.102.142.155   <none>        8080:32236/TCP   13s     app=hello-minikube
kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP          7m24s   <none>
```

포트포워딩

```powershell
C:\Users\User>kubectl port-forward service/hello-minikube 9090:8080
Forwarding from 127.0.0.1:9090 -> 8080
Forwarding from [::1]:9090 -> 8080
Handling connection for 9090
Handling connection for 9090
```



방법 **2 : 클러스터 외부에서 파드로 접근 2. LoadBalancer 타입의 서비스 생성 후 minikube tunnel 실행**