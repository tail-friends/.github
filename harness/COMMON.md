# 꼬리친구 AI 하네스 공통 규칙

서버·앱·웹 레포가 함께 지키는 규칙의 원본이다. 각 레포의 `AGENTS.md`에 이 내용을 그대로 복사해 넣고, 그 아래에 레포별 규칙을 이어서 쓴다.

## 레포에 적용하는 방법

1. 레포 루트에 `AGENTS.md`를 만들고 아래 "팀 공통 규칙" 전체를 복사한다. Codex는 `AGENTS.md`를 읽는다.
2. 그 아래에 레포별 규칙(폴더 구조, 이름 규칙, 테스트, 하지 말 것)을 쓴다.
3. Claude Code를 쓰면 레포 루트에 `CLAUDE.md`를 만들고 `@AGENTS.md` 한 줄만 넣는다.
4. Claude Code를 쓰면 `.claude/settings.json`에 아래를 넣어 커밋·PR의 Claude 표시를 끈다.
   ```json
   { "attribution": { "commit": "", "pr": "", "sessionUrl": false } }
   ```
5. 이 파일을 고치면 각 레포의 `AGENTS.md` 공통 부분도 같이 고친다.

참고 예시: [tail-server/AGENTS.md](https://github.com/tail-friends/tail-server/blob/develop/AGENTS.md)

---

# 팀 공통 규칙

## 작업 흐름

1. GitHub 이슈를 먼저 만든다.
2. `develop`을 최신으로 받고 이슈 번호로 브랜치를 딴다.
3. 작은 단위로 여러 번 커밋한다.
4. push하고 `develop`으로 PR을 올린다.
5. 승인 1개와 CI(Build) 통과 후 머지한다.
6. 머지 후 연결된 이슈를 닫는다. (`develop`은 기본 브랜치가 아니라 `closes #N`으로 자동으로 닫히지 않는다.)

## 브랜치

| 브랜치 | 용도 |
|---|---|
| `main` | 배포본. develop에서 릴리즈할 때만 병합 |
| `develop` | 개발 통합본. PR로만 병합 |
| `feature/#이슈번호-설명` | 기능 개발. develop에서 따고 develop으로 PR. 예: `feature/#12-animal-api` |
| `fix/#이슈번호-설명` | 버그 수정 |
| `hotfix/설명` | 배포본 긴급 수정. main에서 따고 main과 develop에 모두 병합 |

## 커밋

형식: `타입: 한글로 한 줄 설명 (#이슈번호)`

| 타입 | 언제 |
|---|---|
| `feat` | 기능 추가 |
| `fix` | 버그 수정 |
| `refactor` | 동작 변화 없는 구조 변경 |
| `docs` | 문서 |
| `test` | 테스트 |
| `chore` | 빌드·설정·의존성 |
| `style` | 포맷·오타 |

- 한 커밋에는 한 가지 변경만 담는다. 의존성 추가, 설정 변경, 기능 구현, 테스트, 문서는 나눠서 커밋한다.
- 커밋하기 전에 빌드가 깨지지 않는지 확인한다.

## PR

- 제목은 커밋 메시지 형식과 같다. 예: `feat: 개체 등록 API 추가 (#12)`
- 본문은 `.github/pull_request_template.md`를 따른다. 확인하지 못한 항목은 체크하지 않고 이유를 적는다.
- 리뷰어: 같은 파트의 다른 담당자. 담당자(Assignee): PR을 올린 사람.
- 기능 PR은 **Squash and merge**, 릴리즈 PR(develop → main)은 **Create a merge commit**으로 머지한다.
- 승인 없이 머지하지 않는다. 브랜치 보호 규칙으로 main·develop은 승인 1개와 Build 체크가 필수다.

## API 변경

- 노션 API 명세서를 먼저 고치고 코드를 고친다.
- 앱·웹이 이미 쓰는 API는 필드를 지우거나 이름을 바꾸지 않고 추가만 한다.

## 공통 응답 형식

```json
// 성공
{ "success": true, "data": { ... }, "error": null }

// 실패
{ "success": false, "data": null, "error": { "code": "ANIMAL_NOT_FOUND", "message": "개체를 찾을 수 없어요" } }
```

- 날짜는 `YYYY-MM-DD`, 시각은 ISO 8601(UTC). 화면에서 KST로 바꿔 보여준다.
- 목록은 커서 방식: `?cursor={마지막 ID}&size=20`, 응답에 `nextCursor`(없으면 null).
- 다른 사용자의 데이터에 접근하면 존재를 숨기기 위해 404를 돌려준다.

## AI 도구 사용 규칙

- 커밋 메시지와 PR 본문에 AI 도구 표시를 넣지 않는다. (`Co-Authored-By: Claude ...`, `Generated with Claude Code`, Codex 서명 등)
- 비밀값(DB 비밀번호, JWT 시크릿, AI 키, `.env` 내용)을 코드·문서·커밋·PR·노션에 쓰지 않는다.
- push, PR 생성, 머지, 이슈 닫기처럼 밖으로 나가는 작업은 사람이 요청했을 때만 한다.
- `main`·`develop`에 직접 push하거나 force push하지 않는다.
- 확인하지 못한 것을 확인했다고 쓰지 않는다. 테스트를 돌리지 못했으면 PR과 답변에 그렇게 적는다.
