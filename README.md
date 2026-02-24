# 🏢 직원관리 시스템 (Firebase 연동 버전)

> 모든 기기에서 실시간 데이터 공유 가능!

---

## 🔥 Firebase 설정 방법 (필수!)

### 1단계 — Firebase 프로젝트 만들기

1. [console.firebase.google.com](https://console.firebase.google.com) 접속
2. **프로젝트 추가** 클릭
3. 프로젝트 이름 입력 (예: `employee-management`) → 계속
4. Google 애널리틱스는 **사용 안 함** 선택 → **프로젝트 만들기**

---

### 2단계 — Firestore 데이터베이스 만들기

1. 왼쪽 메뉴 **빌드 → Firestore Database** 클릭
2. **데이터베이스 만들기** 클릭
3. **테스트 모드에서 시작** 선택 (나중에 보안 규칙 변경 가능)
4. 위치 선택 → `asia-northeast3 (서울)` 권장 → **완료**

---

### 3단계 — 웹 앱 등록 및 설정값 복사

1. Firebase 콘솔 홈에서 **`</>`** (웹) 아이콘 클릭
2. 앱 닉네임 입력 (예: `employee-web`) → **앱 등록**
3. 아래와 같은 코드 블록이 보입니다:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

4. 이 값 전체를 복사해두세요.

---

### 4단계 — index.html 에 설정값 입력

`index.html` 파일을 열고 아래 부분을 찾아서 교체합니다:

```javascript
/* ★★★ 아래 값을 본인 Firebase 프로젝트 설정으로 교체하세요 ★★★ */
const firebaseConfig = {
    apiKey:            "YOUR_API_KEY",           ← 여기를 교체
    authDomain:        "YOUR_PROJECT_ID.firebaseapp.com",
    projectId:         "YOUR_PROJECT_ID",
    storageBucket:     "YOUR_PROJECT_ID.appspot.com",
    messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
    appId:             "YOUR_APP_ID"
};
```

---

### 5단계 — Firestore 보안 규칙 설정

Firebase 콘솔 → Firestore → **규칙** 탭에서 아래로 교체:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

> ⚠️ 이는 개발용 설정입니다. 운영 환경에서는 인증 기반 보안 규칙을 적용하세요.

---

## 🚀 GitHub + Netlify 배포

### GitHub 업로드

1. [github.com](https://github.com) → **New repository** 생성
2. 파일 3개 드래그 앤 드롭 (`index.html`, `netlify.toml`, `README.md`)
3. **Commit changes** 클릭

### Netlify 연동

1. [netlify.com](https://netlify.com) → GitHub으로 로그인
2. **Add new site → Import an existing project → GitHub**
3. 저장소 선택 → Publish directory: `.`
4. **Deploy site** 클릭

---

## 🔑 초기 관리자 계정

| 아이디 | 비밀번호 |
|--------|----------|
| `admin` | `admin1234` |

> ⚠️ 첫 로그인 후 **관리자 패널 → 관리자 계정 설정**에서 반드시 변경하세요!

---

## 📋 권한 정책

| 기능 | 일반 사용자 | 관리자 |
|------|:-----------:|:------:|
| 대시보드 · 직원관리 · 직무교육 | ✅ | ✅ |
| **연차관리** | ❌ | ✅ |
| 회원 승인/거절 | ❌ | ✅ |
| 관리자 계정 설정 | ❌ | ✅ |

---

## ✨ Firebase 연동 장점

- 📡 **실시간 동기화** — 한 컴퓨터에서 저장하면 다른 컴퓨터에서 즉시 반영
- 💾 **데이터 영구 보존** — 브라우저 캐시 삭제해도 데이터 유지
- 👥 **멀티 기기 지원** — 사무실 PC, 집 노트북, 스마트폰 모두 동일한 데이터
- 🆓 **무료** — Firebase Spark 플랜으로 소규모 사용 완전 무료
