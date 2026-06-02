---
description: AI Space 프로젝트를 백업합니다. "백업해줘", "데이터 저장해줘", "스냅샷 만들어줘", "backup" 등 백업 요청 시 자동으로 사용됩니다.
argument-hint: "[프로젝트 이름]"
---

# AI Space 프로젝트 백업

배포된 프로젝트의 소스 코드와 DB 데이터를 백업하고 다운로드 URL을 발급합니다.

## 워크플로우

### 1. 프로젝트 확인

1. `list_my_projects` 로 프로젝트 목록 조회
2. 대상 선택:
   - 사용자가 지정 → 해당 프로젝트
   - 1개 → 자동 선택
   - 2개 이상 → 선택 요청
3. `get_project_status` 로 상태 확인
   - **`success` 상태일 때만 백업 가능**
   - `building` / `failed` 상태면 사용자에게 안내 후 중단

### 2. 백업 실행

`backup_project` 호출 (`wait: true` 권장 — 동기 완료 대기).

### 3. 결과 안내

백업 완료 후 다음을 안내합니다:

| 항목 | 값 |
|------|-----|
| 다운로드 URL | 응답의 `download_url` |
| 유효 기간 | **7일** |
| 포함 내용 | 소스 코드 + `/app/user_data/` + DB 덤프 (있는 경우) |

## 안내사항

- 백업 다운로드 URL은 7일 후 만료됩니다. 필요 시 `backup_project` 를 다시 호출하세요
- 백업에는 소스 코드, `user_data` 디렉토리, DB 덤프(있는 경우)가 포함됩니다
- 실행 중이 아닌 프로젝트(`failed`, `building` 등)는 백업 불가

## 파괴적 작업 전 백업 권유

`update_project(reset=true)`, `force=true` 등 파괴적 작업 전에는 사용자에게 먼저 `backup_project` 호출을 제안하세요. 자세한 가드 정책은 [`rules/aispace.md#파괴적-작업-가드`](../../rules/aispace.md#파괴적-작업-가드) 참조.

## 도구 매핑

| 작업 | MCP 도구 |
|------|----------|
| 백업 생성 | `backup_project` |
| 사전 상태 확인 | `get_project_status` |

## 에러 대응

- **MCP 인증 에러**: [`rules/aispace.md#mcp-인증-에러`](../../rules/aispace.md#mcp-인증-에러) 참조
