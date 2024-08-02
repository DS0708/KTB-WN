# 클라우드 과정 11강 : AWS의 Kubernetes, EKS, Jenkins

## EKS 실습
- Cluster 생성 실습
    - 관련 VPC 구성
    - role 구성
        - AmazonEKSClusterPolicy
        - AmazonEKSServicePolicy
    - EKS Cluster 생성
        - 아래에 terraform 코드로 생성한 vpc와 해당 subnet 생성
        - 제일 최신 버전을 적용하는 것이 좋음
        - 클러스터 엔드포인트 액세스는 프라이빗
        - 나머지는 default 값으로 진행
- 컴퓨팅 - 노드 그룹 생성
    - 클러스터 생성 후 별도의 시간이 지난 후 진행
    - 관련 role 구성
        - AmazonEC2ContainerRegistryReadOnly
        - AmazonEKS_CNI_Policy
        - AmazonEKSWorkerNodePolicy
    - 노드 그룹 생성


## 관련 VPC와 밑에 CI 실습 구성하는 tf 코드
```conf
provider "aws" {
region = "ap-northeast-2"
}

variable "instance_count" {
default = 1
}

variable "instance_type" {
default = "t3.medium"
}

variable "existing_security_group_id" {
default = "sg-01fe8c4f5c8d5aeaa"
description = "ID of an existing security group to associate with the EC2 instances"
}

variable "subnet_ids" {
default = ["subnet-04d88cc60c57c4fb3", "subnet-0f93e87ed8fb436fd"]
description = "List of subnet IDs to deploy EC2 instances"
}

resource "aws_instance" "eks_ec2" {
count                        = var.instance_count
ami                          = "ami-062cf18d655c0b1e8"  # Ensure this is the correct AMI for your region
instance_type                = var.instance_type
key_name                     = "covyEc2"  # Ensure this key pair is already created in your AWS account
subnet_id                    = element(var.subnet_ids, count.index % length(var.subnet_ids))
vpc_security_group_ids       = [var.existing_security_group_id]

tags = {
    Name = "jenkins-${count.index}"
}
}

output "instance_ips" {
value = aws_instance.eks_ec2.*.public_ip
}
```
- 기존에 생성했던 VPC, Subnet 사용
- Public IP 자동할당이 안되어 있어서 SSH 연결이 안되었던 issue 해결


## CI, Jenkins 실습
- 사전 세팅
    - 이전 시간에 사용했던 terraform 사용하여 ec2 1개 배포
        - jenkins 실행 만으로 메모리 1.2GB 사용함에 주의 -> 성능이 조금 높은 이미지를 사용해야함
    - IAM에서 AdministratorAccess 권한의 유저 생성 -> Access Key 발급받고 aws configure로 연결

