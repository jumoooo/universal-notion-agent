# ⚡ 빠른 참조 가이드 (Quick Reference)

> **자주 사용하는 패턴과 해결 방법**

## 📋 목차

1. [browser_evaluate 올바른 사용법](#browser_evaluate-올바른-사용법)
2. [제목 오염 즉시 복구](#제목-오염-즉시-복구)
3. [페이지 생성 안정적인 방법](#페이지-생성-안정적인-방법)
4. [파일 내용 업로드](#파일-내용-업로드)
5. [검증 패턴](#검증-패턴)
6. [Claude CLI 전용 패턴 (macOS)](#claude-cli-전용-패턴-macos) ⭐ NEW

---

## 1️⃣ browser_evaluate 올바른 사용법

### ❌ 잘못된 사용 (절대 금지)

```javascript
// element/ref 파라미터 사용 시 제목 오염!
await browser_evaluate((param) => {
  // ...
}, param, element="설명", ref="eXXX");
```

### ✅ 올바른 사용

```javascript
// 순수 함수만 전달
await browser_evaluate(() => {
  const param = "값"; // 함수 내부에서 정의
  const element = document.querySelector('h1');
  return { success: true, value: element.innerText };
});
```

---

## 2️⃣ 제목 오염 즉시 복구

### 패턴: 검증 → 복구

```javascript
// 1. 제목 검증
const titleCheck = await browser_evaluate(() => {
  const expected = "README";
  const actual = document.querySelector('h1[contenteditable="true"]')?.innerText.trim();
  return {
    success: actual === expected,
    actual: actual,
    needsRecovery: actual !== expected
  };
});

// 2. 오염 감지 시 즉시 복구
if (titleCheck.needsRecovery) {
  await browser_evaluate(() => {
    const titleElement = document.querySelector('h1[contenteditable="true"]');
    titleElement.focus();
    
    const range = document.createRange();
    range.selectNodeContents(titleElement);
    const selection = window.getSelection();
    selection.removeAllRanges();
    selection.addRange(range);
    
    document.execCommand('insertText', false, 'README');
    return { success: true };
  });
  
  await browser_wait_for({ time: 1 });
}
```

---

## 3️⃣ 페이지 생성 안정적인 방법

### 패턴: JavaScript 직접 클릭 → ID 추출 → Navigate

```javascript
// 1. JavaScript로 버튼 직접 클릭
const clicked = await browser_evaluate(() => {
  const treeItems = Array.from(document.querySelectorAll('[role="treeitem"]'));
  const targetItem = treeItems.find(item => 
    item.textContent.includes('부모페이지제목')
  );
  
  if (!targetItem) return { success: false };
  
  const btn = targetItem.querySelector('[aria-label*="하위 페이지 추가"]');
  if (!btn) return { success: false };
  
  btn.click();
  return { success: true };
});

await browser_wait_for({ time: 2 });

// 2. 페이지 ID 추출
const pageInfo = await browser_evaluate(() => {
  const match = window.location.href.match(/\?p=([a-f0-9]+)/);
  return match ? { success: true, pageId: match[1] } : { success: false };
});

// 3. 새 페이지 URL로 navigate
const pageTitle = "README";
const newUrl = `https://www.notion.so/${pageTitle}-${pageInfo.pageId}`;
await browser_navigate({ url: newUrl });
await browser_wait_for({ time: 2 });
```

---

## 4️⃣ 파일 내용 업로드

### 패턴: browser_run_code 사용

```javascript
await browser_run_code({
  code: `async (page) => {
    // 파일 내용 (백틱 이스케이프 주의)
    const content = \`전체
파일
내용\`;
    
    // 클립보드에 복사
    await page.evaluate((text) => {
      return navigator.clipboard.writeText(text);
    }, content);
    
    // 붙여넣기
    await page.keyboard.press('Control+KeyV');
    
    // 대기
    await page.waitForTimeout(5000);
    
    return { success: true };
  }`
});
```

---

## 5️⃣ 검증 패턴

### 제목 검증

```javascript
const titleOk = await browser_evaluate(() => {
  const expected = "README";
  const actual = document.querySelector('h1[contenteditable="true"]')?.innerText.trim();
  return { success: actual === expected, actual: actual };
});
```

### 내용 검증

```javascript
const contentOk = await browser_evaluate(() => {
  const lastLine = "마지막 줄 텍스트";
  const pageText = document.body.innerText;
  return { success: pageText.includes(lastLine) };
});
```

### 종합 검증

```javascript
const finalCheck = await browser_evaluate(() => {
  const expectedTitle = "README";
  const lastLine = "Universal Notion Agent v2.1.2";
  
  const titleElement = document.querySelector('h1[contenteditable="true"]');
  const titleOk = titleElement?.innerText.trim() === expectedTitle;
  
  const pageText = document.body.innerText;
  const contentOk = pageText.includes(lastLine);
  
  return {
    success: titleOk && contentOk,
    titleOk: titleOk,
    contentOk: contentOk,
    pageUrl: window.location.href
  };
});
```

---

## 🔍 빠른 체크리스트

### browser_evaluate 사용 시

- [ ] element/ref 파라미터 **사용하지 않음**
- [ ] 필요한 값은 **함수 내부에서 정의**
- [ ] 순수 함수만 전달

### 제목 관리

- [ ] 제목 입력 후 **즉시 검증**
- [ ] 오염 감지 시 **즉시 복구**
- [ ] 내용 업로드 전/후 **재확인**

### 페이지 생성

- [ ] JavaScript 직접 클릭 사용
- [ ] 대화상자 열림 시 Escape
- [ ] 페이지 ID 추출 → navigate

### 내용 업로드

- [ ] browser_run_code 사용
- [ ] 백틱 이스케이프
- [ ] 충분한 대기 시간 (5초)

---

## 6️⃣ Claude CLI 전용 패턴 (macOS)

### ⚡ 핵심: pbcopy + Meta+v

**가장 안정적인 업로드 방법** (청크 분할 없이 한 번에!)

```bash
# 1. Bash로 시스템 클립보드에 복사
pbcopy < /path/to/file.md
```

```javascript
// 2. browser_run_code로 붙여넣기
async (page) => {
  await page.keyboard.press('Meta+v');
  await page.waitForTimeout(10000); // 대용량은 충분히 대기
  return { success: true };
}
```

### ✅ Chrome 프로필 설정 (.mcp.json)

```json
{
  "mcpServers": {
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

### ✅ 제목 복구 (붙여넣기 후)

```javascript
// 붙여넣기 후 제목이 "새 페이지"로 바뀌면 즉시 복구
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

### ❌ Claude CLI에서 피해야 할 것

1. **청크 분할 업로드** → 순서 역전, 중복 발생
2. **기존 내용 삭제 시도** → Notion Undo가 복원
3. **browser_evaluate에 element/ref 파라미터** → 제목 오염

### 📖 Claude CLI 상세 가이드

- **설정 가이드**: `Platform_Guides/Claude/Claude_설정_가이드.md`
- **성공 예제**: `Platform_Guides/Claude/Claude_성공_예제.md`

---

## 📚 자세한 내용

- **전체 가이드**: `07_브라우저_자동화_함정.md`
- **문제 해결**: `TROUBLESHOOTING.md`
- **에러 처리**: `03_에러_처리.md`
- **Claude CLI 가이드**: `Platform_Guides/Claude/Claude_설정_가이드.md`

---

**버전**: 2.1.3
**업데이트**: 2026-01-13 (Claude CLI 섹션 추가)
