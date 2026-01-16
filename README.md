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

- **🚨 자동 Fallback 전략 (2단계)**: Notion MCP 실패 시 자동으로 Playwright로 전환하여 사용자가 지정한 위치에 반드시 생성 보장
  
- **✅ 문서 품질 보장**: 각 단계별 검증으로 제목 변경 및 순서 섞임 방지
  
- **Notion API 제한 극복**: API 실패 시 브라우저 자동화로 직접 작업하여 모든 상황 대응
  
- **안정적인 대용량 파일 처리**: 청크 분할 및 재시도 로직
  
- **기존 내용 보존**: 기존 페이지 내용을 절대 삭제하지 않음
  
- **범용성**: 모든 MCP 지원 플랫폼에서 동일하게 작동

---

## 💡 프로젝트 배경

### 왜 만들게 되었나요?

1. **문제 발견** 🔍
   - Cursor에서 Notion MCP로 글을 올릴 때 **간헐적으로 오류 발생**
   - API 호출은 성공하지만 실제로는 엉뚱한 위치에 생성되는 문제
   - 파일이 크면 업로드 중 연결 끊김

2. **첫 번째 해결 시도** 📝
   - 안정성을 추가한 프롬프트 작성
   - 잘 올라가긴 하는데... 새로운 문제 발견

3. **프롬프트의 폭발적 성장** 📈
   - 다른 오류 해결 추가
   - 안정성 강화 추가
   - 사용자 편의성 개선 추가
   - **결과: 프롬프트가 1,700줄 돌파!** 😱

4. **에이전트화 결정** 🤖
   - "이럴 거면 에이전트로 만들고 파일로 분리하자!"
   - Core Guides로 로직 분리
   - 독립 실행 가능한 Agent 시스템 구축

5. **범용화 확장** 🌐
   - "다른 AI IDE에서도 사용할 수 있으면 좋겠다"
   - Claude CLI, Antigravity 지원 추가
   - MCP 기반으로 표준화

### 핵심 철학

> **"가장 낮은 수준의 LLM에서도 안정적으로 작동해야 한다"**

- **Cursor Pro Auto 기준으로 프롬프트 작성**
  - 가장 수준이 낮은 LLM 기반
  - 여기서 잘 돌아가면 더 높은 수준의 LLM은 당연히 잘 돌아감

---

## 🚀 빠른 시작

### 1. 필수 요구사항

- **AI 플랫폼**: Cursor Pro, Claude CLI, 또는 Antigravity 중 하나
- **Node.js**: v18 이상
- **MCP 서버**: **Notion MCP + Playwright MCP (둘 다 필수)**
  - Notion MCP: 1차 시도용
  - Playwright MCP: 2차 Fallback용 (Notion MCP 실패 시 자동 사용)

### 2. 프로젝트에 포함

**`Universal_Notion_Agent/` 폴더만 원하는 마크다운이 있는 위치로 이동**:

```bash
# 예시: 프로젝트 루트에 복사
cp -r Universal_Notion_Agent /path/to/your/project/

# 또는 Git Submodule로 추가
git submodule add https://github.com/YOUR_USERNAME/Universal-Notion-Agent.git Universal_Notion_Agent
```

**💡 배치 팁**:
- 전역 위치에 두어도 상관없음
- AI가 `@Universal_Notion_Agent/` 로 호출만 가능하면 어디든 OK
- 독립적으로 실행되므로 다른 파일과 격리됨

### 3. MCP 서버 설정

플랫폼별 설정 가이드를 참조하세요:

- **Cursor Pro**: `Universal_Notion_Agent/Platform_Guides/Cursor/Cursor_설정_가이드.md`
- **Claude CLI**: `Universal_Notion_Agent/Platform_Guides/Claude/Claude_설정_가이드.md`
- **Antigravity**: `Universal_Notion_Agent/Platform_Guides/Antigravity/Antigravity_설정_가이드.md`

### 4. 사용 방법

**✨ 핵심: AI에게 모든 것을 맡기세요!**

#### Step 1: Agent 시작 (가이드 강제 준수 모드)

**AI CLI에 다음 프롬프트만 전달**:

```
@Universal_Notion_Agent/ 시작
```

AI Agent가 **독립적으로 자동 실행**:

- ✅ **모든 Core_Guides 읽기 및 강제 준수 모드 활성화**
- ✅ **셋업 검진**: 현재 환경 및 MCP 서버 연결 상태 확인
- ✅ **셋업 안내**: 미완료 항목이 있으면 어떤 부분을 설정해야 하는지 자동 안내
- ✅ **준비 완료 보고**: 모든 준비가 완료되면 바로 사용 가능 상태 알림
- ✅ 사용 예시 프롬프트 제공

**🔴 중요**: 
- 다른 셋업 전에 **무조건 먼저** `@Universal_Notion_Agent/ 시작` 실행
- 이 README를 읽기 전에 AI에게 물어보세요
- **셋업 검진, 셋업, 실행 전부 AI에게 맡기면 됩니다**

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
│   ├── Quick_Fix_Chrome_Profile.md              # Chrome 프로필 설정 (빠른 해결)
│   ├── Quick_Fix_Google_OAuth.md                # OAuth 에러 해결 (빠른 해결)
│   ├── Quick_Reference.md                       # 빠른 참조
│   ├── TROUBLESHOOTING.md                       # 문제 해결
│   │
│   ├── Core_Guides/                             # 핵심 실행 로직 (8개)
│   │   ├── 00_메인_가이드.md
│   │   ├── 01_실행_가이드.md
│   │   ├── 02_코드_패턴.md
│   │   ├── 03_에러_처리.md
│   │   ├── 04_템플릿.md
│   │   ├── 05_브라우저_프로필_설정.md
│   │   ├── 06_중요_주의사항.md
│   │   └── 07_브라우저_자동화_함정.md           # ⭐ 실전 검증됨
│   │
│   ├── Platform_Guides/                         # 플랫폼별 MCP 설정 (3개)
│   │   ├── Cursor/Cursor_설정_가이드.md
│   │   ├── Claude/Claude_설정_가이드.md
│   │   └── Antigravity/Antigravity_설정_가이드.md
│   │
│   ├── Examples/                                # 사용 예시 (2개)
│   │   ├── 기본_업로드_예시.md
│   │   └── 부모_페이지_지정_예시.md
│   │
│   └── Templates/                               # 프롬프트 템플릿 (1개)
│       └── 사용자_프롬프트_템플릿.md
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

