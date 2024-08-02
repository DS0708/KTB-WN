# KTB 클라우드 과정 필기 노트 / 7월 11일 (9일차) - DockerCompose


## docker compose
- 여러 Docker 컨테이너를 하나의 애플리케이션으로 정의하고 관리하기 위한 도구
    - 복잡한 멀티 컨테이너 애플리케이션을 쉽게 설정하고 실행


- 기능 설명
    - build
        - context: Dockerfile이 있는 디렉토리를 지정
        - dockerfile: 사용할 Dockerfile의 경로를 지정
        ```
        build:
            context:./app
            dockerfile:Dockerfile
        ```
    - image
        - 사용할 Docker 이미지를 지정
            - 이미지를 빌드하지 않고 이미 존재하는 이미지를 사용할 때 사용
    - ports
        - 호스트와 컨테이너 간의 포트 매핑을 지정
        ```
        ports:
            -"5000:5000"
        ```
    - environment
        - 컨테이너 내에서 사용할 환경 변수를 지정
        ```
        environment:
            -FLASK_ENV=development
            -DATABASE_URI=mysql://~~~~
        ```
    - volumes
        - 호스트와 컨테이너 간의 파일 시스템 마운트를 지정
        ```
        volumes:
            -./app:/app
        ```
    - depends_on
        - 컨테이너 간의 의존성을 지정합니다. 예를 들어, web 서비스는 db 서비스가 시작된 후에 시작
        ```
        depends_on:
            -db
        ```
    - networks
        - 서비스가 연결될 네트워크를 지정
        ```
        networks:
            -app-network
        ```
    - restart
        - 컨테이너 재시작 정책을 지정합니다. always, on-failure, unless-stopped 등의 값을 가질 수 있음
        ```
        restart: always
        ```
    
## docker compose 기능
- 멀티 컨테이너 애플리케이션 정의
    - docker-compose.yml 파일을 통해 여러 컨테이너를 정의
        - 각 컨테이너의 이미지, 볼륨, 네트워크 설정 등이 포함
- 서비스 간 의존성 관리
    - 여러 컨테이너 간의 의존성을 정의하고, 필요한 순서에 따라 컨테이너를 시작할 수 있음
        - 예를 들어, 웹 애플리케이션과 데이터베이스를 사용하는 경우, 데이터베이스가 먼저 시작되고 웹 애플리케이션이 시작되도록 설정할 수 있음
- 일관된 개발 환경 제공
    - 동일한 docker-compose.yml 파일을 사용하여 로컬 개발 환경과 프로덕션 환경을 일관되게 설정할 수 있음
        - 개발과 운영 간의 환경 차이를 줄여줍니다.
- 간편한 실행 및 종료
    - docker-compose up 명령을 통해 정의된 모든 컨테이너를 한 번에 시작하고, docker-compose down 명령을 통해 모든 컨테이너를 한 번에 종료할 수 있음

## docker compose의 주요 명령어
- docker-compose up
    - docker-compose.yml 파일에 정의된 모든 서비스를 빌드하고 시작
- docker-compose down
    - 실행 중인 모든 서비스를 중지하고, 네트워크와 볼륨을 포함한 모든 리소스를 정리
- docker-compose ps
    - 현재 실행 중인 서비스를 목록화
- docker-compose logs
    - 실행 중인 서비스의 로그를 출력

## 실습 1
- docker-compose 명령어 사용해보기

## 실습 2
- docker-compose를 통하여 앱 실행 및 관리해보기
        







