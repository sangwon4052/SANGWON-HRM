# 🏢 직원관리 시스템 (Firebase 연동)

> Firebase Firestore REST API를 활용한 웹 기반 직원관리 시스템

---

## ✨ 주요 기능

| 기능 | 설명 | 권한 |
|------|------|------|
| 📊 **대시보드** | 전체/재직/퇴사 직원 수, 교육 예정 통계 | 모든 사용자 |
| 👥 **직원관리** | 직원 정보 등록/수정/삭제/검색, 자격증 관리 | 모든 사용자 |
| 📅 **연차관리** | 연차 자동계산, 사용등록, 일괄부여, 사용내역 관리 | 🔒 **관리자 전용** |
| 📚 **직무교육** | 자격증별 교육일정 자동생성, D-Day 표시 | 모든 사용자 |
| 💾 **백업관리** | Firebase 데이터 JSON 백업/복구 | 모든 사용자 |
| 🛡️ **관리자 패널** | 회원 승인/거절, 계정 관리 | 🔒 **관리자 전용** |
| 🔐 **인증 시스템** | 로그인, 회원가입, 관리자 승인 기능 | - |

---

## 🚀 빠른 시작

### 1️⃣ Firebase 프로젝트 설정

#### (1) Firebase 프로젝트 생성
1. [Firebase Console](https://console.firebase.google.com) 접속
2. **프로젝트 추가** 클릭
3. 프로젝트 이름 입력 (예: `my-employee-system`)
4. Google 애널리틱스 **비활성화** (선택사항)
5. **프로젝트 만들기** 클릭

#### (2) Firestore Database 생성
1. Firebase 콘솔 → **Firestore Database** 클릭
2. **데이터베이스 만들기** 클릭
3. **테스트 모드로 시작** 선택
4. 위치 선택: `asia-northeast3 (서울)` 권장
5. **사용 설정** 클릭

#### (3) Firestore 보안 규칙 설정
1. Firestore → **규칙** 탭 클릭
2. 기존 내용 삭제 후 아래 코드 붙여넣기:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

3. **게시** 버튼 클릭

> ⚠️ **운영 환경 보안 규칙** (선택사항):
> ```javascript
> rules_version = '2';
> service cloud.firestore {
>   match /databases/{database}/documents {
>     match /{document=**} {
>       allow read, write: if request.auth != null;
>     }
>   }
> }
> ```

#### (4) Firebase API 키 확인
1. Firebase 콘솔 → **프로젝트 설정** (⚙️) 클릭
2. **일반** 탭에서 **웹 API 키** 확인

---

### 2️⃣ 시스템 설정

#### index.html 파일 수정
`index.html` 파일을 열어 Firebase 설정값을 변경하세요:

```javascript
// ============================
// Firebase 설정 (29번째 줄 근처)
// ============================
const FB_PROJ = 'your-project-id';      // ← Firebase 프로젝트 ID로 변경
const FB_KEY = 'your-api-key';          // ← Firebase API 키로 변경
```

**설정 방법:**
- `FB_PROJ`: Firebase 콘솔 → 프로젝트 설정 → **프로젝트 ID** 복사
- `FB_KEY`: Firebase 콘솔 → 프로젝트 설정 → **웹 API 키** 복사

---

### 3️⃣ 시스템 실행

#### 방법 1: VS Code Live Server (권장)
1. VS Code에서 `index.html` 파일 열기
2. 우클릭 → **Open with Live Server** 클릭
3. 브라우저에서 자동 실행

#### 방법 2: Python HTTP Server
```bash
python -m http.server 8080
# 브라우저에서 http://localhost:8080 접속
```

#### 방법 3: Netlify/Vercel 배포
- **파일 드래그 앤 드롭**으로 즉시 배포 가능
- 자동 HTTPS 적용

> ⚠️ **주의**: `file://` 프로토콜(더블클릭 실행)로는 Firebase API 접근이 차단됩니다.

---

## 🔑 초기 로그인 정보

시스템 첫 실행 시 관리자 계정이 자동으로 생성됩니다:

| 항목 | 값 |
|------|-----|
| **아이디** | `admin` |
| **비밀번호** | `admin1234` |

> ⚠️ **보안 경고**: 첫 로그인 후 **관리자 패널 → 관리자 계정 설정**에서 반드시 비밀번호를 변경하세요!

---

## 📋 기능 상세 설명

### 📊 대시보드
- **전체 직원 수**: 시스템에 등록된 모든 직원
- **재직 직원 수**: 퇴사일이 없거나 미래인 직원
- **퇴사 직원 수**: 퇴사일이 현재 또는 과거인 직원
- **교육 예정 (6개월)**: 향후 6개월 이내 교육 일정

### 👥 직원관리
- **직원 등록**: 이름, 성별, 주소, 연락처, 입사일/퇴사일 등록
- **자격증 관리**: 자격증명, 취득일, 5단계 교육 일정 입력
  - 초급교육 / 중급교육 / 고급교육 / 특급교육 / 평가사교육
- **검색**: 이름, 이메일, 전화번호로 실시간 검색
- **수정/삭제**: 직원 정보 수정 및 연관 데이터 일괄 삭제

### 📅 연차관리 (관리자 전용)
- **자동 연차 계산** (법정 기준):
  - 1년 미만: 12일 고정
  - 1년 이상: 15일 기본
  - 3년 이상: 2년마다 1일 추가 (최대 25일)
- **연차 사용 등록**: 0.5일(반차) 단위 등록 가능
- **사용 내역 관리**: 내역 조회, 수정, 삭제 (삭제 시 잔여 일수 자동 복구)
- **일괄 연차 부여**: 재직 중인 전 직원에게 동일 연차 일괄 적용
  - 잔여 연차 부족 인원 자동 제외
  - 미리보기 기능 제공

### 📚 직무교육
- **교육 일정 자동 생성**: 직원 등록 시 자격증 교육일 기반 자동 생성
- **D-Day 표시**: 교육일까지 남은 일수 자동 계산
- **상태 시각화**:
  - 🟢 **완료**: 교육일 지남
  - 🔴 **D-Day**: 교육 당일
  - 🟡 **임박**: 30일 이내
  - 🔵 **예정**: 31일 이후

### 💾 백업관리
- **백업 다운로드**: Firebase의 모든 데이터를 JSON 파일로 추출
  - 직원정보, 연차정보, 교육일정, 사용자 정보 포함
  - 파일명에 날짜/시간 자동 포함
- **백업 복구**: JSON 파일을 업로드하여 Firebase 데이터 완전 복원
  - ⚠️ 기존 데이터 삭제 후 교체

### 🛡️ 관리자 패널 (관리자 전용)
- **회원 승인 관리**: 가입 신청 승인/거절
- **전체 회원 목록**: 등록된 모든 사용자 조회 및 삭제
- **관리자 계정 설정**: 아이디 및 비밀번호 변경

---

## 🗂️ 데이터 구조

### Firestore Collections

#### `employees` (직원 정보)
```javascript
{
  id: "1735123456789",              // 고유 ID
  name: "홍길동",                   // 성명
  gender: "남",                     // 성별
  address: "서울시 강남구...",      // 주소
  phone: "010-1234-5678",          // 전화번호
  email: "hong@example.com",       // 이메일
  hireDate: "2020-01-15",          // 입사일
  resignDate: "",                   // 퇴사일 (비어있으면 재직)
  certificates: [                   // 자격증 배열
    {
      name: "정보처리기사",
      date: "2019-05-20",
      education: {
        basic: "2020-06-01",
        intermediate: "2021-06-01",
        advanced: "2022-06-01",
        expert: "2023-06-01",
        evaluator: "2024-06-01"
      }
    }
  ],
  updatedAt: "2026-02-26T..."      // 최종 수정일
}
```

#### `annualLeaves` (연차 정보)
```javascript
{
  id: "leave_emp123_2026",          // 고유 ID
  employeeId: "emp123",             // 직원 ID
  fiscalYear: 2026,                 // 회계년도
  totalDays: 17,                    // 총 연차
  usedDays: 5.5,                    // 사용 연차
  yearsOfService: 5,                // 근속연수
  usageHistory: [                   // 사용 내역 배열
    {
      date: "2026-03-15",
      days: 1,
      reason: "병가",
      isBulk: false,
      recordDate: "2026-02-26T..."
    }
  ],
  createdAt: "2026-01-01T...",
  updatedAt: "2026-02-26T..."
}
```

#### `educations` (교육 일정)
```javascript
{
  id: "edu_emp123_기사_basic_...",   // 고유 ID
  employeeId: "emp123",             // 직원 ID
  employeeName: "홍길동",           // 직원명
  certificate: "정보처리기사",      // 자격증명
  certificateDate: "2019-05-20",   // 취득일
  type: "초급교육",                // 교육유형
  educationDate: "2020-06-01",     // 교육일정
  createdAt: "2026-02-26T..."
}
```

#### `sysUsers` (사용자 계정)
```javascript
{
  id: "admin",                      // 아이디 (고유)
  pw: "admin1234",                  // 비밀번호 (평문)
  name: "관리자",                   // 이름
  role: "admin",                    // 역할 (admin / user)
  status: "approved",               // 상태 (pending / approved / rejected)
  createdAt: "2026-02-26T...",
  approvedAt: "2026-02-26T..."      // 승인일
}
```

---

## 🔧 문제 해결

### Q1. "Firebase 연결 실패: Failed to fetch" 오류
**원인:**
- Firebase 프로젝트 ID나 API 키가 잘못됨
- Firestore 보안 규칙이 차단 중
- 로컬 파일(file://)로 실행 중

**해결:**
1. `index.html`의 `FB_PROJ`, `FB_KEY` 확인
2. Firestore 규칙을 `allow read, write: if true;`로 설정
3. 웹 서버(Live Server 등)로 실행

### Q2. "HTTP 403 PERMISSION_DENIED" 오류
**원인:** Firestore 보안 규칙이 접근을 차단

**해결:**
1. Firebase 콘솔 → Firestore → 규칙
2. 아래 규칙으로 변경 후 **게시**:
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

### Q3. 다른 컴퓨터에서 데이터가 안 보임
**원인:** Firebase 프로젝트 설정이 다름

**해결:**
- 모든 컴퓨터에서 **동일한 Firebase 프로젝트 ID와 API 키** 사용
- `index.html` 파일의 Firebase 설정 통일

### Q4. 로그인이 안 됨
**원인:**
- 기본 관리자 계정이 생성되지 않음
- 회원가입 후 승인 대기 상태

**해결:**
1. 브라우저 콘솔(F12) 확인
2. Firebase 콘솔에서 `sysUsers` 컬렉션 확인
3. 기본 관리자 계정이 없으면 수동 생성:
   - 문서 ID: `admin`
   - 필드: `id=admin`, `pw=admin1234`, `role=admin`, `status=approved`

---

## 📁 파일 구조

```
employee-management-system/
├── index.html          # 메인 HTML 파일 (모든 기능 포함)
└── README.md           # 이 문서
```

---

## 🌐 배포 방법

### Netlify 배포 (권장)
1. [Netlify](https://netlify.com) 로그인
2. **Sites** → **Add new site** → **Deploy manually**
3. `index.html` 파일 드래그 앤 드롭
4. 배포 완료 후 제공된 URL로 접속

### GitHub Pages 배포
```bash
# 1. GitHub 저장소 생성
git init
git add .
git commit -m "직원관리 시스템 초기 배포"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/employee-system.git
git push -u origin main

# 2. GitHub → Settings → Pages
# Source: main branch 선택
# 저장 후 https://YOUR_USERNAME.github.io/employee-system/ 접속
```

### Vercel 배포
1. [Vercel](https://vercel.com) 로그인
2. **New Project** 클릭
3. `index.html` 파일 업로드
4. 자동 배포 완료

---

## ⚠️ 보안 주의사항

### 1. Firestore 보안 규칙
현재 설정(`allow read, write: if true;`)은 **개발/테스트용**입니다.

**운영 환경 권장 규칙:**
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /sysUsers/{userId} {
      allow read: if request.auth != null;
      allow create: if true;  // 회원가입 허용
      allow update, delete: if request.auth.uid == userId;
    }
    match /employees/{document=**} {
      allow read, write: if request.auth != null;
    }
    match /annualLeaves/{document=**} {
      allow read, write: if request.auth != null;
    }
    match /educations/{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

### 2. 비밀번호 보안
- 현재 비밀번호는 **평문**으로 저장됩니다.
- 실제 운영 시 bcrypt 등 해시 함수 사용 권장
- 관리자 비밀번호를 반드시 변경하세요!

### 3. API 키 노출
- `index.html`에 API 키가 노출되어 있습니다.
- Firebase 보안 규칙으로 접근 제어 필수
- API 키 도메인 제한 설정 권장:
  - Google Cloud Console → API 및 서비스 → 사용자 인증 정보
  - API 키 클릭 → 애플리케이션 제한사항 → HTTP 참조자 설정

---

## 📊 현재 완료된 기능

### ✅ 구현 완료
- [x] Firebase Firestore REST API 연동
- [x] 로그인/회원가입/로그아웃
- [x] 관리자 승인 시스템
- [x] 직원 CRUD (등록/조회/수정/삭제)
- [x] 자격증 관리 (다중 등록)
- [x] 연차 자동 계산 (법정 기준)
- [x] 연차 사용 등록 및 내역 관리
- [x] 일괄 연차 부여
- [x] 교육 일정 자동 생성
- [x] D-Day 계산 및 상태 표시
- [x] 데이터 백업/복구 (JSON)
- [x] 반응형 디자인
- [x] 대시보드 통계

### 🔜 추후 개선 가능 항목
- [ ] 비밀번호 해시 암호화
- [ ] 파일 첨부 기능 (이력서, 증명서 등)
- [ ] 이메일 알림 (교육 임박, 연차 승인 등)
- [ ] 부서/직급 관리
- [ ] 급여 관리
- [ ] 권한 세분화 (부서장, 팀장 등)
- [ ] 로그 기록 (사용자 활동 추적)
- [ ] 통계 차트 (Chart.js 연동)
- [ ] 엑셀 내보내기/가져오기

---

## 📞 문의 및 지원

- **프로젝트 버전**: 1.0.0
- **최종 업데이트**: 2026-02-26
- **Firebase 프로젝트**: `sangwon-hrm` (기본값)
- **라이선스**: MIT

---

## 🎯 권장 사용 시나리오

### 소규모 사업장 (10~50명)
- 직원 정보 통합 관리
- 연차 자동 계산으로 업무 효율 향상
- 자격증 교육 일정 놓치지 않기

### 교육기관
- 강사 자격증 관리
- 교육 이수 일정 추적
- 회원 승인 시스템 활용

### 프리랜서 팀
- 팀원 정보 관리
- 프로젝트별 참여 인원 파악
- 백업을 통한 데이터 이동

---

## 🙏 감사합니다!

이 시스템이 업무 효율 향상에 도움이 되길 바랍니다. 😊

**시작하기**: `index.html` 파일을 웹 서버에서 실행하고 `admin / admin1234`로 로그인하세요!
