# 데이터베이스 설정 가이드

정운 투표 사이트에서 투표 결과와 이메일 로그를 저장하기 위한 데이터베이스 설정 방법을 안내합니다.

## 🔥 Firebase Firestore 설정

### 1. Firebase 프로젝트 생성
1. [Firebase Console](https://console.firebase.google.com/) 접속
2. "프로젝트 추가" 클릭
3. 프로젝트 이름 입력 (예: `jeongun-vote-db`)
4. Google Analytics 설정 (선택사항)

### 2. Firestore 데이터베이스 생성
1. 좌측 메뉴에서 "Firestore Database" 선택
2. "데이터베이스 만들기" 클릭
3. 보안 규칙: "테스트 모드에서 시작" 선택
4. 위치: `asia-northeast3 (Seoul)` 선택

### 3. 웹 앱 설정
1. 프로젝트 개요 → "웹 앱 추가" (</> 아이콘)
2. 앱 닉네임 입력
3. Firebase SDK 설정 정보 복사
4. 투표 사이트의 DB 설정에서 다음 정보 입력:
   - API Key
   - Project ID
   - Auth Domain

### 4. 보안 규칙 설정 (선택사항)
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true; // 개발용 - 실제 운영시 수정 필요
    }
  }
}
```

## 📊 Airtable 설정

### 1. Airtable 계정 생성
1. [Airtable](https://airtable.com/) 가입
2. 새 베이스 생성

### 2. 테이블 구조 설정

#### Votes 테이블
| 필드명 | 타입 | 설명 |
|--------|------|------|
| Voter Email | Single line text | 투표자 이메일 |
| Vote Type | Single select | know, dontKnow |
| Timestamp | Date | 투표 시간 |
| IP Address | Single line text | IP 주소 |
| User Agent | Long text | 브라우저 정보 |

#### EmailLogs 테이블
| 필드명 | 타입 | 설명 |
|--------|------|------|
| Recipient Email | Single line text | 수신자 이메일 |
| Status | Single select | success, failed |
| Know Votes | Number | 안다 투표 수 |
| Dont Know Votes | Number | 모른다 투표 수 |
| Total Votes | Number | 총 투표 수 |
| Timestamp | Date | 발송 시간 |
| Error Message | Long text | 오류 메시지 |

### 3. API 키 생성
1. 계정 설정 → API → Personal access tokens
2. "Create new token" 클릭
3. 권한: `data.records:read`, `data.records:write` 선택
4. 베이스 선택
5. 생성된 토큰을 투표 사이트 DB 설정에 입력

### 4. Base ID 확인
- Airtable 베이스 URL에서 `app` 다음의 문자열이 Base ID
- 예: `https://airtable.com/appABC123DEF456/tblXYZ789` → `appABC123DEF456`

## 📈 Google Sheets 설정

### 1. Google Cloud Console 설정
1. [Google Cloud Console](https://console.cloud.google.com/) 접속
2. 새 프로젝트 생성 또는 기존 프로젝트 선택
3. API 및 서비스 → 라이브러리
4. "Google Sheets API" 검색 후 활성화

### 2. API 키 생성
1. API 및 서비스 → 사용자 인증 정보
2. "사용자 인증 정보 만들기" → "API 키"
3. 생성된 API 키를 투표 사이트 DB 설정에 입력

### 3. 스프레드시트 생성
1. [Google Sheets](https://sheets.google.com/) 접속
2. 새 스프레드시트 생성

#### Votes 시트 구조
| A | B | C | D | E |
|---|---|---|---|---|
| Timestamp | Voter Email | Vote Type | IP Address | User Agent |

#### EmailLogs 시트 구조
| A | B | C | D | E | F | G |
|---|---|---|---|---|---|---|
| Timestamp | Recipient Email | Status | Know Votes | Dont Know Votes | Total Votes | Error Message |

### 4. 스프레드시트 공개 설정
1. 공유 → "링크가 있는 모든 사용자" 권한을 "편집자"로 설정
2. 또는 API 키로 접근할 수 있도록 권한 설정

### 5. Spreadsheet ID 확인
- Google Sheets URL에서 `/d/` 다음의 문자열이 Spreadsheet ID
- 예: `https://docs.google.com/spreadsheets/d/1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgvE2upms/edit` 
- → `1BxiMVs0XRA5nFMdKvBdBZjgmUUqptlbs74OgvE2upms`

## 🚀 투표 사이트에서 설정하기

1. 투표 사이트 접속
2. 투표 진행 후 로그 섹션 확인
3. "DB 설정" 버튼 클릭
4. 원하는 데이터베이스 체크박스 선택
5. 각 데이터베이스의 설정 정보 입력
6. "설정 저장" 클릭

## 📝 데이터 구조

### 투표 데이터 (votes)
```json
{
  "voterEmail": "user@example.com",
  "voteType": "know", // "know" 또는 "dontKnow"
  "timestamp": "2024-10-27T10:30:00Z",
  "ipAddress": "192.168.1.1",
  "userAgent": "Mozilla/5.0..."
}
```

### 이메일 로그 데이터 (emailLogs)
```json
{
  "recipientEmail": "user@example.com",
  "status": "success", // "success" 또는 "failed"
  "voteResults": {
    "knowVotes": 6,
    "dontKnowVotes": 4,
    "totalVotes": 10
  },
  "timestamp": "2024-10-27T10:35:00Z",
  "errorMessage": null,
  "emailjsResponse": {
    "status": 200,
    "text": "OK"
  }
}
```

## 🔒 보안 고려사항

1. **API 키 관리**: API 키는 안전한 곳에 보관하고 정기적으로 갱신
2. **접근 권한**: 필요한 최소한의 권한만 부여
3. **데이터 암호화**: 민감한 정보는 암호화하여 저장
4. **로그 모니터링**: 비정상적인 접근 시도 모니터링

## 🛠️ 문제 해결

### Firebase 연결 실패
- API 키와 프로젝트 ID 확인
- 브라우저 콘솔에서 오류 메시지 확인
- Firebase 프로젝트 설정에서 도메인 허용 목록 확인

### Airtable 연결 실패
- API 키 권한 확인
- Base ID 정확성 확인
- 테이블명과 필드명 일치 여부 확인

### Google Sheets 연결 실패
- API 키 활성화 상태 확인
- 스프레드시트 공유 권한 확인
- Spreadsheet ID 정확성 확인

## 📞 지원

문제가 발생하면 각 서비스의 공식 문서를 참조하거나 커뮤니티에 문의하세요:

- [Firebase 문서](https://firebase.google.com/docs)
- [Airtable API 문서](https://airtable.com/developers/web/api/introduction)
- [Google Sheets API 문서](https://developers.google.com/sheets/api)
