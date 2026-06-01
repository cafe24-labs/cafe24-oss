---
description: GitHub 저장소를 AI SPACE에 연결하여 배포합니다. "GitHub 연동해줘", "저장소 연결해서 배포해줘", "github" 등을 요청할 때 자동으로 사용됩니다.
argument-hint: "[GitHub 저장소 URL 또는 연동 요청]"
---

# AI SPACE GitHub 연동 배포

외부 GitHub 저장소를 AI SPACE에 연결하여 배포하고 관리합니다.

## 연동 절차

### 1단계: GitHub 연동 상태 확인

`external_repo` (action: `connect`)를 호출하여 현재 GitHub 연동 상태를 확인합니다.

- **이미 연동됨**: 2단계로 진행
- **미연동**: 응답의 `connect_url`을 사용자에게 안내하여 GitHub App 설치를 유도합니다
  - "아래 링크에서 GitHub App을 설치해주세요: {connect_url}"
  - 설치 완료 후 다시 진행하도록 안내

### 2단계: 저장소 목록 조회

`external_repo` (action: `list_repos`)로 연결 가능한 저장소 목록을 조회합니다.

- 사용자가 저장소 URL을 지정한 경우: 해당 저장소 사용
- URL을 지정하지 않은 경우: 목록을 보여주고 사용자에게 선택 요청

### 3단계: 배포

`external_repo` (action: `deploy`)로 선택한 저장소를 배포합니다.

### 4단계: 결과 확인

`site_verify`로 배포된 사이트의 접속 상태를 확인하고 URL을 안내합니다.

## 코드 업데이트

이미 연동된 저장소의 최신 코드를 반영하려면:

`external_repo` (action: `repo_update`)를 호출합니다.

- "코드 업데이트해줘", "최신 코드 반영해줘" 등의 요청에 사용

## 저장소 연결 해제

`external_repo` (action: `disconnect`)로 연결을 해제할 수 있습니다.

- 연결을 해제해도 이미 배포된 앱은 정상 운영됩니다
- 이후 수동 배포(파일 업로드)로 전환됩니다

## 안내사항

- 공개/비공개 저장소 모두 지원합니다 (GitHub App 인증 기반)
- GitHub App 설치는 저장소 단위로 권한을 부여합니다
- 연동 후에는 GitHub에 push할 때마다 "코드 업데이트해줘"로 간편하게 재배포할 수 있습니다

## 인증 에러 대응

MCP 도구 호출 시 `requires re-authorization`, `token expired`, `Needs authentication` 에러가 발생하면:

1. **`/login`은 해결 방법이 아닙니다.** `/login`은 Claude Code 자체 인증이며 MCP 서버 인증과 무관합니다.
2. 사용자에게 **`/mcp` 명령을 입력**하도록 안내합니다.
3. 서버 목록에서 **cafe24-aispace**를 선택하고 **Authenticate**를 진행하면 브라우저에서 Cafe24 로그인이 열립니다.
4. 로그인 완료 후 다시 요청하면 정상 동작합니다.

> 이 인증은 MCP 서버의 OAuth 토큰 갱신이며, `/login`이나 `/cafe24-login`과는 별개입니다.
