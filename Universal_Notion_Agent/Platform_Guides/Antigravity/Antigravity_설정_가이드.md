# ⚙️ Antigravity용 Notion Agent 설정 가이드

> 💡 **Antigravity 전용**: 로컬 에이전트 실행 플랫폼 Antigravity에서 범용 Notion Agent를 사용하기 위한 설정 가이드

## 📋 TL;DR (핵심 요약)

**목적**: Antigravity 플랫폼에서 MCP 서버를 설정하여 Notion Agent 사용 준비

**필수 사항**:

1. Antigravity 플랫폼 설치 및 실행
2. MCP 서버 설정 (Notion + Playwright)
3. Notion 계정 연동

**Antigravity 특징**:

- ✅ MCP 네이티브 지원
- ✅ 브라우저 자동화(Playwright) 최적화
- ✅ Cursor/Claude와 유사한 JSON 설정 형식
- ✅ 로컬 컴퓨터에서 직접 명령 수행

---

## 🚀 1단계: Antigravity 설치 및 실행

### 필수 조건

- ✅ macOS 또는 Linux (권장: macOS)
- ✅ Node.js v18 이상
- ✅ npm 또는 yarn

### 설치 방법

**방법 1: Homebrew (macOS)** (권장):

```bash
brew install antigravity
```

**방법 2: npm 글로벌 설치**:

```bash
npm install -g antigravity
```

**방법 3: 소스 빌드**:

```bash
git clone https://github.com/antigravity/antigravity.git
cd antigravity
npm install
npm run build
npm link
```

### 실행 확인

```bash
# Antigravity 실행
antigravity

# 또는
ag

# 버전 확인
antigravity --version
```

---

## 🔌 2단계: MCP 서버 설정

### 2-1. 설정 파일 위치

**Antigravity 설정 파일**:

- macOS: `~/Library/Application Support/Antigravity/antigravity.json`
- Linux: `~/.config/antigravity/antigravity.json`

### 2-2. 설정 방법

**방법 1: Antigravity UI에서 설정** (권장):

1. Antigravity 실행
2. `Settings` 또는 `⚙️` 아이콘 클릭
3. `MCP Servers` 탭 선택
4. `Add Server` 클릭
5. 다음 정보 입력:

**Notion MCP 서버**:

- Server Name: `notion`
- Command: `npx`
- Args: `-y`, `@notionhq/mcp-server-notion`

**Playwright MCP 서버**:

- Server Name: `playwright`
- Command: `npx`
- Args: `-y`, `@executeautomation/playwright-mcp-server`

6. `Save` 클릭

**방법 2: 설정 파일 직접 편집**:

`antigravity.json` 파일을 직접 편집:

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server-notion"]
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@executeautomation/playwright-mcp-server"]
    }
  },
  "playwright": {
    "headless": false,
    "slowMo": 50
  }
}
```

### 2-3. Antigravity 전용 최적화 설정

**Playwright 최적화** (Antigravity는 브라우저 자동화에 최적화되어 있음):

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server-notion"]
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@executeautomation/playwright-mcp-server"]
    }
  },
  "playwright": {
    "headless": false,
    "slowMo": 50,
    "timeout": 30000,
    "navigationTimeout": 30000,
    "viewport": {
      "width": 1920,
      "height": 1080
    }
  },
  "logging": {
    "level": "info",
    "showBrowserLogs": true
  }
}
```

---

## 🔐 3단계: Notion 계정 연동

### 3-1. Notion 로그인

1. Antigravity 실행
2. 새 세션 시작
3. 다음 프롬프트 입력:

```
Notion MCP 연결 상태 확인해줘
```

4. Antigravity가 자동으로 브라우저를 열어 Notion 로그인 페이지로 이동
5. Notion 계정으로 로그인
6. 권한 승인
7. 인증 완료 후 자동으로 Antigravity로 돌아감

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
MCP 서버 목록 보여줘
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
- ✅ Playwright 브라우저 자동화 연결 성공
- ✅ Notion 로그인 상태 유지
- ✅ 브라우저가 GUI로 정상 표시됨

---

## 🎯 5단계: 범용 가이드 참조 설정

### Antigravity에서 가이드 참조 방법

**방법 1: 파일 경로 직접 지정** (권장):

```
Universal_Notion_Agent/Core_Guides/00_메인_가이드.md 파일을 읽고,
그 가이드를 따라 노션_셋업_가이드.md 파일을 Notion에 업로드해줘
```

**방법 2: Antigravity 프로젝트 설정**:

1. Antigravity에서 `New Project` 생성
2. 프로젝트 이름: `Notion Agent`
3. `Add Directory` 클릭
4. `Universal_Notion_Agent/` 폴더 선택
5. `Save` 클릭

