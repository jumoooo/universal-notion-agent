# 🔧 문제 해결 가이드 (Troubleshooting)

> **실전에서 검증된 해결 방법 모음**

## 📋 개요

Universal Notion Agent 사용 중 발생할 수 있는 문제와 검증된 해결 방법을 정리한 가이드입니다.

---

## 🚨 문제 1: 제목이 "[object HTMLDivElement]제목"으로 변경됨

### 증상

- 페이지 제목이 `"README"` → `"[object HTMLDivElement]README"`
- 페이지 URL도 이상하게 변경됨
- 사이드바 페이지 이름도 오염됨

### 원인

`browser_evaluate` 함수 호출 시 `element`/`ref` 파라미터를 잘못 사용했을 때 발생합니다.

```javascript
// ❌ 잘못된 사용
await browser_evaluate((expectedTitle) => {
  // ...
}, expectedTitle);
// 파라미터: element="제목 검증", ref="e652"
// → expectedTitle이 element 객체로 전달됨!
```

### 해결 방법 ✅

**방법 1: 순수 함수만 사용** (권장)

```javascript
// ✅ 올바른 사용
await browser_evaluate(() => {
  const expectedTitle = "README"; // 함수 내부에서 정의
  const titleElement = document.querySelector('h1[contenteditable="true"]');
  const actualTitle = titleElement?.innerText.trim();
  
  return {
    success: actualTitle === expectedTitle,
    expected: expectedTitle,
    actual: actualTitle
  };
});
```

**방법 2: 제목 즉시 복구**

```javascript
// 제목이 오염된 경우 복구
const titleFixed = await browser_evaluate(() => {
  const titleElement = document.querySelector('h1[contenteditable="true"]');
  
  if (!titleElement) {
    return { success: false, error: "제목 영역을 찾을 수 없습니다" };
  }
  
  // 제목 영역 포커스 및 전체 선택
  titleElement.focus();
  titleElement.click();
  
  const range = document.createRange();
  range.selectNodeContents(titleElement);
  const selection = window.getSelection();
  selection.removeAllRanges();
  selection.addRange(range);
  
  // 올바른 제목으로 덮어쓰기
  document.execCommand('insertText', false, 'README');
  
  return { 
    success: true, 
    currentTitle: titleElement.innerText.trim()
  };
});

await browser_wait_for({ time: 1 });
```

### 예방 체크리스트

- [ ] `browser_evaluate` 사용 시 element/ref 파라미터 **사용하지 않음**
- [ ] 필요한 값은 함수 **내부에서 직접 정의**
- [ ] 제목 입력 후 **즉시 검증**
- [ ] 오염 감지 시 **즉시 복구**

---

## 🚨 문제 2: Ctrl+Shift+P로 페이지 이동 대화상자가 열림

### 증상

- `Ctrl+Shift+P` 입력 시 하위 페이지가 생성되지 않음
- 대신 "페이지 옮길 곳 선택" 대화상자가 열림
- 새 페이지 생성이 안 됨

### 원인

- Notion UI 변경으로 단축키 동작이 달라짐
- 페이지 상태에 따라 다른 기능 실행

### 해결 방법 ✅

**단계별 해결**:

```javascript
// 1. 대화상자 닫기
await browser_press_key({ key: "Escape" });
await browser_wait_for({ time: 1 });

// 2. JavaScript로 "하위 페이지 추가" 버튼 직접 클릭
const clicked = await browser_evaluate(() => {
  // 대상 페이지 항목 찾기
  const treeItems = Array.from(document.querySelectorAll('[role="treeitem"]'));
  const targetItem = treeItems.find(item => 
    item.textContent.includes('기타 공유 페이지') // 부모 페이지 제목
  );
  
  if (!targetItem) {
    return { success: false, error: "대상 페이지를 찾을 수 없습니다" };
  }
  
  // "하위 페이지 추가" 버튼 찾기
  const addPageButton = targetItem.querySelector('[aria-label*="하위 페이지 추가"]') ||
                        targetItem.querySelector('button[aria-label*="Add a page"]');
  
  if (!addPageButton) {
    return { success: false, error: "하위 페이지 추가 버튼을 찾을 수 없습니다" };
  }
  
  // 버튼 클릭
  addPageButton.click();
  
  return { success: true, message: "하위 페이지 추가 버튼 클릭 완료" };
});

if (!clicked.success) {
  throw new Error(clicked.error);
}

await browser_wait_for({ time: 2 });
```

### 예방 체크리스트

- [ ] 단축키 실행 후 **대화상자 열림 확인**
- [ ] 대화상자 감지 시 **Escape로 닫기**
- [ ] JavaScript로 **버튼 직접 클릭** 사용
- [ ] 클릭 성공 여부 **반드시 확인**

---

## 🚨 문제 3: 사이드 패널에서 페이지가 열림

### 증상

