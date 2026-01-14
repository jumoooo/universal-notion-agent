# 🎯 Claude CLI 성공 예제

> **실제 검증된 업로드 사례** (2026-01-13)
>
> ROADMAP.md (554줄) → Notion 페이지 업로드 성공

---

## 📋 환경 정보

- **OS**: macOS (Darwin 23.5.0)
- **Claude CLI**: claude-code (Opus 4.5)
- **MCP 서버**: @playwright/mcp (Chrome 프로필 사용)
- **파일 크기**: 554줄, ~23KB

---

## 🚀 전체 워크플로우

### 1단계: 사전 설정 확인

**`.mcp.json` 파일 (프로젝트 루트)**:

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
        "--user-data-dir=/Users/hansejun/Library/Application Support/Google/Chrome/NotionAgent"
      ]
    }
  }
}
```

### 2단계: 프롬프트 입력

```
@Universal_Notion_Agent/ 실행

ROADMAP.md 파일을 Notion의 "개인 페이지"에 업로드해줘
```

### 3단계: 브라우저 열기 및 로그인 확인

**Claude가 수행한 작업**:

```javascript
// browser_navigate로 Notion 접속
await browser_navigate({ url: "https://www.notion.so" });

// browser_snapshot으로 로그인 상태 확인
// 사이드바에 "일의 Notion" 워크스페이스 이름 확인 → 로그인 성공
```

### 4단계: 새 페이지 생성

**사이드바에서 "새 페이지" 버튼 클릭**:

```javascript
// browser_click으로 "새 페이지" 버튼 클릭
await browser_click({ element: "새 페이지 버튼", ref: "e120" });
```

### 5단계: 제목 입력 및 검증

**browser_type으로 제목 입력**:

```javascript
// 제목 영역에 텍스트 입력
await browser_type({
  element: "제목 영역 (h1)",
  ref: "e2934",
  text: "견적서 확인 서비스 개발 로드맵"
});
```

**browser_evaluate 순수 함수로 검증**:

```javascript
// ✅ 올바른 사용 - 순수 함수
() => {
  const expectedTitle = '견적서 확인 서비스 개발 로드맵'; // 함수 내부에서 정의
  const titleElement = document.querySelector('h1[contenteditable="true"]');
  const actualTitle = titleElement?.innerText.trim();

  return {
    success: actualTitle === expectedTitle,
    expected: expectedTitle,
    actual: actualTitle
  };
}

