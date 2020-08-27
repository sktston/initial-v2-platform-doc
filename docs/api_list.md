<p>Initial Platform Console API Documents</p>
An API to issue, manage, and verify self-sovereign identity credentials

### Swagger API Service Link

##### Non-interactive Swagger API Service Link
<a href="https://app.swaggerhub.com/apis-docs/khujin1/Initial-REST-API-Service/v0.5.3"  target="_blank">Initial-REST-API Swagger API Site</a>
<br>Note: The interactive forms are disabled, as no `host` property was set in the specification. 

##### Interactive Swagger API Service Link
<a href="http://172.27.18.102:8021/api/doc"  target="_blank">SKT Initial-REST-API Swagger API Site</a>
<br>Note: This site is only for SKT internal user. External user access is not allowed.


### Initial Cloud Agent REST APIs list

#### Wallet
- DID and Tag Policy Management APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `POST` | /wallet​/did​/create | Local non-Public DID 생성 
  `GET` | /wallet/did | DID list 출력 
  `POST` | /wallet/did/public | Cloud Agent의 Public DID를 새롭게 Assign(Steward, Endorser 역할을 가지고 있는 Issuer만 해당됨. 사전에 DID가 Ledger에 Public 하게 등록되어 있어야 함.) 
  `GET` | /wallet/did/public | 현재 Cloud Agent의 Public DID 출력 
  `POST` | /wallet/set-did-endpoint | DID의 Endpoint setting 
  `GET` | /wallet/set-did-endpoint | DID Endpoint 정보 출력 
  `PATCH` | /wallet/did/local/rotate-keypair | Rotate keypair for a local non-public DID 



#### Connection
- Connection Management APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `GET` | /connections | Agent to Agent connections ist 출력
  `GET` | /connections/{conn_id} | 특정 connection ID 기록 출력
  `POST` | /connections/create-static | Public DID를 Assign 
  `POST` | [/connections/create-invitation](../auto_connection/#step-1-faber-alice-create-invitation-send-invitation) | 현재 할당된 Public DID 출력 
  `POST` | **[/connections/receive-invitation](../auto_connection/#step-2-alice-receive-invitation-request-connection)** | DID에 Endpoint setting 
  `POST` | **[/connections/{conn_id}/accept-invitation](../manual_connection/#step-3-alice-faber-accept-invitation-request-connection)** | DID Endpoint 정보 출력 
  `POST` | /connections/{conn_id}/accept-request | Rotate keypair for a local non-public DID 
  `POST` | /connections/{conn_id}/establish-inbound/{ref_id} | Rotate keypair for a local non-public DID 
  `POST` | /connections/{conn_id}/remove | Rotate keypair for a local non-public DID 


#### Schema
- Schema operation APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `POST` | ​**[/schemas](../schema_creddef/#step-1-faber-create-schema)** | Sends a schema to the ledger
  `GET` | /schemas/created | Search for matching schema that agent originated
  `GET` | /schemas/{schema_id} | Gets a schema from the ledger

#### credential-definition
- Credential definition operations APIs

   Method  | Resource | Description 
  :---: | --- | --- 
  `POST` | ​**[/credential-definitions](../schema_creddef/#step-2-faber-create-credential-definition-revocation-registry)** | Sends a credential definition to the ledger
  `GET` | ​/credential-definitions​/created | Search for matching credential definitions that agent originated
  `GET` | /credential-definitions/{cred_def_id} | Gets a credential definition from the ledger
  
  
#### issue-credential
- Credential issue, revocation

   Method  | Resource | Description 
  :---: | --- | --- 
  `GET` | ​/issue-credential/records | Fetch all credential exchange records
  `GET` | /issue-credential/records/{cred_ex_id} | Fetch a single credential exchange record
  `POST` | /issue-credential/create | Send holder a credential, automating entire flow
  `POST` | /issue-credential/send | Send holder a credential, automating entire flow
  `POST` | /issue-credential/send-proposal | Send issuer a credential proposal
  `POST` | /issue-credential/send-offer | Send holder a credential offer, independent of any proposal
  `POST` | /issue-credential/records/{cred_ex_id}/send-offer | Send holder a credential offer in reference to a proposal with preview
  `POST` | /issue-credential/records/{cred_ex_id}/send-request | Send issuer a credential request
  `POST` | /issue-credential/records/{cred_ex_id}/issue | Send holder a credential
  `POST` | /issue-credential/records/{cred_ex_id}/store | Store a received credential
  `POST` | /issue-credential/revoke | Revoke an issued credential
  `POST` | /issue-credential/publish-revocations | Publish pending revocations to ledger
  `POST` | /issue-credential/clear-pending-revocations | Clear pending revocations
  `POST` | /issue-credential/records/{cred_ex_id}/remove | /issue-credential/records/{cred_ex_id}/remove
  `POST` | /issue-credential/records/{cred_ex_id}/problem-report | Send a problem report for credential exchange

#### present-proof
- Proof presentation

   Method  | Resource | Description 
  :---: | --- | --- 
  `GET` | /present-proof/records | Fetch all present-proof exchange records
  `GET` | /present-proof/records/{pres_ex_id} | Fetch a single presentation exchange record
  `GET` | /present-proof​/records​/{pres_ex_id}​/credentials | Fetch credentials for a presentation request from wallet
  `POST` | /present-proof​/send-proposal| Sends a presentation proposal
  `POST` | /present-proof/create-request | Creates a presentation request not bound to any proposal or existing connection
  `POST` | /present-proof/send-request | Sends a free presentation request not bound to any proposal 
  `POST` | /present-proof/records/{pres_ex_id}/send-request | Sends a presentation request in reference to a proposal
  `POST` | /present-proof​/records​/{pres_ex_id}​/send-presentation | Sends a proof presentation
  `POST` | /present-proof/records/{pres_ex_id}/verify-presentation | Verify a received presentation
  `POST` | /present-proof/records/{pres_ex_id}/remove | Remove an existing presentation exchange record  
