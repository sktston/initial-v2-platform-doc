Auto Connection Example
================

<div class="admonition note">
<p class="admonition-title">note</p>
<p> API 사용을 위해서는 Access Token이 필요함 </p>
</div>

- Access Token Example
```
curl --location --request GET 'http://localhost/wallet/did'\
--header 'Authorization: Bearer ab7aca56-5c36-4fbe-a9fe-2ae4937c63de'
```

<br><br>


**Faber**(Issuer/Verifier)와 **Alice**(Holder/Prover)의 Connection(연결) 예제<br>
<br>

![did connections](img/connections.png)


Connection이란 Peer to Peer 사이 Pairwise ID 및 PKI(Public Key Infrastructure) 기반으로 신뢰있는 통신 체널을 구축하는 개념이다. <br>
Auto Connection은 최소한의 API 사용으로 Key 생성 및 교환으로 상호 연결을 완료한다.
<br>

Connection State는 아래와 같음.

Topic | State | Description
--- | --- | ---
Connection | invitation | Invitation 생성하거나 받은 상태
Connection | request | Connection 요청한 상태
Connection | response | Connection accept 상태
Connection | active | Connection 완료 

<p></p>
#### Auto Connection API 사용 Summary
1. Faber(기관/발행자) : /connections/create-invitation (초대장 생성 API 호출)<br>
2. Alice(개인/Holder) : /connections/receive-invitation (초대장 수락 API 호출)<br>
3. Alice : connection request 자동 실행 <br>
4. Faber : accept connection request 자동 실행<br>

<br><br>

### STEP 1. Faber(Issuer/Verifier) --> Alice(Holder) : create invitation & send invitation.

* Method and Resource

    `POST` `/connections​/create-invitation` 새로운 초대장 생성
<p></p>

* Parameter

     Name | Description 
     --- | --- 
     alias | Connection 별칭 지정 (e.g 김증명_대학제증명연결)
     auto_accept | Alice가 초대장 수락 시 자동 connection 설정 (**default : true**)
     multi_use | 초대장을 once/multi 사용여부. QR코드등 인쇄시 `true` 설정 필요 (**default : false**)
     public | Public DID를 기반으로 초대장 생성 (**default : true**)

<p></p>

* Request Example 

```java
curl --location --request POST 'https://dev-console.myinitial.io/agent/api/connections/create-invitation?alias=intial%20test&auto_accept=true&public=true' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer 2xxxxxxx-1234-5678-abcd-c5fb0286f2cc' \
--data-raw ''
```

* Response body example(생성된 초대장)
```json
{
  "connection_id": null,
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "e21b7bc6-cd06-4f46-9d3b-a5c2319e4978",
    "label": "모바일학생증발급",
    "imageUrl": "https://kr.object.ncloudstorage.com/dev-image-file/d41d8cd9_9e72dab7_1618189875",
    "did": "did:ssw:3z2a2EKbB1HAejew3xavfA"
  },
  "invitation_url": "https://dev-console.myinitial.io/agent/endpoint?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiZTIxYjdiYzYtY2QwNi00ZjQ2LTlkM2ItYTVjMjMxOWU0OTc4IiwgImxhYmVsIjogIlx1YmFhOFx1YmMxNFx1Yzc3Y1x1ZDU1OVx1YzBkZFx1Yzk5ZFx1YmMxY1x1YWUwOSIsICJpbWFnZVVybCI6ICJodHRwczovL2tyLm9iamVjdC5uY2xvdWRzdG9yYWdlLmNvbS9kZXYtaW1hZ2UtZmlsZS9kNDFkOGNkOV85ZTcyZGFiN18xNjE4MTg5ODc1IiwgImRpZCI6ICJkaWQ6c3N3OjN6MmEyRUtiQjFIQWVqZXczeGF2ZkEifQ=="
}
```
    
   * Connection State check : `get` `/connections​/{conn_id}`
   
    |  | Faber | Alice |
    | --- | --- | --- |
    | connection state | **`invitation`** | N/A |
  

<p></p>
* Next Step
    
**invitation**내용이나 **invitation_url** 혹은 url의 **QR code**를 Alice에게 전달하여 초대.


<br>
<br>

### STEP 2. Alice(Holder) --> Faber(Issuer/Verifier) : receive invitation & request connection.



<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 Holder가 initial app 일 경우 SDK에서 자동으로 처리됨 </p>
</div>


<br><br>    

### STEP 3. Faber(Issuer/Verifier) : Webhook Message 확인 및 Connection 정보 관리 

webhook을 통해 아래 Message가 전달 됨.

기관 개발자는 
state로 연결 정상완료여부 확인하고, their_did(사용자 DID), connection_id(사용자와 communication 필요할때 사용하는 id)등을 확인 하면 된다.
사용자DID는 정책상 Privacy 보호를 위해 변경(앱재설치등)되기 때문에, 고객 식별자로 사용 불가능 하다. 
```json
{
  "invitation_mode":"once",
  "invitation_key":"2dJj8a9BZeEWfy5NyEmkzEiPu1qX5o6NCuUadnsX4VnT",
  "rfc23_state":"completed",
  "routing_state":"none",
  "accept":"auto",
  "created_at":"2021-04-23 03:56:27.025875Z",
  "state":"active",
  "their_did":"L4gGrQSBCgN2Cyspg33Ki9",
  "connection_id":"b257848f-5018-4dac-96c2-e403d96e499f",
  "my_did":"SxaJpncFYQKdHBGAX1EYuj",
  "updated_at":"2021-04-23 03:56:27.414165Z",
  "their_role":"inviter",
  "their_label":"대학학생증"
}
```


### [Option] Connection 정보 관리 Guide

Connection 정보는 기본적으로 기관 Wallet에 저장된다. STEP3의 Webhook으로 전달된 message 내용과 동일하다.
`GET` `/connections` 으로 connection을 확인하면 아래와 처럼 connection information 을 확인 할 수 있다.<br>
향후 해당 connection 기반으로 peer에게 message를 보내거나, 증명서 발행 요청 및 검증요청 하기 위해서는
connection id와 유저 정보를 alias 등으로 mapping 관리해야 한다.

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> 향후 OOB(Out-of-Band)기능 지원을 통해 connection을 자동관리할 예정 </p>
</div>


```json
        {
  "invitation_mode": "once",
  "invitation_key": "2dJj8a9BZeEWfy5NyEmkzEiPu1qX5o6NCuUadnsX4VnT",
  "rfc23_state": "completed",
  "routing_state": "none",
  "accept": "auto",
  "created_at": "2021-04-23 03:56:27.025875Z",
  "state": "active",
  "their_did": "L4gGrQSBCgN2Cyspg33Ki9",
  "connection_id": "b257848f-5018-4dac-96c2-e403d96e499f",
  "my_did": "SxaJpncFYQKdHBGAX1EYuj",
  "updated_at": "2021-04-23 03:56:27.414165Z",
  "their_role": "inviter",
  "their_label": "대학학생증"
}
```
<br><br>

### [Option] Delete Connection 

/connections/{conn_id}/remove