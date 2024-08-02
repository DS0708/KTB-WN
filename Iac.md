# KTB 클라우드 과정 필기 노트 / 7월 18일 (14일차) - iac



## 목차
1. IaC
2. Terraform
3. Ansible


## IaC(Infrastructure As Code)
- IaC란 서버, 네트워크, 데이터베이스, 애플리케이션 구성 등 IT 인프라를 수동으로 관리하는 대신 코드로 정의하고 관리하는 방식
- IaC는 프로그래밍 언어나 구성 파일을 사용하여 인프라를 자동으로 배포하고 관리할 수 있게 해준다.

### IaC를 사용하는 이유
- 자동화 및 일관성 : 수동 설정에서 발생할 수 있는 오류를 줄이고, 인프라 배포 및 관리의 일관성 유지
- 버전 관리 : 코드로 정의된 인프라는 버전 관리 시스템(Git 등)을 통해 변경 이력을 추적하고 복구할 수 있다.
- 재사용성 : 코드 모듈화 및 템플릿화를 통해 재사용성을 높여 시간과 비용을 절감할 수 있다.
- 협업: 코드 기반 접근 방식은 개발팀과 운영팀 간의 협업을 촞긴하고 DevOps 문화의 핵심 요소이다.
- 가시성 및 투명성 : 코드가 모든 설정을 명시적으로 정의하므로, 인프라 구성 상태를 명확히 파악할 수 있다.

### Iac의 구현 방식
- 선언적 접근 방식 
    - 원하는 최종 상태를 정의하고, 시스템이 그 상태로 자동으로 구성된다.
    - 사용자는 원하는 결과를 명시하며, 도구가 이를 달성하기 위한 과정을 결정
    - 예: Terraform, AWS CloudFormation, Kubernetes YAML 파일
    - 장점 : 설정이 간결하고 이해하기 쉬우며, 인프라의 현재 상태와 원하는 상태를 비교하여 필요한 변경 사항만 적용한다.
- 명령적 접근 방식
    - 수행할 명령어와 순서를 명시하고 사용자는 인프라를 설정하기 위한 단계별 명령을 작성한다.
    - 예 : Ansible, Chef, Puppet
    - 장점 : 세부적인 제어가 가능하며, 복잡한 논리와 조건 처리를 쉽게 구현할 수 있다.

### IaC도구
- Terraform : 클라우드 인프라를 코드로 정의하는 오픈소스 도구이며 AWS, Azure, GCP 등 다양한 클라우드 프로바이더를 지원하며, 선언적 접근 방식을 사용
- AWS CloudFormation: AWS 리소스를 템플릿으로 정의하여 배포하는 서비스. 선언적 접근 방식을 사용하며 AWS에 특화되어 있음
- Ansible: 서버 구성 및 애플리케이션 배포를 자동화하는 도구. YAML 포맷의 플레이북을 사용하며, 명령적 접근 방식을 채택
- Chef/Puppet: 서버 구성 관리 도구로, 인프라를 코드로 정의하고 자동화하며 Ruby 기반의 명령적 접근 방식을 사용한다
- Kubernetes: 컨테이너화된 애플리케이션 배포, 스케일링, 운영을 자동화하는 오픈소스 플랫폼이다. 선언적 YAML 파일을 통해 클러스터 상태를 관리한다.

### IaC의 워크플로우
- Planning : 요구 사항을 분석하고 인프라 구조를 설계
- Coding : 인프라를 코드로 정의 -> 이 단계에서 템플릿, 모듈, 변수 등을 사용
- Version Control : 작성된 코드를 Git 등의 버전 관리 시스템에 저장하고 이력을 관리
- Testing: 인프라 코드가 예상대로 작동하는지 확인한다. 테스트 환경에서 코드를 실행하고 결과를 검증한다.
- Deployment: 검증된 코드를 실제 환경에 배포한다. 도구의 apply 명령어를 사용하여 인프라를 프로비저닝한다.
- Monitoring & Management : 배포된 인프라를 모니터링하고 필요에 따라 수정 및 업데이트 하며 상태를 지속적으로 점검하여 안정성을 유지한다.

