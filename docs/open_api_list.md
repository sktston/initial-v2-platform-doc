**initial Cloud Agent APIs**
===
<p>initial Platform DID Agent API Documents</p>

### **DID Agent 안내** 
- 기관사용자는 아래의 DID API를 사용하여 Legacy 시스템(ERP, HR시스템 등)과 연동 및 증명서를 손쉽게 발생할 수 있습니다.
- initial DID Platform의 DID Agent는 Event Driven(이벤트 구동형) 방식이기 때문에 기관 사용자는 Webhook 서버를 준비하고 url 등록해아 합니다.

### **DID Cloud Agent Open API Swagger page**

initial console 가입 Master 회원은, 아래 사이트에서 Test 가능합니다.  

[Open API Swagger Site 이동](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/)

### **DID Agent REST API Method**

1. 기본 URI : 
      - {domain}/Resorce/Method
      - e.g. `https://dev-console.myinitial.io/agent/api/connections/create-invitation`
2. 요청형식 (Header)
      - ** Content-Type : application/json **
      - ** Accept: application/json **
      - ** Authorization: {{Bearer token}} **
      - e.g. `curl -X POST "https://dev-console.myinitial.io/agent/api/connections/create-invitation" -H "accept: application/json" -H "Authorization: Bearer 2ca4dd8a-1234-1234-1234-c5fb0286f2cc" -H "Content-Type: application/json" -d "{}"`
3. 응답형식 (Header)
      - Content-Type : application/json; charset=utf-8

### **DID Agent API 지원 List**

#### Mandatory(필수) APIs

   Method  | Resource | Description | Swagger 
  :---: | --- | --- | :---:
  `POST` | ** [/connections/create-invitation](https://initial-v2-platform.readthedocs.io/ko/master/open_api_auto_connection/#step-1-invitation) ** | 새로운 (invitation) 초대장 만들어서 연결하기 |  [TEST](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/connection/post_connections_create_invitation)
  `POST` | ** [/connections/{conn_id}/send-message](https://initial-v2-platform.readthedocs.io/ko/master/open_api_message) ** | 연결된 사용자에게 Message(개인정보 이용 동의서등) | [TEST](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/basicmessage/post_connections__conn_id__send_message)
  `POST` | ** [/issue-credential/records/{cred_ex_id}/send-offer](https://initial-v2-platform.readthedocs.io/ko/master/open_api_proof/#step-1) ** | 발행자(issuer)가 사용자(holder)에게 VC 발행 | [TEST](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/issue-credential%20v1.0/post_issue_credential_records__cred_ex_id__send_offer)
  `POST` | ** [/present-proof/send-verification-request](https://initial-v2-platform.readthedocs.io/ko/master/open_api_proof/#step-1) ** | 검증자(verifier)가 사용자(holder)에게 VC 검증 요청하기 | [TEST](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/present-proof%20v1.0/post_present_proof_send_verification_request)
  `POST` | [/revocation/revoke](https://initial-v2-platform.readthedocs.io/ko/master/open_api_revocation/#step-2-vc-revoke) | 발행자(issuer)가 사용자(holder)에게 발행한 VC 폐기 | [TEST](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/revocation/post_revocation_revoke)

<br>

#### Optional Service APIs

   Method  | Resource | Description | Swagger
  :---: | --- | --- | :---:
  `GET` | /connections | 모든 연결(connections) list 확인 | [TEST](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/connection/get_connections)
  `GET` | /connections/{conn_id} | 특정 connection ID 정보 확인 | [TEST](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/connection/get_connections__conn_id_)
  `GET` | /issue-credential/records | credential 발급관련 모든 기록 확인
  `GET` | /present-proof/records | 모든 Verify(present-proof) 기록 확인
  `GET` | /resolver/resolve/{did} | W3C DID Doc 가져오기
  `POST` | /issue-credential/records/{cred_ex_id}/problem-report | 발급 과정 취소
  `POST` | /present-proof/records/{pres_ex_id}/problem-report | 검증/제출 과정 취소

