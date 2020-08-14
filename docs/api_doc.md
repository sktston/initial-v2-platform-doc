# Initial Platform Console API Documents
An API to issue, manage, and verify self-sovereign identity credentials

### Initial Cloud Agent REST APIs

#### Wallet - DID and Tag Policy Management APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `POST` | /wallet​/did​/create | Local non-Public DID 생성 
  `GET` | /wallet/did | DID list 출력 
  `POST` | /wallet/did/public | Cloud Agent의 Public DID를 새롭게 Assign(Steward, Endorser 역할을 가지고 있는 Issuer만 해당됨. 사전에 DID가 Ledger에 Public 하게 등록되어 있어야 함.) 
  `GET` | /wallet/did/public | 현재 Cloud Agent의 Public DID 출력 
  `POST` | /wallet/set-did-endpoint | DID의 Endpoint setting 
  `GET` | /wallet/set-did-endpoint | DID Endpoint 정보 출력 
  `PATCH` | /wallet/did/local/rotate-keypair | Rotate keypair for a local non-public DID 



#### Connection - Connection Management APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `GET` | /connections | Agent to Agent connections ist 출력
  `GET` | /connections/{conn_id} | 특정 connection ID 기록 출력
  `POST` | /connections/create-static | Public DID를 Assign 
  `POST` | /connections/create-invitation | 현재 할당된 Public DID 출력 
  `POST` | /connections/receive-invitation | DID에 Endpoint setting 
  `POST` | /connections/{conn_id}/accept-invitation | DID Endpoint 정보 출력 
  `POST` | /connections/{conn_id}/accept-request | Rotate keypair for a local non-public DID 
  `POST` | /connections/{conn_id}/establish-inbound/{ref_id} | Rotate keypair for a local non-public DID 
  `POST` | /connections/{conn_id}/remove | Rotate keypair for a local non-public DID 
