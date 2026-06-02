---
description: AI Space 프로젝트의 환경변수를 관리합니다. "환경변수 설정해줘", "API 키 추가해줘", "환경변수 보여줘", "환경변수 삭제", "env" 등 환경변수 관련 요청 시 자동으로 사용됩니다.
argument-hint: "[조회/설정/삭제 요청]"
---

# AI Space 환경변수 관리

AI Space 프로젝트의 환경변수를 조회·설정·삭제합니다.

## 워크플로우

### 1. 프로젝트 확인

1. `list_my_projects` 로 프로젝트 목록 조회
2. 대상 선택:
   - 사용자가 지정 → 해당 프로젝트
   - 1개 → 자동 선택
   - 2개 이상 → 선택 요청

### 2. 현재 환경변수 조회

`project_env(action: "get")` 으로 현재 설정된 환경변수 목록을 가져옵니다.

### 3. 요청별 처리

#### 조회

환경변수 목록을 표로 안내. 민감한 값(비밀번호, API 키 등)은 마스킹 여부를 사용자에게 확인하세요.

#### 설정

```
project_env(action: "set", env_vars: {"API_KEY": "your-api-key", "DEBUG": "false"})
```

- 여러 변수를 한 번에 설정 가능
- 기존 값이 있으면 덮어씁니다

#### 삭제

```
project_env(action: "delete", env_key: "API_KEY")
```

### 4. 앱 재시작 (필요 시)

환경변수 변경 후 즉시 반영이 필요하면 `update_project` 로 재시작합니다. 사용자에게 재시작 여부를 확인하세요.

## 주의사항

- **DB 환경변수 설정 금지**: `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` 는 서버 자동 주입. 수동 설정 시 충돌
- **Redis 환경변수**: `REDIS_HOST`, `REDIS_PORT` 도 서버 자동 주입 대상
- **민감 정보**: 코드에 하드코딩하지 말고 환경변수로 관리
- **`DB_USERNAME` 금지**: 반드시 `DB_USER` 사용 (서버 주입 변수명)

## 도구 매핑

| 작업 | MCP 도구 |
|------|----------|
| 조회/설정/삭제 | `project_env` |
| 앱 재시작 | `update_project` |

## 에러 대응

- **MCP 인증 에러**: [`rules/aispace.md#mcp-인증-에러`](../../rules/aispace.md#mcp-인증-에러) 참조
