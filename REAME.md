# Minikube study for k8s understand

## 명령어
- brew install minikube
- minikube start
- minikube stop

## Default 
Pod
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

ConfigMap
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


Service
- kubernetes
- kube-dns

Service Account
![alt text](resource/image.png)

