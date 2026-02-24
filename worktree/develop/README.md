develop: 로컬 최상위 폴더(~/develop)


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

```
# develop 하위 폴더에 설정 파일 생성
.gitconfig
.gitignore_global

# 참조 설정
PS C:\Users\name\Desktop\develop\project0> git config --global include.path "C:/Users/name/Desktop/develop/.gitconfig"
PS C:\Users\name\Desktop\develop\project0> git config --global core.excludesfile "C:/Users/name/Desktop/develop/.gitignore_global"

# 목록 확인
git config --global --list
```
