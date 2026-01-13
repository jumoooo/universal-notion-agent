# 🚨 빠른 해결: Google OAuth 로그인 차단 에러

> **"브라우저 또는 앱이 안전하지 않을 수 있습니다" 에러 즉시 해결**

## ⚡ 빠른 해결 (3분)

### 방법 1: Chrome 프로필 사용 (가장 빠름) ⭐

#### Windows

**1단계**: Chrome 완전 종료

```bash
# 작업 관리자에서 모든 Chrome 프로세스 종료
# 또는 Chrome 우클릭 → 종료
```

**2단계**: 프로필 경로 확인

```
C:\Users\[사용자명]\AppData\Local\Google\Chrome\User Data
```

**3단계**: Agent에게 다음과 같이 요청

```
Chrome 프로필을 사용해서 다시 시도해줘.
프로필 경로: C:\Users\[사용자명]\AppData\Local\Google\Chrome\User Data
```

#### macOS

**프로필 경로**:

```
/Users/[사용자명]/Library/Application Support/Google/Chrome
```

#### Linux

**프로필 경로**:

```
~/.config/google-chrome
```

---

### 방법 2: 수동 로그인 (지금 당장)

**1단계**: Agent가 연 브라우저에서 직접 로그인

**2단계**: 로그인 완료 후 Agent에게 입력

```
완료
```

**3단계**: Agent가 자동으로 계속 진행

---

## 🔍 에러 확인

다음과 같은 메시지가 표시되면 이 가이드를 참조:

```
로그인할 수 없음

브라우저 또는 앱이 안전하지 않을 수 있습니다.

다른 브라우저를 사용해 보세요.
```

---

## 📚 자세한 가이드

**완전한 해결 방법 및 설명**:

→ `Core_Guides/05_브라우저_프로필_설정.md`

**내용**:

- 4가지 해결 방법 상세 설명
- 플랫폼별 구현 방법
- 에러 원인 분석
- 디버깅 체크리스트

---

## ✅ 해결 확인

로그인 에러가 더 이상 발생하지 않으면 성공!

**다음 단계**:

```
@Universal_Notion_Agent/ 시작
```

→ Agent가 정상적으로 작동합니다 🎉

---

**Made with ❤️ for Universal Notion Agent**
