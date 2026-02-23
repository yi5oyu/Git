<div align="center">

# **Git**

소스 코드의 변경 사항을 기록하고 여러 명이 협업할 수 있게 돕는 분산 버전 관리 시스템

| [목차](#링크) • [목차](#링크)  |

</div>

---

## 환경 설정

`로컬 저장소 생성/연결`

```
# 폴더 생성/이동
mkdir project && cd ./project

# 저장소 초기화
git init

# 기본 브랜치명 설정 (main or master)
git branch -M main

# 모든 파일 스테이징
git add .

# 첫 커밋
git commit -m "init: ~"

# github 원격 저장소 연결
git remote add origin <레포지토리_주소>

# 푸시
git push -u origin main
```

> 연결된 원격 저장소 목록/주소 확인
> git remote -v

`.gitignore`

```
# git이 추적하지 않을 파일 설정
# IDE 설정: .idea/, .vscode/
# 빌드 결과물: build/, out/, target/
# 환경 변수: .env(보안 정보)

.env
.vscode/
.idea/
*.iml
bin/
obj/
.vs/
*.user
*.db
*.db-journal
```

`VSCode 플러그인`

```
# 변경사항 시각적으로 볼 수 있음
Git Graph
```

## 커밋 컨벤션

[git-styleguide](https://udacity.github.io/git-styleguide/)

`커밋 메시지`

```
type: Subject
(공백)
body
(공백)
footer

# 제목 (type)
# 명령문 형태로 작성 (최대 50자 이내로 작성, 마침표를 사용하지 않음)

# 본문 (body)
# 변경 사항에 대한 자세한 설명이 필요할 경우 추가 (각 줄은 72자 이내로 작성)
# 어떻게 변경했는지보다는 왜 변경했는지 변경된 이유와 배경, 상세한 설명 포함

# 꼬리말 (footer)
# 관련된 이슈 번호를 참조
```

> feat(FE): 로그인 버튼 구현 (#13) (메인 이슈를 제목에 포함)

`type(커밋 유형)`

```
# 커밋 메시지 앞에 커밋의 목적을 나타내는 태그
feat        : 기능 (새로운 기능)
fix         : 버그 (버그 수정)
refactor    : 리팩토링
design      : CSS 등 사용자 UI 디자인 변경
comment     : 필요한 주석 추가 및 변경
style       : 스타일 (코드 형식, 세미콜론 추가: 비즈니스 로직에 변경 없음)
docs        : 문서 수정 (문서 추가, 수정, 삭제, README)
test        : 테스트 (테스트 코드 추가, 수정, 삭제: 비즈니스 로직에 변경 없음)
chore       : 기타 변경사항 (빌드 스크립트 수정, assets, 패키지 매니저 등)
init        : 초기 생성
rename      : 파일 혹은 폴더명을 수정하거나 옮기는 작업만 한 경우
remove      : 파일을 삭제하는 작업만 수행한 경우
ci          : CI 관련 설정 수정
release     : 버전 릴리즈
```

## 템플릿

`마크다운(.md) 또는 YAML(.yml) 형식으로 작성할 수 있음`

```
템플릿 유형
1. 이슈(Issue) 템플릿 [<프로젝트(root)폴더>/.github/ISSUE_TEMPLATE/]
 - 버그 리포트(Bug report)
 - 기능 요청(Feature request)
 - 사용자 정의 템플릿(Custom template)
 - 질문, 문서 개선, 작업 요청 등...

2. 풀 리퀘스트(Pull Request) 템플릿 [<프로젝트(root)폴더>/.github/PULL_REQUEST_TEMPLATE/]
 - PR 템플릿 
```

## 명령어

```bash
# 상태 확인/기록

# 커밋
# 스테이징된 변경 사항 커밋
git commit -m "메시지"
# 가장 최근 커밋 수정(메시지 오타, 빠뜨린 파일이 있을 때 사용)
git commit --amend

# 현재 브랜치를 특정 커밋 시점으로 되돌림
# 커밋 히스토리(해시값 등..)
git log
# --soft: 커밋만 취소하고 변경된 내용은 스테이징 상태로 유지
# --mixed(디폴트): 밋과 스테이징을 모두 취소하고 작업 내역(코드)만 남김
# --hard: 특정 커밋 시점 이후의 모든 변경 사항과 커밋을 완전히 삭제
# HEAD^ = 최신 커밋 취소, HEAD~(숫자) = 최근 커밋부터 숫자까지 커밋 취소
git reset <reset 모드> <리셋시점>
# 스테이징 영역에 올린 특정 파일을 다시 워킹 디렉토리로 내림
git reset HEAD <파일명>
# 기존의 커밋 히스토리 삭제하지 않고 특정 커밋의 내용을 취소하는 새로운 커밋 생성해 안전하게 되돌림
git revert <커밋해시>

# 브랜치
# 현재 파일 상태 확인
git status
# 브랜치 확인
git branch
# 브랜치 생성
git branch <브랜치이름>
# 브랜치 삭제
git branch -d <브랜치>
# 브랜치 변경
git checkout <브랜치>
git switch <브랜치>
# 브랜치 생성/이동
git switch -c <새브랜치>
# 브랜치 병합
git merge <브랜치>
# 이름 변경
git branch -m <이전 브랜치> <새 브랜치>
git push origin --delete <이전 브랜치>
git push origin <새 브랜치>

# 다른 브랜치로 이동할때 진행중인 파일 임시 저장
git stash
# 최근 작업 다시 꺼내오고 목록에서 삭제
git stash pop
# 현재 임시 저장된 작업 목록을 확인
git stash list
# 하나의 저장소에서 여러 개의 작업 디렉토리를 동시에 사용
git worktree add <path> <branch>
```

## 깃 전략

### [Git flow](https://github.com/yi5oyu/Git/tree/main/git_flow)

`대규모 프로젝트, 배포 주기가 정해진 개발에 적합한 브랜치 전략`

`브랜치 종류`

```
master/main: 제품 출시 버전을 관리하는 메인 브랜치
develop: 다음 출시 버전을 위해 모든 기능을 통합하고 개발하는 브랜치
feature: 새로운 기능을 개발하는 브랜치(develop에서 분기됨)
release: 배포 전 품질 검사(QA)/최종 버그 수정을 위한 브랜치
hotfix: 이미 출시된 제품에서 발생한 긴급 버그를 수정하기 위한 브랜치
```

### [Github flow](https://github.com/yi5oyu/Git/tree/main/github_flow)

`규모가 작고 main(master) 브랜치를 중심으로 수시로 배포가 일어나는 환경(CI/CD)에 적합한 전략`

## [Git Worktree](https://github.com/yi5oyu/Git/tree/main/worktree)


<!--
### Git Bash

`windows 환경에서 리눅스 터미널 명령어를 그대로 사용할 수 있게 하며 Git 명령어를 안정적으로 실행할 수 있는 환경`

버전 관리 시스템 (VCS): 파일 변화를 시간에 따라 기록했다가 나중에 특정 시점의 버전을 다시 꺼내 올 수 있는 시스템
분산 버전 관리 시스템 (DVCS): 모든 개발자가 저장소 전체를 자신의 컴퓨터로 복제(Clone)해 오프라인에서도 작업 가능한 시스템
-->