### IaC의 장점
- 신속한 배포 : 코드로 정의된 인프라는 자동화된 프로세스를 통해 신속하게 배포 가능
- 효율성 : 반복적인 작업을 자동화하여 시간과 비용을 절감할 수 있다.
- 일관성 : 모든 환경에서 동일한 설정을 적용하여 환경 간 차이를 줄일 수 있다.
- 복구 능력 : 코드 기반의 인프라는 장애 발생 시 빠르게 복구 가능
- 확장성 : 필요에 따라 인프라를 쉽게 확장하거나 축소할 수 있다.



## Terraform
- 인프라를 코드로 관리하는 도구

### Terraform의 특징
- 프로바이더: AWS, Azure, GCP 등 다양한 클라우드 서비스 Provider를 지원
- 인프라 코드화 : JSON 유사한 HCL(HashiCorp Configuration Language) 사용
- 버전 관리 : 인프라 상태를 파일로 저장하여 버전 관리가 가능
- 실행 계획: 변경 사항을 적용하기 전에 계획을 미리 볼 수 있다.
- 모듈화: 재사용 가능한 모듈을 통해 코드 유지보수가 용이해진다.

### Terraform의 구성요소
- Providers: 클라우드 서비스 제공자와의 인터페이스
    - ex. provider "aws"{region="ap-northeast-2"}
- Resources: 클라우드 자원을 정의
    - ex. resource "aws_instance" "example"{ami="ami-123456" instance_type="t2.micro"}
- Variables: 변수 사용을 통해 코드 재사용성 증가
    - ex. variable "region"{default="ap-northeast-2"}
- Outputs: 출력 값을 정의하여 다른 구성 요소와 연계
    - ex. output "instance_ip" {value = aws_instance.example.public_ip}
- Modules: 코드의 재사용을 위해 모듈화
    - ex. module "vpc" {source = "./modules/vpc"}

### Terraform의 워크 플로우
- terraform init - 초기화 : 프로젝트 디렉토리를 초기화하고 필요한 플로그인을 다운로드
- terraform plan - 계획 수립: 변경 사항을 시뮬레이션하고 계획서 출력
- terraform apply - 적용 : 계획된 변경 사항을 실제 인프라에 적용
- terraform state - 상태 관리 : 현재 인프라 상태 확인 및 관리
- terraform destroy - 종료 : 인프라 자원을 제거


### Terraform 실습
- AWS IAM에서 사용자 새성
- AWS 사용자에게 AdministratorAccess 권한 주기
- 액세스 키 발급 (CLI 용)
- 관련 패키지 설치
    ```bash
    brew install awscli
    brew install terraform
    brew install ansible
    ```
- aws configure 설정
    ```bash
    aws configure
    ```
    - output 형식 json?
- main.tf 생성
    ```conf
    provider "aws" {
    region = "ap-northeast-2"
    }

    variable "instance_count" {
    default = 2
    }

    variable "instance_type" {
    default = "t2.micro"
    }

    resource "aws_vpc" "main" {
    cidr_block = "192.169.0.0/16"

    tags = {
        Name = "main-vpc"
    }
    }

    resource "aws_subnet" "main" {
    vpc_id            = aws_vpc.main.id
    cidr_block        = "192.169.1.0/24"
    availability_zone = "ap-northeast-2a"
    map_public_ip_on_launch = true

    tags = {
        Name = "main-subnet"
    }
    }

    resource "aws_internet_gateway" "main" {
    vpc_id = aws_vpc.main.id

    tags = {
        Name = "main-igw"
    }
    }

    resource "aws_route_table" "main" {
    vpc_id = aws_vpc.main.id

    route {
        cidr_block = "0.0.0.0/0"
        gateway_id = aws_internet_gateway.main.id
    }

    tags = {
        Name = "main-route-table"
    }
    }

    resource "aws_route_table_association" "main" {
    subnet_id      = aws_subnet.main.id
    route_table_id = aws_route_table.main.id
    }

    resource "aws_security_group" "main" {
    vpc_id = aws_vpc.main.id

    ingress {
        from_port   = 22
        to_port     = 22
        protocol    = "tcp"
        cidr_blocks = ["0.0.0.0/0"]
    }

    egress {
        from_port   = 0
        to_port     = 0
        protocol    = "-1"
        cidr_blocks = ["0.0.0.0/0"]
    }

    tags = {
        Name = "main-sg"
    }
    }

    resource "aws_instance" "web" {
    count         = var.instance_count
    ami           = "ami-04ea5b2d3c8ceccf8"
    instance_type = var.instance_type
    key_name      = "covyEc2"
    subnet_id     = aws_subnet.main.id
    vpc_security_group_ids = [aws_security_group.main.id]

    tags = {
        Name = "WebServer-${count.index}"
    }
    }

    output "instance_ips" {
    value = aws_instance.web.*.public_ip
    }
    ```
