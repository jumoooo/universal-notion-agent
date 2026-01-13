# ⚙️ Claude CLI (claude-code)용 Notion Agent 설정 가이드

> 💡 **Claude CLI 전용**: Anthropic의 공식 CLI 도구 `claude-code`에서 범용 Notion Agent를 사용하기 위한 설정 가이드

## 📋 TL;DR (핵심 요약)

**목적**: Claude CLI (`claude-code`)에서 MCP 서버를 설정하여 Notion Agent 사용 준비

**필수 사항**:

1. `claude-code` CLI 도구 설치
2. MCP 서버 설정 (Notion + Playwright)
3. Notion 계정 연동

---

## 🚀 1단계: claude-code CLI 설치

### 필수 조건

- ✅ Node.js 설치 (v18 이상)
- ✅ npm 또는 yarn 설치
- ✅ Anthropic API 키 (Claude Pro 구독 권장)

### 설치 방법

```bash
# claude-code 전역 설치
npm install -g @anthropic-ai/claude-code

# 버전 확인
claude --version
```

### 확인 방법

```bash
# claude-code 실행
claude

# 도움말 확인
claude --help
```

---

## 🔌 2단계: MCP 서버 설정

### 2-1. claude-code 설정 모드 진입

```bash
# claude-code 실행
claude

# 설정 모드 진입
/config
```

### 2-2. 설정 파일 위치

**자동 생성되는 설정 파일**:

- macOS/Linux: `~/.claude/config.json`
- Windows: `%USERPROFILE%\.claude\config.json`

### 2-3. MCP 서버 설정 추가

**방법 1: CLI 내부 명령어 사용** (권장):

```bash
# claude-code 실행 후
claude

# MCP 서버 추가
/config add-mcp notion npx -y @notionhq/mcp-server-notion
/config add-mcp playwright npx -y @executeautomation/playwright-mcp-server

# 설정 확인
/config list
```

**방법 2: 설정 파일 직접 편집**:

`~/.claude/config.json` 파일을 직접 편집:

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
  }
}
```

---

## 🔐 3단계: Notion 계정 연동

### 3-1. Notion 로그인

1. `claude` 명령어로 Claude CLI 실행
2. 다음 프롬프트 입력:

```
Notion MCP 연결 상태 확인해줘
```

3. 터미널에 OAuth URL이 표시되면 브라우저에서 열기
4. Notion 계정으로 로그인
5. 권한 승인
6. 인증 토큰이 자동으로 저장됨

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

### Claude CLI에서 가이드 참조 방법

**방법 1: 파일 경로 직접 지정** (권장):

```bash
claude

# 프롬프트
Universal_Notion_Agent/Core_Guides/00_메인_가이드.md 파일을 읽고,
그 가이드를 따라 [파일명].md 파일을 Notion에 업로드해줘
```

**방법 2: 작업 디렉토리 설정**:

```bash
# 프로젝트 디렉토리로 이동
cd E:\hanghae99\my\Notion_Agent

# claude 실행
claude

# 프롬프트 (상대 경로 사용 가능)
./Universal_Notion_Agent/Core_Guides/00_메인_가이드.md 를 참조하여
노션_셋업_가이드.md 파일을 Notion에 업로드해줘
```

**방법 3: 가이드 내용 직접 제공**:

```bash
claude

# 프롬프트
다음 가이드를 따라 작업해줘:
[가이드 내용 붙여넣기]

그리고 [파일명].md 파일을 Notion에 업로드해줘
```

---

## 🔧 문제 해결

### MCP 서버 연결 실패

**증상**: "MCP server not found" 에러

**해결**:

1. claude-code 재시작:

```bash
# 현재 세션 종료 (Ctrl+C 또는 /exit)
# 다시 시작
claude
```

2. 설정 파일 확인:

```bash
cat ~/.claude/config.json
# 또는
type %USERPROFILE%\.claude\config.json
```

3. Node.js 버전 확인:

```bash
node -v
# v18 이상이어야 함
```

4. MCP 서버 수동 실행 테스트:

```bash
npx -y @notionhq/mcp-server-notion
# 정상 작동 확인
```

5. 설정 재설정:

```bash
claude
/config reset
/config add-mcp notion npx -y @notionhq/mcp-server-notion
```

### Notion 로그인 실패

**증상**: Notion 로그인 페이지가 열리지 않음

**해결**:

1. 브라우저 쿠키 및 캐시 삭제
2. Notion에 직접 로그인 후 재시도
3. 다른 브라우저 사용
4. Claude Desktop 재시작

### 브라우저 자동화 실패

**증상**: "Browser automation failed" 에러

**해결**:

1. Playwright 브라우저 설치:

```bash
npx playwright install chromium
```

2. claude-code 재시작
3. 시스템 권한 확인 (방화벽, 보안 프로그램)
4. Playwright 수동 테스트:

```bash
npx playwright test
```

### Anthropic API 키 설정

**증상**: "API key not found" 에러

**해결**:

1. API 키 설정:

```bash
# 환경 변수로 설정
export ANTHROPIC_API_KEY="your-api-key-here"

# 또는 claude-code 설정에 추가
claude
/config set api-key your-api-key-here
```

2. API 키 확인:

```bash
claude
/config show
```

---

## 📝 Claude CLI 특수 사항

### CLI 환경의 브라우저 자동화

Claude CLI 환경에서는 GUI 기반 브라우저 자동화가 제한될 수 있습니다.

**대안**:

1. **Headless 모드 사용**: Playwright를 headless 모드로 실행
2. **스크린샷 활용**: 브라우저 상태를 스크린샷으로 확인
3. **로그 확인**: 터미널에서 상세한 실행 로그 확인

### 터미널 기반 워크플로우

```bash
# 1. 프로젝트 디렉토리로 이동
cd E:\hanghae99\my\Notion_Agent

# 2. claude-code 실행
claude

# 3. 가이드 참조하여 작업 요청
Universal_Notion_Agent/Core_Guides/00_메인_가이드.md 를 참조하여
노션_셋업_가이드.md 를 Notion에 업로드해줘

# 4. 진행 상황 터미널에서 확인
```

---

## 📚 참조

### 범용 가이드

- **메인 가이드**: `../../Core_Guides/00_메인_가이드.md`
- **실행 가이드**: `../../Core_Guides/01_실행_가이드.md`
- **코드 패턴**: `../../Core_Guides/02_코드_패턴.md`
- **에러 처리**: `../../Core_Guides/03_에러_처리.md`
- **템플릿**: `../../Core_Guides/04_템플릿.md`

### Claude 공식 문서

- [Anthropic Documentation](https://docs.anthropic.com)
- [claude-code GitHub](https://github.com/anthropics/claude-code)
- [Claude MCP Guide](https://modelcontextprotocol.io)

---

**Made with ❤️ for Claude CLI (claude-code) Users**

---

## 📝 출처

**작성자**: 김준모  
**이메일**: rnsdlsdmtlk@gmail.com  
**버전**: 2.0.0 (Claude CLI Platform)
