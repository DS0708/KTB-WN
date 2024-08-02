# KTB 클라우드 과정 필기 노트 / 7월 17일 (13일차) - AWS 2


## 목차
1. AWS의 EBS
2. AWS의 EFS
3. AWS의 S3
4. AWS의 Lambda
5. AWS의 CloudWatch
6. AWS의 CloudTrail


## EBS(Elastic Block Storage)
- Amazon Elastic Block Store(EBS)는 AWS의 블록 스토리지 서비스로, EC2 인스턴스에 사용되는 지속적인 스토리지
- 데이터베이스, 파일 시스템, 로그 스토리지, 애플리케이션 데이터 등에서 사용될 수 있다.
- 블록 스토리지는 데이터를 블록 단위로 저장하며, 이러한 블록들은 개별적으로 다룰 수 있다.
- EBS는 데이터의 지속성을 보장하고, 인스턴스를 중지하거나 종료해도 데이터는 유지된다.(특정 EBS는 제외)


### EBS 주요 특징
- 탄력성 : 필요에 따라 크기 조절 가능, 스냅샷을 통해 백업 및 복원 가능
- 고성능 : 다양한 성능 옵션(IOPS)이 제공, 종류로는 범용 SSD, 프로비저닝된 IOPS SSD, 처리량 최적화 HDD, 콜드 HDD 등이 있다.
- 내구성 : 데이터 복제 및 높은 가용성을 제공
- 보안 : 데이터 암호화 및 IAM 정책을 통한 접근 제어 가능
- 스냅샷 : EBS 스냅샷을 사용하여 볼륨의 시점 복사본을 생성할 수 있다, 스냅샷은 S3에 저장되며, 이를 통해 데이터 백업 및 복구가 가능

### EBS 볼륨 타입
- General Purpose SSD (gp3, gp2) : 균형 잡힌 성능과 비용
- Provisioned IOPS SSD (io1, io2) : 높은 IOPS가 필요한 워크로드
- Throughput Optimized HDD(st1) : 높은 처리량이 필요한 워크로드
- Cold HDD (sc1) : 저비용, 적은 빈도의 액세스가 필요한 워크로드

> 대규모 시스템이 아닌 이상 io1, io2는 너무 비용이 비싸서 사용하지 않으며 거의 gp3, gp2 수준에서 커버가 가능하다.

### EBS 사용 예
- 데이터베이스 스토리지 : 고성능이 요구되는 데이터베이스 (ex: MySQL, PostgreSQL, Oracle 등)에 io1, io2 볼륨을 사용하여 안정적인 성능 제공
- 파일 시스템 : EC2 인스턴스에서 파일 시스템을 구축하여 데이터를 저장하고 관리하는 데 gp2, gp3 볼륨 사용
- 빅 데이터 및 분석 : st1 볼륨을 사용하여 대규모 데이터 세트를 효율적으로 처리하고 분석
- 백업 및 아카이브 : sc1 볼륨을 사용하여 자주 접근하지 않는 데이터를 저렴하게 저장, 스냅샷 기능을 활용하여 데이터 백업 및 복구를 수행


### EBS 실습
- ec2의 ebs 스토리지 용량 증가 
- ec2가 사용하는 EBS 볼륨으로 들어가 수정에서 용량을 바꿔야함 !!  
- 후 아래 코드 실행
    ```bash
    sudo su     #root계정으로 변경

    lsblk       #현재 디스크 구성 확인

    growpart /dev/xvda 1    #/dev/xvda 디바이스의 첫 번쨰 파티션을 확장

    xfs_growfs /dev/xvda1   #파티션을 확장한 후 파일 시스템이 새로운 파티션 크기를 사용하도록 확장
    ```

> 참고로 Ext4 파일 시스템의 경우 'resize2fs /dev/xvda1'을 사용한다.

