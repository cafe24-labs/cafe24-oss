# Cafe24 AI Space Plugin for Claude Code

Cafe24 AI Space를 Claude Code에서 **대화만으로** 사용할 수 있는 공식 플러그인입니다.

"커피숍 홈페이지 만들어서 배포해줘" 한 줄이면 코드 생성·서버 생성·도메인 할당·SSL 인증서 발급까지 자동으로 진행됩니다.

## 기능

- **앱 배포**: 자연어로 코드 생성부터 배포까지 한 번에
- **GitHub 연동**: 외부 저장소를 연결해 push 시 자동 재배포
- **상태/로그**: 프로젝트 상태, 빌드/실행 로그, 리소스 사용량 조회 및 에러 분석
- **환경변수 관리**: API 키 등 시크릿을 대화로 설정/조회/삭제
- **백업**: 소스 코드 + DB 덤프 백업 및 다운로드
- **데이터베이스**: MySQL, PostgreSQL, SQLite, Redis 자동 연결 (환경변수 자동 주입)
- **도메인**: `{user_id}-{name}.mycafe24.ai` 자동 할당, 커스텀 도메인 지원

## 사전 준비

- [카페24 계정](https://www.cafe24.com)
- [AI Space 신청](https://hosting.cafe24.com/?controller=new_product_page&page=ai-space) (14일 무료 체험)
- 별도 API 키 불필요 — OAuth 2.0 자동 인증

## 설치 방법

Claude Code 터미널에서 아래 명령을 순서대로 입력하세요.

### Step 1. Marketplace 등록

```
/plugin marketplace add cafe24-aispace/aispace-claude-plugin
```

### Step 2. 플러그인 설치

```
/plugin install cafe24-aispace
```

설치 범위를 묻는 화면이 나오면 **Install for you (user scope)** 를 선택합니다.

### Step 3. Claude Code 재시작

```
exit
claude
```

플러그인의 MCP 서버를 로드하려면 Claude Code를 한 번 재시작해야 합니다.

### Step 4. Cafe24 계정 연결 (최초 1회)

```
/mcp
```

1. `/mcp` 입력 후 서버 목록에서 **cafe24-aispace** 를 선택합니다.
2. **Authenticate** 를 선택하면 브라우저에서 Cafe24 로그인 창이 열립니다.
3. AI Space를 신청한 계정으로 로그인하면 연결 완료입니다.

> ⚠️ **이 단계는 필수입니다.** 플러그인 설치만으로는 인증이 자동 진행되지 않습니다.
> `/mcp`에서 한 번만 인증을 진행하면, 이후에는 토큰이 자동 갱신됩니다.

### Step 5. 사용 시작

인증이 끝나면 자연어로 바로 호출됩니다.

```
내 프로젝트 목록 보여줘
```

## Skills

자연어 요청에 따라 아래 Skill 중 적절한 것이 자동으로 활성화됩니다. (수동 호출은 `/cafe24-aispace:<skill>` 형식)

| Skill | 호출 | 설명 |
|-------|------|------|
| deploy | `/cafe24-aispace:deploy` | 새 웹 앱 배포 (자연어 생성 / 파일 업로드) |
| status | `/cafe24-aispace:status` | 프로젝트 상태·로그·작업 진행률 조회 |
| env | `/cafe24-aispace:env` | 환경변수 조회/설정/삭제 |
| backup | `/cafe24-aispace:backup` | 백업 생성 및 다운로드 URL 발급 |
| github-connect | `/cafe24-aispace:github-connect` | GitHub 저장소 연결 배포 |

## MCP 도구 (18개)

플러그인이 호출하는 실제 MCP 도구 — 사용자가 직접 의식할 필요는 없지만 무엇이 가능한지 한눈에 보려면 참고하세요.

| 도구 | 설명 |
|------|------|
| `_deployment_guide` | 배포 가이드(가용 옵션 안내) |
| `deploy_project` | 새 프로젝트 배포 |
| `update_project` | 기존 프로젝트 코드 업데이트/재시작 |
| `backup_project` | 백업 생성 (DB 덤프 포함) |
| `project_upload` | 파일 업로드 (curl 우선, 대용량용) |
| `upload_json` | JSON 인라인 업로드 (소형) |
| `project_env` | 환경변수 조회/설정/삭제 |
| `project_option` | 런타임/DB 옵션 추가·변경 |
| `project_acl` | 접근 제어 |
| `list_my_projects` | 내 프로젝트 목록 |
| `list_my_spaces` | 내 Space 목록 (빈 공간 확인) |
| `get_project_status` | 프로젝트 상태/리소스 |
| `get_project_logs` | 빌드/실행 로그 |
| `get_job_status` | 비동기 작업 진행률 추적 |
| `site_verify` | 사이트 HTTP 접근 확인 |
| `external_repo` | GitHub 저장소 연결/배포 |
| `import_database` | DB 데이터 임포트 |
| `finalize_db_import` | DB 임포트 확정 |

## 사용 예시

```
커피숍 홈페이지 만들어서 배포해줘
my-blog 상태 보여줘
my-blog에서 500 에러 나는데 로그 분석해줘
my-blog 환경변수에 API_KEY=xxx 추가해줘
my-blog 백업해줘
내 GitHub 저장소 연결해서 자동 배포 설정해줘
어제 버전으로 롤백해줘
```

## 지원 런타임

| 런타임 | 버전 | 감지 파일 |
|--------|------|-----------|
| Node.js | 20 | `package.json` |
| Python | 3.11 (FastAPI/Flask/Django) | `requirements.txt` |
| PHP | 8.2 (Apache) | `composer.json` 또는 `index.php` |
| Static | Nginx | `index.html` |

Go, Java, Rust는 현재 미지원입니다.

## 데이터베이스

| DB | 비고 |
|------|------|
| MySQL | 환경변수 `DB_HOST`/`DB_PORT`/`DB_NAME`/`DB_USER`/`DB_PASSWORD` 서버 자동 주입 |
| PostgreSQL | 동일 (서버 자동 주입) |
| SQLite | `/app/user_data/db/` 경로에 파일 생성 |
| Redis | `REDIS_HOST`/`REDIS_PORT` 자동 주입 |
| Memcached | 자동 주입 |

## 트러블슈팅

**MCP 인증 에러 (`token expired`, `requires re-authorization`, `Needs authentication`)**

`/login`은 Claude Code 자체 인증으로 무관합니다. **`/mcp` → `cafe24-aispace` → Authenticate** 로 재인증하세요. 자세한 내용은 [`rules/aispace.md`](./rules/aispace.md#mcp-인증-에러)를 참고하세요.

**`SOURCE_MISMATCH` 에러**

프로젝트 source 타입과 호출한 도구가 일치하지 않을 때 발생합니다. `get_project_status`로 source를 확인하고 적합한 도구를 사용하세요 — `internal`이면 `update_project`/`project_upload(update=true)`, `github`면 `external_repo`의 `repo_update`/`repo_write_file`.

**배포 실패 / 빌드 에러**

`get_project_logs`로 빌드 로그를 확인하세요. 자연어로 "왜 에러 나지?"라고 물으면 자동으로 로그 분석을 수행합니다.

**백업 다운로드 URL 만료**

백업 다운로드 URL은 발급 후 **7일** 동안만 유효합니다. 만료 시 `backup_project`를 다시 호출하세요.

**공간(Space) 부족**

`list_my_spaces`로 빈 공간을 확인하고, 없으면 [AI Space 웹콘솔](https://hosting.cafe24.com/?controller=new_product_page&page=ai-space)에서 추가 신청하세요.

## 요구사항

- Claude Code `>=2025.06`
- Cafe24 AI Space 신청 계정

## 문서

- [AI Space 도움말](https://aispacedocs-docs.mycafe24.ai/)
- [MCP 서버 가이드](https://aispacedocs-docs.mycafe24.ai/?page=mcp-server)
- [AI 연동 가이드](https://aispacedocs-docs.mycafe24.ai/?page=ai-integration)

## 피드백

버그·개선 의견은 [Issues](https://github.com/cafe24-aispace/aispace-claude-plugin/issues)로 부탁드립니다.

## 라이선스

MIT — 자세한 내용은 [LICENSE](./LICENSE).
