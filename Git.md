# 클라우드 과정 10강 : git 중심의 소스코드 및 프로젝트 관리 전략 - Git



## 소스코드를 관리한다는 것은?
- 소스코드 백업 : 내가 작성한 코드를 서버에 올려 백업
- 버전관리 : 이전 버전으로 복구 가능하게 하는 것
- 협업 관리 : 누가 언제 어떤 소스코드를 수정했는지 확인 가능 한 것
- 보안 관리 : 허용된 사람만 소스코드에 접근 가능하게 하는 것


## 어떤 도구를 사용하여 소스코드를 관리할까 ?
- SCM(Software Configuration Management, 형상관리도구)
    - 소프트웨어의 변경사항을 체계적으로 추적하고 통제하는 것
- 자주 거론되는 SCM 도구
    - CVS (Concurrent Versions System)
    - SVN (Subversion)
    - GIT (Global Information Tracker)

> 그 외로 VCS(Version Control System)이라 불리기도 하고,
> GIT은 DVCS(Distributed Version Control System)으로 불리기도 한다.

## SVN vs GIT
- SVN
    - 폴더 중심으로 checkout / commit / push 진행
    - 따라서, 서버 중앙에서 push에 따른 conflict 제어 등이 이러남
    - 모든 개발자는 로컬에서 별도의 브랜치를 갖고 개발하는 것이 아닌, 모두 하나의 브랜치(=폴더)에서 수정된 파일을 서버에 직접 업로드
    - 단순하고, 직관적이고 commit/push를 가능한 신중히 해야함
- GIT
    - 브랜치 중심으로 checkout / commit / push 진행
    - 따라서, 로컬에서 해당 브랜치의 최신 버전을 받아 conflict를 해결하여 push 등이 일어남
    - 모든 개발자는 로컬에서 별도의 브랜치를 갖고 개발하여, local history 관리가 가능하고 로컬에서 개발된 사항을 별도로 업로드하는 방식
    - 복잡하지만, commit/push를 브랜치 기준으로 하기 때문에 다수의 개발자들과 협업하기에 용이

## GIT 브랜치 전략

> 만약, 전체 소스코드를 하나의 브랜치로 관리한다면 => 오히려 SVN이 좋은 선택

- 브랜치는 그럼 어떤 기준으로 만들까?
    - 소프트웨어 개발 정책에 따라 브랜치 전략이 연결되어야 한다.
    - 제품 관리 정책에 따라 브랜치 전략이 나뉘어지게 된다.

- 소프트웨어 개발 정책
    - Develop(개발) - QA(검증) - Production(배포)에 맞춰 브랜치 구별
    - 정적 소스코드 검사 (lint)나 코드 리뷰 등을 어떤 브랜치에서 할지 고민 필요
    - 테스트코드가 있다면 어떤 브랜치에 적용할지 고민 필요

- 제품 관리 정책
    - 고객의 요구사항에 따라 하나의 제품이 여러 버전으로 구별될 수 있음
    - 예) 퍼블릭으로 B2C 형태의 오픈된 버전과 폐쇄형으로 B2B/B2G 형태의 설치형 버전 등등
    - OSMU(One-Source Multi-Use)를 유지할 것이냐, 제품을 나눌 것이냐

## 소프트웨어 개발 정책 기준의 GIT 브랜치 전략
- 기본 브랜치
    - master (또는 main) 브랜치: 배포가 가능한 최신 버전의 소스코드를 담은 브랜치
    - develop 브랜치 : 개발 기준 최신 버전의 소스코드를 담은 브랜치
- 개발용 브랜치
    - feature / {업무ID or 이름} : develop 브랜치를 기준으로 특정 feature를 추가하기 위해 생성된 브랜치. 최종적으로 develop 브랜치에 merge 된다.
    - hotfix / {업무ID or 이름 or 날짜} : master 브랜치를 기준으로 빠르게 제품에 배포하기 위해 생성된 브랜치. 최종적으로 master, develop 브랜치에 merge 된다.
- 배포용 브랜치
    - release / {날짜} : develop 브랜치를 기준으로 배포를 하기 위해 생성된 브랜치. 해당 브랜치를 기준으로 QA가 진행되고, 최종적으로 master 브랜치에 merge 합니다.


