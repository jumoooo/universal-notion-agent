# ⚙️ Claude CLI (claude-code)용 Notion Agent 설정 가이드

> 💡 **Claude CLI 전용**: Anthropic의 공식 CLI 도구 `claude-code`에서 범용 Notion Agent를 사용하기 위한 설정 가이드
>
> ✅ **검증 완료**: 2026-01-13 실제 업로드 성공 사례 기반

## 📋 TL;DR (핵심 요약)

**목적**: Claude CLI (`claude-code`)에서 MCP 서버를 설정하여 Notion Agent 사용 준비

**필수 사항**:

1. `claude-code` CLI 도구 설치
2. **프로젝트별 `.mcp.json` 파일 설정** (권장)
3. **Chrome 프로필 설정** (Google OAuth 차단 우회 필수)
4. Notion 수동 로그인

**핵심 성공 패턴**:
- macOS: `pbcopy`로 시스템 클립보드에 복사 → `Meta+v`로 붙여넣기
- 청크 분할 없이 **한 번에 전체 업로드**
- 페이지 문제 시 **새 페이지 생성 후 업로드**

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

## 🔌 2단계: MCP 서버 설정 (프로젝트별 .mcp.json)

### ⚠️ 중요: 프로젝트별 설정 권장

Claude CLI에서는 **프로젝트 루트에 `.mcp.json` 파일을 생성**하는 것이 가장 안정적입니다.

### 2-1. `.mcp.json` 파일 생성

프로젝트 루트 디렉토리에 `.mcp.json` 파일 생성:

```json
{
  "mcpServers": {
    "notion": {
      "command": "npx",
      "args": ["-y", "@notionhq/mcp-server-notion"]
    },
    "playwright": {
      "command": "npx",
      "args": [
        "-y",
        "@playwright/mcp",
        "--browser=chromium",
        "--user-data-dir=/Users/[사용자명]/Library/Application Support/Google/Chrome/NotionAgent"
      ]
    }
  }
}
```

### 2-2. Chrome 프로필 경로 설정 (OS별)

**macOS**:
```json
"--user-data-dir=/Users/[사용자명]/Library/Application Support/Google/Chrome/NotionAgent"
```

**Windows**:
```json
"--user-data-dir=C:\\Users\\[사용자명]\\AppData\\Local\\Google\\Chrome\\User Data\\NotionAgent"
```

**Linux**:
```json
"--user-data-dir=/home/[사용자명]/.config/google-chrome/NotionAgent"
```

### 2-3. 설정 확인

```bash
# 프로젝트 디렉토리에서 claude 실행
cd /path/to/your/project
claude

# MCP 서버 확인 (claude 내부에서)
# 자동으로 .mcp.json 파일의 MCP 서버들이 로드됨
```

---

## 🔐 3단계: Chrome 프로필 설정 (필수!)

### ⚠️ 왜 필요한가?

Playwright가 새 브라우저 인스턴스를 열면 **로그인 상태가 유지되지 않음**.
Google OAuth는 자동화된 브라우저를 차단함.

**해결책**: 전용 Chrome 프로필을 만들어 **수동으로 Notion에 로그인**해두면, Playwright가 해당 프로필을 사용하여 로그인 상태 유지.

### 3-1. Chrome 프로필 디렉토리 생성

```bash
# macOS
mkdir -p "/Users/$(whoami)/Library/Application Support/Google/Chrome/NotionAgent"

# Windows (PowerShell)
New-Item -ItemType Directory -Path "$env:LOCALAPPDATA\Google\Chrome\User Data\NotionAgent" -Force

# Linux
mkdir -p ~/.config/google-chrome/NotionAgent
```

### 3-2. 수동 로그인 (최초 1회)

Claude CLI에서 다음 명령 실행:

```
브라우저 열고 https://www.notion.so 로 이동해줘
```

또는 직접:
```bash
# macOS
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" --user-data-dir="/Users/$(whoami)/Library/Application Support/Google/Chrome/NotionAgent" https://www.notion.so
```

**수동으로 Notion에 로그인** (Google OAuth 또는 이메일)

### 3-3. 로그인 유지 확인

로그인 후 Claude CLI에서:

```
브라우저 스냅샷 찍어줘
```

사이드바에 워크스페이스 이름이 보이면 성공!

---

## ✅ 4단계: 설정 검증

### 검증 체크리스트

1. **MCP 서버 연결 확인**:

Claude CLI 실행 후 다음 도구들이 사용 가능한지 확인:
- `mcp__playwright__browser_navigate`
- `mcp__playwright__browser_snapshot`
- `mcp__playwright__browser_click`

2. **브라우저 자동화 확인**:

```
브라우저 열고 notion.so로 이동해서 스냅샷 찍어줘
```

3. **Notion 로그인 상태 확인**:

스냅샷에서 사이드바에 워크스페이스 이름(예: "일의 Notion")이 보이면 성공

### 성공 조건

- ✅ Playwright MCP 서버 연결 성공
- ✅ Chrome 프로필로 브라우저 열림
- ✅ Notion 로그인 상태 유지됨
- ✅ 사이드바에서 페이지 목록 확인 가능

---

## 🎯 5단계: 업로드 실행

### 권장 프롬프트 형식

```
@Universal_Notion_Agent/ 실행

[파일명].md 파일을 Notion의 "[부모 페이지명]" 페이지에 업로드해줘
```

### 실제 성공 예제

```
@Universal_Notion_Agent/ 실행

ROADMAP.md 파일을 Notion의 "개인 페이지"에 업로드해줘
```

