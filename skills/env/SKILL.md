---
description: AI SPACE 프로젝트의 환경변수를 관리합니다. "환경변수 설정해줘", "API 키 추가해줘", "환경변수 보여줘", "env" 등을 요청할 때 자동으로 사용됩니다.
argument-hint: "[조회/설정/삭제 요청]"
---

# AI SPACE 환경변수 관리

AI SPACE 프로젝트의 환경변수를 조회, 설정, 삭제합니다.

## 절차

### 1단계: 프로젝트 확인

1. `list_my_projects`로 프로젝트 목록 조회
2. 대상 프로젝트 선택:
   - 사용자가 지정한 경우: 해당 프로젝트 사용
   - 프로젝트 1개: 자동 선택
   - 프로젝트 2개 이상: 사용자에게 선택 요청

### 2단계: 현재 환경변수 조회

`project_env` (action: `get`)으로 현재 설정된 환경변수 목록을 조회합니다.

### 3단계: 요청에 따라 처리

#### 환경변수 조회

현재 환경변수 목록을 표 형태로 안내합니다. 민감한 값(비밀번호, API 키 등)은 마스킹하여 표시할지 사용자에게 확인합니다.

#### 환경변수 설정

`project_env` (action: `set`)를 호출합니다.

```
project_env(action: "set", env_vars: {"API_KEY": "sk-xxx", "DEBUG": "false"})
```

- 여러 환경변수를 한 번에 설정할 수 있습니다
- 기존 값이 있으면 덮어씁니다

#### 환경변수 삭제

`project_env` (action: `delete`)를 호출합니다.

```
project_env(action: "delete", env_key: "API_KEY")
```

### 4단계: 앱 재시작 (필요 시)

환경변수 변경 후 앱에 반영이 필요하면 `update_project`로 앱을 재시작합니다. 사용자에게 재시작 여부를 확인합니다.

## 주의사항

- **DB 환경변수 설정 금지**: `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD`는 서버가 자동 주입합니다. 수동으로 설정하면 충돌이 발생할 수 있습니다.
- **Redis 환경변수**: `REDIS_HOST`, `REDIS_PORT`도 서버 자동 주입 대상입니다.
- 민감한 정보(API 키, 비밀번호 등)는 코드에 하드코딩하지 말고 환경변수로 관리하세요.

## 인증 에러 대응

MCP 도구 호출 시 `requires re-authorization`, `token expired`, `Needs authentication` 에러가 발생하면:

1. **`/login`은 해결 방법이 아닙니다.** `/login`은 Claude Code 자체 인증이며 MCP 서버 인증과 무관합니다.
2. 사용자에게 **`/mcp` 명령을 입력**하도록 안내합니다.
3. 서버 목록에서 **cafe24-aispace**를 선택하고 **Authenticate**를 진행하면 브라우저에서 Cafe24 로그인이 열립니다.
4. 로그인 완료 후 다시 요청하면 정상 동작합니다.

> 이 인증은 MCP 서버의 OAuth 토큰 갱신이며, `/login`이나 `/cafe24-login`과는 별개입니다.
