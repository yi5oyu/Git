develop: 로컬 최상위 폴더(~/develop)

1. 하위 폴더에 새프로젝트(레포지토리) 생성/연결

2. .bare 폴더 생성

`git init --bare .bare`

3. main 작업 폴더 생성

`git -C .bare worktree add ../<프로젝트이름> -b main`

4. GitHub(원격 저장소) 연결

`git -C .bare remote add origin https://github.com/<githubId>/<repository>.git`