## GIT-FLOW 도구 활용
- git 브랜치 관리 도구. 간단히 설치할 수 있고 가벼움
- master / develop / feature / hotfix / release 브랜치 관리 방식을 git-flow가 관리
- 예를 들어, hotfix를 merge할 때 master와 develop 모두 merge해야하는데 git flow hotfix finish -Fp {브랜치명} 명령어로 모두 merge가능
    - master, develop 브랜치로 merge
    - Fetch를 받아 최신 버전인지 자동 확인
    - 자동 Push까지 진행

- 실무에서 사용되는 GIT-FLOW
    - https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow 
    - https://techblog.woowahan.com/2553/

- 구름에서 사용하는 GIT-FLOW
    - ClickUp이라는 업무 도구에 Github를 연동하여 Task 카드의 ID를 feature, hotfix 브랜치 이름으로 두고 Task 카드에 커밋 내용들이 연결되도록하여 Task 카드만 보더라도 개발 내용을 알 수 있도록 구현

## Git Repository 웹 서비스 선택
- git을 통해 커밋된 사항을 push할 Remote 서버가 필요한데, 보통 Github를 많이 사용

### Github만 사용될까 ?
- Github 외에도 Gitlab, Bitbucket 등 대체제도 많음
- 특히, Github가 MS에 2018 인수되면서 여러 대안이 나옴
- B2B에서는 Github Enterprise외에도 Gitlab을 이용해 자체 구축하거나, Atlassian에 종속성이 높은 프로젝트인 경우 Bitbucket도 많이 사용됨
- Gitlab의 경우, CI/CD를 원 솔루션으로 기능을 포함시키고 있고 Github는 3rd Party를 따로 구축해야하는데 주요 특징

> Github 사용을 많이 해봤으면, Gitlab 사용도 해보면 경험상 나쁘진 않음

## 정적 소스코드 검사, 코드리뷰, 테스트코드 적용

### 정적 소스코드 검사
- 보통 commit 시 소스코드 검사를 하여 여러 컨벤션 체크나 문법 체크를 진행
- git에서는 commit이 들어가기 전에 특정 스크립트를 실행시켜주는 git hooks라는 기능을 제공
- 여러 도구가 많은데 husky 많이 사용 중
- 프론트엔드 사이드에서는 husky로 git hooks 설정해서 lint-staged 이용해서 ESLint 많이 사용

### 코드 리뷰
- 코드 리뷰는 작성된 코드의 목적을 이해하고, 목적에 맞게 작성되었는지와 구조적으로 알맞게 작성되었는지 그리고 효율적으로 작성되었는지 등을 확인하기 위한 작업
- 따라서, 전체 코드 작성된 상황을 확인할 수 있어야 작업 능률이 올라가서 보통 로컬보다는 git repository를 담은 서비스(ex: Github)에서 보통 확인
- 보통 feature 브랜치를 develop 브랜치에 merge 전에 코드 리뷰를 하는 경우가 많음

### 테스트 코드 적용
- 정적 소스코드와 마찬가지로 commit 전에 Unit test를 하는 경우가 많음
- 필요에 따라서는 CI/CD에 테스트 코드를 넣고, 실패하면 배포를 시키지 않는 경우도 있다.
- Jenkins, argoCD 등에 E2E Test를 많이 적용. E2E Test는 playwright 많이 사용



## 코드 리뷰를 위한 Pull Request(PR) 전략
- Git Repository 관리 시스템으로 가장 많이 사용되는 Github에서는 Pull Request라 불리고, Gitlab에서는 Merge Request(MR)으로 불림

### 메인 라인 브랜치 (예 : develop) merge 프로세스
- 개발자가 목적(feat, hotfix 등)에 맞게 코드를 생성/수정 진행 후 PUSH
- Github 내 Repository 대시보드에서 Pull Request 탭에서 develop 기준으로 Pull Request 발행 진행
- 코드 리뷰를 효율적으로 하기 위해 보통 Conversation에 아래 사항을 적시
    - Task Summary: 태스크 목적 설명
    - Description : 코드 작성 사항 상세 설명
    - Self Checklist : PR의 적절한 라벨을 추가했는지? PR 제목을 컨벤션에 맞게 작성했는지? 등등
    - Document : 관리 기획 문서, 테크 스펙 등
- 리뷰어 선정 및 Review Request 진행
- Review 진행 및 피드백 반영
- Merge 진행