**💡 작동 원리**: 모든 시나리오에서 동일한 2단계 Fallback 전략 적용
1. **1차**: Notion MCP API로 빠른 생성 시도
2. **2차**: 실패 시 자동으로 Playwright 브라우저 자동화로 전환하여 정확한 위치에 생성

---

## ⚙️ 기술 스택

### MCP 서버 (2개 필수)

1. **Notion MCP Server** (`@notionhq/mcp-server-notion`)
   - **역할**: 1차 시도 - Notion API로 빠른 페이지 생성
   - **실패 시**: 자동으로 Playwright로 전환

2. **Playwright MCP Server** (`@executeautomation/playwright-mcp-server`)
   - **역할**: 2차 Fallback - 브라우저 자동화로 직접 작업
   - **장점**: API 제한 없음, 권한 문제 없음, 100% 성공 보장

### 지원 플랫폼

- **Cursor Pro**: MCP 네이티브 지원
- **Claude CLI (claude-code)**: Anthropic 공식 CLI 도구
- **Antigravity**: 로컬 에이전트 실행 플랫폼

### 핵심 기술

- **MCP (Model Context Protocol)**: AI와 외부 도구 간 통신 프로토콜
- **Notion API**: Notion 페이지 생성 및 관리 (1차 시도)
- **Playwright**: 브라우저 자동화 (2차 Fallback)

---

## 🔧 문제 해결

### 🔐 Chrome 자동 로그인 설정 (권장) ⭐

**빠른 설정 (5분)**: `Universal_Notion_Agent/Quick_Fix_Chrome_Profile.md` 참조

**효과**:
- ✅ Notion 자동 로그인
- ✅ Google OAuth 차단 우회
- ✅ 매번 로그인 불필요
- ✅ **Playwright Fallback 시에도 자동 로그인 상태 유지**

---

### Google OAuth 로그인 에러

**증상**: "브라우저 또는 앱이 안전하지 않을 수 있습니다"

**빠른 해결**: `Universal_Notion_Agent/Quick_Fix_Chrome_Profile.md` 참조

**자세한 가이드**: `Universal_Notion_Agent/Core_Guides/05_브라우저_프로필_설정.md` 참조

### 기타 문제

- **MCP 서버 연결 실패**: 플랫폼별 설정 가이드 재확인
  - ⚠️ **중요**: Notion MCP와 Playwright MCP **둘 다** 설정되어야 함
- **파일 업로드 실패**: `Universal_Notion_Agent/Core_Guides/03_에러_처리.md` 참조
- **대용량 파일 처리**: 자동으로 청크 분할 처리됨
- **Notion MCP 실패**: 걱정 마세요! 자동으로 Playwright로 전환되어 처리됩니다

---

## 🎓 개발 철학 & 참고사항

### LLM 수준 기반 설계

이 프로젝트는 **Cursor Pro Auto (낮은 수준의 LLM)** 기준으로 프롬프트를 작성했습니다.

**이유**:
- ✅ 낮은 수준에서 잘 돌아가면 → 높은 수준에서는 **높은 확률로** 잘 돌아감
- ✅ 최소 사양 보장으로 **범용성 확보**
- ✅ 더 강력한 LLM에서는 **더욱 안정적**으로 작동

### Agent 학습 효과

**한 번 성공하면 계속 성공합니다!**

- 🧠 Agent가 실패 사례를 학습
- 📈 같은 채팅에서는 이후 업로드 시 **높은 안정성** 유지
- 🔄 재시도할수록 성공률 증가

**💡 팁**: 첫 업로드에서 실패하더라도 포기하지 마세요. Agent가 학습 중입니다!

---

## 🤝 기여

이 프로젝트는 오픈소스이며, **개선 제안 및 기여를 환영합니다!** 🎉

### 기여 방법

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. **Open a Pull Request** (PR 템플릿을 자동으로 제공합니다)

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

### v2.1.4 (2026-01-14)

- ✅ **README 대폭 개선**
  - 프로젝트 배경 및 개발 철학 섹션 추가
  - 사용 방법 명확화 (AI에게 모든 것을 맡기는 방식 강조)
  - LLM 수준 기반 설계 철학 설명 추가
- ✅ **PR 템플릿 추가**
  - 상세한 문제 해결 과정 기록용 템플릿
  - AI 프롬프트 작성 팁 포함
  - MCP 도구 사용 체크리스트 추가
- ✅ **부모 페이지 위치 검증 로직 강화**
  - `01_실행_가이드.md`: API 성공 후 실제 위치 검증 단계 추가
  - `02_코드_패턴.md`: `verifyPageUnderParent` 검증 함수 패턴 추가

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
- ✅ **Claude CLI**: 테스트 완료
- ⏳ **Antigravity**: 테스트 완료

---

**Made with ❤️ by 김준모**

**Universal Notion Agent v2.1.4**