- 결과물
```bash
[root@ip-192-168-1-162 ec2-user]# lsblk
NAME      MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
xvda      202:0    0  10G  0 disk
├─xvda1   202:1    0   8G  0 part /
├─xvda127 259:0    0   1M  0 part
└─xvda128 259:1    0  10M  0 part /boot/efi
[root@ip-192-168-1-162 ec2-user]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        4.0M     0  4.0M   0% /dev
tmpfs           475M     0  475M   0% /dev/shm
tmpfs           190M  440K  190M   1% /run
/dev/xvda1      8.0G  1.6G  6.5G  20% /
tmpfs           475M     0  475M   0% /tmp
/dev/xvda128     10M  1.3M  8.7M  13% /boot/efi
tmpfs            95M     0   95M   0% /run/user/1000
[root@ip-192-168-1-162 ec2-user]# growpart /dev/xvda 1
CHANGED: partition=1 start=24576 old: size=16752607 end=16777183 new: size=20946911 end=20971487
[root@ip-192-168-1-162 ec2-user]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        4.0M     0  4.0M   0% /dev
tmpfs           475M     0  475M   0% /dev/shm
tmpfs           190M  440K  190M   1% /run
/dev/xvda1      8.0G  1.6G  6.5G  20% /
tmpfs           475M     0  475M   0% /tmp
/dev/xvda128     10M  1.3M  8.7M  13% /boot/efi
tmpfs            95M     0   95M   0% /run/user/1000
[root@ip-192-168-1-162 ec2-user]# xfs_growfs /dev/xvda1
meta-data=/dev/xvda1             isize=512    agcount=2, agsize=1047040 blks
         =                       sectsz=4096  attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1
data     =                       bsize=4096   blocks=2094075, imaxpct=25
         =                       sunit=128    swidth=128 blks
naming   =version 2              bsize=16384  ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=4096  sunit=4 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 2094075 to 2618363
[root@ip-192-168-1-162 ec2-user]# df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        4.0M     0  4.0M   0% /dev
tmpfs           475M     0  475M   0% /dev/shm
tmpfs           190M  440K  190M   1% /run
/dev/xvda1       10G  1.6G  8.5G  16% /
tmpfs           475M     0  475M   0% /tmp
/dev/xvda128     10M  1.3M  8.7M  13% /boot/efi
tmpfs            95M     0   95M   0% /run/user/1000
```


## EFS(Elastic FileSystem)
- 클라우드에서 파일 스토리지를 제공하는 서비스로, POSIX 인터페이스와 호환
    - POSIX 인터페이스 : Portable Operating System Interface + uniX
    - OS에 종속되지 않는 커널 형식(시스템 콜)을 지원하는 인터페이스
- 이는 여러 EC2 인스턴스가 동시에 데이터를 읽고 쓸 수 있도록 하며, 파일 시스템의 확장성과 고가용성을 보장
- 같은 VPC내에서 RWX(Read Write Many)를 지원, 이는 여러 인스턴스가 같은 파일시스템에 접근하여 읽기, 쓰기가 가능하다는 의미

> EBS는 고성능이 요구되는 경우나 각 인스턴스에 고유한 스토리지가 필요한 경우, EFS는 여러 인스턴스가 데이터를 공유해야 하는 경우에 적합

### EFS주요 특징
- 확장성 : 파일 시스템 크기가 자동으로 조정되며, 필요에 따라 확장 및 축소 가능 -> 별도의 관리 작업 없이 수 페타바이트 규모로 확장 가능
- 고가용성 및 내구성 : 여러 가용 영역(AZ)에 데이터를 자동으로 복제하여 높은 가용성과 내구성 제공
- `동시 접근 : 여러 EC2 인스턴스에서 동시에 Access할 수 있으며, 이는 공유 파일 시스템이 필요한 워크로드에 적합`
- 성능 모드
    - Standard Performance Mode : 대부분의 워크로드에 적합한 기본 성능 모드
    - Provisioned Throughput Mode : 고성능이 필요한 워크로드에 적합하며, 처리량을 사용자가 지정할 수 있음
- 안정성 : VPC 내에서 파일 시스템을 마운트하여 네트워크 수준에서 보안을 강화할 수 있다. 또한 IAM을 사용하여 Access 권한 제어도 가능
- 비용 효율성 : 사용한 만큼만 비용을 지불하는 요금제이다.

### EFS 실습
- EFS 생성 전 보안그룹 생성(인바운드: NFS 포트, 서브넷 CIDR 오픈)
- EFS 보안 그룹 수정 -> efs -> 네트워크 -> 관리에서 해당 서브넷에 보안그룹 추가
- 새로 생성한 2개의 EC2에 모두 EFS를 마운트
    ```bash
    sudo su     #root권한으로 변경
    
    apt-get update
    apt-get install -y nfs-common   #NFS 클라이언트 설치

    mkdir -p /mnt/efs       #마운트할 디렉토리 생성

    mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-0acf393f18f886420.efs.ap-northeast-2.amazonaws.com:/ /mnt/efs
    ```
    - 참고로 mkdir -p 옵션은 해당 디렉토리를 모두 생성하는 것
    - mount -t [type] -o [option] [mount 대상] [mount할 로컬 위치]