프롬프트:

```
프로젝트 내 Core_Guides/00_메인_가이드.md 를 참조하여
노션_셋업_가이드.md 파일을 Notion에 업로드해줘
```

**방법 3: Context 추가**:

```
다음 가이드를 컨텍스트로 추가해줘:
[가이드 파일 경로 또는 내용]

그리고 [파일명].md 파일을 Notion에 업로드해줘
```

---

## 🔧 문제 해결

### MCP 서버 연결 실패

**증상**: "MCP server not found" 또는 "Connection failed" 에러

**해결**:

1. Antigravity 재시작:

```bash
# 현재 세션 종료
# Antigravity 재실행
antigravity
```

2. 설정 파일 확인:

```bash
cat ~/Library/Application\ Support/Antigravity/antigravity.json
```

3. Node.js 버전 확인:

```bash
node -v
# v18 이상이어야 함
```

4. MCP 서버 수동 실행 테스트:

```bash
npx -y @notionhq/mcp-server-notion
```

5. 설정 재설정 (Antigravity UI):

- `Settings` → `MCP Servers` → `Reset All`
- 다시 추가

### Notion 로그인 실패

**증상**: Notion 로그인 페이지가 열리지 않음

**해결**:

1. 브라우저 쿠키 및 캐시 삭제
2. Notion에 직접 로그인 후 재시도
3. Antigravity 설정에서 기본 브라우저 확인
4. Playwright 브라우저 재설치:

```bash
npx playwright install chromium
```

### 브라우저 자동화 실패

**증상**: "Browser automation failed" 또는 브라우저가 열리지 않음

**해결**:

1. Playwright 브라우저 설치:

```bash
npx playwright install chromium
npx playwright install webkit
npx playwright install firefox
```

2. Antigravity 설정에서 headless 모드 확인:

```json
{
  "playwright": {
    "headless": false
  }
}
```

3. 시스템 권한 확인:

- macOS: `System Preferences` → `Security & Privacy` → `Accessibility`
- Antigravity에 접근 권한 부여

4. 방화벽/보안 프로그램 확인

### Antigravity 실행 오류

**증상**: "Command not found: antigravity" 또는 실행 실패

**해결**:

1. 경로 확인:

```bash
which antigravity
```

2. 재설치:

```bash
brew reinstall antigravity
# 또는
npm install -g antigravity --force
```

3. 권한 확인:

```bash
ls -la $(which antigravity)
chmod +x $(which antigravity)
```

---

## 💡 Antigravity 특수 기능

### 1. 실시간 브라우저 모니터링

Antigravity는 브라우저 자동화를 GUI로 실시간 확인할 수 있습니다:

```json
{
  "playwright": {
    "headless": false,
    "slowMo": 100
  }
}
```

- `headless: false`: 브라우저를 화면에 표시
- `slowMo`: 동작 속도 조절 (ms 단위, 디버깅에 유용)

### 2. 자동 스크린샷

에러 발생 시 자동으로 스크린샷 저장:

```json
{
  "playwright": {
    "screenshot": {
      "onError": true,
      "path": "./screenshots"
    }
  }
}
```

### 3. 세션 관리

Antigravity는 여러 세션을 동시에 관리할 수 있습니다:

```
# 세션 1: Notion 업로드
새 세션 시작
노션_셋업_가이드.md를 Notion에 업로드해줘

# 세션 2: 다른 작업
새 세션 시작
다른 작업 수행
```

### 4. 로컬 파일 시스템 접근

Antigravity는 로컬 파일에 직접 접근할 수 있어 빠릅니다:

```
현재 디렉토리의 모든 .md 파일을 찾아서
각각 Notion에 업로드해줘
```

---

## 📚 참조

### 범용 가이드

- **메인 가이드**: `../../Core_Guides/00_메인_가이드.md`
- **실행 가이드**: `../../Core_Guides/01_실행_가이드.md`
- **코드 패턴**: `../../Core_Guides/02_코드_패턴.md`
- **에러 처리**: `../../Core_Guides/03_에러_처리.md`
- **템플릿**: `../../Core_Guides/04_템플릿.md`

### Antigravity 공식 문서

- [Antigravity Documentation](https://antigravity.dev/docs)
- [Antigravity GitHub](https://github.com/antigravity/antigravity)
- [MCP Protocol Guide](https://modelcontextprotocol.io)

---

**Made with ❤️ for Antigravity Users**

---

## 📝 출처

**작성자**: 김준모  
**이메일**: rnsdlsdmtlk@gmail.com  
**버전**: 2.0.0 (Antigravity Platform)
