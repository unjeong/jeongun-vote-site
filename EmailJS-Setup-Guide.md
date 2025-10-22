# EmailJS 설정 가이드

투표 결과를 실제 이메일로 발송하기 위해 EmailJS 서비스를 설정해야 합니다.

## 1. EmailJS 계정 생성

1. [EmailJS 웹사이트](https://www.emailjs.com/)에 접속
2. "Sign Up" 클릭하여 무료 계정 생성
3. 이메일 인증 완료

## 2. 이메일 서비스 연결

1. Dashboard → "Email Services" 클릭
2. "Add New Service" 클릭
3. Gmail, Outlook 등 원하는 이메일 서비스 선택
4. 서비스 연결 및 인증 완료
5. **Service ID** 복사 (예: `service_abc123`)

## 3. 이메일 템플릿 생성

1. Dashboard → "Email Templates" 클릭
2. "Create New Template" 클릭
3. 다음 템플릿 내용 입력:

### 템플릿 설정
- **Template Name**: `vote_result_template`
- **Subject**: `정운 투표 결과 - {{vote_date}}`

### 템플릿 내용 (HTML)
```html
<h2>🗳️ 정운 투표 결과</h2>

<p>안녕하세요 {{to_name}}님,</p>

<p>정운 투표에 참여해주셔서 감사합니다!</p>

<h3>📊 최종 투표 결과</h3>
<div style="background: #f8f9fa; padding: 20px; border-radius: 10px; margin: 20px 0;">
    <p><strong>총 투표 수:</strong> {{total_votes}}개</p>
    <p><strong>정운을 안다:</strong> {{know_votes}}표 ({{know_percentage}}%)</p>
    <p><strong>정운을 모른다:</strong> {{dont_know_votes}}표 ({{dont_know_percentage}}%)</p>
    <p><strong>투표 날짜:</strong> {{vote_date}}</p>
</div>

<p>투표에 참여해주셔서 다시 한 번 감사드립니다! 🎉</p>

<hr>
<p><small>이 메일은 정운 투표 사이트에서 자동으로 발송되었습니다.</small></p>
```

4. "Save" 클릭
5. **Template ID** 복사 (예: `template_xyz789`)

## 4. Public Key 확인

1. Dashboard → "Account" → "General" 탭
2. **Public Key** 복사 (예: `user_abcdefghijk`)

## 5. 코드에 설정값 적용

`index.html` 파일에서 다음 부분을 수정:

```javascript
// 466번째 줄 근처 - EMAILJS_CONFIG 객체 수정
const EMAILJS_CONFIG = {
    publicKey: "YOUR_PUBLIC_KEY",    // ← 여기에 Public Key 입력
    serviceId: "YOUR_SERVICE_ID",    // ← 여기에 Service ID 입력
    templateId: "YOUR_TEMPLATE_ID"   // ← 여기에 Template ID 입력
};
```

### 예시:
```javascript
const EMAILJS_CONFIG = {
    publicKey: "user_abcdefghijk",
    serviceId: "service_abc123", 
    templateId: "template_xyz789"
};
```

### ⚠️ 중요: 설정값 확인 방법

1. **Public Key 확인**:
   - [EmailJS Dashboard](https://dashboard.emailjs.com/admin/account) → Account 탭
   - "Public Key" 섹션에서 복사

2. **Service ID 확인**:
   - EmailJS Dashboard → Email Services
   - 연결한 서비스의 "Service ID" 복사

3. **Template ID 확인**:
   - EmailJS Dashboard → Email Templates  
   - 생성한 템플릿의 "Template ID" 복사

## 6. 테스트

1. 웹사이트에서 이메일 입력
2. 10번 투표 완료
3. 입력한 이메일로 결과 메일 수신 확인

## 주의사항

- EmailJS 무료 플랜: 월 200개 이메일 제한
- 실제 배포 전 반드시 테스트 필요
- Public Key는 공개되어도 안전함 (클라이언트용)
- Service ID와 Template ID도 공개되어도 안전함

## 문제 해결

### 이메일이 안 보내질 때:
1. EmailJS 콘솔에서 로그 확인
2. 스팸 폴더 확인
3. 이메일 서비스 연결 상태 확인
4. 브라우저 개발자 도구에서 에러 메시지 확인

### 설정이 어려울 때:
- 현재는 데모 모드로 작동 (실제 이메일 발송 없이 성공 메시지만 표시)
- 나중에 EmailJS 설정 후 적용 가능