> 여기서 mount를 하려고 했더니 DNS 서버가 efs의 dns를 인식하지 못했다. nslookup을 사용하여
> ec2가 efs의 dns를 인식하는지 확인했는데 그러지 못했음

- TroubleShooting : AWS의 공식문서를 찾아보니 mount 명령에서 DNS 이름을 사용하기 위한 조건이 3가지 있었음
    1. 연결 중인 EC2 인스턴스가 VPC 내에 있어야 하고, Amazon에서 제공하는 DNS 서버를 사용하도록 구성되어 있어야 한다.
    2. 연결 중인 EC2 인스턴스의 VPC에는 DNS 확인 및 DNS 호스트 이름이 활성화되어 있어야 한다.
    3. 연결 중인 EC2 인스턴스는 EFS 파일 시스템과 동일한 VPC 내에 있어야 한다.

> 나는 새로운 나의 VPC를 만들어 사용하고 있는데 위의 조건중 2번을 만족하지 않았다. 그래서 VPC 설정에서 'DNS 확인' 및 'DNS 호스트 이름'을 활성화 시켜 문제를 해결하였음

- 다시 2개의 ec2에 efs mount하기 
    ```bash
    [root@ip-192-168-1-162 efs]# df -T | grep nfs
    fs-0acf393f18f886420.efs.ap-northeast-2.amazonaws.com:/ nfs4     9007199254739968       0 9007199254739968   0% /mnt/efs
    ```

- 1개의 EC2에서 EFS내에 파일 주입
    ```bash
    root@ip-192-168-1-178:/mnt/efs# echo "Hello covy" >> test.txt
    root@ip-192-168-1-178:/mnt/efs# ls
    test.txt
    ```
- 다른 1개의 EC2에서 파일 내용 확인
    ```bash
    [root@ip-192-168-1-162 efs]# ls
    test.txt
    ```
- umount
    ```bash
    sudo umount -l /mnt/efs
    ```


## S3(Simple Storage Service)
- 클라우드 기반의 객체 스토리지 서비스로 데이터를 객체 단위로 저장
- 각 객체는 파일 데이터와 메타데이터로 구성되며, 전 세계 어디서나 인터넷을 통해 접근 가능
- 정적 이미지를 제공하는 모든 페이지에서 사용가능하고 권한 설정에 따라 누구나 다운로드하도록 할 수 있다.

### S3의 주요 특징
- 내구성 및 가용성 : S3는 99.9999% 내구성을 제공, 데이터가 여러 가용 영역에 복제되어 높은 가용성 보장
- 확장성 : 데이터의 양과 상관없이 거의 무한대로 확장 가능 -> 대규모 데이터 저장 및 분석에 적합
- 보안 : 데이터를 암호화하여 저장하며, 전송 중인 데이터도 암호화할 수 있다.
- 비용 효율성 : 저장한 데이터 양과 사용한 리소스에 따라 비용을 지불 -> 자주 접근하는 데이터와 자주 접근하지 않는 데이터를 각각 다르게 요금이 부과되는 다양한 스토리지 클래스를 제공
- 다양한 스토리지 클래스
    - S3 Standard: 자주 접근하는 데이터에 적합한 기본 스토리지 클래스
    - S3 Intelligent-Tiering: 접근 패턴에 따라 자동으로 비용을 최적화해주는 스토리지 클래스
    - S3 Standard-IA (Infrequent Access): 자주 접근하지 않는 데이터에 적합
    - S3 One Zone-IA: 하나의 가용 영역에만 저장되는 저비용 스토리지
    - S3 Glacier: 저렴한 장기 보관용 스토리지
    - S3 Glacier Deep Archive: 가장 저렴한 장기 보관용 스토리지
- 데이터 관리 기능 : 버전 관리, 수명 주기 정책, 객체 잠금, 이벤트 알림 등을 통해 데이터를 효율적으로 관리 가능

