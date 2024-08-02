# KTB 클라우드 과정 필기 노트 / 7월 15일 (11일차) - AWS




## AWS의 AZ(Availability Zone)
- AZ는 하나의 Region 내에 위치한 데이터 센터 그룹
- 각각의 AZ는 독립된 데이터 센터로 구축되어 있고 물리적으로 거리도 상당히 떨어져 있음
- 따라서 각각의 AZ에 문제가 생기더라도 다른 AZ에 영향을 끼치지 않는다.
- 1개의 Region내에 최소 3개의 AZ가 포함
- 고가용성과 내결함성을 위해 보통의 경우 여러 AZ에 걸쳐 앱을 배포한다. ex) ap-northeast-2a(서울 A), ap-northeast-2c(서울 C)

## AWS의 Region
- Region은 지리적(최대 국가 단위)으로 분리된 여러 위치로 구분
- 예를들어 아시아에는 서울,도쿄 등이 있고 외국에는 오리건, 뭄바이 등의 리전이 존재
- 각각의 리전끼리 통신하는 데에는 별도의 비용이 추가
- 다만 글로벌 서비스 특성상 빠른 서비스를 제공하기 위해 여러 리전에 걸쳐 앱을 배포하기도 한다.


## VPC(Virtual Private Cloud)
- VPC는 Virtual Private Cloud의 준말
- 거창해보이지만 사실은 내가 사용할 개인 네트워크를 구성하는 요소
- 특수한 상황이 생기지 않는 이상 VPC에서 사용하는 요소는 다음과 같다.
    1. Subnet
    2. Routing Table
    3. Security Group
    4. Internet Gateway
    5. NAT Gateway

- VPC의 이점
    - 격리성: 격리된 환경에서 구성하기 때문에 다른 네트워크에 간섭받지 않는다.
    - 보안성: 보안 그룹을 통해 세부적인 보안 설정을 할 수 있다.
    - 확장성: 필요에 따라 서브넷을 추가로 구성하거나 라우팅 테이블, 보안 그룹 등을 쉽게 추가 가능
    - 유연성: 격리된 환경이지만 요구사항에 따라 온프레미스와 혼합해 하이브리드 클라우드를 구성할 수 있다.

## 서브넷
- 서브넷은 VPC 내에서 실제 VM이 올라갈 네트워크 대역을 설정하는 요소
- 크게 Private Subnet, Public Subnet으로 나뉜다.
- 프라이빗 서브넷은 온프레미스로 치면 인트라넷 네트워크, 퍼블릭 서브넷은 인터넷 네트워크이다.
- 둘을 나누는 요소는 인터넷 게이트웨이의 유무와 라우팅 테이블에 인터넷 게이트웨이를 추가했는지 이다.
- 프라이빗 서브넷은 NAT 게이트웨이를 통하지 않는 이상 인터넷 통신이 불가능하다.
- 퍼블릭 서브넷은 인터넷 게이트웨이를 통하기 때문에 기본적으로 인터넷 통신이 가능하다.


## 라우팅 테이블
- 앞서 설명드린 서브넷에서 사용되는 네트워크의 네비게이션 같은 존재
- 기본적으로 네트워크의 모든 흐름은 라우팅 테이블을 기반으로 동작하게 된다.
- 네트워크 강의시간에 배웠던 CIDR을 기반으로 동작하며 각각의 대역에 대해 경로를 설정해줄 수 있다.

## 보안그룹
- ACL(Access Control List)처럼 출발지 IP, Port에 따라 출입을 막거나 허용할 수 있다.
- 서브넷에 진입하는 인바운드, 나갈때의 아웃바운드를 나눠 통제한다.
- 일반적으로는 아웃바운드는 문제가 되지 않는 이상 통신의 원활함을 위해 대부분 열어두는 편이다.
- 따라서 대부분의 통제는 인바운드에서 이뤄지며 접속 및 통신용 포트를 제외한 나머지는 통제하는 편

## 인터넷 게이트웨이
- 앞서 언급한 퍼블릭 서브넷을 결정하는 요소
- 라우팅 테이블과 조합되어 사용되며 퍼블릭 서브넷에서 조차 특정 대역만 인터넷이 가능하도록 설정하기도 한다.
- 물론 대부분의 경우 관리 비용이 더 들어가기 때문에 다 열어두는 편

