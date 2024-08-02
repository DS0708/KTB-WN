# KTB 클라우드 과정 필기 노트 / 7월 9일 (7일차) - Introduction to Cloud 2

## 앞으로 우리가 배우고 알게 될 내용
1. Docker
2. Kubernetes
3. AWS
4. CI/CD
5. IaC
6. Monitoring


## 현업에서 클라우드 팀?은 어떻게 일을 할까?
- 빌드 환경 구축
- 배포 환경 구축
- 권한 관리
- 모니터링
- 자동화
- 장애 대응
- 보안
- 인프라 운영
- 성능/비용 최적화

## 프로젝트 진행하기
- 우리들은 회사가 아닌 팀 프로젝트 위주로 진행되고 있기 때문에 당연히 이 모든걸 알고 가기 어려움
- 프로젝트 마다 개발, 설계 된 내용들이 있기 때문에 우리는 공통적으로 아래의 항목들에 대해서 협업이나 주도적으로 진행이 필요
    - 빌드에서 부터 배포까지
    - 로깅시스템
    - 장애대응
    - 서버구축
    - Dockerfile 작성 및 최적화
    - 새로운 기능에 대한 리서치 및 테스트


## 클라우드가 지원하는 프로젝트 범위?
- 클라우드 팀이 다른 팀과 협업하면서 개발도 진행하고 배포가 필요한가 ?
    - 개발자는 개발된 코드가 어떻게 배포 되고 운영이 어떻게 되는 지 알아야 함
    - 클라우드 팀도 동일하게 개발이 어떻게 되고 있고, 운영하는 성능에 문제가 될 만한 쿼리나 구조가 존재하지 않는 지 확인은 필요

## 프로젝트 과정에 필요한 클라우드
- X 목적을 통하여, Y 서비스를 개발하고 운영
    - 운영전까지 클라우드팀에서 해야 할 것들은 무엇이 있을까?
    - 여기서 초기 과정에 클라우드는 어떤 것들을 해야할까??

## 고민해야 할 사항
- 어떤 서버에서 운영할 것인가?
    - 운영 서버
        - Debian
        - Redhat
        - Window
    - 데이터 베이스
        - RDBMS
        - NoSQL
    - 프록시 서버
    - 로그 시스템
    - 언어 별 웹 프레임워크

- 기능들은 무엇이 있고 어떻게 지원해야 할까?
    - 설계 된 내용 중 아래의 기능이 필요?
        - 운영하는 서버에 https를 바로 설정하지 않거나, 캐시 등을 빠르게 지원이 필요하다?
        -> ReverseProxy 세팅 준비
        - 개발 단계에 대해서 console.log, print 등 stdout 처리?
        -> 간단한 로깅 라이브러리 구현하여, 관리할 수 있도록 지원
        - 빌드, 배포, 장애대응을 대비하여 알람이 필요?
        -> discord hook 기능을 이용하여, 알림을 받을 수 있도록 지원


## 우리가 지금부터 해야할 내용
- 팀 프로젝트의 주제에 대해 설계 및 기획된 내용 검토
    - 개발 과정에 필요한 내용이 무엇인 지 확인
    - 개발 완료 된 후 운영에 어떤것들이 필요한 것인지 확인
    - 자동화 가능한 것들은 없는지 확인
- 모든 순간 협업을 할 수는 없음
    - 각 팀들끼리 모여서 프로젝트가 진행되기 때문에 서로 같은 목표에 대해서 논의하여, 준비
    -> 풀스텍은 개발, 클라우드는 이후 운영 준비를 위한 대비 등

## 실습 1
- 현재 진행중인 프로젝트에 대하여 설계된 내용 리뷰
    - 클라우드팀에서 해당 프로젝트에 어떤 것부터 지원해야하는 지 정리
    - 개발팀과 논의 후 빠르게 지원이 되었어야하는 내용들에 대한 정리
        - 로깅시스템
        - 디비 구축
        - 빌드 및 테스트 빌드 환경 구축
        - 깃 전략
        - 배포 전략
        - ...
- 지원해야하는 기능들에 대한 공부
    - Build -> Docker, Container, ...
    - 알림 -> discord hook, Logging, ...
    - Linux -> Crontab, Systemd


------

# Docker  6 강

## Container는 무엇일까?
- Docker를 공부하기 전 Container란 무엇일까?
    - 운영체제 수준에서 CPU, 메모리, 스토리지, 네트워크 등 리소스를 쉽게 공유할 수 있음
        - 운영체제에서 프로세스를 격리하여 별도의 실행 환경을 제공

        - Traditional Deployment
        ```
        App App App
            OS
          Hardware
        ```

        - Virtualized Deployment
        ```
        App     App   App     App
        Bin/Library   Bin/Library
        OS            OS
        Vm            Vm
             Hypervisor
                 OS
               Hardware
        ```

        - Container Deployment
        ```
        App
        Bin/Library
        container     container      container
                Container Runtime
                      OS
                   Hardware
        ```

- Container 기능?
    - Container는 어떻게 하나의 운영체제위에 여러가지 어플리케이션을 배포할 수 있을까?
        - ControlGroup
        - Namespace
        - Network
    - 빌드, 실행, 네트워크 관리 등 다양한 기능 존재

- 장점
    - 가벼움
    - 유지관리
    - 다양한 환경지원
    - 이식 가능성

## Container vs VirtualMachine
<img src="../img/0709-1.png">

## Docker 탄생 배경
- 기존에 있던 어떤 문제점들이 존재했기 때문에 도커가 탄생하게 되었을까?
- 우리는 왜 도커를 사용할까?
    - 환경에서의 이점
        - 다양한 Image 지원
        - container의 복잡성을 간단하게 사용할 수 있게 지원
    - 자원 관리
        - VM 대비 확장 및 축소의 편리함
        - 프로세스 및 container 별 리소스 제한
    - 독립된 환경에서 작업
        - 격리된 공간에서 애플리케이션 운영
        - 논리적으로 설정된 프로세스 격리

## Docker 내부 구조
- 현재까지 Container에 대한 개념을 잡았고, Docker는 결국 Container를 효율적으로 관리할 수 있도록 지원
- 그럼 어떻게 효율적으로 관리할 수 있는 걸까?

## Docker 3일차 이후에 배울 내용
- Union File System
    - OverlayFileSystem
        - Lower Dir
        - Upper Dir
        - Merge Dir
    - CRI? OCI?
    - Docker가 Container를 배포(운영)하는 방식