### S3 사용 예시
- 백업 및 복원 : 중요한 데이터의 백업 및 복원에 사용
- 데이터 아카이빙 : 오랫동안 보관해야 하지만 자주 접근하지 않는 데이터의 아카이빙에 적합 -> S3 Glacier와 Glacier Deep Archive를 사용하면 저렴한 비용으로 데이터를 장기 보관 가능
- 빅 데이터 분석: 대규모 데이터 세트를 저장하고 분석하는 데 사용 -> S3의 확장성과 다양한 통합 도구를 통해 데이터를 효과적으로 분석할 수 있다.
- 웹사이트 호스팅 : 정적 웹사이트 호스팅에 사용 가능 -> HTML, CSS, JavaScript 파일을 S3 버킷에 저장하고, 이를 통해 웹사이트를 제공 가능
- 미디어 저장 및 배포 : 이미지, 비디오, 오디오 파일과 같은 미디어 콘텐츠를 저장하고 전 세계에 배포하는 데 사용


### S3 실습
- S3의 특정 이미지를 퍼블릭에 오픈하고 웹 주소로 접속 후 확인



## Lambda
- AWS Lambda는 서버를 프로비저닝하거나 관리할 필요 없이 코드를 실행할 수 있는 컴퓨팅 서비스
- 사용자는 단지 코드를 업로드하고 실행 조건을 설정하면, 나머지 인프라 관리는 AWS가 자동으로 처리

> 서버를 프로비저닝한다는 것은 서버를 설정하고 필요한 소프트웨어 및 하드웨어 자원을 준비하여 사용할 수 있도록 만드는 과정을 의미한다.

### Lambda 주요 특징
- 서버리스 아키텍처: 서버를 프로비저닝, 관리, 확장할 필요가 없다 -> AWS가 모든 인프라를 관리하므로 개발자는 코드에만 집중 가능
- 이벤트 기반 실행 : Lambda 함수는 다양한 이벤트 소스(S3, DynamoDB, Kinesis, SNS, CloudWatch, API Gateway 등)으로 부터 트리거된다.
- 자동 확장: Lambda 함수는 요청에 따라 자동으로 확장됨 -> 이는 수요에 맞게 컴퓨팅 자원을 효율적으로 사용 가능하게 함
- 비용 효율성 : 실행 시간과 요청 수에 따라 비용이 청구 -> 실행이 짧거나, 비정기적으로 실행되는 작업에 적합하며, 사용하지 않는 시간에는 비용이 발생하지 않는다.
- 다양한 언어 지원 : Node.js, Python, Java, Go, .NET core, Ruby 등 다양한 프로그래밍언어를 지원한다. -> 각 언어에 맞는 런타임 환경 제공
- 통합 및 확장성 : Lambda는 AWS의 다른 서비스와 원활하게 통합되며, 커스텀 런타임을 사용하여 다른 언어와 환경도 지원할 수 있다.


### Lambda의 사용 예
- 데이터 처리 : S3 버킷에 파일 업로드 이벤트가 발생할 때마다 Lambda 함수를 트리거하여 파일을 처리 -> 예를 들어 이미지 리사이징, 로그 파일 분석, 데이터 포맷 변환 등을 수행 가능
- 백엔드 서비스 : API Gateway와 통합하여 RESTful API 백엔드 서비스로 Lambda를 사용 가능 -> 서버 관리 없이 확장 가능한 API를 쉽게 구축 가능
- 실시간 파일 처리 : DynamoDB 또는 Kinesis 스트림의 데이터를 실시간으로 처리하여 로그 분석, 데이터 필터링, 알람 트리거 등을 수행
- 자동화 작업: CloudWatch 이벤트를 통해 일정에 따라 Lambda 함수를 실행하여 백업, 보고서 생성, 시스템 유지보수 작업 등을 자동화 가능

### Lambda 실습 1
- Lambda에서 함수 생성 (Runtime Python3.x)
- Add Trigger에서 S3 추가 (모든 이벤트)
- Lambda 함수 코드 작성
    ```python
    import json
    import boto3

    def lambda_handler(event, context):
        print("Received event: " + json.dumps(event, indent=2))

        bucket = event['Records'][0]['s3']['bucket']['name']
        key = event['Records'][0]['s3']['object']['key']
        
        s3 = boto3.client('s3')
        response = s3.get_object(Bucket=bucket, Key=key)
        data = response['Body'].read().decode('utf-8')
        
        print("Data from S3 object:", data)
        
        return {
            'statusCode': 200,
            'body': json.dumps('Processing complete!')
        }
    ```