- 하위 페이지 추가 버튼 클릭 후 사이드 패널에 페이지가 열림
- URL 파라미터: `?p=페이지ID&showMoveTo=true`
- 메인 영역이 아닌 사이드 패널에서 작업 필요
- 요소 클릭이 차단될 수 있음

### 해결 방법 ✅

**새 페이지 URL로 직접 navigate** (가장 안정적):

```javascript
// 1. URL에서 페이지 ID 추출
const pageInfo = await browser_evaluate(() => {
  const currentUrl = window.location.href;
  const match = currentUrl.match(/\?p=([a-f0-9]+)/);
  
  if (!match) {
    return { success: false, error: "페이지 ID를 찾을 수 없습니다" };
  }
  
  return { 
    success: true, 
    pageId: match[1],
    currentUrl: currentUrl
  };
});

if (!pageInfo.success) {
  throw new Error(pageInfo.error);
}

// 2. 새 페이지 전용 URL 구성
const pageTitle = "README"; // 또는 formatPageTitle(fileName)
const newPageUrl = `https://www.notion.so/${pageTitle}-${pageInfo.pageId}`;

// 3. 직접 navigate
await browser_navigate({ url: newPageUrl });
await browser_wait_for({ time: 2 });

// 4. 이제 메인 영역에서 작업 가능
```

### 예방 체크리스트

- [ ] 페이지 생성 후 **URL 확인**
- [ ] `?p=` 파라미터 **추출**
- [ ] 전용 URL로 **직접 navigate**
- [ ] 메인 영역에서 **작업 진행**

---

## 🚨 문제 4: browser_click으로 클릭이 차단됨

### 증상

```
TimeoutError: locator.click: Timeout 5000ms exceeded.
- <div>…</div> intercepts pointer events
```

### 원인

- 다른 요소가 클릭을 가로막음
- Playwright가 안전한 클릭을 기다리다 타임아웃

### 해결 방법 ✅

**JavaScript로 직접 클릭**:

```javascript
// ❌ browser_click 사용 시 차단됨
await browser_click({ element: "하위 페이지 추가", ref: "e681" });
// → TimeoutError 발생

