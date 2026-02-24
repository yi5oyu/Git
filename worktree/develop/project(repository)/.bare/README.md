`폴더 구조`

```
.bare/
├── 📁 hooks/          # 클라이언트 or 서버 측에서 실행될 스크립트 저장
├── 📁 info/           # 저장소에 대한 부가 정보 관리
├── 📁 objects/        # 모든 커밋 데이터, 파일 내용(Blob), 트리 구조가 압축 저장되는 DB 핵심
├── 📁 refs/           # 브랜치(heads)와 태그(tags)가 가리키는 커밋 해시 값 저장
├── 📁 worktrees/      # 연결된 외부 워크트리(다른 브랜치)의 메타데이터 관리
├── 📄 config          # 이 저장소의 설정 파일 (core.bare=true 등)
├── 📄 description     # GitWeb 등에서 사용되는 저장소 설명 파일
└── 📄 HEAD            # 현재 이 Bare 저장소가 기본으로 바라보는 브랜치 가리킴
```
