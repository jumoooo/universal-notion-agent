# ⚡ Quick Fix: Chrome 프로필 자동 로그인 설정

> 💡 **5분 안에 끝내는 Chrome 프로필 자동 로그인 설정**

## 🎯 목적

- Notion 자동 로그인 (수동 로그인 불필요)
- Google OAuth 차단 우회
- 기존 Chrome 로그인 상태 활용

## ⚠️ 중요 전제 조건

**이 방법은 다음 경우에만 완전 자동 로그인됩니다:**

✅ Chrome 계정 = Notion 로그인 계정 (같은 Google 계정)

**만약 계정이 다르다면:**
- ❌ Chrome: user1@gmail.com
- ❌ Notion: user2@gmail.com 또는 company@email.com

→ **해결책**: 아래 "계정이 다른 경우" 섹션 참조

---

## 🚀 빠른 설정 (3단계)

### 1단계: MCP 설정 파일 열기

**Windows**:
```
C:\Users\[사용자명]\.cursor\mcp.json
```

**macOS**:
```
~/.cursor/mcp.json
```

**Linux**:
```
~/.cursor/mcp.json
```

### 2단계: Playwright MCP 설정 수정

**기존 설정 찾기**:
```json
"playwright-mcp": {
  "command": "npx",
  "args": ["@playwright/mcp@latest"]
}
```

**↓ 이렇게 변경**:
```json
"playwright-mcp": {
  "command": "npx",
  "args": ["@playwright/mcp@latest"],
  "env": {
    "PLAYWRIGHT_LAUNCH_OPTIONS": "{\"channel\":\"chrome\",\"args\":[\"--disable-blink-features=AutomationControlled\",\"--disable-automation\"]}"
  }
}
```

### 3단계: Cursor 재시작

1. Cursor 완전히 종료
2. Cursor 재실행
3. **Chrome도 완전히 종료** (중요!)

---

## ✅ 확인 방법

**테스트 프롬프트**:
```
@Universal_Notion_Agent/ 시작
```

**성공 시**:
```
✅ Notion Agent 준비 완료!

현재 환경:
- 🖥️ 플랫폼: Cursor Pro
- 🔌 MCP 연결: Notion API ✅ | 브라우저 자동화 ✅
- 🌐 Notion 로그인: 완료 ✅ (자동 로그인)
```

---

## 🔧 문제 해결

### Chrome이 이미 실행 중이라는 에러

**증상**:
```
Error: Failed to launch browser: Cannot start Chrome because it's already running
```

**해결**:
1. Chrome 완전히 종료 (작업 관리자에서 확인)
2. 다시 시도

### Google OAuth 여전히 차단됨

**증상**:
```
로그인할 수 없음
브라우저 또는 앱이 안전하지 않을 수 있습니다.
```

**해결**:
1. `mcp.json`에 `env` 설정이 정확히 추가되었는지 확인
2. Cursor 재시작했는지 확인
3. Chrome 완전히 종료했는지 확인

### 자동 로그인이 안됨

**원인 1**: Chrome에 Notion 로그인 정보가 없음

**해결**:
1. 일반 Chrome 브라우저에서 Notion에 로그인
2. "로그인 상태 유지" 체크
3. 다시 Agent 실행

**원인 2**: Chrome 계정 ≠ Notion 계정

**해결**: 아래 "계정이 다른 경우" 섹션 참조

---

## 🔐 Chrome 계정 ≠ Notion 계정인 경우

### 상황

- Chrome 로그인: `user1@gmail.com`
- Notion 로그인: `user2@gmail.com` 또는 `company@email.com`

### 해결 방법 1: 전용 Chrome 프로필 생성 (권장) ⭐

**1단계: 전용 프로필 폴더 생성**

```bash
# Windows
mkdir e:\hanghae99\my\Notion_Agent\chrome_profile_notion

# macOS/Linux
mkdir -p ~/notion_agent/chrome_profile
```

**2단계: mcp.json 수정**

```json
"playwright-mcp": {
  "command": "npx",
  "args": ["@playwright/mcp@latest"],
  "env": {
    "PLAYWRIGHT_LAUNCH_OPTIONS": "{\"channel\":\"chrome\",\"args\":[\"--disable-blink-features=AutomationControlled\",\"--disable-automation\"],\"userDataDir\":\"e:\\\\hanghae99\\\\my\\\\Notion_Agent\\\\chrome_profile_notion\"}"
  }
}
```

**주의**: Windows 경로는 `\\`로 이스케이프 (두 번 백슬래시)

