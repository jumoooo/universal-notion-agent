# ⚙️ Cursor Pro용 Notion Agent 설정 가이드

> 💡 **Cursor Pro 전용**: Cursor Pro에서 범용 Notion Agent를 사용하기 위한 설정 가이드

## 📋 TL;DR (핵심 요약)

**목적**: Cursor Pro에서 MCP 서버를 설정하여 Notion Agent 사용 준비

**필수 사항**:

1. Cursor Pro 구독 활성화
2. MCP 서버 설정 (Notion + Browser Extension)
3. Notion 계정 연동

---

## 🚀 1단계: Cursor Pro 요구사항 확인

### 필수 조건

- ✅ Cursor Pro 구독 (또는 Cursor Pro 체험)
- ✅ Cursor 최신 버전 설치 (MCP 지원 버전)

### 확인 방법

1. Cursor 실행
2. `Settings` → `Account` 확인
3. Pro 구독 상태 확인

---

## 🔌 2단계: MCP 서버 설정

### 2-1. Notion MCP 서버 설치

**설정 파일 위치**:

- Windows: `%APPDATA%\Cursor\User\globalStorage\anysphere.cursor-settings\mcp_settings.json`
- macOS: `~/Library/Application Support/Cursor/User/globalStorage/anysphere.cursor-settings/mcp_settings.json`
- Linux: `~/.config/Cursor/User/globalStorage/anysphere.cursor-settings/mcp_settings.json`

**Notion MCP 설정 추가**:

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server-notion"]
    }
  }
}
```

### 2-2. Playwright MCP 서버 설치 (권장: Chrome 프로필 사용)

**동일한 설정 파일에 추가**:

**옵션 A: Chrome 프로필 사용 (권장) ⭐**

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server-notion"]
    },
    "playwright-mcp": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"],
      "env": {
        "PLAYWRIGHT_LAUNCH_OPTIONS": "{\"channel\":\"chrome\",\"args\":[\"--disable-blink-features=AutomationControlled\",\"--disable-automation\"]}"
      }
    }
  }
}
```

**장점**:
- ✅ **기존 Chrome 로그인 상태 자동 사용**
- ✅ Google OAuth 차단 우회
- ✅ Notion 자동 로그인
- ✅ 수동 로그인 불필요

**옵션 B: 기본 설정 (매번 로그인 필요)**

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server-notion"]
    },
    "playwright-mcp": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"]
    }
  }
}
```

**단점**:
- ⚠️ 매번 수동 로그인 필요
- ⚠️ Google OAuth 차단 가능

### 2-3. 전체 설정 예시 (권장)

**Chrome 프로필 사용 (자동 로그인)**:

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server-notion"]
    },
    "playwright-mcp": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"],
      "env": {
        "PLAYWRIGHT_LAUNCH_OPTIONS": "{\"channel\":\"chrome\",\"args\":[\"--disable-blink-features=AutomationControlled\",\"--disable-automation\"]}"
      }
    }
  }
}
```

**📝 참고**: Chrome 프로필 설정에 대한 자세한 내용은 `Core_Guides/05_브라우저_프로필_설정.md`를 참조하세요.

---

## 🔐 3단계: Notion 계정 연동

### 3-1. Notion 로그인

1. Cursor에서 새 채팅 시작
2. 다음 명령어 실행 요청:

```
Notion MCP 연결 상태 확인해줘
```

3. 브라우저가 자동으로 열리며 Notion 로그인 페이지로 이동
4. Notion 계정으로 로그인
5. 권한 승인

### 3-2. 권한 설정

**필요한 권한**:

- 페이지 읽기
- 페이지 쓰기
- 페이지 생성
- 페이지 이동

**확인 방법**:

```
Notion API 권한 확인해줘
```

---

## ✅ 4단계: 설정 검증

### 검증 체크리스트

1. **MCP 서버 연결 확인**:

```
MCP 서버 상태 확인해줘
```

2. **Notion API 연결 확인**:

```
Notion 워크스페이스 정보 가져와줘
```

3. **브라우저 자동화 확인**:

```
브라우저 열고 notion.so로 이동해줘
```

### 성공 조건

- ✅ Notion API 연결 성공
- ✅ 브라우저 자동화 연결 성공
- ✅ Notion 로그인 상태 유지

---

## 🎯 5단계: 범용 가이드 참조 설정

### Cursor에서 가이드 참조 방법

**방법 1: @ 멘션 사용** (권장):

```
@Universal_Notion_Agent/Core_Guides/00_메인_가이드.md 를 참조하여
[파일명].md 파일을 Notion에 업로드해줘
```

**방법 2: 폴더 전체 참조**:

```
@Universal_Notion_Agent/Core_Guides 의 모든 가이드를 참조하여
[파일명].md 파일을 Notion에 업로드해줘
```

**방법 3: 자동 감지** (New Chat):

```
@Universal_Notion_Agent 의 가이드를 따라
[파일명].md 파일을 Notion에 업로드해줘
```

---

## 🔧 문제 해결

### MCP 서버 연결 실패

**증상**: "MCP server not found" 에러

**해결**:

1. Cursor 재시작
2. `mcp_settings.json` 파일 경로 확인
3. JSON 문법 오류 확인 (trailing comma, 중괄호 등)
4. Node.js 설치 확인 (`node -v`)

### Notion 로그인 실패

**증상**: Notion 로그인 페이지가 열리지 않음

**해결**:

1. 브라우저 쿠키 및 캐시 삭제
2. Notion에 직접 로그인 후 재시도
3. 다른 브라우저 사용

### 브라우저 자동화 실패

**증상**: "Browser automation failed" 에러

**해결**:

1. Playwright 브라우저 재설치:

```bash
npx playwright install chromium
```

2. Cursor 재시작
3. 시스템 권한 확인 (방화벽, 보안 프로그램)

---

## 📚 참조

### 범용 가이드

- **메인 가이드**: `../../Core_Guides/00_메인_가이드.md`
- **실행 가이드**: `../../Core_Guides/01_실행_가이드.md`
- **코드 패턴**: `../../Core_Guides/02_코드_패턴.md`
- **에러 처리**: `../../Core_Guides/03_에러_처리.md`
- **템플릿**: `../../Core_Guides/04_템플릿.md`

### Cursor 공식 문서

- [Cursor Documentation](https://cursor.sh/docs)
- [Cursor MCP Guide](https://cursor.sh/docs/mcp)

---

**Made with ❤️ for Cursor Pro Users**

---

## 📝 출처

**작성자**: 김준모  
**이메일**: rnsdlsdmtlk@gmail.com  
**버전**: 2.0.0 (Cursor Platform)