- Lambda관련 Role 설정, S3권한 CloudWatch 권한

### Lambda 실습 2
- 함수 생성 (Python3.x Runtime)
    ```python
    import json

    def lambda_handler(event, context):
        # 이벤트 데이터를 로그로 출력
        print("CloudWatch Alarm Event: ", json.dumps(event))
        
        # 알람 메시지 추출
        alarm_message = event['detail']
        print("Alarm Message: ", json.dumps(alarm_message))

        return {
            'statusCode': 200,
            'body': json.dumps('Alarm processed successfully')
        }
    ```
- Role 생성 (AWSLambdaBasicExecutionRole, CloudWatchEventsFullAccess)


## CloudWatch
- AWS 인프라와 애플리케이션의 상태를 모니터링하고 관리하는 서비스
- CloudWatch는 다양한 AWS 서비스와 통합되어 메트릭을 수집하고, 경보를 설정하며, 로그를 모니터링하고 분석할 수 있다.

### CloudWatch 주요 특징
- 메트릭 수집 및 모니터링: EC2, RDS, S3, DynamoDB 등 다양한 AWS 서비스에서 메트릭을 자동으로 수집
- 로그 모니터링 : CloudWatch Logs를 통해 애플리케이션 로그, 시스템 로그, 사용자 정의 로그 파일을 수집하고 모니터링 가능
- 알람 설정: 특정 조건이 충족되면 알람을 생성하여 SNS 주제로 알람을 보내거나, Lambda 함수를 트리거하거나, Auto Scaling 조치를 취할 수 있다.
- 대시보드: CloudWatch 대시보드를 사용하여 다양한 메트릭을 시각화하고 모니터링할 수 있다. -> 사용자 정의 대시보드를 생성하여 중요한 메트릭을 한눈에 볼 수 있다.
- 이벤트: CloudWatch Events를 통해 시스템 이벤트를 감지하고, 지정된 대상(ex: Lambda, SQS, SNS 등)으로 알림 보내기 가능 -> 예약된 작업(Cron Jobs)도 설정할 수 있다.
- 경보 및 대응: 특정 메트릭이 임계값을 초과하면 경보를 설정하여 운영자에게 알리고, 자동으로 대응 조치를 취할 수 있다.

### CloudWatch 사용 예
- 인프라 모니터링: EC2 인스턴스의 CPU 사용량, 디스크 I/O, 네트워크 트래픽 등을 모니터링하여 시스템 성능을 분석하고 문제를 조기에 발견할 수 있다.
- 애플리케이션 모니터링: 애플리케이션의 사용자 정의 메트릭(예: HTTP 요청 수, 오류율 등)을 수집하여 애플리케이션 상태를 모니터링
- 로그 분석: CloudWatch Logs를 통해 애플리케이션 로그를 수집하고, 로그 데이터를 실시간으로 분석 가능
- 자동 대응: CloudWatch의 알람을 설정하여 특정 조건이 충족되면 자동으로 대응 조치 가능 -> 예를 들어, EC2 인스턴스의 CPU 사용량이 일정 수준을 초과하면 Auto Scaling을 통해 인스턴스를 추가 가능
- 이벤트 처리: CloudWatch Events를 사용하여 시스템 이벤트를 실시간으로 처리하고, 지정된 대상에게 알림을 보낼 수 있다.


### CloudWatch 실습
- EventBridge 규칙 생성
- 이름 입력 후 다음 - AWS 이벤트 - 이벤트 패턴 (AWS 서비스, CloudWatch, CloudWatch Alarm State Change)
- 다음 - Lambda 함수 - 2번 실습 람다 지정
- CloudWatch 대시보드 - 경보 - 경보 생성
- 지표 선택 - EC2 - 인스턴스별 지표 - 인스턴스 ID 입력 - CPUUtilization
- Create Alarm
- 알람 설정할 메트릭 - 인스턴스 CPU 사용률
- 알람 조건 - CPU 10% 이상



## CloudTrail
- AWS 계정 내에서 발생하는 모든 API 호출을 기록하는 서비스
- 로그는 AWS Management Console, AWS CLI, SDK 및 기타 AWS 서비스에서 발생하는 모든 활동을 포함
- CloudTrail 로그 파일은 지정된 S3 버킷에 저장되며, 로그를 분석하고 모니터링 하는 데 사용할 수 있다.

### CloudTrail의 주요기능