- Jenkins 설치 및 최초 설정 [참고](https://www.jenkins.io/doc/book/installing/linux/#debianubuntu)
    ```
    sudo su

    wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | gpg --dearmor > jenkins.gpg
    mv jenkins.gpg /usr/share/keyrings/
    echo "deb [signed-by=/usr/share/keyrings/jenkins.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list

    apt update
    apt install jenkins openjdk-11-jdk docker.io -y

    chown jenkins:jenkins /var/run/docker.sock

    systemctl enable jenkins
    systemctl start jenkins
    systemctl status jenkins
    ```

    > 이렇게 설정을하니 java.net 소켓 어쩌구 에러가 뜸 -> 그래서 공식사이트 다운로드를 보면서 진행

    ```
    sudo su

    wget -O /usr/share/keyrings/jenkins-keyring.asc   https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]"   https://pkg.jenkins.io/debian-stable binary/ | sudo tee   /etc/apt/sources.list.d/jenkins.list > /dev/null

    apt-get update
    apt-get install jenkins

    apt update
    apt install fontconfig openjdk-17-jre

    apt install docker.io -y
    chown jenkins:jenkins /var/run/docker.sock

    systemctl enable jenkins
    systemctl start jenkins
    systemctl status jenkins
    ```

- Jenkins 최초 접속 후 설정
    - public-ip:8080 접속
        ```
        sudo cat /var/lib/jenkins/secrets/initialAdminPassword
        ```
    - Install suggested plugins 선택
    - Jenkins 관리 - Plugins - Available plugins
    - GitHub Integration, Docker Pipeline, Amazon ECR, AWS Credentials -> 이렇게 4개의 플러그인 설치 및 설치 후 Jenkins 재시작 체크

- Github Webhook 세팅
    - https://github.com/jacob2513/nodetest 저장소 fork
    - ECR(Elastic Container Registry)에 nodetest로 repository 생성
    - 레포 Settings - Webhooks - Add webhook (Github에서 진행)
        - Payload URL: http://<JENKINS_SERVER_IP>:8080/github-webhook/ 
        - Content type : application/json
        - Secret: 빈칸
        - Just the push event 선택
    - 우측 상단 프로필 클릭
        - settings 선택 - Developer Settings - Personal access tokens - Tokens(classic)
        - Expiration : 7days - Select scopes repo 전체선택, admin:repo_hook 전체 선택
    - Generate Token - ghp_XXX 토큰 복사 -> Jenkins 대시보드 - Jenkins 관리 - System - Github Server 검색 - + Add - Credential 생성 - Secret text의 Secret에 ghp_XXX 토큰 붙여넣기
    - Manage hooks 체크 후 저장

- Credential 세팅 및 Pipeline 생성  
    - Jenkins 관리 - Credentials - (global)클릭 + add credential선택 - Kind : AWS Credentials 선택
    - ID : ecr_credentials_id
    - 관리자 권한 가진 사용자의 액세스키 발급후, key id, secret key 복붙
    - Jenkins 메인페이지에서 좌측 새로운 Item 클릭 - Pipeline 선택 후 OK - GitHub project - Project url : github.com/user-id/repo-name
    - GitHub hook trigger for GITScm polling -> 선택을 무조건 해줘야한다.
    - Jenkins Pipeline code
        ```
        pipeline {
            agent any

            environment {
                REPO = 'DS0708/nodetest'
                ECR_REPO = '905418374604.dkr.ecr.ap-northeast-2.amazonaws.com/nodetest'
                ECR_CREDENTIALS_ID = 'ecr:ap-northeast-2:ecr_credentials_id'
            }

            stages {
                stage('Checkout') {
                    steps {
                        git branch: 'main', url: "https://github.com/${REPO}.git"
                    }
                }

                stage('Build Docker Image') {
                    steps {
                        script {
                            dockerImage = docker.build("${ECR_REPO}:latest")
                        }
                    }
                }

                stage('Push to ECR') {
                    steps {
                        script {
                            docker.withRegistry("https://${ECR_REPO}", "$ECR_CREDENTIALS_ID") {
                                dockerImage.push('latest')
                            }
                        }
                    }
                }
            }
        }
        ```

> 여기까지 진행하면, github에 코드를 push 하면 github-webhook이 젠킨스에게 트리거를 보내고
> 그 트리거에 응답하여 젠킨스는 빌드한다. 이때 파이프라인에 설정된 stage에 따라 빌드 후, 도커
> 이미지를 만들고 이 이미지를 AWS의 ECR에 베포한다.

- 앱 배포 및 확인 (Jenkins 호스트에서 진행)
    - deployment.yaml
        ```
        apiVersion: apps/v1
        kind: Deployment
        metadata:
        name: nodetest
        labels:
            app: nodetest
        spec:
        replicas: 1
        selector:
            matchLabels:
            app: nodetest
        template:
            metadata:
            labels:
                app: nodetest
            spec:
            containers:
            - name: nodetest
                image: 905418374604.dkr.ecr.ap-northeast-2.amazonaws.com/nodetest:latest
                ports:
                - containerPort: 80
        ```
    - 클러스터 설정 - 액세스 - 액세스 항목 생성
    - IAM 보안 주체 ARN : 기존에 생성한 IAM User 추가 - 다음
    - 정책 이름 : AmazonEKSAdminPolicy - 액세스 범위: 클러스터 - 정책 추가 - 다음 - 생성
        ```
        sudo apt install -y unzip
        curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
        unzip awscliv2.zip
        sudo ./aws/install
        aws configure
        curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
        chmod +x ./kubectl
        sudo mv ./kubectl /usr/local/bin/kubectl
        aws eks update-kubeconfig --region ap-northeast-2 --name your-cluster
        kubectl apply -f deployment.yaml

        kubectl get pods
        ```
        > 이런 에러가 발생한다. 아직 kubernetes에 대한 개념이 부족한 것 같아서, 여기까지 진행하고 나머지는 나중에 해결할 것
        ```
        error: error validating "deployment.yaml": error validating data: failed to download openapi: the server has asked for the client to provide credentials; if you choose to ignore these errors, turn validation off with --validate=false
        ```



## 실습 정리
- EKS 접속 후 노드 그룹 삭제
- EKS 클러스터 삭제
- Terraform destroy








