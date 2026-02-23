<div align="center">

# **Git Worktree**

하나의 Git 저장소에서 여러 개의 작업 디렉토리를 동시에 사용할 수 있음

| [개요](#개요) • [트러블슈팅/복구](#트러블-슈팅) • [워크트리 명령어](#워크트리-명령어) • [Bare 레포지토리](#bare-레포지토리) |

</div>

## 개요

`빠른 컨텍스트 전환`: 임시 저장(stash)없이 폴더 이동만으로 브랜치 전환해 즉시 다른 작업을 동시에 수행할 수 있음  
`독립적 공간`: 물리적으로 분리된 폴더를 가지며 브랜치 옮겨 다니며 발생할 수 있는 덮어쓰기/충돌 최소화  

> 의존성에 따라 디스크 용량이 커질 수 있음  
> git 2.5 이상 필요

`워크트리 폴더 구조`

```bash
project/
├── src/
├── ...                
└── 📁 .git/
    ├── object/
    ├── ...
    └── 📁 worktrees/
        └── 📁 feat-backend/

feat-backend/
├── src/
├── ...                
└── .git                          # 파일(gitdir: /project/.git/worktrees/feat-backend)
```

`워크트리 사용`

```bash
# 1. 기존에 있는 브랜치를 가져옴
# path: ../<폴더 이름>
# branch: <브랜치 이름>
# git worktree add ../feat-backend feat/14-backend
git worktree add <path> <branch>

# 새로운 브랜치 생성과 동시에 체크아웃/폴더 생성 
git worktree add <path> -b <branch>

# 2. 브랜치 폴더로 이동
project cd ../feat-backend
```

## 트러블 슈팅

### 워크트리 생성 위치

`이미 사용중인 브랜치 안에 워크트리 생성하면 추적하지 않은 폴더로 인식(체크아웃된 브랜치 밖에 워크트리 생성해야함 ../)`

```
# 폴더 구조
folder/                     # root
├── 📁 project             # main 브랜치
└── 📁 feat-backend        # feat/14-backend 브랜치
```

### already checked out error

`한 곳에서 동시에 같은 브랜치를 참조(워크트리 연결) 할 수 없음`

### 의존성 관리

새로 만든 워크트리는 새 폴더로 각 프로젝트의 설정 파일, 환경변수, 포트번호 등을 수동으로 지정    

`원래 진행중인 체크아웃된 브랜치의 의존성 파일/폴더 없음(.gitignore에 등록된 파일, .git, .env, package.json ...)`    

> 새 워크트리를 만들면 빌드해야함(gradlew, npm build ...)
> 여러 워크트리를 동시에 띄울 경우 포트 충돌할 수 있음

### 워크트리 Cleanup

`git worktree remove 명령어를 쓰지 않고 탐색기에서 폴더를 삭제하면 git 저장소는 워크트리가 존재하고 있다고 생각해 같은 경로로 워크트리를 만들려 하면 에러가 발생`

```bash
# 워크트리 정보 강제 청소
git worktree prune
```

### 복구

```bash
# 1. 정리 후 재생성 (커밋하지 않은 변경사항은 되돌릴 수 없음)
# git worktree remove 명령어를 쓰지 않고 탐색기 or rm -rf 명령어로 직접 삭제한 경우
# 1-1. 워크트리 메타데이터 삭제
git worktree prune
# 1-2. 같은 브랜치로 새 워크트리 생성
git worktree add ../feat-backend feat/14-backend

# 2. Staged 변경 복구 (git add로 스테이징 변경사항 있음)
# 폴더 삭제했지만 prune 실행 전 (스테이징된 변경사항은 메인저장소 .git에 남아있음)
# 2-1. 삭제된 워크트리 폴더 재생성
mkdir feat-backend
# 2-2. git 연결 파일 수동 생성(.git 파일)
# gitdir: <원본저장소>/.git/worktrees/<워크트리이름>
echo "gitdir: User/name/project/.git/worktrees/feat-backend" > feat-backend/.git
# 2-3. 워크트리로 이동 후 상태 확인
cd feat-backend
# 스테이징된 변경사항
git status
# 2-4. 삭제된 파일 복구
git checkout -- .

# 3. 브랜치 복구 (워크트리와 연결된 브랜치까지 삭제한 경우 unknown revision HEAD)
# 3-1. reflog에서 삭제된 브랜치 마지막 커밋 찾기
git reflog # or gitk --reflog
# 3-2. 브랜치 재생성
# git branch <브랜치이름> <커밋해시>
git branch feat/14-backend ab2a35d
# 3-3. 워크트리에서 체크아웃
cd ../feat-backend
git checkout feat/14-backend

# 4. 연결 복구 (워크트리 폴더 이동된 경우)
# 이동한 워크트리 or 메인 저장소에서 repair 
# 이동한 워크트리에서 실행
cd <새로운_경로>/feat-backend
git worktree repair
# 메인 저장소에서 실행
cd <메인_저장소>
git worktree repair <새로운_경로>/feat-backend

```

## 워크트리 명령어

### 워크트리 조회

```bash
# 현재 등록된 모든 워크트리 조회
git worktree list

# 폴더경로 | 초신 커밋 해시 | 브랜치 이름
/Users/name/project         fba25sc [main]              # 메인 워크트리(워크트리 명령어로 제거할 수 없음)
/Users/name/feat-backend    a4b6cgd [feat/14-backend]   # 연결된 워크트리

# 상세 정보 포함
git worktree list -v

# 스크립트용 출력 형식
git worktree list --porcelain
```

### 워크트리 생성

```bash
git worktree add <path> <branch>
git worktree add <path> -b <branch>
```

### 워크트리 제거

```bash
# 브랜치 삭제가 아닌 물리적 파일 공간 삭제
# git worktree remove ../feat-backend
git worktree remove <path>
```

> 워크트리 Cleanup    
> git의 내부에 기록되 같이 정리해야함    
> 탐색기에서 폴더를 삭제 or rm -rf로 삭제하면 안됨    
> 저장하지 않은 변경사항이 있다면 커밋하거나 삭제

### 워크트리 lock

`네트워크나 드라이브 연결이 끊긴 상태에서 prune을 실행하면 없다고 생각함`

```bash
git worktree lock <path>
```

## Bare 레포지토리

`워크트리 안에서의 작업은 같고 워크트리의 관리(생성/삭제/조회)만 .bare를 사용`

`폴더구조`

```
C:/Users/name/                 # 홈 디렉토리(~/) 
└── 📁 develop/                # develop 안에서 생성되는 모든 프로젝트에 적용
    ├── .gitconfig             # Git 전역 설정
    ├── .gitignore_global      # 모든 프로젝트에 적용될 Ignore 규칙
    └── 📁 folder/             # root
        ├── .env.shared        # 전역 .env 파일
        ├── 📁 .bare/          # Bare 폴더
        ├── 📁 project/        # main 워크트리
        └── 📁 feat-backend/   # 브랜치 연결된 워크트리

or

📁 folder/                     # root
├── .env.shared                # 전역 .env 파일
├── .gitconfig                 # Git 전역 설정
├── .gitignore_global          # 모든 브랜치에 적용될 Ignore 규칙
├── 📁 .bare/                  # Bare 폴더
├── 📁 project/                # main 워크트리
└── 📁 feat-backend/           # 브랜치 연결된 워크트리
```

### 설정

`생성`

```bash
# 1. root 폴더 생성/이동
mkdir folder && cd folder

# 2. Bare 레포지토리
git clone --bare <클론할_레포지토리_주소> .bare

# 3. 메인 워크트리 생성
# bare 폴더 안에서 실행한 것처럼 함
git -C .bare worktree add ../project main
```

`Bare로 전환`

```bash
# ~/folder/project/ (체크아웃된 main 브랜치)

# 1. 상위 폴더로 이동
cd ../

# 2. .git 폴더를 .bare로 이름 이동/변경
mv project/.git .bare

# 3. 소스 코드가 없는 Bare 타입 선언
git -C .bare config --bool core.bare true

# 4. 기존 폴더 삭제/워크트리 생성
rm -rf project
git -C .bare worktree add ../project main
```

#### 전역 설정

`.gitignore`

```bash
# --global 설정은 항목당 단 하나의 경로만 가질 수 있음(둘 중에 하나 선택)

# 모든 Git 프로젝트 범위
# 1. .gitconfig 연결
git config --global include.path "~/develop/.gitconfig"
# 2. .gitignore_global 연결
git config --global core.excludesfile "~/develop/.gitignore_global"

# 프로젝트 범위
# 1. .gitconfig 연결
git config --global include.path "~/develop/folder/.gitconfig"
# 2. .gitignore_global 연결
git config --global core.excludesfile "~/develop/folder/.gitignore_global"
```

> 둘 다 사용할 경우 덮어쓰기됨

`.env`

```bash
# .env.shared
# 새로운 워크트리 폴더에서 명령어 실행(관리자 권한 필요)
mklink .env ..\.env.shared
```

### 트러블 슈팅

`Bare 레포지토리 루트에서 git pull 실행하면 안됨(.bare 폴더엔 파일이 없어 에러 발생)`

```bash
# git pull은 최신 코드를 가져와서(fetch) 현재 작업 공간에 병합하는 명령
# 실제 워크트리 폴더로 가서 git pull 실행

# 데이터베이스만 업데이트
git fetch
```

### Bare 명령어

```bash
# 브랜치 조회/생성
git -C .bare branch
git -C .bare branch feat/16-box

# 브랜치 생성 [기준 브랜치 명시] (main에서 갈라져나오는 새 브랜치)
# git -C .bare worktree add ../<워크트리_폴더명> -b <브랜치명> <기준_브랜치명>
git -C .bare worktree add ../feat-backend -b feat/14-backend main

# 워크트리 조회
git -C .bare worktree list

# 워크트리 삭제
git -C .bare worktree remove ../<폴더명>

# 워크트리 정리
git -C .bare worktree prune

# 워크트리 폴더 이동시 정보 업데이트
# 폴더 이름 변경, 연결 끊김 등... 해결 
git -C .bare worktree repair
```

### 예제

`PR 브랜치 가져오기`

```bash
# 1. PR 브랜치 가져오기
# github 15번 PR에서 코드를 pr-15라는 로컬 브랜치로 다운로드(.bare 폴더안에 PR 코드 저장)
# git -C .bare fetch origin <>:<브랜치명>
git -C .bare fetch origin pull/15/head:pr-15

# 2. 워크트리 생성
# git -C .bare worktree add <워크트리_폴더> <브랜치명>
git -C .bare worktree add ../review-pr-15 pr-15

# 폴더 이동/정리
# 폴더 이동
cd ../review-pr-15
# 정리
git -C .bare worktree remove review-pr-15
git -C .bare branch -d pr-15
```