## Merge 전략

### Merge
- 개발자 작업한 커밋들이 하나의 브랜치로 묶어 주요 브랜치 (예: develop)에 merge
- Github 내 Commit 리스트를 보면 커밋들이 묶인 브랜치가 merge된 것을 볼 수 있다.
- 제품 리더 성향에 따라 다르지만, Merge 기간이 길고 선택적으로 Merge 할 때 자주 사용 (오픈 소스 커뮤니티 등에서 많이 쓰임)

### Squash and Merge
- Merge와 다르게, 개발자가 작업한 커밋들이 하나의 커밋으로 뭉쳐져서 하나의 브랜치로 묶여 주요 브랜치에 merge
- Github 내 Commit 리스트를 보면 커밋들이 묶인 브랜치가 merge된 것을 볼 수 있음. Merge와 동일
- Merge 기간이 짧고, Commit이 많은 경우는 이 전략을 자주 사용함
- 우리가 아는 대부분의 제품 개발은 보통 이 Merge 방식을 채택

### Rebase and Merge
- 개발자가 작업한 커밋들이 주요 브랜치에 바로 merge. 마치 주요 브랜치에서 작업한 것처럼 보임
- Github 내 Commit 리스트를 보면 커밋들이 작업한 그대로 보임
- Commit 하나 하나를 신중히 관리할 때 사용. 제품 개발팀 분위기나 제품에 따라 채택되는 경우 존재

## 제품 관리 정책 기준의 GIT 브랜치 전략

### 하나의 소스코드인데 연결된 제품이 여러 개일 수 있을까?
- 프론트엔드 서버 기준으로 같은 기능이어도 고객에 따라 용어가 달라져야 하는 경우
- 프론트엔드 서버 기준으로 사용되는 데이터는 같은데, UI/UX가 고객에 따라 달라져야 하는 경우
- 퍼블릭 버전 (SaaS) vs 설치형 버전

### 기능을 옵션화 vs 별도의 제품 구별
- 기능 옵션화를 하면 OSMU(One-Source Multi-Use) 가능. 단, 기능이 복잡해짐
- 별도의 제품으로 구별하게 되면 제품 목적은 명확해지나, 공통 기능을 개발할 때는 동시에 Merge를 해야하는 불편함 존재
- 예를 들어, 교육용 채팅 서비스를 간단히 만들었는데, 기업용 채팅 서비스를 만들어 시장 진출하자고 하면 ?

### GIT 브랜치 전략 측면
- 해당 Repo에서 별도 master 브랜치 생성 후, 업데이트용 브랜치 2개로 만들어서 두 master 브랜치에 별도 PR 진행
- 커밋만 따로 빼서 cherry-pick으로 별도 master에 merge 진행도 가능
- 보통 기능을 옵션화하여 OSMU를 유지하거나, 아예 Repo를 나누고 공통 Product 관리 Repo를 별도로 관리하기도 함

## 자주 사용되는 GIT 명령어 리스트

### 기본 명령어
- git clone
- git checkout
- git add
- git commit
- git push
- git branch
- git status
- git log

### 실무에서 자주 사용하는 명령어
- git reset
- git diff
- git stash
- git cherry-pick


## GIT Tag 전략
대부분의 제품은 master 브랜치 기준으로 최신 버전을 유지하고 배포되기 때문에 Tag를 사용하는 경우가 드물다. 단, 오픈소스처럼 버전을 갖고 단계별로 배포되어야 하는 경우에 자주 사용

### X.Y.Z 버전을 통한 엄데이트 명시
- 업데이트 규모나 정책에 따라 Major / Minor / Fix 로 나누고, 이를 숫자로 표현하여 .을 붙여 버전을 명시
    - X : Major 업데이트, 새로운 기능들이 추가되었을 때나 전체적인 업데이트가 발생했을 때
    - Y : Minor 업데이트, 큰 기능에서 조금의 기능들이 업데이트 되거나 개선 되었을 때
    - Z : Fix 업데이트, 오류 수정 및 성능 개선 되었을 때
- 예) v2.0.1

### 제품 출시 전략 : Nightly Build + Tag Release
- 매일 또는 주기적인 업데이트 사항은 Nightly Build 버전으로 제공하여 안정적이지 않은 버전이라도 좀 더 빠르게 제품을 사용해 볼 수 있게 제공
- 주기적인 업데이트 사항을 묶어 QA를 통해 안정화된 버전을 Tag Release 버전으로 공유


