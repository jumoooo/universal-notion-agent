# 🌐 Universal Notion Agent

> **AI Agent를 활용한 마크다운 파일 자동 Notion 업로드 시스템**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Platform](https://img.shields.io/badge/Platform-Cursor%20%7C%20Claude%20CLI%20%7C%20Antigravity-blue.svg)]()
[![MCP](https://img.shields.io/badge/MCP-Compatible-green.svg)](https://modelcontextprotocol.io)

**지원 플랫폼**: Cursor Pro, Claude CLI (claude-code), Antigravity

---

## 📋 개요

Universal Notion Agent는 마크다운 파일을 Notion 페이지로 자동 업로드하는 AI Agent 시스템입니다. MCP (Model Context Protocol)를 기반으로 하여 여러 AI 플랫폼에서 동일하게 작동합니다.

### 주요 기능

- ✅ **마크다운 → Notion 페이지 자동 변환**
- ✅ **대용량 파일 청크 분할 업로드**
- ✅ **부모 페이지 지정 지원**
- ✅ **자동 내용 검증**
- ✅ **강력한 에러 처리**
- ✅ **다중 플랫폼 지원** (Cursor / Claude CLI / Antigravity)

### 핵심 특징

- **🚨 자동 Fallback 전략**: API 실패 시 즉시 브라우저 자동화로 전환 (사용자가 지정한 위치에 반드시 생성)
- **✅ 문서 품질 보장**: 각 단계별 검증으로 제목 변경 및 순서 섞임 방지
  - 제목 입력 후 즉시 검증
  - 내용 영역 이동 확인
  - 각 청크 입력 시 제목 보호 및 연결성 확인
- **Notion API 제한 극복**: Playwright 브라우저 자동화로 API 제한 우회
- **안정적인 대용량 파일 처리**: 청크 분할 및 재시도 로직
- **기존 내용 보존**: 기존 페이지 내용을 절대 삭제하지 않음
- **범용성**: 모든 MCP 지원 플랫폼에서 동일하게 작동

---

## 🚀 빠른 시작

### 1. 필수 요구사항

- **AI 플랫폼**: Cursor Pro, Claude CLI, 또는 Antigravity 중 하나
- **Node.js**: v18 이상
- **MCP 서버**: Notion MCP, Playwright MCP

### 2. 프로젝트에 포함

**`Universal_Notion_Agent/` 폴더를 원하는 프로젝트에 복사**:

```bash
# 예시: 프로젝트 루트에 복사
cp -r Universal_Notion_Agent /path/to/your/project/

# 또는 Git Submodule로 추가
git submodule add https://github.com/YOUR_USERNAME/Universal-Notion-Agent.git Universal_Notion_Agent
```

### 3. MCP 서버 설정

플랫폼별 설정 가이드를 참조하세요:

- **Cursor Pro**: `Universal_Notion_Agent/Platform_Guides/Cursor/Cursor_설정_가이드.md`
- **Claude CLI**: `Universal_Notion_Agent/Platform_Guides/Claude/Claude_설정_가이드.md`
- **Antigravity**: `Universal_Notion_Agent/Platform_Guides/Antigravity/Antigravity_설정_가이드.md`

### 4. 사용 방법

#### Step 1: Agent 시작 (가이드 강제 준수 모드)

**AI CLI에 다음 프롬프트 전달**:

```
@Universal_Notion_Agent/ 시작
```

AI Agent가 자동으로:

- ✅ **모든 Core_Guides 읽기 및 강제 준수 모드 활성화**
- ✅ 현재 환경 및 MCP 서버 연결 상태 확인
- ✅ 미완료 셋업 안내 또는 준비 완료 보고
- ✅ 사용 예시 프롬프트 제공

**🔴 중요**: 시작 명령어를 입력하면 AI Agent는 모든 가이드를 자동으로 읽고 **강제로 준수**합니다.

#### Step 2: 작업 실행

Agent가 제공하는 예시를 참고하여 작업 요청:

```
document.md 파일을 Notion에 업로드해줘
```

**그게 전부입니다!** 🎉

#### Step 3: 작업 종료 (선택)

가이드 준수 모드를 해제하려면:

```
@Universal_Notion_Agent/ 종료
```

AI Agent가 일반 모드로 복귀합니다.

---

## 📁 프로젝트 구조

```
프로젝트 루트/
├── README.md                                    # 프로젝트 전체 소개
├── LICENSE                                      # MIT 라이선스
├── QUICK_START.md                               # 빠른 시작 가이드
├── CHANGELOG.md                                 # 변경 이력
├── CONTRIBUTING.md                              # 기여 가이드
├── AGENT_작성_가이드.md                         # Notion Agent 실행 로직
├── AGENT_작성_가이드_범용_템플릿.md              # 범용 Agent 작성 템플릿
│
├── Universal_Notion_Agent/                      # 🤖 AI Agent 실행 엔진 (독립 실행)
│   ├── README.md                                # Agent 엔진 설명
│   ├── Agent_실행_가이드.md                     # AI Agent 동작 정의
│   └── Core_Guides/                             # 핵심 실행 로직 (8개)
│       ├── 00_메인_가이드.md
│       ├── 01_실행_가이드.md
│       ├── 02_코드_패턴.md
│       ├── 03_에러_처리.md
│       ├── 04_템플릿.md
│       ├── 05_브라우저_프로필_설정.md
│       ├── 06_중요_주의사항.md
│       └── 07_브라우저_자동화_함정.md           # ⭐ 실전 검증됨
│
└── docs/                                        # 📚 사용자 문서 (독립 폴더)
    ├── README.md                                # 문서 가이드
    ├── Universal_Notion_Agent_README.md         # Agent 전체 소개
    ├── Quick_Fix_Chrome_Profile.md              # Chrome 프로필 설정
    ├── Quick_Fix_Google_OAuth.md                # OAuth 에러 해결
    ├── Quick_Reference.md                       # 빠른 참조
    ├── TROUBLESHOOTING.md                       # 문제 해결
    │
    ├── platform-guides/                         # 플랫폼별 MCP 설정 (3개)
    │   ├── Cursor/Cursor_설정_가이드.md
    │   ├── Claude/Claude_설정_가이드.md
    │   └── Antigravity/Antigravity_설정_가이드.md
    │
    ├── examples/                                # 사용 예시 (2개)
    │   ├── 기본_업로드_예시.md
    │   └── 부모_페이지_지정_예시.md
    │
    └── templates/                               # 프롬프트 템플릿 (1개)
        └── 사용자_프롬프트_템플릿.md
```

---

## 🎯 사용 시나리오

### 시나리오 1: 기본 업로드

```
document.md 파일을 Notion에 업로드해줘
```

### 시나리오 2: 부모 페이지 지정

```
document.md 파일을
https://www.notion.so/[페이지ID]
하위에 업로드해줘
```

### 시나리오 3: 제목 지정

```
document.md 파일을
"📚 프로젝트 문서"로
Notion에 업로드해줘
```

---

## ⚙️ 기술 스택

### MCP 서버

- **Notion MCP Server**: `@notionhq/mcp-server-notion`
- **Playwright MCP Server**: `@executeautomation/playwright-mcp-server`

### 지원 플랫폼

- **Cursor Pro**: MCP 네이티브 지원
- **Claude CLI (claude-code)**: Anthropic 공식 CLI 도구
- **Antigravity**: 로컬 에이전트 실행 플랫폼

### 핵심 기술

- **MCP (Model Context Protocol)**: AI와 외부 도구 간 통신 프로토콜
- **Playwright**: 브라우저 자동화
- **Notion API**: Notion 페이지 생성 및 관리

---

## 🔧 문제 해결

### 🔐 Chrome 자동 로그인 설정 (권장) ⭐

**빠른 설정 (5분)**: `Universal_Notion_Agent/Quick_Fix_Chrome_Profile.md` 참조

**효과**:
- ✅ Notion 자동 로그인
- ✅ Google OAuth 차단 우회
- ✅ 매번 로그인 불필요

---

### Google OAuth 로그인 에러

**증상**: "브라우저 또는 앱이 안전하지 않을 수 있습니다"

**빠른 해결**: `Universal_Notion_Agent/Quick_Fix_Chrome_Profile.md` 참조

**자세한 가이드**: `Universal_Notion_Agent/Core_Guides/05_브라우저_프로필_설정.md` 참조

### 기타 문제

- **MCP 서버 연결 실패**: 플랫폼별 설정 가이드 재확인
- **파일 업로드 실패**: `Core_Guides/03_에러_처리.md` 참조
- **대용량 파일 처리**: 자동으로 청크 분할 처리됨

---

## 🤝 기여

이 프로젝트는 오픈소스이며, 개선 제안 및 기여를 환영합니다!

### 기여 방법

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📚 추가 자료

### Agent 개발자를 위한 문서

- **범용 Agent 작성 템플릿**: `AGENT_작성_가이드_범용_템플릿.md`
- 새로운 AI Agent 개발 시 참조 문서
- 표준화된 5단계 작업 흐름
- 일관된 사용자 경험 제공

### 공식 문서

- **MCP Protocol**: https://modelcontextprotocol.io
- **Notion API**: https://developers.notion.com
- **Playwright**: https://playwright.dev

---

## 📄 라이선스

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 📝 변경 이력

### v2.1.3 (2026-01-14)

- ✅ **Core Guides 문서 업데이트**
  - `01_실행_가이드.md`: 실행 절차 및 검증 단계 상세화
  - `02_코드_패턴.md`: 코드 패턴 및 함수 예시 추가
- ✅ 문서 품질 개선 및 가이드 보완

### v2.1.2 (2026-01-13)

- ✅ **실전 검증된 브라우저 자동화 함정 가이드 추가** (`07_브라우저_자동화_함정.md`)
- ✅ **Troubleshooting 가이드 추가** (실제 문제 해결 사례 기반)
- ✅ browser_evaluate 올바른 사용법 문서화
- ✅ 제목 오염 문제 해결 방법 추가
- ✅ 사이드 패널 문제 해결 방법 추가

### v2.1.1 (2026-01-13)

- ✅ Google OAuth 로그인 에러 해결 가이드 추가
- ✅ 브라우저 프로필 설정 가이드 추가
- ✅ Quick Fix 문서 추가

### v2.1.0 (2026-01-13)

- ✅ "@Universal_Notion_Agent/ 시작" 기능 구현
- ✅ 자동 셋업 확인 및 안내
- ✅ 범용 Agent 작성 템플릿 추가

### v2.0.0 (2026-01-13)

- ✅ 범용 버전으로 리팩토링
- ✅ Cursor Pro, Claude CLI, Antigravity 지원
- ✅ 플랫폼별 설정 가이드 추가
- ✅ MCP 표준 프로토콜 기반으로 재설계

---

## 👤 작성자

**김준모**

📧 Email: rnsdlsdmtlk@gmail.com

---

## 🧪 테스트 상태

- ✅ **Cursor Pro**: 테스트 완료
- ⏳ **Claude CLI**: 테스트 예정
- ⏳ **Antigravity**: 테스트 예정

---

**Made with ❤️ by 김준모**

**Universal Notion Agent v2.1.3**
