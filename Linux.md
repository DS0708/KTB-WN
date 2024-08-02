# KTB 클라우드 과정 필기 노트 / 7월 5일 (5일차) - linux


## 목차
- 리눅스 실습환경 구성
- 리눅스란?
- 리눅스의 역사와 철학
- 리눅스의 구조
- 리눅스의 대표적인 배포판
- 패키지 매니저
- 리눅스에서의 권한 및 소유자
- 리눅스 루트 ~~~~


## Linux란 ?
- Unix기반의 OS 중 하나
- 컴퓨터 운영 체제 중 하나로, 전 세계적으로 널리 사용되고 있다.
- 서버 데스크탑, 모바일 기기에서 주로 사용한다.
- open source였기 때문에 엄청 커질 수 있었다.

> 오픈소스란 누구나 열람, 수정 배포할 수 있는 소프트웨어이며 공개적으로 접근 가능하며 사용자들이
> 자유롭게 수정하고 개선할 수 있음

- GPL License
    - GNU General Public License(GPL) : 자유 소프트웨어 재단에서 만든 라이선스
    - 소프트웨어를 자유롭게 사용할 수 있고, 소스 코드를 수정 및 배포할 수 있는 권리를 보장
    - 수정 및 배포시에도 동일한 GPL 라이선스를 유지해야 함

## Linux의 구조
- 크게 4가지 단계로 나눌 수 있으며, 어플리케이션, 쉘, 커널, 하드웨어로 나눌 수 있다.
    - Application : 일반적으로 사용하는 바이너리, 명렁어, 빌드된 파일들을 통틀어서 하는 말
    - Shell : 어플리케이션이 커널에서 사용하는 명령어 등을 가려주고 쉘 자체를 사용해 스크립트 만들기 가능 -> 주요 기능으로는 명령어 해석, 스크립트 실행, 환경 관리(환경 변수 설정 및 관리)
    - Kernel : OS의 핵심 부분으로 하드웨어와 직접 상호작용하는 단계이며, 시스템 자원(CPU, Memory 등)을 관리하고 어플리케이션/쉘 <-> 하드웨어 간의 통신을 중재

- 주요 기능
    - 프로세스 관리 : 프로세스 생성, 스케줄링, 종료 등을 관리
    - 메모리 관리 : 가상 메모리, 페이징, 스왑 메모리 등을 통해 효율적으로 메모리를 관리
        - 가상 메모리 : 보조기억장치를 이용해 실제 사용 가능한 메모리보다 더 많은 메모리를 사용하게 하는 기법
        - 페이징 : 메모리를 더 효율적으로 사용하기 위해 고안된 기법으로 메모리를 일정 크기만큼 잘라서 사용하는 기법
        - 스왑 메모리 : 실제로 사용하지 않는 나머지 부분(페이지)을 보조기억장치의 특정 영역(스왑 영역)으로 옮겨서 공간을 확보하는 기법
    - 파일 시스템 관리 : 파일의 저장, 접근, 조작을 지원하는 파일 시스템을 관리
    - 장치 관리 : 하드웨어 장치와의 상호작용을 관리하고, 장치 드라이버를 통해 이를 추상화
    - 보안 및 접근 제어 : 사용자와 시스템 자원 간의 보안 정책을 관리하고 적용한다.

> 앞서 설명한 기능 제어들을 종합해 Linux 심화 실습으로 뺄 예정이고 이 실습은 곧 Docker 강의와
> 연결될 예정



## 리눅스의 대표적인 배포판

- Ubuntu
    - Debian 계열중에 가장 유명한 배포판
    - 초보자도 쉽게 쓰도록 설계되었고 직관적인 GUI 제공
    - 방대한 커뮤니티 덕분에 레퍼런스가 많아 문제 해결이 쉽다.
    - 2년단위의 버전 출시 및 각 버전당 5년간의 유지보수로 보안성이 강하고 안정적
- CentOS
    - RHEL(RedHat Enterprise Linux)계열로 안정성과 보아이 뛰어나 주로 기업에서 많이 사용하는 OS
    - Ubuntu와 마찬가지로 커뮤니티가 방대하여 레퍼런스가 많다.
    - CentOS 7까지는 무료였으나 CentOS 8부턴느 유료
- Arch Linux
    - 경량화와 커스터마이징에 특화된 OS
    - 경량화에 초점이 맞춰져 있어 OS 자체의 용량이 굉장히 작다.


## 패키지 매니저
- Linux의 패키지 매니저는 배포판의 종류에 따라 명칭과 명령어가 다르다.
- 일반적으로 생각하는 언어의 패키지 매니저인 pip, npm, yarm, maven, gradle과 비슷한 역할을 수행한다고 생각하면 된다.
- 패키지 매니저는 패키지 설치, 업데이트, 삭제, 의존성 관리(설치, 삭제 포함)등을 담당하고 있다.
- 간단하게 Debian, RHEL 계열의 패키지 매니저를 살펴보면
    - APT
        - Debian 계열에서 주로 사용하는 패키지 매니저
        - 필요한 패키지를 설치할 때 의존성 패키지를 자동으로 설치
        - 배포판의 OS 버전에 따라 레포지토리를 따로 두지만 버전이 다르다고 해서 패키지가 무조건 호환이 불가능한건 아니고 서로 다른 OS 버전의 패키지를 불러올 수도 있다.
    - YUM/DNF
    

<img src="../img/0705-1.png">