## NAT(Network Address Translation) 게이트 웨이
- 용도는 다양하지만 기본적으로는 프라이빗 서브넷에서도 인터넷이 가능하게 하기 위해 사용
- 분리해놓은 프라이빗 환경에서 사용하는 관계로 인터넷 게이트웨이처럼 모든 경로를 열어두지는 않고 앱들이 외부와 통신할 수있도록 특정 포트만 보안 그룹을 통해 열어두는 편

## VPC 실습
- region : ap-northeast-2
- az : ap-northeast-2a, ap-northeast-2c
- private-a : 192.168.1.0/24
- public-a : 192.168.2.0/24
- public-c : 192.168.3.0/24
- public-nat : 192.168.4.0/24

## AWS EC2(Elastic Cloud Compute)
- UTM을 통해 VM을 생성하든 것과 구조는 비슷
- CPU, Memory, 저장소 용량 등을 미리 정하고 생성
- 인스턴스 유형은 다음과 같다.
    - 범용(G) 인스턴스 : 균형 잡힌 컴퓨팅, 메모리, 네트워크 리소스 제공. ex) t3, m5 시리즈
    - 컴퓨팅 최적화(C) 인스턴스 : 높은 CPU 성능을 요구하는 워크로드에 적합. ex) c5 시리즈.
    - 메모리 최적화(R) 인스턴스 : 메모리 집약적인 애플리케이션에 최적화되어 있다. ex) r5 시리즈.
    - 가속화 컴퓨팅(P,G,F) 인스턴스: GPU, FPGA 등 특수 하드웨어를 사용. ex) p3, g4 시리즈.
    - 스토리지 최적화(I,D,H) 인스턴스: 대용량, 고속 스토리지에 최적화되어 있다. ex) i3, d2 시리즈

## EC2 실습
- 공통 보안 그룹 생성: 22번, 80번 포트 오픈
- internet gateway 1개
- nat gateway 1개
- ec2 3개(public-a, public-c, private-a)
- routing table 1개(internet gateway용)
- public-a, public-c subnet에 routing table 반영
- routing table 2개(igw와 3개의 public 서브넷 연결 ,nat gateway와 1개의 private 서브넷 연결)
- private subnet에 routing table 반영


## ALB 실습
- 주어진 파이썬 코드를 2개의 인스턴스에서 실행시키고 ALB를 연결해 실행될 때마다 변경되는 private ip를 확인하는 실습
    - 파이썬 코드
        ```python
        from flask import Flask, jsonify
        import subprocess
        import re

        app = Flask(__name__)

        def get_private_ip():
            try:
                # ip addr 명령어 실행
                result = subprocess.run(['ip', 'addr'], stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
                
                # 명령어가 성공적으로 실행되었는지 확인
                if result.returncode != 0:
                    raise Exception(result.stderr)
                
                # 정규식을 사용하여 192.168로 시작하는 IP 주소 추출
                ip_pattern = re.compile(r'inet\s+(192\.168\.\d+\.\d+)')
                match = ip_pattern.search(result.stdout)
                
                if match:
                    # 매칭된 IP 주소를 반환
                    return match.group(1)
                else:
                    return None
            except Exception as e:
                print(f"An error occurred: {e}")
                return None

        @app.route('/', methods=['GET'])
        def private_ip():
            ip = get_private_ip()
            if ip:
                return jsonify({"private_ip": ip})
            else:
                return jsonify({"error": "Failed to retrieve private IP"}), 500

        if __name__ == '__main__':
            app.run(host='0.0.0.0', port=80)
        ```
- ALB 1개 생성
- ALB 보안 그룹도 기존 생성한 보안 그룹으로 변경
- public 인스턴스 2개에 python 코드 주입
    ```bash
    sudo apt-get update
    sudo apt-get python3-pip
    sudo python3 app.py
    ```
- 로드밸런서의 DNS로 접속하여 ip가 바뀌는지 확인

## 실습용 AWS 오브젝트 제거
- ALB 제거
- EC2 인스턴스 제거
- Security Group 제거
- Subnet 제거
- NAT Gateway 제거
- Routing Table 제거
- Internet Gateway 제거
- VPC 제거










