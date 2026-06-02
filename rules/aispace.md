# Cafe24 AI Space Rules

> Cafe24 AI Space MCP 도구 사용 시 자동 로딩되는 규칙. 모든 skill에서 이 문서를 참조한다.

## 금지 사항

- **Dockerfile / docker-compose 생성 금지** — AI Space가 컨테이너를 자동 관리
- **`.env`에 DB credentials 직접 작성 금지** — 서버가 자동 주입
- **업데이트 목적의 delete 후 재배포 금지** — `update_project` 또는 `project_upload(update=true)` 사용
- **`DB_USERNAME` 사용 금지** — 반드시 `DB_USER` 사용 (서버 주입 변수명)

## 코드 생성 경로

- 자연어 생성 시 소스 코드는 `aispace_files/{name}/` 하위에 생성
- `/tmp` 금지 — 영속 저장은 `/app/user_data/`만 보존됨

## 배포 전 체크리스트

- 런타임 지정 확인 (`nodejs` | `python` | `php` | `static`)
- 환경 변수에 시크릿을 하드코딩하지 않음 — `project_env`로 별도 설정
- `.gitignore`에 `node_modules`, `__pycache__`, `vendor` 등 포함 확인

## 지원 런타임

| 런타임 | 감지 파일 | 비고 |
|--------|----------|------|
| Node.js | `package.json` | npm start 또는 package.json scripts.start |
| Python | `requirements.txt` | gunicorn/uvicorn 권장 |
| PHP | `composer.json` | Apache/Nginx 자동 설정 |
| Static | `index.html` | Nginx 서빙 |

Go, Java, Rust는 미지원.

## 데이터베이스

- MySQL, PostgreSQL, SQLite, Redis 지원
- DB 연결 시 `DB_HOST`, `DB_USER`, `DB_PASSWORD`, `DB_NAME` 환경변수 자동 주입
- DB 임포트는 `import_database` → `finalize_db_import` 2단계

## 도메인

- 배포 시 `*.mycafe24.ai` 서브도메인 자동 할당 + SSL 인증서 자동 발급
- 커스텀 도메인은 별도 설정 필요

## 리소스 제한

| 티어 | RAM | 스토리지 | 동시접속 |
|------|-----|---------|---------|
| Basic | 256MB | 1GB | 50 |
| Plus | 512MB | 2GB | 100 |
| Pro | 1GB | 5GB | 200 |
| Business | 2GB | 10GB | 500 |

## MCP 인증 에러

MCP 도구 호출 시 `requires re-authorization`, `token expired`, `Needs authentication` 에러가 발생하면:

1. **`/login`은 해결 방법이 아닙니다.** `/login`은 Claude Code 자체 인증이며 MCP 서버 인증과 무관합니다.
2. 사용자에게 **`/mcp` 명령을 입력**하도록 안내합니다.
3. 서버 목록에서 **cafe24-aispace** 를 선택하고 **Authenticate** 를 진행하면 브라우저에서 Cafe24 로그인이 열립니다.
4. 로그인 완료 후 다시 요청하면 정상 동작합니다.

> 이 인증은 MCP 서버의 OAuth 토큰 갱신이며, `/login`이나 `/cafe24-login`과는 별개입니다. 한 번 인증한 뒤로는 refresh token으로 자동 갱신됩니다.

## SOURCE_MISMATCH 에러

프로젝트의 source 타입과 호출한 도구가 일치하지 않으면 `SOURCE_MISMATCH` 에러가 발생합니다.

1. `get_project_status` 로 `source` 필드를 먼저 확인합니다.
2. `source="internal"` → `update_project` 또는 `project_upload(update=true)` 사용
3. `source="github"` → `external_repo` 의 `repo_update` 또는 `repo_write_file` 사용

## 파괴적 작업 가드

`force=true`, `update_project(reset=true)` 등 프로젝트 데이터를 삭제할 가능성이 있는 옵션을 사용할 때:

1. **반드시 "이 작업은 되돌릴 수 없습니다" 경고 후 사용자 재확인**
2. 가능하면 먼저 `backup_project` 를 호출해 백업 권유