// ✅ JavaScript로 직접 클릭
const clicked = await browser_evaluate(() => {
  const button = document.querySelector('[aria-label="하위 페이지 추가"]');
  
  if (!button) {
    return { success: false, error: "버튼을 찾을 수 없습니다" };
  }
  
  button.click();
  return { success: true };
});
```

### 예방 체크리스트

- [ ] `browser_click` 실패 시 **JavaScript 직접 클릭** 시도
- [ ] 버튼 존재 여부 **먼저 확인**
- [ ] 클릭 성공 여부 **반환값으로 확인**

---

## 🚨 문제 5: 파일 내용 업로드 시 클립보드 복사 실패

### 증상

- 클립보드에 복사되지 않음
- 또는 잘못된 내용이 복사됨

### 원인

- `browser_evaluate`의 파라미터로 대용량 텍스트 전달 시 문제 발생
- 함수 문자열 길이 제한

### 해결 방법 ✅

**browser_run_code 사용**:

```javascript
// ✅ browser_run_code로 파일 내용 전달
await browser_run_code({
  code: `async (page) => {
    // 파일 내용을 여기에 직접 포함
    const fileContent = \`파일의
전체
내용을
여기에
붙여넣기\`;
    
    // 클립보드에 복사
    await page.evaluate((text) => {
      return navigator.clipboard.writeText(text);
    }, fileContent);
    
    // 붙여넣기
    await page.keyboard.press('Control+KeyV');
    
    // 대기
    await page.waitForTimeout(5000);
    
    // 검증
    const result = await page.evaluate(() => {
      const lastLine = "마지막 줄 텍스트";
      const pageText = document.body.innerText;
      return {
        success: pageText.includes(lastLine),
        pageLength: pageText.length
      };
    });
    
    return result;
  }`
});
```

### 주의사항

- 파일 내용에 백틱(`)이 있으면 **이스케이프** 필요: `` content.replace(/`/g, '\\`') ``
- 매우 큰 파일은 **청크로 분할** 권장

---

## ✅ 완전한 체크리스트: 안전한 업로드

### 준비 단계

- [ ] 파일 읽기 완료
- [ ] 제목 포맷팅 완료 (`.md` 제거, `_` → 공백)
- [ ] 부모 페이지 URL 확인

### 페이지 생성 단계

- [ ] 1. 부모 페이지 접속
- [ ] 2. 편집 권한 확인 (contenteditable + 하위 페이지 추가 버튼)
- [ ] 3. JavaScript로 "하위 페이지 추가" 버튼 직접 클릭
- [ ] 4. 대화상자 열림 시 Escape로 닫기
- [ ] 5. 페이지 생성 확인 (사이드바에 새 항목)
- [ ] 6. URL에서 페이지 ID 추출 (`?p=페이지ID`)
- [ ] 7. 새 페이지 전용 URL로 navigate

### 제목 입력 및 검증 단계

- [ ] 8. `browser_type`으로 제목 입력
- [ ] 9. **즉시 제목 검증** (`browser_evaluate` 순수 함수)
- [ ] 10. 제목 오염 감지 시 **즉시 복구**
- [ ] 11. 제목 복구 후 **재검증**

### 내용 업로드 단계

- [ ] 12. 내용 영역으로 이동 (`browser_evaluate` 순수 함수)
- [ ] 13. 내용 영역 포커스 확인
- [ ] 14. `browser_run_code`로 파일 내용 클립보드 복사
- [ ] 15. `Control+KeyV`로 붙여넣기
- [ ] 16. 5초 대기 (Notion 처리 시간)

### 최종 검증 단계

- [ ] 17. 제목 최종 확인 (오염 없는지)
- [ ] 18. 마지막 줄 텍스트 확인
- [ ] 19. 페이지 URL 반환

---

## 🎯 핵심 규칙 정리

### ✅ 반드시 지켜야 할 것

1. **browser_evaluate는 순수 함수만**
   - element/ref 파라미터 사용 금지
   - 필요한 값은 함수 내부에서 정의

2. **대용량 텍스트는 browser_run_code**
   - 파일 내용 전달 시 필수
   - 백틱 이스케이프 주의

3. **제목 입력 후 즉시 검증**
   - 오염 감지 즉시 복구
   - 복구 후 재검증

4. **사이드 패널 문제는 직접 navigate**
   - 페이지 ID 추출
   - 전용 URL 구성
   - navigate로 메인 영역에서 작업

### ❌ 절대 하지 말아야 할 것

1. **browser_evaluate에 element/ref 파라미터 전달**
   - 객체가 문자열로 변환됨
   - 제목 오염 발생

2. **제목 검증 생략**
   - 오염을 늦게 발견
   - 복구 어려움

3. **사이드 패널에서 작업**
   - 클릭 차단 가능
   - 불안정함

4. **browser_click에만 의존**
   - 차단 시 대안 없음
   - JavaScript 직접 클릭 병행

---

## 📖 참조 가이드

### 문제별 참조

| 문제                          | 참조 가이드                              |
| ----------------------------- | ---------------------------------------- |
| browser_evaluate 사용법       | `07_브라우저_자동화_함정.md` (이 문서)   |
| 제목 오염 문제                | `07_브라우저_자동화_함정.md` 함정 1      |
| 페이지 생성 문제              | `07_브라우저_자동화_함정.md` 함정 2, 3   |
| Google OAuth 차단             | `05_브라우저_프로필_설정.md`             |
| 편집 권한 없음                | `03_에러_처리.md` ERR_BROWSER_NO_EDIT_PERMISSION |
| 일반적인 에러                 | `03_에러_처리.md`                        |

---

## 🔄 검증된 전체 워크플로우 요약

```
1. 부모 페이지 접속
   ↓
2. 편집 권한 확인 (필수)
   ↓
3. JavaScript로 "하위 페이지 추가" 버튼 클릭
   ↓
4. 대화상자 열림? → Escape
   ↓
5. 페이지 ID 추출
   ↓
6. 새 페이지 URL로 navigate
   ↓
7. 제목 입력 (browser_type)
   ↓
8. ✅ 제목 검증 (browser_evaluate 순수 함수)
   ↓
9. 오염 감지? → 즉시 복구
   ↓
10. 내용 영역으로 이동 (browser_evaluate 순수 함수)
    ↓
11. 내용 업로드 (browser_run_code)
    ↓
12. ✅ 최종 검증 (제목 + 내용)
    ↓
13. 완료 보고
```

---

## 💡 추가 팁

### Tip 1: 항상 검증하기

```javascript
// 모든 중요한 작업 후 검증
const result = await browser_evaluate(() => {
  // 기대하는 상태 확인
  // 성공/실패 반환
});

if (!result.success) {
  // 즉시 복구 또는 에러 보고
}
```

### Tip 2: 대기 시간 충분히

```javascript
// 각 단계별 권장 대기 시간
await browser_wait_for({ time: 1 });  // 제목 입력 후
await browser_wait_for({ time: 2 });  // 페이지 생성 후, navigate 후
await browser_wait_for({ time: 5 });  // 대용량 붙여넣기 후
```

### Tip 3: URL 파라미터 활용

```javascript
// 페이지 ID는 URL 파라미터에서 추출
const match = url.match(/\?p=([a-f0-9]+)/);
const pageId = match[1];

// 깨끗한 URL 구성
const cleanUrl = `https://www.notion.so/${title}-${pageId}`;
```

---

**작성 날짜**: 2026-01-13  
**검증 상태**: ✅ 실전 테스트 완료  
**버전**: 1.0.0

---

**Universal Notion Agent - Troubleshooting Guide**