- VPC, ec2, Security Group 등 IaC 방식을 통해 생성된 리소스 확인
- 만들어 진 리소스 삭제
    ```
    terraform destroy
    ```

## Ansible
- Ansible은 IT 자동화 도구로, 서버 구성 관리, 애플리케이션 배포, 작업 자동화를 지원

### Ansible 특징
- Agentless: 관리 대상 서버에 에이전트를 설치할 필요 없이 SSH를 통해 연결 진행
- 모듈 기반: 다양한 모듈을 통해 다양한 작업 수행 가능
- YAML 언어 사용 : 플레이북 작성 시 가독성이 높은 YAML 포맷 사용
- 확장성 : 커뮤니티 모듈 및 플러그인을 통해 기능 확장 가능

### Ansible의 구성요소
- Inventory: 관리할 호스트 목록을 정의
    - ex) hosts.ini
- Playbooks: 작업 정의서로, YAML 포맷 사용
    - ex) site.yml
- Modules: 특정 작업을 수행하는 코드 블록
    - ex) ansible.builtin.shell
- Roles: 관련 작업을 그룹화하여 재사용 가능
    - ex) roles/web
- Tasks: 실행할 작업을 정의
    - ex) tasks/main.yml
- Handlers: 특정 조건에 따라 실행되는 작업
    - ex) handlers/main.yml
- Templates: Jinja2 템플릿을 통해 설정 파일 생성
    - ex) templates/nginx.conf.j2

### Ansible의 워크플로우
- 인벤토리 작성: 관리할 호스트 목록 작성
- 플레이북 작성: 수행할 작업을 정의한 YAML 파일 작성
- 작업 실행: ansible-playbook 명령어로 플레이북 실행
- 상태 확인: ansible 명령어로 호스트 상태 확인 및 관리

### Ansible 실습 : 생성된 ec2 인스턴스 2개에 nginx 설치하고 실행하기
- Terraform을 통해 생긴 인스턴스의 ip를 ansible의 호스트로 사용 (host.ini)
    ```bash
    echo "[webservers]" > hosts.ini\nterraform output -json instance_ips | jq -r '.[]' >> hosts.ini
    ```
- ansible.cfg
    ```conf
    [defaults]
    inventory = hosts.ini
    private_key_file = ~/.ssh/covyEc2.pem
    host_key_checking = False
    remote_user = ec2-user
    ```
- playbook.yml 작성
    ```yaml
    - hosts: webservers
    become: yes
    tasks:
        - name: Install Nginx
        yum:
            name: nginx
            state: present

        - name: Start Nginx
        service:
            name: nginx
            state: started
            enabled: true
    ```
    - hosts: hosts.ini를 읽어서 사용 (value는 파일 내의 이름)
    - become: 원격지에서 sudo 권한을 사용할지 결정
    - tasks: 원격지에서 실행할 명령 설정
    - enabled: 부팅 시 실행할 것인지 결정
- 명령어 실행
    ```bash
    ansible-playbook -i hosts.ini playbook.yml
    ```
















