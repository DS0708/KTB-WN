# KTB 7월 22일 필기노트 - K8s


## Kubernetes(K8s)
- 컨테이너화된 워크로드와 서비스를 관리하기 위한 이식성 있고, 확장가능한 오픈소스 플랫폼

- 서비스 디스커버리와 로드 밸런싱
    - 쿠버네티스는 DNS 이름을 사용하거나, 자체 IP 주소를 사용하여 컨테이너를 노출할 수 있음
    - 컨테이너에 대한 트래픽이 많으면, 로드밸런싱하고 배포하여 안정적으로 운영 가능
- 스토리지 오케스트레이션
    - 쿠버네티스를 사용하면 로컬 저장소, 클라우드 공급자 등과 같이 원하는 저장소 시스템 사용 가능
- 자동화된 롤아웃과 롤백
    - 배포된 컨테이너의 원하는 상태를 서술할 수 있으며, 현재 상태를 원하는 상태로 변경

- 자동화된 빈 패킹
    - 컨테이너가 필요로하는 CPU와 Memory를 쿠버네티스에게 지시
        - 쿠버네티스는 컨테이너를 노드에 맞추어서 리소스를 가장 잘 사용할 수 있도록 지원
- 자동화 된 복구
    - 실패한 컨테이너를 다시 시작하고, 교체하며, 사용자가 정의한 상태 이외에는 지속적으로 복구 가능
- 시크릿과 구성관리
    - 암호, OAuth 토큰 및 SSH 키와 같은 중요한 정보를 저장하고 관리
    - 컨테이너 이미지를 재구성하지 않고, 시크릿 및 애플리케이션 구성을 배포 및 업데이트 가능

## Kubernetes 공부법
- 공식페이지
- 자격증
- 프로젝트


## Kubernetes Workload
- Pod
    - 쿠버네티스에서 생성하고 관리할 수 있는 배포 가능한 가장 작은 단위
    - 하나 이상의 컨테이너 그룹
    ```
    apiVersion: v1
    kind: Pod
    metadata:
        name: nginx
    spec ~~
    ```

- Replicaset
    - 레플리카 파드 지합의 실행을 항상 안정적으로 유지
        - 명시된 파드의 개수에 대한 가용성을 보증하는데 사용
    - Replicaset의 대안으로 Deployment 사용 권장

- Deployment
    - 파드와 레플리카셋에 대한 선언적 업데이트를 제공
    - 파드 템플릿이 변경된 경우에만 디플로이먼트의 롤아웃이 트리거

- Statefulset
    - Stateful을 관리하는 데 사용하는 워크로드
    - 파드들의 순서 및 고유성을 보장
    - Deploymment와는 다르게, Statefulset은 독자성을 유지

- Statefulset 권장 환경
    - 안정된, 고유한 네트워크 식별자
    - 안정된, 지속성을 갖는 스토리지
    - 순차적인, 정상 배포(gracefule deploymnet)와 스케일링
    - 순차적인, 자동 롤링 업데이트

- 코드 참고 및 실습
    - PV & PVC 확인 후 실습 진행

- Daemonset
    - 모든(또는 일부) 노드가 파드의 사본을 실행
        - 노드가 클러스터에 추가되면 파드도 추가

- Daemonset 용도
    - 모든 노드에서 클러스터 스토리지 데몬 실행
    - 모든 노드에서 로그 수집 데몬 실행
    - 모든 노드에서 노드 모니터링 데몬 실행

- Job / CronJob
    - Job
        - 하나 이상의 파드를 생성하고, 지정된 수의 파드가 성공적으로 종료될 때 까지 계속해서 파드의 실행을 재시도
        - 여러 파드를 병렬로 실행 가능
    - Cronjob
        - 반복 일정에 따라 Job을 생성
        - CronTab 파일의 한 줄과 같음

- Namespace
    - 클러스터 내에서의 리소스를 논리적으로 분리하는 매커니즘을 제공

- 목적
    - 리소스를 효율적으로 관리 및 운영을 하기 위함
    - 사용자 접근제어(ABAC, RBAC)를 관리하기 위함