## 소스코드 관리를 위해 개발팀이 갖고 가면 좋은 정책 or 컨벤션
- 기본 브랜치 정책
    - main 브랜치 하나로 관리할 것인가
    - develop, master 등의 브랜치 전략을 쓸 것인가
- feature, hotfix에 대한 정책
    - 어떤 상황에서 hotfix를 쓰게 하고, feature를 쓰게 해야할까
    - 바쁘면 무조건 hotfix가 맞을까
- git commit 컨벤션
    - git commit 메시지는 어떻게 규격화 해야할까 - 영어인지 한국어인지
    - git commit 전에 어디까지 검사를 하는게 맞을까
- PR 정책
    - 코드 리뷰는 몇 명에게 받을까?
    - 코드 리뷰는 누구에게 받을까 ?
    - 코드 리뷰 기준은 어떻게 둬야 할까?
    - Merge 정책은 어떻게 설정하는게 좋을까
- Task 관리 도구와의 연동 정책
    - Jira, Trello 등의 업무 관리 도구에서 Task 카드와 git 브랜치를 어떻게 연결할 것인지
    - 예) Task 카드와 feature 브랜치를 1:1 매핑하고, feature 브랜치가 merge 되면 Task 카드의 status를 Doing에서 Done로 바꾼다던지
- 기술 문서 정책
    - 소스코드 개발물이 어떠한 기획으로 개발되었는지 알기 위한 테크 스펙 문서를 어떻게 연결할 것인가
    - Task 관리 도구에 연동하는 것도 방법
- API Documentation 정책
    - API를 일일히 문서 형태로 만드는 것보다 오픈소스나 SaaS를 써서 자동화하여 만드는 것이 효율적
    - API 스펙은 OpenAPI 스펙을 가장 많이 사용하고, 소스코드로부터 API Docs를 추출하는 형식. 주석을 통해 만들어지거나 API 구성을 인식해서 만들어지기도 함.

## GIT COMMIT 정책

### COMMIT 메시지 정책
- Commit 메시지 구조는 Subject(제목) / Body(본문) / Footer(꼬리말) 로 구별
- 실제 협업 시에 Commit 제목을 가장 많이 보고, 어떻게 코딩을 했는지는 실제 작성된 코드를 보는 경우가 많음
- 본문, 꼬리말 내용은 기능이나 변경 사항 검색 시에 유효. 테크 스펙 문서를 검색해서 commit을 찾아가는 경우도 있음.

### COMMIT 제목 정책
- type(scope): message 형식
- 자주 사용하는 type들
    - feat
    - fix
    - refactor
    - docs
    - chore
- scope는 제품 내 범위로 설정
    - api, apply, router, learn, teach 등등

## CHANCELOG.md 파일 만들기
Repository 별로 업데이트 사항을 commit 메시지를 일일히 보기 어렵기에 CHANGELOG.md 파일을 만드는 경우가 많음. CHANGELOG.md 파일은 commit 메시지의 '제목'을 모아 만듦

### 간단하게 git log로 만들기
- git log --pretty="-%s" 명령어 결과를 CHANGELOG.md 파일로 만드는게 가장 쉬운 방법
- git log --pretty="-%s" > CHANGELOG.md 파일로 만들면, 간단히 결과 확인 가능
- 다만, 날짜나 commit 분류가 되지 않아 너무 단순함

### CHANGELOG 라이브러리 사용하기
- CHANGELOG 가독성 있게 만들어주는 라이브러리를 찾아 적용
- standard-version이 직관적이라 많이 사용
    - 단, 커밋이 type(scope): message 형식이어야 CHANGELOG에 남음
    - 자동으로 tag도 생성해준다.

## Github Actions 활용하기
- 특정 Repo에 대해 pull, push와 같은 여러 상황에서 자동으로 원하는 워크플로우를 동작시켜주는 기능
- 보통 CI/CD를 간단히 구현할 때 많이 사용, Github에 종속성이 생기기 때문에 Github가 실행하는 스크립트가 외부 자원에 접근이 가능해야 함
- 즉, Github IP들을 열어야 할 수 있는데 이 뜻은 Github 전체에서 접근이 가능해진다는 뜻
    - https://api.github.com/meta 

