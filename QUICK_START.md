# ⚡ 빠른 시작 가이드

> **3단계로 Universal Notion Agent 사용 시작하기**

---

## 📦 1단계: 프로젝트에 포함

### 방법 1: 직접 복사 (권장)

```bash
# Universal_Notion_Agent 폴더를 프로젝트에 복사
cp -r Universal_Notion_Agent /path/to/your/project/
```

### 방법 2: Git Clone

```bash
cd /path/to/your/project
git clone https://github.com/YOUR_USERNAME/Universal-Notion-Agent.git Universal_Notion_Agent
```

### 방법 3: Git Submodule (Git 프로젝트인 경우)

```bash
cd /path/to/your/project
git submodule add https://github.com/YOUR_USERNAME/Universal-Notion-Agent.git Universal_Notion_Agent
```

---

## ⚙️ 2단계: MCP 서버 설정

### 플랫폼별 설정 가이드

사용 중인 AI 플랫폼의 설정 가이드를 참조하세요:

#### Cursor Pro

→ `Universal_Notion_Agent/Platform_Guides/Cursor/Cursor_설정_가이드.md`

**핵심 설정**:

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

#### Claude CLI (claude-code)

→ `Universal_Notion_Agent/Platform_Guides/Claude/Claude_설정_가이드.md`

**핵심 설정**:

```bash
# ~/.claude/config.json
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

#### Antigravity

→ `Universal_Notion_Agent/Platform_Guides/Antigravity/Antigravity_설정_가이드.md`

**핵심 설정**: GUI에서 MCP 서버 추가

---

## 🚀 3단계: Agent 시작

### AI CLI에 다음 프롬프트 전달

```
@Universal_Notion_Agent/ 시작
```

### AI Agent가 자동으로 수행

1. ✅ **환경 확인**

   - 현재 AI 플랫폼 감지
   - MCP 서버 연결 상태 확인
   - Notion 로그인 상태 확인

2. ✅ **셋업 상태 보고**

   - 미완료 항목 안내
   - 또는 "준비 완료" 보고

3. ✅ **사용 예시 제공**
   - 실제 사용 가능한 프롬프트 예시
   - 플랫폼별 최적화된 예시

---

## 📝 4단계: 작업 실행

### 기본 사용 예시

#### 예시 1: 단일 파일 업로드

```
document.md 파일을 Notion에 업로드해줘
```

#### 예시 2: 부모 페이지 지정

```
document.md 파일을
https://www.notion.so/[페이지ID]
하위에 업로드해줘
```

#### 예시 3: 제목 지정

```
document.md 파일을
"📚 프로젝트 문서"라는 제목으로
Notion에 업로드해줘
```

---

## ✅ 확인 사항

### 셋업 완료 시 다음과 같이 표시됩니다

```
✅ Notion Agent 준비 완료!

**현재 환경**: Cursor Pro
**MCP 서버**:
  - ✅ Notion MCP: 연결됨
  - ✅ Playwright MCP: 연결됨
**Notion 로그인**: ✅ 완료

📝 **사용 예시**:
1. "README.md 파일을 Notion에 업로드해줘"
2. "docs/ 폴더의 모든 .md 파일을 Notion에 업로드해줘"

준비가 완료되었습니다! 위 예시를 참고하여 작업을 요청하세요.
```

---

## 🔧 문제 해결

### Google OAuth 로그인 에러

**증상**: "브라우저 또는 앱이 안전하지 않을 수 있습니다"

**빠른 해결**:

```
Chrome 프로필을 사용해서 다시 시도해줘
```

**자세한 가이드**: `Quick_Fix_Google_OAuth.md`

### MCP 서버 연결 실패

**확인 사항**:

1. Node.js 설치 여부 (`node --version`)
2. MCP 서버 설정 파일 경로
3. 플랫폼 재시작 (Cursor, Claude CLI 등)

**자세한 가이드**: 플랫폼별 설정 가이드 참조

---

## 📚 추가 자료

### 상세 문서

- **메인 가이드**: `Core_Guides/00_메인_가이드.md`
- **실행 가이드**: `Core_Guides/01_실행_가이드.md`
- **에러 처리**: `Core_Guides/03_에러_처리.md`

### 사용 예시

- **기본 업로드**: `Examples/기본_업로드_예시.md`
- **부모 페이지 지정**: `Examples/부모_페이지_지정_예시.md`

---

## 🎉 완료!

이제 마크다운 파일을 Notion에 자동으로 업로드할 수 있습니다!

**질문이나 문제가 있으면**:

- GitHub Issues: 버그 리포트 및 기능 제안
- Discussions: 일반적인 질문 및 토론

---

**Universal Notion Agent - Quick Start Guide**