## Linux에서의 권한 및 소유자
- 기본적으로 Linux에서의 모든 파일과 디렉토리는 User, Group에 의해 소유된다.
- User : 파일을 생성한 주체로써 생성된 파일은 기본적으로 사용자에 의해 소유
- Group : 여러 사용자가 속해있는 그룹으로 기본적으로 그룹 내의 같은 사용자들은 권한을 공유

- 실습 - 사용자, 그룹 생성하기
```bash
sudo passwd
su root
adduser kakao
cat /etc/passwd
usermod -aG kakao kakao
cat /etc/group
addgroup kakao-tech-bootcamp
usermod -aG kakao-tech-bootcamp
cat /etc/group
```

- 실행권한
    - Read : 읽기 권한
    - Write : 쓰기 권한
    - Execute : 실행 권한
    - 한 개의 파일에 대한 권한은 총 3개의 권한을 가질 수 있으며, Read 4, Write 2, Execute 1이다.
    - 한개의 파일이 갖는 모든 권한은 User, Group, Others로 나눠진다.
    - 예를 들어 755라면 User는 모든 권한, Group는 읽기와 실행, Others도 읽기와 실행의 권한을 가지는 것을 의미

- 실습 - 파일 권한 조작
```
cd /tmp
echo 'echo abcd' > test.sh
cat test.sh
./test.sh
chmod +x test.sh
./test.sh
ls -al test.sh
chmod -r test.sh
cat test.sh
```

- 실습 - 파일 소유자 조작
```bash
covy@kakao-tech-bootcamp:/tmp$ chmod 770 test.sh
covy@kakao-tech-bootcamp:/tmp$ su kakao
Password:
kakao@kakao-tech-bootcamp:/tmp$ ./test.sh
bash: ./test.sh: Permission denied
kakao@kakao-tech-bootcamp:/tmp$ exit
exit
covy@kakao-tech-bootcamp:/tmp$ sudo chown kakao:kakao test.sh
[sudo] password for covy:
covy@kakao-tech-bootcamp:/tmp$ ./test.sh
-bash: ./test.sh: Permission denied
covy@kakao-tech-bootcamp:/tmp$ su kakao
Password:
kakao@kakao-tech-bootcamp:/tmp$ ./test.sh
abcd
kakao@kakao-tech-bootcamp:/tmp$
```

## 리눅스의 루트 디렉토리
- Root Directory
    - 리눅스의 디렉토리는 기본적으로 루트 디렉토리 (/)를 기준으로 나뉨
    - 시스템의 모든 자원은 루트 디렉토리 하위에서 관리된다.
- /bin(/user/bin)
    - 기본적인 사용자 명령어가 저장되는 디렉토리
    - ls, cp, mv, rm, bash 등의 기본 명령어도 포함
- /lib(/user/lib)
    - 시스템과 어플리케이션이 사용하는 공유 라이브러리들이 포함
    - lib.so, ld-linux-x86-64.so.2 등
- /usr
    - 앞서 설명한 디렉토리들이 모두 포함되어 있다.
    - 사용자 명령어, 응용 프로그램, 라이브러리 등이 저장되는 디렉토리
- /dev
    - 장치파일들이 저장되는 디렉토리
    - 주로 첫번째 하드 디스크 파티션이나 터미널, 널 장치 등이 포함
- /etc
    - 시스템 설정 파일들이 저장되는 디렉토리
    - 사용자 계정 정보(passwd), 암호 정보(shadow), 호스트네임/IP 매핑 파일(hosts) 등이 포함
- /opt
    - CLI보다는 GUI에서 자주 사용되는 디렉토리
    - 설치 매니저를 통해 설치된 어플리케이션이 주로 포함
- /proc
    - 커널과 프로세스에 대한 가상 파일 시스템이 저장되는 위치
    - /proc/cpuinfo (CPU 정보), /proc/meminfo (메모리 정보)
    - 시스템 상태와 커널 정보등을 제공하며 동적으로 생성되는 파일들로 구성된다.
    - 이거 하나 때문에 잘 안된적도 있다. 쿠버네티스에서 ..?
- /home
    - 사용자들의 개인 디렉토리가 저장되는 위치
    - 기본적으로 권한과 소유자가 해당 유저에 맞게 설정되어 있다.
- /root
    - root 계정의 홈 디렉토리이다
    - 디렉토리, 파일에 대한 권한 및 소유자도 기본적으로 root로 설정된다.
- /tmp
    - 임시파일을 저장하는 디렉토리
    - 주로 사용 후에 삭제되어도 문제 없는 파일이나 디렉토리들이 포함
    - 따라서 이 페이지 다음에 나올 실습 또한 tmp 디렉토리에서 진행

- 실습 - 패키지 설치 및 확인
    - nginx 패키지 설치 해보기
    ```
    apt-get install nginx
    cd /etc/nginx
    ls
    cd /usr/sbin
    ls
    ```
    - etc 에는 nginx 설정 파일이 저장 있고 sbin에는 nginx 명령어가 사용될 수 있는 코드들이 저장

- 실습 - 환경변수 및 실행해보기
```
cd /
test.sh
export
exprot PATH=$PATH:/tmp
test.sh
```

## 리눅스 파일 시스템 비교

<img src="../img//0705-2.png" width="200" height="200">

- 실습 - lsblk 명령어 등 파일 시스템 형식 확인
```
lsblk
df
df -TH
```






