### 어떻게 "구성할까"
- Repo 밑에 .github/workflow/{이름}.yml 파일을 만들면 자동으로 연동됨
- yml 안에서 event를 정의하거나, 수동으로 Trigger 하고 싶은 경우, workflow_dispatch로 정의하면 됨

### 어떻게 "사용할까"
- Jenkins 등을 구축하지 않고 CI/CD를 빠르게 구현할 때 활용
- 외부 자원 접근 문제로 github actions에 연결된 yml 파일을 self-hosted runner로 물리 서버로 연동하기도 함
- HTTPS API로 수동 Call도 가능. 서비스와 연결하거나 Jenkins에서 Call 할 수도 있음


## 실습1 git 기본 명령어 수행
- 로컬에서 나만의 브랜치 생성, 삭제
    - 브랜치 생성
        - `git branch {브랜치명}` : 로컬에서 현재 브랜치 기준으로 새로운 {브랜치 이름}을 생성
        - `git branch` 명령어로 생성된 브랜치 확인 가능
    - 브랜치 삭제
        - `git branch -d {브랜치명}` : 로컬에서 해당 브랜치 삭제
        - git branch 명령어로 삭제되었는지 확인

- 생성된 브랜치로 이동
    - 브랜치 변경
        - `git checkout`

- 새로 생성된 파일 추가
    - 새로 생성된 파일은 git add를 통해 추가해야 이후 변경사항이 추적됨
    - git status로 Untracked files 리스트 확인
    - git add {새로운 파일 위치}로 파일 추가
    - git status로 Changes to be committed로 파일 추가된 것 확인

- 추가한 파일을 commit
    - `git commit -m {message}`
    - git log로 올라간 commit 리스트 확인 가능

- push해보기
    - commit 된 내용을 `git push origin {브랜치}` 명령어를 이용하여 올리기
    - 올리고 Repository로 가서 push된 내용 확인

- 파일 수정하고 변경사항 확인하기
    - `git status` 를 통해 현재 git 기준 변경사항 확인 가능
    - 수정했던 파일들이 modified가 붙은 상태로 리스트업된 것을 확인 가능

- 수정된 사항 모두 날리기
    - 파일 잠깐 수정해서 테스트해보고, 수정 사항 날리고 싶을 때
        - `git checkout -- .` 명령어로 수정된 사항 모두 날아갔는지 확인
        - 생성된 파일은 삭제되지 않는다. -> 파일은 직접 삭제 해야함 !!


## git 실무에서 자주 사용하는 명령어 체험
- 아직 push하지 않은 커밋 취소
    - 파일을 수정하거나 생성하여 add한 후, commit 진행
    - push가 되지 않은 상태에서 commit을 취소하고 싶다면
    - `git reset HEAD^`을 통해 직전 commit 취소 가능
    - git reset 방식을 더 자세히 알고 싶으면 구글링 해볼 것!!

- 내가 수정한 파일의 수정 사항을 빠르게 보고 싶을 때
    - 파일을 수정한 후, 수정된 사항을 기존 파일과 비교하여 보고 싶은 경우
    - `git diff` 명령어로 확인 가능
    - `git diff {파일 위치}`로 하면 해당 파일에 대한 수정사항 확인 가능

- 내가 수정한 곳이 잘못된 브랜치라 올바른 브랜치로 수정사항을 옮기고 싶을 때
     - 열심히 수정했는데 잘못된 브랜치에서 수정하고 있었다면, 내 수정사항을 잠깐 임시 저장해놓고, 올바른 브랜치로 옮겨 임시 저장된 사항을 불러올 수 있음
     - 잘못된 브랜치에서 파일을 수정하고 commit 되기 전 `git status`로 수정사항 확인
     - `git stash` 명령어로 임시 저장하고, `git status`로 없어진 수정사항 확인
     - `git checkout` 등으로 올바른 브랜치로 이동
     - `git stash pop`으로 임시 저장했던 내용이 불러와지고 `git status`로 변경사항 확인
     - 이후 `commit/push` 진행

