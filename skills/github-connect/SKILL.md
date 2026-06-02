---
description: GitHub 저장소를 AI Space에 연결해 배포 및 자동 재배포를 설정합니다. "GitHub 연동해줘", "저장소 연결해서 배포해줘", "github 자동 배포", "코드 업데이트해줘" (이미 연동된 경우) 등 GitHub 관련 요청 시 자동으로 사용됩니다.
argument-hint: "[GitHub 저장소 URL 또는 연동 요청]"
---

# AI Space GitHub 연동 배포

외부 GitHub 저장소를 AI Space에 연결해 배포·재배포·관리합니다.

## 워크플로우

### 1. GitHub App 연동 확인

`external_repo(action: "list_repos")` 를 우선 호출해 연결 가능한 저장소 목록을 시도합니다.

- **성공** → 이미 GitHub App 이 설치되어 있음 → 2단계
- **미연동 응답** → 응답의 `connect_url` 을 사용자에게 안내:
  - "아래 링크에서 GitHub App을 설치해주세요: `{connect_url}`"
  - 설치 완료 후 재시도

### 2. 저장소 선택

- 사용자가 저장소 URL을 지정 → 해당 저장소 사용
- 미지정 → `list_repos` 결과를 보여주고 선택 요청

### 3. 배포

`deploy_project` 와 함께 `external_repo` 옵션을 지정해 GitHub source 로 프로젝트를 생성합니다.

- 프로젝트 이름 + Space + 런타임 지정
- source 가 `github` 으로 등록됨 (`get_project_status` 의 `source` 필드에서 확인 가능)

### 4. 결과 검증

`get_job_status` 로 진행률 추적 → `site_verify` 로 접속 확인 → 최종 URL 안내.

## 코드 업데이트 (이미 연동된 경우)

`external_repo(action: "repo_update")` 호출. "코드 업데이트해줘", "최신 코드 반영해줘" 등의 요청에 사용.

부분 파일 수정만 필요한 경우: `external_repo(action: "repo_write_file")` 로 특정 파일만 갱신 가능.

## 저장소 연결 해제

`external_repo(action: "disconnect")` 로 해제 가능.

- 해제 후에도 이미 배포된 앱은 정상 운영
- 이후에는 수동 배포(`project_upload`) 로 전환

## 안내사항

- 공개/비공개 저장소 모두 지원 (GitHub App 인증 기반)
- GitHub App 설치는 저장소 단위로 권한을 부여
- 연동 후 GitHub push 시마다 "코드 업데이트해줘" 한 줄로 재배포 가능
- **source 가 `github` 이면 `update_project` / `project_upload(update=true)` 는 금지** — `SOURCE_MISMATCH` 에러 발생. [`rules/aispace.md#source_mismatch-에러`](../../rules/aispace.md#source_mismatch-에러) 참조

## 도구 매핑

| 작업 | MCP 도구 |
|------|----------|
| 저장소 목록 | `external_repo(action: "list_repos")` |
| 배포 | `deploy_project` (+ external_repo source) |
| 코드 업데이트 | `external_repo(action: "repo_update")` |
| 파일 수정 | `external_repo(action: "repo_write_file")` |
| 연결 해제 | `external_repo(action: "disconnect")` |
| 진행률 | `get_job_status` |
| 검증 | `site_verify` |

## 에러 대응

- **MCP 인증 에러**: [`rules/aispace.md#mcp-인증-에러`](../../rules/aispace.md#mcp-인증-에러) 참조
- **`SOURCE_MISMATCH`**: [`rules/aispace.md#source_mismatch-에러`](../../rules/aispace.md#source_mismatch-에러) 참조
