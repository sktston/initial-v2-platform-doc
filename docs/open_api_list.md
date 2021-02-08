<p>initial Platform DID Agent API Documents</p>

### DID Agent 안내 
- 기관사용자는 아래의 DID API를 사용하여 Legacy 시스템(ERP, HR시스템 등)과 연동하여 증명서를 손쉽게 발생할 수 있다.
- initial Platform의 DID Agent는 Event Driven(이벤트 구동형) 방식이기 때문에 기관 사용자는 Webhook 서버를 준비하고 url 등록해아 한다.

### DID Agent API 지원 List

#### Mandatory(필수) APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `POST` | **[/connections/create-invitation](../auto_connection/#step-1-faber-alice-create-invitation-send-invitation)** | 새로운 초대장 만들기 
  `POST` | **[/issue-credential/send-offer](#)** | 발행자(issuer)가 사용자(holder)에게 VC 보내기
  `POST` | **[/present-proof/send-request](#)** | 검증자(Verifier)가 사용자(holder)에게 VC 검증 요청하기 

<br>

#### Optional Service APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `GET` | /connections | 모든 연결(connections) list 출력
  `GET` | /connections/{conn_id} | 특정 connection ID 정보 출력
  `POST` | /connections/{conn_id}/send-message| Send a basic message to a connection (지원예정)
  
  <br>
  
### Webhook 서버 개발 안내 
- initial DID Agent는 event driven 방식으로 REST API 요청에 대한 응답을 Webhook 서버로 전달합니다
- 처리결과를 받기 위한 기관 사용자의 Webhook URL은 기관정보에 필수 등록해야 합니다. e.g)https://domain.com/webhooks
- Webhook 서버의 API Key는 보안 강화를 위한 선택 사항입니다. https://domain.com/webhooks#org-api-key 와 같이 입력하시면 header의 x-api-key 항목에 값으로 "org-api-key"를 함께 전송합니다.
- 기관 사용자는 보안강화를 위한 IP기반 방화벽 설정 하세요 (initial platform의 IP대역 xxx.xxx.xxx.xxx/24는 별도로 안래 예정입니다)
- Webhook 서버 예제는 아래 reference code (DID 발행/검증 Demo)에서 확인할 수 있습니다. 


![platform arch](img/initial_platform_architecture.png)