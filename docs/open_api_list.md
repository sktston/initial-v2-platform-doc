<p>Initial Platform API Documents</p>

Cloud Agent의 REST API List<br>
기관사업자의 Legacy 시스템(ERP, HR시스템 등)과 연동하여 증명서를 손쉽게 발생할 수 있다.

### API 지원 List

#### Mandatory(필수) APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `POST` | **[/connections/create-invitation](../auto_connection/#step-1-faber-alice-create-invitation-send-invitation)** | 새로운 초대장 만들기 
  `POST` | **[/issue-credential/send-offer](#)** | 발행자(issuer)가 사용자(holder)에게 VC 보내기
  `POST` | **[/present-proof/send-request](#)** | 검증자(Verifier)가 사용자(holder)에게 VC 검증 요청하기 



<br><br>

#### Optional Service APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `GET` | /connections | 모든 연결(connections) list 출력
  `GET` | /connections/{conn_id} | 특정 connection ID 정보 출력
  `POST` | /connections/{conn_id}/send-message| Send a basic message to a connection (지원예정)