---

## 🔧 문제 해결 (Claude CLI 전용)

### 문제 1: Google OAuth 차단

**증상**:
- "Sign in with Google temporarily disabled for this app"
- 로그인 페이지가 반복됨

**해결**:
1. Chrome 프로필 경로 확인 (`.mcp.json`의 `--user-data-dir`)
2. 해당 프로필로 Chrome 직접 실행하여 수동 로그인
3. Claude CLI 재시작

```bash
# macOS - Chrome 프로필로 직접 열기
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --user-data-dir="/Users/$(whoami)/Library/Application Support/Google/Chrome/NotionAgent" \
  https://www.notion.so
```

### 문제 2: 내용 순서가 뒤바뀜

**증상**:
- Phase 4 → 3 → 2 → 1 순서로 역순 표시
- 청크 업로드 시 순서 문제

**해결**:
- **청크 분할 대신 한 번에 전체 업로드** (권장)
- macOS에서 `pbcopy` 활용:

```bash
# 시스템 클립보드에 파일 내용 복사
pbcopy < /path/to/file.md
```

그리고 `browser_run_code`로 `Meta+v` 붙여넣기

### 문제 3: 내용이 삭제되지 않음 (Undo 복원)

**증상**:
- `Meta+A` + `Backspace`로 삭제해도 내용이 다시 나타남
- Notion의 Undo 기능이 자동 실행됨

**해결**:
- 기존 페이지 정리 대신 **새 페이지 생성 후 업로드**
- 문제 페이지는 휴지통으로 이동

### 문제 4: 제목이 "새 페이지"로 변경됨

**증상**:
- 붙여넣기 후 제목이 "새 페이지"로 바뀜
- 제목 영역이 덮어쓰기됨

**해결**:
- 붙여넣기 후 **즉시 제목 검증 및 복구**:

```javascript
// browser_evaluate 순수 함수로 제목 복구
() => {
  const titleElement = document.querySelector('h1[contenteditable="true"]');
  titleElement.focus();
  const range = document.createRange();
  range.selectNodeContents(titleElement);
  const selection = window.getSelection();
  selection.removeAllRanges();
  selection.addRange(range);
  document.execCommand('insertText', false, '원하는 제목');
  return { success: true };
}
```

### 문제 5: browser_click 타임아웃

**증상**:
- "TimeoutError: locator.click: Timeout 5000ms exceeded"
- "div intercepts pointer events"

**해결**:
- `browser_click` 대신 **JavaScript 직접 클릭** 사용:

```javascript
// browser_evaluate로 직접 클릭
() => {
  const button = document.querySelector('[aria-label="삭제, 복제 등…"]');
  if (button) {
    button.click();
    return { success: true };
  }
  return { success: false };
}
```

---

## 📝 Claude CLI 성공 패턴

### ✅ 검증된 업로드 워크플로우

```
1. Chrome 프로필 설정 완료 (수동 Notion 로그인)
   ↓
2. Claude CLI에서 브라우저 열기
   ↓
3. 대상 페이지로 이동 (navigate)
   ↓
4. 새 페이지 생성 (사이드바 "새 페이지" 버튼)
   ↓
5. 제목 입력 (browser_type)
   ↓
6. 제목 검증 (browser_evaluate 순수 함수)
   ↓
7. Enter로 내용 영역 이동
   ↓
8. pbcopy로 파일 내용 클립보드 복사 (Bash)
   ↓
9. Meta+v로 붙여넣기 (browser_run_code)
   ↓
10. 최종 검증 (순서, 내용 확인)
```

### ✅ macOS 클립보드 활용 패턴 (권장)

```bash
# 1. Bash로 시스템 클립보드에 복사
pbcopy < /path/to/file.md
```

```javascript
// 2. browser_run_code로 붙여넣기
async (page) => {
  await page.keyboard.press('Meta+v');
  await page.waitForTimeout(10000); // 대용량 파일은 충분히 대기
  return { success: true };
}
```

### ✅ 제목 검증 패턴 (순수 함수)

```javascript
// browser_evaluate - 반드시 순수 함수만!
() => {
  const expectedTitle = '원하는 제목'; // 함수 내부에서 정의
  const titleElement = document.querySelector('h1[contenteditable="true"]');
  const actualTitle = titleElement?.innerText.trim();

  return {
    success: actualTitle === expectedTitle,
    expected: expectedTitle,
    actual: actualTitle
  };
}
```

### ❌ 절대 하지 말 것

1. **browser_evaluate에 element/ref 파라미터 사용 금지**
   - 제목이 `[object HTMLDivElement]`로 오염됨

2. **청크 분할 업로드 피하기**
   - 순서 역전, 중복 발생 가능
   - 대신 `pbcopy` + 한 번에 붙여넣기

3. **기존 내용 삭제 시도 피하기**
   - Notion Undo가 복원함
   - 대신 새 페이지 생성

---

## 📚 참조

### 범용 가이드

- **메인 가이드**: `../../Core_Guides/00_메인_가이드.md`
- **실행 가이드**: `../../Core_Guides/01_실행_가이드.md`
- **코드 패턴**: `../../Core_Guides/02_코드_패턴.md`
- **에러 처리**: `../../Core_Guides/03_에러_처리.md`
- **브라우저 자동화 함정**: `../../Core_Guides/07_브라우저_자동화_함정.md`

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
**버전**: 2.1.0 (Claude CLI Platform - 2026-01-13 성공 사례 반영)