**3단계: 첫 실행 시 수동 로그인**

1. Agent 실행: `@Universal_Notion_Agent/ 시작`
2. Chrome 자동으로 열림
3. **Notion에 원하는 계정으로 로그인**
4. "로그인 상태 유지" 체크
5. 로그인 완료 후 "완료" 입력

**4단계: 이후 자동 로그인**

- 이제부터는 자동으로 해당 계정으로 로그인됨
- 메인 Chrome과 독립적으로 작동

### 해결 방법 2: 첫 실행 시 수동 로그인 (간단)

**설정**: 위의 기본 설정 그대로 사용

**사용 시**:
1. Agent 실행하면 Chrome 자동 실행
2. Notion 로그인 페이지 표시
3. **수동으로 원하는 계정 로그인**
4. "로그인 상태 유지" 체크
5. Agent가 자동으로 계속 진행

**장점**: 설정 변경 불필요
**단점**: 매번 로그인 필요할 수 있음

### 해결 방법 3: Chrome 다중 프로필 사용

**Chrome에서 프로필 추가**:

1. Chrome 열기
2. 우측 상단 프로필 아이콘 클릭
3. "다른 프로필 추가"
4. Notion용 프로필 생성
5. 해당 프로필에서 Notion 로그인

**프로필 경로 찾기**:

```
C:\Users\[사용자명]\AppData\Local\Google\Chrome\User Data\Profile 2
```

**mcp.json 수정**:

```json
"playwright-mcp": {
  "command": "npx",
  "args": ["@playwright/mcp@latest"],
  "env": {
    "PLAYWRIGHT_LAUNCH_OPTIONS": "{\"channel\":\"chrome\",\"args\":[\"--disable-blink-features=AutomationControlled\",\"--disable-automation\",\"--profile-directory=Profile 2\"]}"
  }
}
```

---

## 📋 전체 설정 예시

**완성된 `mcp.json`**:

```json
{
  "mcpServers": {
    "Context7": {
      "url": "https://mcp.context7.com/mcp",
      "headers": {}
    },
    "sequential-thinking": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sequential-thinking"]
    },
    "playwright-mcp": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"],
      "env": {
        "PLAYWRIGHT_LAUNCH_OPTIONS": "{\"channel\":\"chrome\",\"args\":[\"--disable-blink-features=AutomationControlled\",\"--disable-automation\"]}"
      }
    },
    "GitKraken": {
      "command": "c:\\Users\\rnsdl\\AppData\\Roaming\\Cursor\\User\\globalStorage\\eamodio.gitlens\\gk.exe",
      "type": "stdio",
      "name": "GitKraken",
      "args": ["mcp", "--host=cursor", "--source=gitlens", "--scheme=cursor"],
      "env": {}
    },
    "Notion": {
      "type": "sse",
      "url": "https://mcp.notion.com/mcp"
    }
  }
}
```

---

## 📊 방법 비교

| 방법 | 자동 로그인 | 설정 난이도 | 다중 계정 지원 |
|------|------------|-----------|---------------|
| **기본 Chrome 프로필** (Chrome 계정 = Notion 계정) | ✅ 완전 자동 | ⭐ 쉬움 | ❌ 불가 |
| **전용 프로필 생성** | ✅ 완전 자동 | ⭐⭐ 보통 | ✅ 가능 |
| **첫 실행 시 수동 로그인** | ⚠️ 첫 1회만 수동 | ⭐ 쉬움 | ✅ 가능 |
| **Chrome 다중 프로필** | ✅ 완전 자동 | ⭐⭐⭐ 어려움 | ✅ 가능 |

**권장 순서**:
1. Chrome 계정 = Notion 계정 → **기본 Chrome 프로필** (이 페이지 상단)
2. Chrome 계정 ≠ Notion 계정 → **전용 프로필 생성** (권장)
3. 간단하게 → **첫 실행 시 수동 로그인**

## 💡 추가 정보

**더 자세한 가이드**:
- `Core_Guides/05_브라우저_프로필_설정.md`: 전체 옵션 및 고급 설정
- `Platform_Guides/Cursor/Cursor_설정_가이드.md`: Cursor 전용 설정 가이드

**문제가 계속되면**:
- 이슈 등록: [GitHub Issues](https://github.com/YOUR_USERNAME/Universal-Notion-Agent/issues)
- 이메일: rnsdlsdmtlk@gmail.com

---

**Made with ❤️ for Universal Notion Agent**

**버전**: 2.1.1 (Chrome Profile Quick Fix)
