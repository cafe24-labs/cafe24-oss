---
description: AI Space 프로젝트의 상태, 로그, 목록, 작업 진행률을 조회합니다. "상태 보여줘", "로그 확인", "프로젝트 목록", "왜 에러 나지?", "배포 진행 상황", "status" 등 조회 요청 시 자동으로 사용됩니다.
argument-hint: "[프로젝트 이름 또는 조회 요청]"
---

# AI Space 프로젝트 상태 확인

배포된 프로젝트의 상태, 로그, 작업 진행률을 조회하고 에러를 분석합니다.

## 워크플로우

### 프로젝트 목록 조회

`list_my_projects` 호출. 각 프로젝트의 이름, URL, 상태, 런타임을 표로 안내합니다.

### 개별 프로젝트 상태

1. 사용자가 프로젝트를 지정한 경우 해당 프로젝트 사용
2. 미지정 시:
   - 1개 → 자동 선택
   - 2개 이상 → `list_my_projects` 결과를 보여주고 선택 요청
3. `get_project_status` 로 상세 조회

표시 항목:

| 항목 | 설명 |
|------|------|
| 프로젝트 URL | 배포된 사이트 접속 주소 |
| 실행 상태 | success, building, failed 등 |
| source | `internal` / `github` |
| 런타임 | PHP, Node.js, Python, Static |
| 배포 모드 | single, pod |
| 리소스 | CPU, 메모리, 스토리지 |

### 로그 조회 및 에러 분석

`get_project_logs` 호출.

- 빌드 로그 / 실행 로그 구분
- 에러 발견 시: 원인 분석 + 해결 방안 제시
- 자연어 "왜 에러 나지?", "배포 실패 원인 알려줘"에도 자동 분석

### 작업 진행률

배포·업데이트·백업 등 비동기 작업: `get_job_status` 로 진행률 추적.

### 빈 공간 현황

"빈 공간 있어?", "공간 현황" 요청 시 `list_my_spaces` 호출 후 사용 가능한 빈 공간 수 안내.

### 사이트 접속 검증

"접속 되는지 확인해줘", "사이트 죽었어?" 요청 시 `site_verify` 호출 — HTTP 상태, 응답 시간, 페이지 내용 검증.

## 도구 매핑

| 작업 | MCP 도구 |
|------|----------|
| 프로젝트 목록 | `list_my_projects` |
| 상태 조회 | `get_project_status` |
| 로그 조회 | `get_project_logs` |
| 작업 진행률 | `get_job_status` |
| 사이트 검증 | `site_verify` |
| 공간 현황 | `list_my_spaces` |

## 에러 대응

- **MCP 인증 에러**: [`rules/aispace.md#mcp-인증-에러`](../../rules/aispace.md#mcp-인증-에러) 참조
