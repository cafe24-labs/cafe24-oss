---
description: Cafe24 AI Space에 새 웹 앱을 배포합니다. "배포해줘", "사이트 만들어줘", "앱 올려줘", "deploy" 등 새 프로젝트 생성 요청 시 자동으로 사용됩니다.
argument-hint: "[프로젝트 설명 또는 요청사항]"
---

# AI Space 앱 배포

사용자의 요청에 따라 Cafe24 AI Space에 웹 앱을 배포합니다.

## 배포 방식

| 방식 | 설명 | 주요 도구 |
|------|------|-----------|
| A. 자연어 생성 | 대화로 코드 생성 후 배포 | `aispace_files/{name}/`에 생성 → `project_upload` |
| B. 로컬 파일 업로드 | 로컬 프로젝트를 직접 업로드 | `project_upload` (curl 우선) |
| C. GitHub 연동 | 외부 저장소 연결, push 시 자동 배포 | `/cafe24-aispace:github-connect` 워크플로우로 전환 |

## 워크플로우

### 1. 빈 공간 확인

`list_my_spaces` 호출. `project_id=null` 인 공간이 빈 공간.

- **빈 공간 없음**: "빈 공간이 없습니다. AI Space 웹콘솔(https://hosting.cafe24.com/?controller=new_product_page&page=ai-space)에서 공간을 추가해주세요." 안내 후 중단
- **1개**: 자동 선택
- **2개 이상**: 사용자에게 선택 요청

### 2. 런타임 + 배포 방식 결정

- 런타임 명시 필수: `nodejs(20)` | `python(3.11)` | `php(8.2)` | `static`
- Go, Java, Rust는 미지원 — 요청 시 사용자에게 안내하고 대안 제시 (예: Python으로 FastAPI 등)

### 3. 프로젝트 생성

`deploy_project` 호출.

- 프로젝트 이름 + 선택한 Space 지정
- `project_name`에 user_id 포함 금지 (서버가 자동 prefix)
- 배포 후 `{user_id}-{name}.mycafe24.ai` 도메인 자동 할당 + SSL

### 4. 파일 업로드 (B/C 방식)

**curl 우선 정책**: curl 업로드 시도 → 실패 시에만 `upload_json` 사용.

- 파일 >10개 OR 단일 >3MB OR 합계 >5MB → archive 모드 (tar.gz)
- `upload_json` 제한: ≤10개 / 개별 ≤3MB / 합계 ≤5MB
- 업로드 세션: `project_upload(action: "create_session")` → 파일 전송 → `project_upload(action: "finalize")`

### 5. 데이터베이스 옵션 (필요 시)

사용자에게 확인 후 `project_option` 으로 추가:

- **MySQL** / **PostgreSQL**: DB 환경변수(DB_HOST, DB_PORT, DB_NAME, DB_USER, DB_PASSWORD) 서버 자동 주입
- **SQLite**: `/app/user_data/db/` 경로에 파일 생성
- **Redis** / **Memcached**: 환경변수 자동 주입

### 6. 진행 상태 추적

`get_job_status` 로 비동기 배포 진행률 추적.

### 7. 결과 검증 + 환경변수

- `site_verify` 로 배포된 사이트 접속 확인
- 환경변수가 필요하면 `/cafe24-aispace:env` 흐름으로 분기 (또는 `project_env(action: "set")` 직접 호출)
- 최종 URL을 사용자에게 안내

## 주의사항

- **Dockerfile 생성 금지**: 런타임 이미지 기반 배포. Dockerfile / docker-compose.yml 등을 생성하지 마세요
- **DB 환경변수 하드코딩 금지**: `os.getenv()` / `getenv()` 등으로 환경변수를 읽도록 코드를 작성하세요
- **영속 데이터 경로**: 업로드, DB 파일 등은 반드시 `/app/user_data/` 하위에 저장

## 도구 매핑

| 단계 | MCP 도구 |
|------|----------|
| Space 조회 | `list_my_spaces` |
| 배포 | `deploy_project` |
| 파일 업로드 | `project_upload`, `upload_json` |
| DB/옵션 | `project_option` |
| 상태 추적 | `get_job_status` |
| 검증 | `site_verify` |
| 환경변수 | `project_env` |
| 배포 가이드 | `_deployment_guide` |

## 에러 대응

- **MCP 인증 에러** (`token expired` 등): [`rules/aispace.md#mcp-인증-에러`](../../rules/aispace.md#mcp-인증-에러) 참조
- **`SOURCE_MISMATCH`**: [`rules/aispace.md#source_mismatch-에러`](../../rules/aispace.md#source_mismatch-에러) 참조