- Initial namespaces
    - default
        - 기본적으로 할당되는 namespace
    - kube-system
        - kubernetes system에 의해 생성되는 API 오브젝트들을 관리하기 위한 namespace
    - kube-public
        - 클러스터 내 모든 사용자로부터 접근 가능하고 읽을 수 있는 오브젝트들을 관리
    - kube-node-lease
        - 클러스터 내 노드의 연결 정보를 관리하기 위한 namespace

## Kubectl
- Kubernetes 클러스터를 관리하기 위한 명령어
    - 기본 명령어
        - 클러스터 조회 "kubectl cluster-info"
        - 현재 컨텍스트와 설정 확인 "kubectl config view"
        - 현재 사용중인 컨텍스트 확인 "kubectl config current-context"
    - object 관리
        - 생성 "kubectl create pods", "kubectl apply pods"
        - 확인 "kubectl get pods", "kubectl get namespaces"
        - 삭제 "kubectl get namespaces"
    - debugging
        - Log "Kubectl logs [pod-name]"
        - describe "kubectl describe [object-name]"
        - top "kubectl top pod"

- 자주 사용하는 명령어
    - Pod 내 실행중인 컨테이너 내에서 직접 실행시키는 명령어
        - exec "kubectl exec mypod -i -t -- ls /usr"
    - 외부로 노출시키는 명령어
        - expose "kubectl expose deployment hello-world --type=LoadBalancer --name=my-service'
    - Pod나 작업(배포)을 생성하는 명령어
        - run "kubectl run nginx --image=nginx"
    - 클러스터 내 오브젝트(deployment, pod, service, ..)에 대한 포트를 호스트 노드와 매핑
        - port-forward "kubectl port-forward pod/nginx 80:80"
    - 다른 클러스터에 접근하기 위하여 kube-config 파일을 활용
        - kubeconfig "kubectl --kubeconfig=[configpath]"


## Yaml
- 기본 문법 설명은 따로 참고
- Kubernetes에서 Yaml
    - 배포하고 관리 및 추적을 위하여 yaml 파일로 관리
- 앱을 배포하고 운영할 때 필요에 따라 명령형 접근법 & 선언형 접근법 고려하여 사용
    - 명령형 접근법

## 실습 1
- 쿠버네티스 클러스터 구성
- 목적: 쿠버네티스 클러스터를 구성하여, master node 1, worker node 2개의 환경 구성

1. microk8s install
2. worker-node join - *microk8s add-node 명령어 사용
3. kubectl get nodes

```bash
sudo snap install microk8s --classic --channel=1.30

alias kubectl='microk8s kubectl'

sudo usermod -a -G microk8s covy

sudo chown -R covy ~/.kube

kubectl get pods -A
```

## 실습 2
- 쿠버네티스 오브젝트 배포
- 목적 : 다양한 형태의 쿠버네티스 오브젝트를 배포
- 오브젝트를 사용하여 앱 배포해보기
    1. Pod 오브젝트를 사용하여 앱 배포
        - image: nginx 사용
    2. Deployment 오브젝트를 사용하여 앱 배포
        - replicas: 3
    3. daemonset 오브젝트를 사용하여 앱 배포
        - 데몬셋 특징에 맞게 배포되는 지 확인
    4. cronjob 오브젝트를 사용하여 앱 배포
        - cronjob -> job -> Pod 배포 형식 확인

- 추가 실습
    - 배포 한 앱이 어떤 노드에 배포되었는지 확인
        - 배포한 앱들이 어떤 규칙을 가지고 배포가 되는지 고민

## 실습 3
- 쿠버네티스 제어
- 목적 : kubectl 명령어 익숙해지기
    - 실습2 이후에 실습 가능
        1. kubectl create, apply 명령어 사용
        2. kubectl delete, patch, edit 명령어 사용
        3. kubectl log, describe 명령어 사용
- 추가 실습?
    - 명령어들이 실행되는 차이점 찾아보기
        - create <-> apply
        - log <-> describe
        - patch <-> edit









