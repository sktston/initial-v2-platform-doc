<p>initial Platform DID Agent API Documents</p>

### DID Agent 안내 
- 기관사용자는 아래의 DID API를 사용하여 Legacy 시스템(ERP, HR시스템 등)과 연동 및 증명서를 손쉽게 발생할 수 있다.
- initial DID Platform의 DID Agent는 Event Driven(이벤트 구동형) 방식이기 때문에 기관 사용자는 Webhook 서버를 준비하고 url 등록해아 한다.

### DID Cloud Agent Open API Swagger page

initial console 가입 Master 회원은, 아래 사이트에서 Test 가능하다.  

[Open API Swagger Site 이동](https://app.swaggerhub.com/apis-docs/khujin1/initial_Cloud_Agent_Open_API/1.0.4#/)

### DID Agent API 지원 List

#### Mandatory(필수) APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `POST` | ** /connections/create-invitation ** | 새로운 초대장 만들어서 연결하기 
  `POST` | ** /connections/{conn_id}/send-message ** | 연결된 사용자에게 Message(개인정보 이용 동의서등)
  `POST` | ** /issue-credential/send-offer ** | 발행자(issuer)가 사용자(holder)에게 VC 발행
  `POST` | ** /issue-credential/records/{cred_ex_id}/send-offer ** | 사용자에게 받은 proposal 기반 발행자(issuer)가 사용자(holder)에게 VC 발행
  `POST` | ** /present-proof/send-verification-request ** | 검증자(verifier)가 사용자(holder)에게 VC 검증 요청하기 
  `POST` | /revocation/revoke | 발행자(issuer)가 사용자(holder)에게 발행한 VC 폐기

<br>

#### Optional Service APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `GET` | /connections | 모든 연결(connections) list 출력
  `GET` | /connections/{conn_id} | 특정 connection ID 정보 출력
  `GET` | /issue-credential/records | credential 발급관련 모든 기록 출력
  `GET` | /present-proof/records | 모든 Verify(present-proof) 기록 출력
  `GET` | /resolver/resolve/{did} | W3C DID Doc 가져오기
  `POST` | /issue-credential/records/{cred_ex_id}/problem-report | Issue Problem report 보내기
  `POST` | /present-proof/records/{pres_ex_id}/problem-report | Verify Problem report 보내기


  
  <br>
  
#### Holder Cloud Wallet APIs (추후 공개 예정)

   Method  | Resource | Description 
  :---: | --- | --- 
  `POST` | /connections/receive-invitation | 초대 수락하며 연결 하기
  `POST` | /issue-credential/records/{cred_ex_id}/send-request | VC 받기
  `POST` | /present-proof/records/{pres_ex_id}/send-presentation | VP(Verifiable Presentation) 보내기 
  `GET` | /credentials | 모든 VC list 가져오기 
  `DELETE` | /credential/{credential_id} | 특정 VC 삭제하기 
