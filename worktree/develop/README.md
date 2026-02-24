`폴더 구조`

```
📁 develop/                   # 로컬 최상위 폴더(~/develop)
    ├── .gitconfig             # Git 전역 설정
    ├── .gitignore_global      # 모든 프로젝트에 적용될 Ignore 규칙
    └── 📁 project/           
        ├── .env.shared        # 전역 .env 파일
        ├── 📁 .bare/          # Bare 폴더
        ├── 📁 project/        # main 워크트리
        └── 📁 feat-ex/        # 다른 브랜치 워크트리
```

`새 bare 생성/연결`

```bash
# 1. develop 하위 폴더에 새 프로젝트(레포지토리) 생성/연결

# 2. .bare 폴더 생성
`git init --bare .bare

# 3. main 작업 폴더 생성
git -C .bare worktree add ../<프로젝트이름> -b main

# 4. GitHub(원격 저장소) 연결
git -C .bare remote add origin https://github.com/<githubId>/<repository>.git
```

`전역 환경 설정`

```bash
# 1. develop/ 하위 폴더에 설정 파일 생성
.gitconfig
.gitignore_global

# 2. 참조 설정
PS C:\Users\name\Desktop\develop\project0> git config --global include.path "C:/Users/name/Desktop/develop/.gitconfig"
PS C:\Users\name\Desktop\develop\project0> git config --global core.excludesfile "C:/Users/name/Desktop/develop/.gitignore_global"

# 목록 확인
git config --global --list
```

`전역 환경 변수 설정`

```bash
# 1. project/ 하위 폴더에 .env.shared 파일 생성

# 2. 생성된 워크트리로 이동
cd ./project

# 3. 링크 생성(관리자 권한 필요)
cmd /c mklink .env ..\.env.shared
```
