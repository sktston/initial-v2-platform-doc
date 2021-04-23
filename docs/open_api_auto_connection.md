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

### STEP 1. Faber --> Alice : create invitation & send invitation.

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

* Example 

    * input <br>
    `auto_accept` : `true`<br>
    `multi_use` : `false`<br>
    `public` : `true`<br>
<br>
    * Response body (생성된 초대장)
```json
{
  "connection_id": "eb180f76-0081-41db-a12c-454fe36b3295",
  "invitation": {
    "@type": "did:sov:BzCbsNYhMrjHiqZDTUASHg;spec/connections/1.0/invitation",
    "@id": "66e7239f-93fe-47f2-939d-ddbc31f929d6",
    "label": "faber.agent",
    "serviceEndpoint": "http://host.docker.internal:8020",
    "recipientKeys": [
      "9RGU3g5Mm4Pziza3E3im12pPjFxPhaFDkZWQQvqjw9Sm"
    ]
  },
  "invitation_url": "http://host.docker.internal:8020?c_i=eyJAdHlwZSI6ICJkaWQ6c292OkJ6Q2JzTlloTXJqSGlxWkRUVUFTSGc7c3BlYy9jb25uZWN0aW9ucy8xLjAvaW52aXRhdGlvbiIsICJAaWQiOiAiNjZlNzIzOWYtOTNmZS00N2YyLTkzOWQtZGRiYzMxZjkyOWQ2IiwgImxhYmVsIjogImZhYmVyLmFnZW50IiwgInNlcnZpY2VFbmRwb2ludCI6ICJodHRwOi8vaG9zdC5kb2NrZXIuaW50ZXJuYWw6ODAyMCIsICJyZWNpcGllbnRLZXlzIjogWyI5UkdVM2c1TW00UHppemEzRTNpbTEycFBqRnhQaGFGRGtaV1FRdnFqdzlTbSJdfQ==",
  "alias": "create_invitation"
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

### STEP 2. Alice --> Faber : receive invitation & request connection.



<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> STEP2는 Holder가 initial app 일 경우 SDK에서 자동으로 처리됨 </p>
</div>


<br><br>    
    
### [Option] Connection 정보 관리 Guide

Connection 정보는 기본적으로 Wallet에 저장된다.
`GET` `/connections` 으로 connection을 확인하면 

아래와 처럼 connection information 을 확인 할 수 있다.<br>
향후 해당 connection 기반으로 peer에게 message를 보내거나, 증명서 발행 요청 및 검증요청 하기 위해서는
connection id와 유저 정보를 alias 등으로 mapping 관리해야 한다.

<div class="admonition Note">
<p class="admonition-title">Note</p>
<p> 향후 OOB(Out-of-Band)기능 지원을 통해 connection을 자동관리할 예정 </p>
</div>


```json
{
  "results": [
    {
      "accept": "auto",
      "initiator": "self",
      "connection_id": "3c6dd936-cde1-465c-b917-d7601933ecaf",
      "created_at": "2020-08-27 08:20:21.355788Z",
      "state": "active",
      "their_label": "alice.agent",
      "my_did": "EXouGSxBs9FDk2Kncp49m9",
      "routing_state": "none",
      "updated_at": "2020-08-27 08:20:57.787795Z",
      "invitation_mode": "once",
      "their_did": "UuLvvU78qtHEJY1UNgHxj",
      "invitation_key": "5pSn7c4Miomp6HeXxuVZdvJmAF4npSW1Np1N2hZbZWqV"
    }
  ]
}
```
<br><br>

### [Option] Delete Connection 

/connections/{conn_id}/remove