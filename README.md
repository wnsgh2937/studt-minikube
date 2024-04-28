# Minikube study for k8s understand

## 명령어
- brew install minikube
- minikube start
- minikube stop



## Default 
## Cluster와 Node
![alt text](resource/cluster.png)

### Pod
- coredns
    - 내부 서비스 / 외부 도메인 서비스 디스커버리
- etcd
    - 각종 키/밸류 저장소 (configmap, secret)
- apiserver
    - kubectl 받는데 
- controller manager
    - Deploy 관리
- kube proxy
    - 타 노드와의 통신
- kube scheduler
    - 자원 관리
- storage provisoner
    - 잘 모르겠지만, 로컬 스토리지 관리같음.

### ConfigMap
-  kube-root-ca.crt
    - ns: default, kube-node-lease, kube-public, kube-system
    - 안전한 통신을 위한 인증서 ( 특히 kubectl )
- cluster-info
- coredns
- extension-apiserver-authentication
- kube-apiserver-legacy-service-account-token-tracking
- kube-proxy
- kubeadm-config
- kubelet-config


### Service
- kubernetes
- kube-dns

### Service Account
- 쿠버네티스 프로세스의 권한(RBAC, 권한기반) 관리
- 사용자( User Account ) 와는 다름

- SA 관련해서 여러 설정들이 있음.
- Cluster Role, Role, Cluster Role Binding 등등

간단히 생각해보면, 어떤 리소스 접근 권한을 만들고, 그 권한을 Service Account에 부여를 한다.
어떤 리소스 접근 권한 -> Role, ClusterRole
- Role = 한 namespace내의 권한
- ClusterRole = 한 Cluster 전체의 권한( 그러니까 속해있지 않는 네임스페이스에서도 권한이 유효하다. )

RoleBinding -> Role을 SA에 바인딩 시킴
CluterRoleBinding -> ClusterRole을 SA에 바인딩 시킴

그러면 어떤 권한이 있냐? 간단하다.
"Resource"의 "CRUD" 권한이다.

어떤 Deployment를 생각해보면 어떤 POD를 CREATE하고 DELETE할 권한이 필요하다.

그러면 이렇게 진행하면 된다.
POD를 Create,Delete할 Role 생성
Deployment가 사용할 SA 생성후, RoleBinding
끝.

그리고 뭐, 모든 POD의 로그를 수집해야한다.
POD를 Read할 ClusterRole 생성 ( 아마 Watch 권한일거임. )
로그 수집 POD에 쓸 SA를 만들고, ClusterRoleBinding

이런식이다.

### Custom Resource Definition
그니까 예를들어서, 어떤 쿠버네티스 규격외 리소스가 필요할때, 예를들어서,, 모든 파드의 로그를 감시하는 리소스라고 하면
pod-watcher 이런 리소스가 필요하다고 했을때,

API 서버 한테 kind: pod-watcher yaml 던져주면
API 서버는 이게 뭔데 씹덕아 하면서 뱉을거임.

그때, pod-wathcer는 이런거야. 하면서 알려주는게 CRD임.

### Ingress, Ingress Controller

Ingress는 L7 로드밸런서이다.
근데 주의해야할 점은, 절대. 물리 리소스가 아니라는 것.
라우팅 규칙들의 모음이다.

Ingress Controller는 Ingress가 적용될 물리 리소스이다.

nginx ingress controller 설치
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.1/deploy/static/provider/cloud/deploy.yaml
뭐가 많이 생긴다.
```
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
configmap/ingress-nginx-controller created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
service/ingress-nginx-controller-admission created
service/ingress-nginx-controller created
deployment.apps/ingress-nginx-controller created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
serviceaccount/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
```
안에 pod 리소스를 보면, 
ingress-nginx               ingress-nginx-controller-69dc6c48ff-7j8s4

인그레스 컨트롤러, 그러니까 물리 L7 LoadBalancer가 생긴 것을 확인할 수 있다. 