- 다른 브랜치에 있는 특정 커밋을 내 브랜치로 옮기기
    - 서로 다른 작업자가 비슷한 업무를 하여 내 작업 파일을 상대에게 주고 싶은 경우, 두 브랜치를 merge 하는 것도 방법이지만 특정 commit만 주어야 conflict가 안나고 빠르게 해결되는 경우 존재
    - devlop 브랜치에 commit을 해야 하는데, main에 했을 때
    - main 브랜치에서 `git log`로 해당 commit id 복사하기
    - `git checkout develop`로 브랜치 체크아웃
    - `git cherry-pick {commit-id}`로 변경
    - main 브런치로 가서 `git reset HEAD^`로 잘못된 커밋 삭제


## GIT-FLOW 활용해보기

- git flow 설치
    ```
    brew install git-flow-avh
    ```

- git flow setting
    ```bash
    $ git flow init
    Initialized empty Git repository in /Users/sindongseong/DsStudy/nosqlTest/.git/
    No branches exist yet. Base branches must be created now.
    Branch name for production releases: [master] main
    Branch name for "next release" development: [develop] dev

    How to name your supporting branch prefixes?
    Feature branches? [feature/] feat/
    Bugfix branches? [bugfix/]  
    Release branches? [release/] 
    Hotfix branches? [hotfix/] 
    Support branches? [support/] 
    Version tag prefix? [] 
    Hooks and filters directory? [/Users/sindongseong/DsStudy/nosqlTest/.git/hooks]
    ```
    > `git branch` 입력 시 main과 dev 브랜치 두 개가 존재 해야함


- main 브랜치, develop 브랜치 Repo에 올리기
    - git flow 사용하기 전에 가장 기본 브랜치 2개를 Repo에 Push
    - git flow init으로 dev 브랜치가 생성되었으면 그대로 `git push origin dev`

- feature 브랜치 따기
    - `git flow feature start 0730-1`
    - 'feat/0730-1'로 브랜치가 생성됨

- Commit 컨벤션
    - `git commit -m "feat(init): {commit Message}"`

- feat 브랜치에 수정 작업이 완료되면
    - `git push origin feat/0730-1`
    - Pull Request 생성하기 -> base는 dev가 되어야함에 주의
    - Review 요청도 해보기 -> Collaborators를 먼저 추가해줘야 한다.

- 코드 리뷰 및 Merge 진행
    - 리뷰자는 PR 상세 페이지의 Conversation에서 수정된 코드를 보며 Comment를 달아준다.
    - Comment를 달 때, Comment 하나로 충분하면 "Add single comment" 버튼을 누르고, 여러 Comment를 남길거면 "Start a review" 버튼으로 진행
    - 개발자는 Comment 내용을 보고 파일 수정하여 push
    - 리뷰자는 수정된 내용을 보고 오른쪽 위에 "Finish your review" 버튼을 눌러, 승인이면 Approve, 아직 수정 사항이 필요하면 Request changes으로 진행
    - 리뷰자가 모두 Approve 해주면, 개발자는 Conversation에서 맨 마지막으로 스크롤을 내려 Merge pull request를 진행
    - 상세한 코드 리뷰 및 Merge 방식은 웹 검색을 해볼 것

- Hotfix 브랜치 생성해보기
    - feature 브랜치를 여러 번 PR -> develop으로 merge 한 다음, 이번엔 hotfix 진행
    - `git flow hotfix start 0730-1`으로 hotfix/0730-1 브랜치로 생성
    
    > hotfix는 로컬에서 1개만 만들 수 있음. 기존 것을 finish해야 새로운 hotfix 브랜치 생성 가능

- Hotfix Finish하기
    - Hotfix 브랜치는 로컬에서 바로 `git flow hotfix finish` 명령어를 통해 main(or master), develop 브랜치에 merge를 진행
    - Hotfix 브랜치에 파일 수정하고 생성하여 commit 한 다음, push를 하지말고 `git flow hotfix finish -Fp 0730-1` 명령어를 사용해야한다.
    - finish 명령어를 수행하면, develop -> main 브랜치로 push 하면서 commit 메시지를 추가하는 에디터 화면이 여러번 뜬다.
    - Github에서 main(or master) 브랜치에 hotfix 브랜치가 merge 된 것을 확인 (로컬)
    - 이 변경 사항을 원격에도 동기화 해주고 로컬과 원격의 dev에도 동기화 해줘야 한다.
        ```
        git checkout main
        git push origin main

        git checkout develop
        git pull origin develop  # 최신 상태 유지
        git merge main
        git push origin develop
        ```








