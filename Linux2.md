# KTB 클라우드 과정 필기 노트 / 7월 8일 (6일차) - linux 심화


## 목차
1. Linux의 cgroup
2. Linux의 namespace
3. Linux의 chroot
4. Linux의 유닉스 도메인 소켓(Unix Domain Socket이란?)
5. Linux의 overlayFS란?


## cgroup
- cgroup은 리눅스 커널에서 제공하는 기능으로, 프로세스 그룹에 대한 시스템 리소스 사용을 관리할 수 있게 해준다.
- 이를 통해 특정 프로세스 그룹이 사용할 수 있는 CPU, 메모리, 디스크 I/O, 네트워크 대역폭 등을 제한하거나 할당할 수 있다.

- 주요 기능
    - 리소스 제한, 리소스 우선순위, 리소스 계정, 리소스 격리

- cgroup - 실습
    - 실습 환경 세팅
    ```
    sudo apt-get install cgroup-tools stress
    ```
    - cgroup CPU 제한 실습
    ```
    sudo cgcreate -g cpu:/cpulimit
    echo 50000 | sudo tee /sys/fs/cgroup/cpulimit/cpu.max
    sudo cgexec -g cpu:/cpulimit strees --cpu2 --vm 1 --timeout 30s & top
    sudo cgdelete -g cpu:/cpulimit
    ```
    - cgroup Memory 제한 실습
    ```
    sudo cgcreate -g memory:/memorylimit
    echo $((512*1024*1024)) | sudo tee /sys/fs/cgroup/memorylimit/memory.max
    sudo cgexec -g memory:/memorylimit stress --vm-bytes 1024M --vm 1 --timeout 30s &
    watch -n 0.5 cat /sys/fs/cgroup/memorylimit/memory.current
    sudo cgdelete -g memory:/memorylimit
    ```

## Linux의 Namespace란?

- Namespace
    - Linux의 namespace는 프로세스가 시스템 리소스를 격리하여 사용할 수 있도록 하는 커널 기능
    - 시스템 자원의 충돌을 방지하고, 각 프로세스나 프로세스 그룹이 독립된 환경에서 실행될 수 있도록 한다.
    - 주로 컨테이너 기술에서 사용되며, 다양한 유형의 네임스페이스가 존재한다.

- Mount Namespace (mnt)
    - 파일 시스템 마운트를 격리하고 각 네임스페이스는 자신의 파일 시스템 트리를 가지고 있어 다른 네임스페이스와 독립적으로 파일 시스템을 마운트하거나 언마운트할 수 있다.

- Process ID Namespace (pid)
 - 프로세스


 
