// 결과: { success: true, expected: "견적서 확인 서비스 개발 로드맵", actual: "견적서 확인 서비스 개발 로드맵" }
```

### 6단계: 내용 영역 이동

**Enter 키로 내용 영역 이동**:

```javascript
await browser_press_key({ key: "Enter" });
```

### 7단계: 파일 내용 업로드 (핵심!)

**Bash로 시스템 클립보드에 복사**:

```bash
pbcopy < /Users/hansejun/Desktop/claude-pratice/universal-notion-agent/ROADMAP.md
```

**browser_run_code로 붙여넣기**:

```javascript
async (page) => {
  // 붙여넣기 (macOS)
  await page.keyboard.press('Meta+v');

  // Notion이 마크다운을 처리할 시간 대기 (대용량이므로 충분히 대기)
  await page.waitForTimeout(10000);

  // 제목 확인
  const titleCheck = await page.evaluate(() => {
    const titleElement = document.querySelector('h1[contenteditable="true"]');
    return {
      title: titleElement?.innerText.trim()
    };
  });

  // 내용 확인 (마지막 줄)
  const contentCheck = await page.evaluate(() => {
    const pageText = document.body.innerText;
    const hasPhase1 = pageText.includes('Phase 1:');
    const hasPhase6 = pageText.includes('Phase 6:');
    const hasLastSection = pageText.includes('성공 체크포인트');

    return {
      hasPhase1: hasPhase1,
      hasPhase6: hasPhase6,
      hasLastSection: hasLastSection
    };
  });

  return {
    titleCheck: titleCheck,
    contentCheck: contentCheck
  };
}
```

### 8단계: 최종 검증

**순서 및 섹션 확인**:

```javascript
() => {
  const pageText = document.body.innerText;

  // 순서 확인 - 각 Phase의 위치 확인
  const phase1Index = pageText.indexOf('Phase 1:');
  const phase2Index = pageText.indexOf('Phase 2:');
  const phase3Index = pageText.indexOf('Phase 3:');
  const phase4Index = pageText.indexOf('Phase 4:');
  const phase5Index = pageText.indexOf('Phase 5:');
  const phase6Index = pageText.indexOf('Phase 6:');

  // 순서가 올바른지 확인 (Phase 1 < 2 < 3 < 4 < 5 < 6)
  const correctOrder =
    phase1Index > 0 &&
    phase2Index > phase1Index &&
    phase3Index > phase2Index &&
    phase4Index > phase3Index &&
    phase5Index > phase4Index &&
    phase6Index > phase5Index;

  // 주요 섹션 존재 확인
  const hasOverview = pageText.includes('📋 개요');
  const hasMetrics = pageText.includes('🎯 성공 지표');
  const hasTechStack = pageText.includes('🏗️ 기술 스택');
  const hasDevPhases = pageText.includes('📅 개발 페이즈');
  const hasRisks = pageText.includes('⚠️ 리스크');
  const hasAssumptions = pageText.includes('📌 가정사항');
  const hasChangeLog = pageText.includes('🔄 변경 이력');
  const hasNotes = pageText.includes('📝 추가 노트');

  return {
    phaseOrder: {
      phase1: phase1Index,
      phase2: phase2Index,
      phase3: phase3Index,
      phase4: phase4Index,
      phase5: phase5Index,
      phase6: phase6Index,
      correctOrder: correctOrder
    },
    sections: {
      overview: hasOverview,
      metrics: hasMetrics,
      techStack: hasTechStack,
      devPhases: hasDevPhases,
      risks: hasRisks,
      assumptions: hasAssumptions,
      changeLog: hasChangeLog,
      notes: hasNotes
    }
  };
}
```

**검증 결과**:

```json
{
  "phaseOrder": {
    "phase1": 1240,
    "phase2": 3341,
    "phase3": 4811,
    "phase4": 6895,
    "phase5": 8402,
    "phase6": 9930,
    "correctOrder": true
  },
  "sections": {
    "overview": true,
    "metrics": true,
    "techStack": true,
    "devPhases": true,
    "risks": true,
    "assumptions": true,
    "changeLog": true,
    "notes": true
  }
}
```

---

## ✅ 성공 요인

### 1. Chrome 프로필 사용

- `--user-data-dir` 옵션으로 전용 프로필 지정
- 수동으로 Notion 로그인 후 세션 유지
- Google OAuth 차단 우회

### 2. pbcopy + Meta+v 패턴

- 시스템 클립보드를 통한 대용량 텍스트 전달
- 청크 분할 없이 한 번에 전체 업로드
- Notion의 마크다운 자동 변환 활용

### 3. 순수 함수만 사용

- `browser_evaluate`에 element/ref 파라미터 미사용
- 필요한 값은 함수 내부에서 직접 정의
- 제목 오염 방지

### 4. 문제 페이지 삭제 후 새 페이지 생성

- 기존 손상된 페이지는 휴지통으로 이동
- 새 페이지에서 깨끗하게 시작
- Notion Undo 문제 회피

---

## ❌ 이전 실패 원인

### 실패 1: 청크 분할 업로드

**증상**: Phase 4 → 3 → 2 → 1 역순, 중복 발생

**원인**: 청크를 순차적으로 붙여넣을 때 커서 위치 문제

**교훈**: 한 번에 전체 업로드 (pbcopy 활용)

### 실패 2: 기존 내용 삭제 시도

**증상**: `Meta+A` + `Backspace`로 삭제해도 복원됨

**원인**: Notion의 Undo 기능이 자동 실행

**교훈**: 새 페이지 생성 후 업로드

### 실패 3: browser_evaluate에 파라미터 전달

**증상**: 제목이 `[object HTMLDivElement]`로 오염

**원인**: element/ref 파라미터가 함수 인자로 전달됨

**교훈**: 순수 함수만 사용, 값은 함수 내부에서 정의

---

## 📊 최종 결과

| 항목 | 결과 |
| --- | --- |
| 제목 | 견적서 확인 서비스 개발 로드맵 ✅ |
| Phase 순서 | 1 → 2 → 3 → 4 → 5 → 6 (정상) ✅ |
| 📋 개요 | 존재 ✅ |
| 🎯 성공 지표 | 존재 ✅ |
| 🏗️ 기술 스택 | 존재 ✅ |
| 📅 개발 페이즈 | 존재 ✅ |
| ⚠️ 리스크 | 존재 ✅ |
| 📌 가정사항 | 존재 ✅ |
| 🔄 변경 이력 | 존재 ✅ |
| 📝 추가 노트 | 존재 ✅ |

**페이지 URL**: https://www.notion.so/2e71a168697280ff912ef3f0ced496f1

---

## 📝 재현 가능한 프롬프트

```
@Universal_Notion_Agent/ 실행

다음 단계로 [파일명].md를 Notion에 업로드해줘:

1. 브라우저 열고 notion.so로 이동
2. 로그인 상태 확인 (사이드바에 워크스페이스 이름 확인)
3. "새 페이지" 버튼 클릭하여 새 페이지 생성
4. 제목 입력: "[원하는 제목]"
5. 제목 검증 (browser_evaluate 순수 함수)
6. Enter로 내용 영역 이동
7. pbcopy로 파일 내용 시스템 클립보드에 복사
8. browser_run_code로 Meta+v 붙여넣기 (10초 대기)
9. 최종 검증 (순서, 섹션 확인)
```

---

**작성 날짜**: 2026-01-13
**검증 상태**: ✅ 실전 테스트 완료
**버전**: 1.0.0
